---
title: Szolgáltatások közötti alkalmazások Azure Active Directory
description: Leírja, hogy milyen szolgáltatások közötti alkalmazások és az alkalmazás típusához tartozó protokollok, regisztráció és tokenek lejáratának alapjai.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154763"
---
# <a name="service-to-service-apps"></a>Szolgáltatások közötti alkalmazások

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A szolgáltatások közötti alkalmazások lehetnek olyan démonok vagy kiszolgálóalkalmazások, amelyeknek egy webes API-ból kell lekérniük az erőforrásokat. A szakaszra két aleset vonatkozik:

- Egy, a OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési típusán alapuló webes API-t meghívó démon

    Ebben az esetben fontos megérteni néhány dolgot. Először is a felhasználói interakció nem lehetséges egy Daemon-alkalmazásban, amelyhez az alkalmazásnak saját identitása szükséges. Egy démon-alkalmazás például egy batch-feladat, vagy egy, a háttérben futó operációsrendszer-szolgáltatás. Az ilyen típusú alkalmazás hozzáférési jogkivonatot kér az alkalmazás identitásával, és megjeleníti az alkalmazás AZONOSÍTÓját, hitelesítő adatait (jelszavát vagy tanúsítványát), valamint az alkalmazás AZONOSÍTÓjának URI-JÁT az Azure AD-hez. A sikeres hitelesítés után a démon kap egy hozzáférési jogkivonatot az Azure AD-től, amelyet a rendszer a webes API meghívására használ.

- Egy olyan kiszolgálói alkalmazás (például webes API), amelynek meg kell hívnia egy webes API-t, amely a OAuth 2,0-re épül a draft-specifikáció alapján.

    Ebben az esetben Képzelje el, hogy egy felhasználó natív alkalmazáson hitelesített, és ez a natív alkalmazásnak webes API-t kell meghívnia. Az Azure AD egy JWT hozzáférési tokent bocsát ki a webes API meghívásához. Ha a webes API-nak egy másik alsóbb rétegbeli webes API-t kell meghívnia, akkor használhatja a folyamaton belüli folyamatot a felhasználó identitásának delegálására és a második szintű webes API-ra való hitelesítésre.

## <a name="diagram"></a>Ábra

