---
title: Natív alkalmazások Azure Active Directory
description: Leírja, hogy milyen natív alkalmazások vannak, és milyen alapismereteket biztosít a protokollok, a regisztráció és a jogkivonat lejárata ehhez az alkalmazás típusához.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154797"
---
# <a name="native-apps"></a>Natív alkalmazások

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A natív alkalmazások olyan alkalmazások, amelyek egy felhasználó nevében webes API-t hívnak meg. Ez a forgatókönyv a OAuth 2,0 engedélyezési kódjának nyilvános ügyféllel való létrehozásához készült, a [OAuth 2,0 specifikációjának](https://tools.ietf.org/html/rfc6749)4,1. szakasza szerint. A natív alkalmazás a OAuth 2,0 protokoll használatával szerzi be a felhasználó hozzáférési jogkivonatát. Ezt a hozzáférési jogkivonatot ezután a rendszer elküldi a webes API-nak a kérelemben, amely engedélyezi a felhasználót, és visszaadja a kívánt erőforrást.

## <a name="diagram"></a>Ábra

![Natív alkalmazás webes API-diagramhoz](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll folyamatábrája

Ha az AD-hitelesítési kódtárakat használja, az alábbiakban ismertetett protokoll-részletek többségét kezelheti Önnek, például a böngésző előugró ablakát, a jogkivonat gyorsítótárazását és a frissítési tokenek kezelését.

1. A böngésző előugró ablakában a natív alkalmazás egy kérést küld az Azure AD engedélyezési végpontjának. Ez a kérelem tartalmazza a natív alkalmazás alkalmazás-AZONOSÍTÓját és átirányítási URI-JÁT a Azure Portalban látható módon, valamint a webes API-hoz tartozó alkalmazás-azonosító URI-t. Ha a felhasználó még nem jelentkezett be, a rendszer felszólítja, hogy jelentkezzen be újra
1. Az Azure AD hitelesíti a felhasználót. Ha több-bérlős alkalmazásra van szükség az alkalmazás használatához, a felhasználónak hozzá kell járulnia, ha még nem tette volna meg. A hozzájárulás megadása és a sikeres hitelesítés után az Azure AD az ügyfélalkalmazás átirányítási URI-JÁT adja vissza az engedélyezési kód válasza alapján.
1. Ha az Azure AD az átirányítási URI-nak adott válaszként válaszol, az ügyfélalkalmazás leállítja a böngésző interakcióját, és kibontja az engedélyezési kódot a válaszból. Ezzel az engedélyezési kóddal az ügyfélalkalmazás kérelmet küld az Azure AD jogkivonat-végpontjának, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás részleteit (az alkalmazás AZONOSÍTÓját és az átirányítási URI-t), valamint a kívánt erőforrást (a webes API-hoz tartozó alkalmazás-azonosító URI-t).
1. Az ügyfélalkalmazás és a webes API engedélyezési kódját és információit az Azure AD ellenőrzi. A sikeres ellenőrzés után az Azure AD két tokent ad vissza: egy JWT hozzáférési tokent és egy JWT frissítési tokent. Az Azure AD továbbá alapszintű adatokat ad vissza a felhasználóról, például a megjelenítendő nevüket és a bérlő AZONOSÍTÓját.
1. HTTPS-kapcsolaton keresztül az ügyfélalkalmazás a visszaadott JWT hozzáférési tokent használja, hogy hozzáadja a JWT karakterláncot egy "tulajdonos" megjelöléssel a webes API-nak küldött kérelem engedélyezési fejlécében. A webes API ezt követően ellenőrzi az JWT tokent, és ha az érvényesítés sikeres, a visszaadja a kívánt erőforrást.
1. Ha a hozzáférési jogkivonat lejár, az ügyfélalkalmazás olyan hibaüzenetet kap, amely jelzi, hogy a felhasználónak újra kell hitelesítenie magát. Ha az alkalmazás érvényes frissítési jogkivonattal rendelkezik, akkor az új hozzáférési jogkivonat beszerzéséhez a felhasználónak nem kell újra bejelentkeznie. Ha a frissítési jogkivonat lejár, az alkalmazásnak interaktív módon kell hitelesítenie a felhasználót.

> [!NOTE]
> Az Azure AD által kiadott frissítési jogkivonat több erőforrás elérésére is használható. Ha például van egy ügyfélalkalmazás, amely jogosult két webes API meghívására, a frissítési token használatával hozzáférési tokent kaphat a másik webes API-hoz is.

## <a name="code-samples"></a>Kódminták

Tekintse meg a natív alkalmazásra vonatkozó példákat a webes API-forgatókönyvek esetében. És térjen vissza gyakran – új mintákat gyakran adunk hozzá. [Natív alkalmazás webes API-hoz](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Alkalmazásregisztráció

Az alkalmazások regisztrálásához az Azure AD 1.0-s verziójának végpontján tekintse meg az alkalmazás [regisztrálása](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)című témakört.

* Egyetlen bérlő – a natív alkalmazást és a webes API-t is regisztrálni kell ugyanabban a címtárban az Azure AD-ben. A webes API beállítható úgy, hogy a natív alkalmazás erőforrásokhoz való hozzáférésének korlátozására szolgáló engedélyeket biztosítson. Az ügyfélalkalmazás ezután kiválasztja a kívánt engedélyeket az "engedélyek más alkalmazásoknak" legördülő menüből a Azure Portal.
* Több-bérlős – először a natív alkalmazás csak a fejlesztő vagy a közzétevő címtárában van regisztrálva. Másodszor, a natív alkalmazás úgy van konfigurálva, hogy jelezze a működéséhez szükséges engedélyeket. A szükséges engedélyek listája egy párbeszédpanelen jelenik meg, ha a célként megadott könyvtárban lévő felhasználó vagy rendszergazda beleegyezik az alkalmazásba, ami elérhetővé teszi a szervezet számára. Egyes alkalmazásokhoz csak felhasználói szintű engedélyek szükségesek, amelyeket a szervezet bármely felhasználója jóváhagyhat. Más alkalmazásokhoz rendszergazdai szintű engedélyek szükségesek, amelyeket a szervezet felhasználója nem tud jóváhagyni. Csak a címtár-rendszergazda engedélyezheti az ilyen szintű engedélyeket igénylő alkalmazásokhoz való hozzáférést. Ha a felhasználó vagy a rendszergazda beleegyezik, csak a webes API lesz regisztrálva a címtárában. 

## <a name="token-expiration"></a>Jogkivonat lejárata

Ha a natív alkalmazás az engedélyezési kódját használja egy JWT hozzáférési jogkivonat beszerzéséhez, akkor a JWT frissítési tokent is kap. Ha a hozzáférési jogkivonat lejár, a frissítési token használatával újra hitelesítheti a felhasználót anélkül, hogy újra be kellene jelentkeznie. Ezt a frissítési tokent a rendszer a felhasználó hitelesítésére használja, amely új hozzáférési jogkivonatot és frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>Következő lépések

- További információ az egyéb [alkalmazási típusokról és forgatókönyvekről](app-types.md)
- Tudnivalók az Azure AD- [alapú hitelesítés alapjairól](v1-authentication-scenarios.md)
