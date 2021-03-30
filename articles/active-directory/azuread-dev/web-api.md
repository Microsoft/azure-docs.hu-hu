---
title: Webes API-alkalmazások Azure Active Directory
description: Leírja, hogy mi a webes API-alkalmazás, és milyen alapismereteket biztosít a protokollok, a regisztráció és a jogkivonat lejárata ehhez az alkalmazás típusához.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154423"
---
# <a name="web-api"></a>Webes API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A webes API-alkalmazások olyan webalkalmazások, amelyeknek egy webes API-ból kell lekérniük az erőforrásokat. Ebben a forgatókönyvben két olyan identitás létezik, amelyet a webalkalmazás a webes API hitelesítésére és meghívására használhat:

- **Alkalmazás identitása** – ez a forgatókönyv OAuth 2,0 ügyfél-hitelesítő adatokat használ az alkalmazásként való hitelesítéshez és a webes API eléréséhez. Az alkalmazás identitásának használatakor a webes API csak azt észlelheti, hogy a webalkalmazás meghívja azt, mivel a webes API nem kap semmilyen információt a felhasználóról. Ha az alkalmazás adatokat kap a felhasználóról, a rendszer az alkalmazási protokollon keresztül küldi el, és az Azure AD nem írja alá. A webes API megbízik abban, hogy a webalkalmazás hitelesítette a felhasználót. Emiatt ezt a mintát megbízható alrendszernek nevezzük.
- **Delegált felhasználói identitás** – ez a forgatókönyv kétféleképpen valósítható meg: az OpenID Connect és a OAuth 2,0 engedélyezési kód engedélyezése egy bizalmas ügyféllel. A webalkalmazás hozzáférési jogkivonatot szerez be a felhasználó számára, amely igazolja, hogy a felhasználó sikeresen hitelesítette a webes API-t, és hogy a webalkalmazás megszerezte a delegált felhasználói identitást a webes API meghívásához. Ezt a hozzáférési jogkivonatot a rendszer elküldi a webes API-nak a kérelemben, amely engedélyezi a felhasználót, és visszaadja a kívánt erőforrást.

Az alábbi folyamat az alkalmazás identitását és a delegált felhasználói azonosító típusát is tárgyalja. A legfontosabb különbség az, hogy a delegált felhasználói identitásnak először be kell szereznie egy engedélyezési kódot ahhoz, hogy a felhasználó bejelentkezzen, és hozzáférhessen a webes API-hoz.

## <a name="diagram"></a>Ábra

