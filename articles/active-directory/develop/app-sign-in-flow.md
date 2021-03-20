---
title: Alkalmazás-bejelentkezési folyamat a Microsoft Identity platformmal | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a webes, asztali és mobil alkalmazások bejelentkezési folyamatát a Microsoft Identity platformon.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1f9f330ab140fa66b5a66a112c47ca2a68ba56bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755703"
---
# <a name="app-sign-in-flow-with-the-microsoft-identity-platform"></a>Alkalmazás-bejelentkezési folyamat a Microsoft Identity platformmal

Ez a témakör a Microsoft Identity platformot használó webes, asztali és mobil alkalmazások alapszintű bejelentkezési folyamatát ismerteti. A Microsoft Identity platform által támogatott bejelentkezési forgatókönyvek megismeréséhez tekintse meg a [hitelesítési folyamatokat és az alkalmazás forgatókönyveit](authentication-flows-app-scenarios.md) .

## <a name="web-app-sign-in-flow"></a>Webalkalmazás bejelentkezési folyamata

Amikor egy felhasználó egy webalkalmazáshoz navigál a böngészőben, a következők történnek:

* A webalkalmazás meghatározza, hogy a felhasználó hitelesítése megtörtént-e.
* Ha a felhasználó nincs hitelesítve, a webalkalmazás delegálja az Azure AD-t a felhasználónak való bejelentkezéshez. A bejelentkezés megfelel a szervezet házirendjének, ami azt jelenti, hogy a felhasználónak meg kell adnia a hitelesítő adatait a [többtényezős hitelesítés](../authentication/concept-mfa-howitworks.md) (más néven kétfaktoros hitelesítés vagy 2FA) használatával, vagy nem használ jelszót (például a Windows Hello használatával).
* A felhasználónak meg kell adnia a hozzáférést az ügyfélalkalmazás által igényelt hozzáféréshez. Ezért kell regisztrálni az ügyfélalkalmazások Azure AD-ben való regisztrálását, hogy a Microsoft Identity platform olyan jogkivonatokat nyújtson, amelyeknek a felhasználó beleegyezett a hozzáférésbe.

A felhasználó sikeres hitelesítése után:

* A Microsoft Identity platform egy jogkivonatot küld a webalkalmazásnak.
* A rendszer menti a cookie-t az Azure AD tartományához társítva, amely a felhasználó identitását tartalmazza a böngésző cookie jar-ban. Amikor az alkalmazás legközelebb megnyitja a Microsoft Identity platform engedélyezési végpontját, a böngésző megadja a cookie-t, így a felhasználónak nem kell újra bejelentkeznie. Ez azt is lehetővé teszi, hogy az egyszeri bejelentkezés elérhető legyen. A cookie-t az Azure AD állítja elő, és csak az Azure AD értelmezhető.
* A webalkalmazás ezután érvényesíti a jogkivonatot. Ha az ellenőrzés sikeres, a webalkalmazás megjeleníti a védett lapot, és menti a munkamenet-cookie-t a böngésző cookie jar-fájljában. Ha a felhasználó egy másik oldalra navigál, a webalkalmazás tudja, hogy a felhasználó a munkamenet-cookie alapján van hitelesítve.

A következő adatsor összefoglalja ezt az interakciót:

![webalkalmazás-hitelesítési folyamat](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>A webalkalmazások meghatározása, hogy a felhasználó hitelesítése megtörtént-e

A webalkalmazás-fejlesztők jelezhetik, hogy az összes vagy csak bizonyos lapok hitelesítést igényelnek-e. Az ASP.NET/ASP.NET Core-ban például az `[Authorize]` attribútum a vezérlő műveleteihez való hozzáadásával valósítható meg.

Ez az attribútum azt eredményezi, hogy a ASP.NET a felhasználó identitását tartalmazó munkamenet-cookie jelenlétét vizsgálja. Ha a cookie nincs jelen, a ASP.NET átirányítja a hitelesítést a megadott identitás-szolgáltatóhoz. Ha az identitás szolgáltatója Azure AD, a webalkalmazás átirányítja a hitelesítést a szolgáltatásba `https://login.microsoftonline.com` , amely megjeleníti a bejelentkezési párbeszédpanelt.

### <a name="how-a-web-app-delegates-sign-in-to-the-microsoft-identity-platform-and-obtains-a-token"></a>A webalkalmazások bejelentkeznek a Microsoft Identity platformba, és jogkivonatot szereznek be

A felhasználó hitelesítése a böngészőn keresztül történik. Az OpenID protokoll szabványos HTTP protokoll-üzeneteket használ.

* A webalkalmazás a Microsoft Identity platform használatához HTTP 302 (átirányítás) üzenetet küld a böngészőnek.
* A felhasználó hitelesítése után a Microsoft Identity platform elküldi a jogkivonatot a webalkalmazásnak a böngészőn keresztüli átirányítás használatával.
* Az átirányítást a webalkalmazás egy átirányítási URI formájában kapja meg. Ez az átirányítási URI regisztrálva van az Azure AD Application objektumban. Több átirányítási URI is lehet, mert az alkalmazás több URL-címen is üzembe helyezhető. Így a webalkalmazásnak is meg kell adnia a használandó átirányítási URI-t.
* Az Azure AD ellenőrzi, hogy a webalkalmazás által eljuttatott átirányítási URI a regisztrált átirányítási URI-k egyike az alkalmazáshoz.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Asztali és mobil alkalmazások bejelentkezési folyamata

A fent ismertetett folyamat enyhe eltérésekkel, asztali és mobil alkalmazásokkal is foglalkozik.

Az asztali és a mobil alkalmazások a hitelesítéshez használhatnak beágyazott webes vezérlőt vagy rendszerböngészőt. Az alábbi ábra azt szemlélteti, hogy egy asztali vagy mobil alkalmazás hogyan használja a Microsoft Authentication Library (MSAL) szolgáltatást a hozzáférési jogkivonatok beszerzéséhez és a webes API-k meghívásához.

![Az asztali alkalmazás megjelenése](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL egy böngészőt használ a tokenek lekéréséhez. Akárcsak a Web Apps esetében, a hitelesítés a Microsoft Identity platformra van delegálva.

Mivel az Azure AD ugyanazt az identitás-cookie-t menti a böngészőben, mint a webalkalmazások esetében, ha a natív vagy a Mobile alkalmazás a rendszerböngészőt használja, a rendszer azonnal beolvassa az SSO-t a megfelelő webalkalmazással.

Alapértelmezés szerint a MSAL a rendszerböngészőt használja. A kivétel a .NET-keretrendszer asztali alkalmazásai, amelyekben egy beágyazott vezérlőt használunk az integráltabb felhasználói élmény biztosításához.

## <a name="next-steps"></a>Következő lépések

A hitelesítési és engedélyezési alapismeretekkel kapcsolatos egyéb témakörökhöz:

* Tekintse meg a [hitelesítés](authentication-vs-authorization.md) és engedélyezés című témakört, amely a Microsoft Identity platform hitelesítésének és engedélyezésének alapvető fogalmait ismerteti.
* Tekintse meg a [biztonsági jogkivonatokat](security-tokens.md) , hogy megtudja, hogyan használhatók a hozzáférési tokenek, a frissítési tokenek és az azonosító tokenek a hitelesítés és az engedélyezés során.
* Tekintse meg az alkalmazás [modelljét](application-model.md) , amelyből megismerheti az alkalmazás regisztrálásának folyamatát, hogy integrálható legyen a Microsoft Identity platformmal.

További információ az alkalmazás bejelentkezési folyamatáról:

* További információ a Microsoft Identity platform által támogatott felhasználók hitelesítéséhez használható egyéb forgatókönyvekről: [hitelesítési folyamatok és alkalmazási forgatókönyvek](authentication-flows-app-scenarios.md) .
* A Microsoft-fiókokkal, az Azure AD-fiókokkal és a Azure AD B2C felhasználókkal együttműködve olyan alkalmazásokat fejleszthet a [MSAL-tárakkal](msal-overview.md) , amelyek segítségével egyetlen, áramvonalas programozási modellben dolgozhat.
