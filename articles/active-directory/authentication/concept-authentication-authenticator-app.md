---
title: Microsoft Authenticator alkalmazás hitelesítési módszere – Azure Active Directory
description: Tudnivalók a Microsoft Authenticator alkalmazás használatáról Azure Active Directory a bejelentkezési események javításához és biztonságossá tételéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e33c87d53580d96363ba15bccbc889370f2479d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212909"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Hitelesítési módszerek a Azure Active Directory-Microsoft Authenticator alkalmazásban

A Microsoft Authenticator alkalmazás további biztonsági szintet biztosít az Azure AD munkahelyi vagy iskolai fiókjához vagy a Microsoft-fiókhoz, és elérhető az [Android](https://go.microsoft.com/fwlink/?linkid=866594) és az [iOS](https://go.microsoft.com/fwlink/?linkid=866594)rendszerhez. A Microsoft Authenticator alkalmazással a felhasználók jelszó nélküli módon hitelesíthetők a bejelentkezés során, vagy további ellenőrzési lehetőségként az önkiszolgáló jelszó-visszaállítás (SSPR) vagy az Azure AD Multi-Factor Authentication eseményei során.

A felhasználók értesítéseket kaphatnak a mobil alkalmazástól a jóváhagyáshoz vagy elutasításhoz, illetve a hitelesítő alkalmazással olyan OAUTH-ellenőrző kód létrehozásához, amely a bejelentkezési felületen adható meg. Ha az értesítési és ellenőrző kódot is engedélyezi, a hitelesítő alkalmazást regisztráló felhasználók bármelyik módszert használhatják az identitásuk ellenőrzéséhez.

Ha a hitelesítő alkalmazást a Felhasználónév és jelszó kombináció helyett a bejelentkezési üzenetben szeretné használni, tekintse meg [a jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md)című témakört.

> [!NOTE]
> A felhasználók nem regisztrálhatják a SSPR, amikor engedélyezik a mobileszköz regisztrálását. Ehelyett a felhasználók regisztrálhatják a mobil alkalmazást a (z) szolgáltatásban [https://aka.ms/mfasetup](https://aka.ms/mfasetup) a kombinált biztonsági adatok regisztrációjának részeként [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Jelszó nélküli bejelentkezés

A felhasználónevek beírása után a jelszó kérése helyett egy olyan felhasználó láthat egy üzenetet, amely a Microsoft Authenticator alkalmazásban engedélyezte a telefonos bejelentkezést az alkalmazásban. Ha a megfelelő szám van kiválasztva, a bejelentkezési folyamat befejeződött.

![Böngészőbeli bejelentkezés – példa arra, hogy a felhasználó jóváhagyja a bejelentkezést](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Ez a hitelesítési módszer magas szintű biztonságot nyújt, és eltávolítja a felhasználót a bejelentkezéshez szükséges jelszó megadására. 

A jelszó nélküli bejelentkezés használatának megkezdéséhez tekintse meg [a jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md)című témakört.

## <a name="notification-through-mobile-app"></a>Értesítés a Mobile App használatával

A hitelesítő alkalmazás segít megakadályozni a fiókok jogosulatlan elérését, és letilthatja a csalárd tranzakciókat, ha értesítéseket küld az okostelefonra vagy a táblaszámítógépre. A felhasználók megtekinthetik az értesítést, és ha ez jogos, válassza az **ellenőrzés** lehetőséget. Ellenkező esetben a **Megtagadás** lehetőséget is kiválaszthatja.

![A bejelentkezési folyamat elvégzésére szolgáló, a hitelesítő alkalmazás értesítésére szolgáló webböngészőt kérő példa képernyőképe](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Ha a szervezete Kínában dolgozik vagy Kínába utazik, az Android-eszközökön a *Mobile App Method használatával történő értesítés* nem működik abban az országban vagy régióban, mint a Google Play-szolgáltatások (beleértve a leküldéses értesítéseket) a régióban. Az iOS-értesítés azonban működik. Android-eszközök esetén az adott felhasználók számára elérhetővé kell tenni alternatív hitelesítési módszereket.

## <a name="verification-code-from-mobile-app"></a>Ellenőrző kód a Mobile appből

A hitelesítő alkalmazás szoftver-tokenként használható az eskü-ellenőrző kód létrehozásához. A Felhasználónév és a jelszó megadása után adja meg a hitelesítő alkalmazás által a bejelentkezési felületen megadott kódot. Az ellenőrzőkód egy második hitelesítési módként szolgál.

Előfordulhat, hogy a felhasználók legfeljebb öt ESKÜvel rendelkező hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást) kombinálnak, amelyet bármikor használatra konfiguráltak.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállítás legmagasabb szintű biztonságának biztosítása érdekében, ha csak egy módszer szükséges az alaphelyzetbe állításhoz, a felhasználók számára csak egy hitelesítési kód adható meg.
>
> Ha két módszerre van szükség, a felhasználók visszaállíthatják az értesítési vagy ellenőrző kódokat a más engedélyezett módszerek kiegészítéseként.

## <a name="next-steps"></a>Következő lépések

A jelszó nélküli bejelentkezés használatának megkezdéséhez tekintse meg [a jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md)című témakört.

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.