![Webalkalmazás webes API-diagramja](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll folyamatábrája

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Alkalmazás-identitás OAuth 2,0 ügyfél-hitelesítő adatokkal

1. A felhasználók bejelentkeznek az Azure AD-be a webalkalmazásban (további információért lásd a **Web Apps** szakaszt).
1. A webalkalmazásnak meg kell szereznie egy hozzáférési jogkivonatot, hogy az hitelesíthető legyen a webes API-ban, és beolvassa a kívánt erőforrást. Kérést küld az Azure AD jogkivonat-végpontjának, amely megadja a hitelesítő adatokat, az alkalmazás AZONOSÍTÓját és a webes API alkalmazásspecifikus AZONOSÍTÓjának URI azonosítóját.
1. Az Azure AD hitelesíti az alkalmazást, és egy JWT hozzáférési tokent ad vissza, amely a webes API meghívására szolgál.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT hozzáférési tokent használja, hogy hozzáadja a JWT karakterláncot a "tulajdonos" jelöléssel a webes API-nak küldött kérelem engedélyezési fejlécében. A webes API ezt követően ellenőrzi az JWT tokent, és ha az érvényesítés sikeres, a visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegált felhasználói identitás OpenID Connecttel

1. A felhasználók az Azure AD-vel jelentkeznek be egy webalkalmazásba (lásd a fenti webalkalmazási szakaszt). Ha a webalkalmazás felhasználója még nem járult hozzá, hogy a webalkalmazás meghívja a webes API-t az Ön nevében, a felhasználónak meg kell egyeznie. Az alkalmazás megjeleníti a szükséges engedélyeket, és ha ezek bármelyike rendszergazdai szintű engedély, a címtárban lévő normál felhasználó nem fogja tudni beleegyezni. Ez a belelépési folyamat csak a több-bérlős alkalmazásokra vonatkozik, nem egy bérlői alkalmazásra, mert az alkalmazás már rendelkezik a szükséges engedélyekkel. Amikor a felhasználó bejelentkezett, a webalkalmazás egy azonosító jogkivonatot kapott a felhasználóval kapcsolatos információkkal, valamint egy engedélyezési kóddal.
1. Az Azure AD által kiadott engedélyezési kód használatával a webalkalmazás egy kérelmet küld az Azure AD jogkivonat-végpontjának, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás részleteit (az alkalmazás AZONOSÍTÓját és az átirányítási URI-t), valamint a kívánt erőforrást (a webes API-hoz tartozó alkalmazás-azonosító URI-t).
1. Az Azure AD ellenőrzi az engedélyezési kódot és a webalkalmazással és a webes API-val kapcsolatos információkat. A sikeres ellenőrzés után az Azure AD két tokent ad vissza: egy JWT hozzáférési tokent és egy JWT frissítési tokent.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT hozzáférési tokent használja, hogy hozzáadja a JWT karakterláncot a "tulajdonos" jelöléssel a webes API-nak küldött kérelem engedélyezési fejlécében. A webes API ezt követően ellenőrzi az JWT tokent, és ha az érvényesítés sikeres, a visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegált felhasználói identitás a OAuth 2,0 engedélyezési kód engedélyezésével

1. Egy felhasználó már be van jelentkezve egy webalkalmazásba, amelynek hitelesítési mechanizmusa független az Azure AD-től.
1. A webalkalmazásnak egy hozzáférési jogkivonat beszerzéséhez engedélyezési kódnak kell lennie, ezért a böngészővel az Azure AD engedélyezési végpontján keresztül küldi el a kérést, így a sikeres hitelesítés után megadhatja az alkalmazás AZONOSÍTÓját és átirányítási URI-JÁT a webalkalmazáshoz. A felhasználó bejelentkezik az Azure AD-be.
1. Ha a webalkalmazás felhasználója még nem járult hozzá, hogy a webalkalmazás meghívja a webes API-t az Ön nevében, a felhasználónak meg kell egyeznie. Az alkalmazás megjeleníti a szükséges engedélyeket, és ha ezek bármelyike rendszergazdai szintű engedély, a címtárban lévő normál felhasználó nem fogja tudni beleegyezni. Ez a beleegyező alkalmazás az önálló és a több-bérlős alkalmazások esetében is érvényes. Az egybérlős esetekben a rendszergazda rendszergazdai beleegyezett a felhasználók nevében való részvételre. Ezt a `Grant Permissions` [Azure Portal](https://portal.azure.com)gombjának használatával teheti meg. 
1. A felhasználó beleegyezése után a webalkalmazás megkapja a hozzáférési token beszerzéséhez szükséges hitelesítési kódot.
1. Az Azure AD által kiadott engedélyezési kód használatával a webalkalmazás egy kérelmet küld az Azure AD jogkivonat-végpontjának, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás részleteit (az alkalmazás AZONOSÍTÓját és az átirányítási URI-t), valamint a kívánt erőforrást (a webes API-hoz tartozó alkalmazás-azonosító URI-t).
1. Az Azure AD ellenőrzi az engedélyezési kódot és a webalkalmazással és a webes API-val kapcsolatos információkat. A sikeres ellenőrzés után az Azure AD két tokent ad vissza: egy JWT hozzáférési tokent és egy JWT frissítési tokent.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT hozzáférési tokent használja, hogy hozzáadja a JWT karakterláncot a "tulajdonos" jelöléssel a webes API-nak küldött kérelem engedélyezési fejlécében. A webes API ezt követően ellenőrzi az JWT tokent, és ha az érvényesítés sikeres, a visszaadja a kívánt erőforrást.

## <a name="code-samples"></a>Kódminták

Tekintse meg a webalkalmazás webes API-forgatókönyvekre vonatkozó példákat. És gyakran tekintse át a gyakran előforduló új mintákat. [Webalkalmazás webes API-hoz](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Alkalmazásregisztráció

Az alkalmazások regisztrálásához az Azure AD 1.0-s verziójának végpontján tekintse meg az alkalmazás [regisztrálása](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)című témakört.

* Egyetlen bérlő – az alkalmazás identitása és a delegált felhasználó identitása esetében a webalkalmazást és a webes API-t ugyanabban a címtárban kell regisztrálni az Azure AD-ben. A webes API beállítható úgy, hogy a webalkalmazás erőforrásokhoz való hozzáférésének korlátozására szolgáló engedélyeket biztosítson. Ha a delegált felhasználói azonosító típusa használatban van, a webalkalmazásnak ki kell választania a kívánt engedélyeket az engedélyek között a Azure Portal **egyéb alkalmazások** legördülő menüjében. Ez a lépés nem kötelező, ha az alkalmazás identitásának típusa használatban van.
* Több-bérlős – először a webalkalmazás úgy van konfigurálva, hogy jelezze a működéséhez szükséges engedélyeket. A szükséges engedélyek listája egy párbeszédpanelen jelenik meg, ha a célként megadott könyvtárban lévő felhasználó vagy rendszergazda beleegyezik az alkalmazásba, ami elérhetővé teszi a szervezet számára. Egyes alkalmazásokhoz csak felhasználói szintű engedélyek szükségesek, amelyeket a szervezet bármely felhasználója jóváhagyhat. Más alkalmazásokhoz rendszergazdai szintű engedélyek szükségesek, amelyeket a szervezet felhasználója nem tud jóváhagyni. Csak a címtár-rendszergazda engedélyezheti az ilyen szintű engedélyeket igénylő alkalmazásokhoz való hozzáférést. A felhasználó vagy a rendszergazda beleegyezése esetén a webalkalmazás és a webes API egyaránt regisztrálva van a címtárában.

## <a name="token-expiration"></a>Jogkivonat lejárata

Ha a webalkalmazás az engedélyezési kódját használja egy JWT hozzáférési jogkivonat beszerzéséhez, akkor a JWT frissítési tokent is kap. Ha a hozzáférési jogkivonat lejár, a frissítési token használatával újra hitelesítheti a felhasználót anélkül, hogy újra be kellene jelentkeznie. Ezt a frissítési tokent a rendszer a felhasználó hitelesítésére használja, amely új hozzáférési jogkivonatot és frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>Következő lépések

- További információ az egyéb [alkalmazási típusokról és forgatókönyvekről](app-types.md)
- Tudnivalók az Azure AD- [alapú hitelesítés alapjairól](v1-authentication-scenarios.md)
