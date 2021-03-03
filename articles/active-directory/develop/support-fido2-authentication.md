---
title: Jelszavas hitelesítés támogatása a FIDO2-kulcsokkal a fejleszthető alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: Ez a telepítési útmutató ismerteti, hogyan támogathatja a FIDO2 biztonsági kulcsokkal rendelkező jelszavas hitelesítést az Ön által fejlesztett alkalmazásokban
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: e7193ee5d076538689078ce634b8170926df20b6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653252"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Jelszavas hitelesítés támogatása FIDO2-kulcsokkal a fejleszthető alkalmazásokban

Ezek a konfigurációk és ajánlott eljárások segítenek elkerülni azokat a gyakori forgatókönyveket, amelyek letiltják az [FIDO2 jelszavas hitelesítését](../../active-directory/authentication/concept-authentication-passwordless.md) a felhasználók számára az alkalmazások számára.

## <a name="general-best-practices"></a>Általános ajánlott eljárások

### <a name="domain-hints"></a>Tartományi útmutatók

Ne használjon tartományi emlékeztetőt a [Kezdőlap-tartomány felderítésének](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)mellőzéséhez. A szolgáltatás célja, hogy egyszerűbbé tegye a bejelentkezést, de az összevont identitás-szolgáltató nem támogatja a jelszó nélküli hitelesítést.

### <a name="requiring-specific-credentials"></a>Megadott hitelesítő adatok megkövetelése

Ha SAML-t használ, ne határozza meg, hogy szükség van-e jelszóra [a RequestedAuthnContext elem használatával](single-sign-on-saml-protocol.md#requestauthncontext).

A RequestedAuthnContext elem nem kötelező, ezért a megoldásához távolítsa el az SAML-hitelesítési kérelmekből. Ez az általános ajánlott eljárás, ahogy az elem használata is megakadályozhatja más hitelesítési lehetőségek használatát, például a többtényezős hitelesítés megfelelő működését.

### <a name="using-the-most-recently-used-authentication-method"></a>A legutóbb használt hitelesítési módszer használata

Először a felhasználó által legutóbb használt bejelentkezési módszert mutatjuk be a rendszer. Ez zavart okozhat, ha a felhasználók úgy vélik, hogy az első megjelenített lehetőséget használják. Azonban választhatnak egy másik lehetőséget is, ha a lent látható módon kiválasztja a "Bejelentkezés egyéb módjai" lehetőséget.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="A felhasználói hitelesítési élmény kiemelésének képe, amely lehetővé teszi a felhasználó számára a hitelesítési módszer módosítását.":::

## <a name="platform-specific-best-practices"></a>Platformokra vonatkozó ajánlott eljárások

### <a name="desktop"></a>Asztal

A hitelesítés megvalósításának ajánlott lehetőségei a következők:

- A Microsoft Authentication Libraryt (MSAL) használó .NET Desktop-alkalmazásoknak a Windows Authentication Managert (WAM) kell használniuk. Ez az integráció és a hozzá tartozó előnyök a [githubon vannak dokumentálva](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- A [WebView2](/microsoft-edge/webview2/) használatával TÁMOGASSA a FIDO2 egy beágyazott böngészőben.
- Használja a rendszerböngészőt. Az asztali platformokhoz készült MSAL-kódtárak alapértelmezés szerint ezt a módszert használják. A FIDO2 böngésző kompatibilitási oldalának használatával ellenőrizheti, hogy a használt böngésző támogatja-e a FIDO2-hitelesítést.

### <a name="mobile"></a>Mobil

Február 2020-én a FIDO2 jelenleg nem támogatott natív iOS-vagy Android-alkalmazásokhoz, de fejlesztés alatt áll.

Az alkalmazások a rendelkezésre álláshoz való előkészítéséhez, és általános ajánlott eljárásként az iOS-és Android-alkalmazások esetében a MSAL-t kell használnia a rendszerböngészőben használt alapértelmezett konfigurációval.

Ha nem használja a MSAL-t, akkor továbbra is a rendszerböngészőt kell használnia a hitelesítéshez. Az egyszeri bejelentkezés és a feltételes hozzáférés funkciói a rendszerböngésző által biztosított megosztott webes felületen alapulnak. Ez azt jelenti, hogy az [Egyéni Chrome-lapokat](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) vagy egy [webszolgáltatáson keresztül hitelesíti a felhasználót | Apple fejlesztői dokumentáció](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS).

### <a name="web-and-single-page-apps"></a>Webes és egyoldalas alkalmazások

A webböngészőben futó alkalmazások FIDO2 jelszavas hitelesítésének rendelkezésre állása a böngésző és a platform kombinációjával függ. A [FIDO2 kompatibilitási mátrixban](../authentication/fido2-compatibility.md) megtekintheti, hogy a felhasználók által tapasztalt kombináció támogatott-e.

## <a name="next-steps"></a>Következő lépések

[A Azure Active Directory jelszóval nem rendelkező hitelesítési beállításai](../../active-directory/authentication/concept-authentication-passwordless.md)