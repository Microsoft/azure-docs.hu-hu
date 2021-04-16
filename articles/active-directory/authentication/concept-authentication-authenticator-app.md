---
title: Microsoft Authenticator alkalmazáshitelesítési módszer – Azure Active Directory
description: Ismerje meg, hogyan felhasználásával Microsoft Authenticator alkalmazás Azure Active Directory a bejelentkezési események javítása és biztonságossá tere érdekében
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3175b1292a7e69506b9193d1182e184e257ebda3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530504"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Hitelesítési módszerek a Azure Active Directory – Microsoft Authenticator alkalmazás

A Microsoft Authenticator alkalmazás egy további biztonsági szintet biztosít az Azure AD munkahelyi vagy iskolai fiókjának, illetve a Microsoft-fiók, [és Android](https://go.microsoft.com/fwlink/?linkid=866594) és iOS rendszeren [érhető el.](https://go.microsoft.com/fwlink/?linkid=866594) Az Microsoft Authenticator-alkalmazással a felhasználók jelszó nélküli hitelesítést használhatnak a bejelentkezés során, vagy további ellenőrzési lehetőségként az önkiszolgáló jelszóát állítás (SSPR) vagy az Azure AD Multi-Factor Authentication eseményei során.

A felhasználók értesítést kaphatnak a mobilalkalmazáson keresztül a jóváhagyásukról vagy megtagadásukról, vagy az Authenticator alkalmazással létrehozhatnak egy OAUTH-ellenőrzőkódot, amely a bejelentkezési felületen írható be. Ha az értesítési és az ellenőrző kódot is engedélyezi, az Authenticator alkalmazást regisztráló felhasználók bármelyik módszerrel ellenőrizhetik személyazonosságukat.

Ha az Authenticator alkalmazást felhasználónév és jelszó kombináció helyett bejelentkezési kérésnél szeretné használni, tekintse meg a Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator [alkalmazással.](howto-authentication-passwordless-phone.md)

> [!NOTE]
> A felhasználók nem regisztrálják a mobilalkalmazásukat az SSPR engedélyezésekor. Ehelyett a felhasználók a kombinált biztonsági információk regisztrációja részeként regisztrálják a mobilalkalmazásukat a [https://aka.ms/mfasetup](https://aka.ms/mfasetup) következőnél: [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Jelszó nélküli bejelentkezés

Ahelyett, hogy a felhasználónevet beírása után jelszót kérne, a Microsoft Authenticator-alkalmazásból telefonos bejelentkezést engedélyező felhasználó egy üzenetet kap, amely arra kéri, hogy koppintson egy számra az alkalmazásban. Ha a megfelelő szám van kiválasztva, a bejelentkezési folyamat befejeződött.

![Példa egy böngészőbe való bejelentkezésre, amely a bejelentkezés jóváhagyását kéri a felhasználótól](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Ez a hitelesítési módszer magas szintű biztonságot nyújt, és nincs szükség arra, hogy a felhasználó jelszót adjon meg a jelentkezzen be. 

A jelszó nélküli bejelentkezés első lépésekhez lásd: Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator [alkalmazással.](howto-authentication-passwordless-phone.md)

## <a name="notification-through-mobile-app"></a>Értesítés mobilalkalmazáson keresztül

Az Authenticator alkalmazás az okostelefonra vagy táblagépre leküldve segíthet megelőzni a fiókokhoz való jogosulatlan hozzáférést, és leállítani a csaló tranzakciókat. A felhasználók megtekinthetik az értesítést, és ha szabályosak, válassza az Ellenőrzés **lehetőséget.** Ellenkező esetben választhatják a **Megtagadás lehetőséget.**

![Képernyőkép egy webböngészőben megjelenő, Authenticator alkalmazásértesítésre vonatkozó kérésről a bejelentkezési folyamat befejezéséhez](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Ha a szervezet alkalmazottai Kínában dolgoznak  vagy Utaznak Kínába, az Android-eszközökön használt mobilalkalmazásos módszer nem működik az adott országban vagy régióban, mivel a Google Play-szolgáltatások (beleértve a leküldéses értesítéseket is) le vannak tiltva a régióban. Az iOS-értesítések azonban működnek. Android-eszközök esetén alternatív hitelesítési módszereket kell elérhetővé tenni ezekhez a felhasználókhoz.

## <a name="verification-code-from-mobile-app"></a>Ellenőrző kód mobilalkalmazásból

Az Authenticator alkalmazás szoftveres jogkivonatként használható OATH ellenőrző kód létrehozásához. A felhasználónév és a jelszó megadása után be kell írnia az Authenticator alkalmazás által biztosított kódot a bejelentkezési felületen. Az ellenőrzőkód egy második hitelesítési módként szolgál.

A felhasználók legfeljebb öt OATH hardvertoken vagy hitelesítő alkalmazás, például a Microsoft Authenticator alkalmazás kombinációját konfigurálják bármikor használatra.

> [!WARNING]
> Az új jelszó önkiszolgáló alaphelyzetbe állításának legmagasabb szintű biztonsága érdekében, ha csak egy módszerre van szükség az alaphelyzetbe állításhoz, a felhasználók csak ellenőrző kódot érhetők el.
>
> Ha két módszerre van szükség, a felhasználók a többi engedélyezett módszer mellett értesítési vagy ellenőrző kóddal is alaphelyzetbe állíthatják az új beállításokat.

## <a name="next-steps"></a>Következő lépések

A jelszó nélküli bejelentkezés első lépésekhez lásd: Jelszó nélküli bejelentkezés engedélyezése [a Microsoft Authenticator alkalmazással.](howto-authentication-passwordless-phone.md)

További információ a hitelesítési módszerek konfigurálásról a [Microsoft Graph REST API.](/graph/api/resources/authenticationmethods-overview)