![Daemon vagy Server alkalmazás webes API-diagramra](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll folyamatábrája

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Alkalmazás-identitás OAuth 2,0 ügyfél-hitelesítő adatokkal

1. Először is a kiszolgálónak az Azure AD-vel kell hitelesítenie magát, anélkül, hogy emberi beavatkozást kellene végeznie, például egy interaktív bejelentkezési párbeszédpanelt. Kérést küld az Azure AD jogkivonat-végpontjának, amely megadja a hitelesítő adatokat, az alkalmazás AZONOSÍTÓját és az alkalmazás-azonosító URI-t.
1. Az Azure AD hitelesíti az alkalmazást, és egy JWT hozzáférési tokent ad vissza, amely a webes API meghívására szolgál.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT hozzáférési tokent használja, hogy hozzáadja a JWT karakterláncot a "tulajdonos" jelöléssel a webes API-nak küldött kérelem engedélyezési fejlécében. A webes API ezt követően ellenőrzi az JWT tokent, és ha az érvényesítés sikeres, a visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegált felhasználói azonosító a OAuth 2,0-ben – a draft-specifikáció nevében

Az alább tárgyalt folyamat azt feltételezi, hogy egy felhasználó egy másik alkalmazásban (például egy natív alkalmazáson) lett hitelesítve, és a felhasználói identitásuk hozzáférési token beszerzésére lett használva az első szintű webes API-hoz.

1. A natív alkalmazás elküldi a hozzáférési jogkivonatot az első szintű webes API-nak.
1. Az első rétegbeli webes API kérelmet küld az Azure AD jogkivonat-végpontjának, amely az alkalmazás AZONOSÍTÓját és hitelesítő adatait, valamint a felhasználó hozzáférési jogkivonatát adja meg. Emellett a rendszer egy on_behalf_of paraméterrel küldi el a kérelmet, amely azt jelzi, hogy a webes API új jogkivonatokat kér, hogy az alárendelt webes API-t hívja az eredeti felhasználó nevében.
1. Az Azure AD ellenőrzi, hogy az első szintű webes API-nak van-e engedélye a második szintű webes API eléréséhez, és érvényesíti a kérést, JWT hozzáférési tokent és JWT frissítési tokent ad vissza az első szintű webes API-hoz.
1. A HTTPS-en keresztül az első rétegbeli webes API ezt követően meghívja a második rétegbeli webes API-t úgy, hogy hozzáfűzi a jogkivonat-karakterláncot a kérelemben található engedélyezési fejlécben. Az első rétegbeli webes API továbbra is meghívhatja a második rétegbeli webes API-t, amíg a hozzáférési jogkivonat és a frissítési tokenek érvényesek.

## <a name="code-samples"></a>Kódminták

Tekintse meg a Daemon vagy a Server Application webes API-forgatókönyvekre vonatkozó példákat: [kiszolgáló vagy démon alkalmazás webes API-hoz](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Alkalmazásregisztráció

* Egyetlen bérlő – az alkalmazás identitása és a delegált felhasználó identitása esetében a démont vagy a kiszolgálói alkalmazást ugyanabban a címtárban kell regisztrálni az Azure AD-ben. A webes API beállítható úgy, hogy a démon vagy a kiszolgáló erőforrásaihoz való hozzáférés korlátozására szolgáló engedélyek körét tegye elérhetővé. Ha a delegált felhasználói azonosító típusa használatban van, a kiszolgálói alkalmazásnak ki kell választania a kívánt engedélyeket. Az alkalmazás regisztrációjának **API-engedély** lapján az **engedély hozzáadása** és az API-család kiválasztása után válassza a **delegált engedélyek** lehetőséget, majd válassza ki az engedélyeket. Ez a lépés nem kötelező, ha az alkalmazás identitásának típusa használatban van.
* Több-bérlős – először a démon vagy a kiszolgálóalkalmazás úgy van konfigurálva, hogy jelezze a működéséhez szükséges engedélyeket. A szükséges engedélyek listája egy párbeszédpanelen jelenik meg, ha a célként megadott könyvtárban lévő felhasználó vagy rendszergazda beleegyezik az alkalmazásba, ami elérhetővé teszi a szervezet számára. Egyes alkalmazásokhoz csak felhasználói szintű engedélyek szükségesek, amelyeket a szervezet bármely felhasználója jóváhagyhat. Más alkalmazásokhoz rendszergazdai szintű engedélyek szükségesek, amelyeket a szervezet felhasználója nem tud jóváhagyni. Csak a címtár-rendszergazda engedélyezheti az ilyen szintű engedélyeket igénylő alkalmazásokhoz való hozzáférést. A felhasználó vagy a rendszergazda beleegyezése esetén mindkét webes API regisztrálva van a címtárában.

## <a name="token-expiration"></a>Jogkivonat lejárata

Ha az első alkalmazás az engedélyezési kódját használja egy JWT hozzáférési jogkivonat beszerzéséhez, akkor a JWT frissítési tokent is kap. Ha a hozzáférési jogkivonat lejár, a frissítési token használatával újra hitelesítheti a felhasználót a hitelesítő adatok kérése nélkül. Ezt a frissítési tokent a rendszer a felhasználó hitelesítésére használja, amely új hozzáférési jogkivonatot és frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>Következő lépések

- További információ az egyéb [alkalmazási típusokról és forgatókönyvekről](app-types.md)
- Tudnivalók az Azure AD- [alapú hitelesítés alapjairól](v1-authentication-scenarios.md)
