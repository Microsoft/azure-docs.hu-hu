---
title: Jelszó nélküli bejelentkezés az Microsoft Authenticator alkalmazással – Azure Active Directory
description: Jelszó nélküli bejelentkezés engedélyezése az Azure AD-be a Microsoft Authenticator alkalmazással
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: d586294f101c271f139867d0046576dc9a32f076
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861786"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással 

Az Microsoft Authenticator alkalmazással jelszó használata nélkül lehet bejelentkezni bármely Azure AD-fiókba. Microsoft Authenticator kulcsalapú hitelesítéssel engedélyezi az eszközhöz kötött felhasználói hitelesítő adatokat, ahol az eszköz PIN-kódot vagy biometriát használ. [Vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) hasonló technológiát használ.

Ez a hitelesítési technológia bármilyen eszközplatformon használható, beleértve a mobileszközöket is. Ez a technológia bármely olyan alkalmazással vagy webalkalmazással is használható, amely integrálható a Microsoft hitelesítési kódtárakkal.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Példa egy böngészőbe való bejelentkezésre, amely arra kéri a felhasználót, hogy hagyja jóvá a bejelentkezést.":::

Azok, akik engedélyezték a telefonos bejelentkezést a Microsoft Authenticator megjelenik egy üzenet, amely arra kéri őket, hogy koppintson egy számra az alkalmazásban. Nincs szükség felhasználónévre vagy jelszóra. Az alkalmazásban a bejelentkezési folyamat befejezéséhez a felhasználónak a következő műveleteket kell végrehajtania:

1. Egyezik a számmal.
2. Válassza a **Jóváhagyás** lehetőséget.
3. Adja meg a PIN-kódját vagy biometrikus azonosítóját.

## <a name="prerequisites"></a>Előfeltételek

Ha jelszó nélküli telefonos bejelentkezést Microsoft Authenticator alkalmazással, a következő előfeltételeknek kell teljesülnie:

- Azure AD Multi-Factor Authentication, ellenőrzési módszerként engedélyezett leküldéses értesítésekkel.
- A legújabb Microsoft Authenticator iOS 8.0-s vagy újabb, illetve Android 6.0-s vagy újabb rendszerű eszközökre van telepítve.
- Az eszköznek, amelyre Microsoft Authenticator alkalmazás telepítve van, regisztrálva kell lennie az Azure AD-bérlőn belül egy adott felhasználó számára. 

> [!NOTE]
> Ha az Azure AD PowerShell Microsoft Authenticator használatával engedélyezte a jelszó nélküli bejelentkezést, akkor az a teljes címtárhoz engedélyezve volt. Ha engedélyezi ezt az új metódust, az a PowerShell-házirendet írja át. Javasoljuk, hogy a bérlő összes felhasználója számára engedélyezze az új Hitelesítési módszerek menüt, különben az új szabályzatban nem található felhasználók nem tudnak többé jelszó nélkül bejelentkezni. 

## <a name="enable-passwordless-authentication-methods"></a>Jelszó nélküli hitelesítési módszerek engedélyezése

Ha jelszó nélküli hitelesítést használ az Azure AD-ban, először engedélyezze a kombinált regisztrációs élményt, majd engedélyezze a felhasználók számára a jelszó nélküli módszert.

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztrációs folyamat engedélyezése

A jelszó nélküli hitelesítési módszerek regisztrációs funkciói a kombinált regisztrációs funkcióra támaszkodnak. Ha lehetővé szeretné tenni, hogy a felhasználók maguk is befejezjék a kombinált regisztrációt, kövesse a kombinált biztonsági információk [regisztrációját engedélyező lépéseket.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Jelszó nélküli telefonos bejelentkezési hitelesítési módszerek engedélyezése

Az Azure AD segítségével kiválaszthatja, hogy mely hitelesítési módszerek használhatók a bejelentkezési folyamat során. A felhasználók ezután regisztrálnak a használni szükséges módszerekre.

A jelszó nélküli telefonos bejelentkezés hitelesítési módszerének engedélyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy *globális rendszergazdai fiókkal.*
1. Keresse meg és válassza *a Azure Active Directory* lehetőséget, majd keresse meg a **Biztonsági hitelesítési** módszerek  >  **házirendek**  >  **lehetőséget.**
1. A **Microsoft Authenticator** válassza ki a következő beállításokat:
   1. **Engedélyezés** – Igen vagy Nem
   1. **Cél** – Minden felhasználó vagy Felhasználók kiválasztása
1. Alapértelmezés szerint minden hozzáadott csoport vagy felhasználó számára engedélyezve van a Microsoft Authenticator jelszó nélküli és leküldéses értesítési módban ("Bármely" módban). Ennek módosítása az egyes sorokhoz:
   1. Nyissa meg **a...**  >  **Konfigurálja a et.**
   1. Hitelesítési **mód esetén** – Bármely, Jelszó nélküli vagy Leküldés
1. Az új szabályzat alkalmazáshoz válassza a Mentés **lehetőséget.**

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Felhasználók regisztrációja és kezelése Microsoft Authenticator

A felhasználók az alábbi lépésekkel regisztrálják magukat az Azure AD jelszó nélküli hitelesítési módszerére:

1. Lépjen a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) lapra.
1. Jelentkezzen be, majd adja hozzá az Authenticator alkalmazást a Metódus hozzáadása > **Authenticator alkalmazáshoz,** majd a **Hozzáadás gombra.**
1. Kövesse az utasításokat a Microsoft Authenticator telepítéséhez és konfiguráláshoz.
1. Az **Authenticator konfigurálásának** befejezéséhez válassza a Kész lehetőséget.
1. A **Microsoft Authenticator** a  regisztrált fiók legördülő menüjéből válassza a Telefonos bejelentkezés engedélyezése lehetőséget.
1. Kövesse az alkalmazás utasításait a fiók jelszó nélküli telefonos bejelentkezéshez való regisztrálásának befejezéséhez.

A szervezetek jelszó használata nélkül is bejelentkeztetheti a felhasználóikat a telefonjukkal. További segítség a Microsoft Authenticator konfigurálásához és a telefonos bejelentkezés engedélyezéséhez: Bejelentkezés a fiókokba a Microsoft Authenticator [alkalmazással.](../user-help/user-help-auth-app-sign-in.md)

> [!NOTE]
> Azok a felhasználók, akik számára a szabályzat nem engedélyezi a telefonos bejelentkezést, többé nem tudják engedélyezni azt a Microsoft Authenticator alkalmazásban.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

A felhasználók az alábbi műveletek végrehajtása után elkezdheti használni a jelszó nélküli bejelentkezést:

- Egy rendszergazda engedélyezte a felhasználó bérlőét.
- A felhasználó frissítette a Microsoft Authenticator, hogy engedélyezze a telefonos bejelentkezést.

Amikor a felhasználó először kezdi el a telefonos bejelentkezési folyamatot, a felhasználó a következő lépéseket végzi el:

1. A bejelentkezési oldalon adja meg a nevét.
2. Kiválasztja a **Tovább lehetőséget.**
3. Ha szükséges, válassza az **Egyéb bejelentkezési módok lehetőséget.**
4. Kiválasztja a Kérelem jóváhagyása a saját Microsoft Authenticator **lehetőséget.**

A felhasználó ekkor egy számot fog mutatni. Az alkalmazás jelszó megadása helyett a megfelelő szám kiválasztásával kéri a felhasználót a hitelesítésre.

Miután a felhasználó jelszó nélküli telefonos bejelentkezést használt, az alkalmazás továbbra is végigvezeti a felhasználót ezen a módszeren. A felhasználó azonban látni fogja a lehetőséget, hogy másik módszert választ.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Példa böngészőbeli bejelentkezésre a Microsoft Authenticator használatával.":::

## <a name="known-issues"></a>Ismert problémák

A következő ismert problémák léteznek.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Nem látom a jelszó nélküli telefonos bejelentkezést

Az egyik esetben a felhasználó függőben lévő jelszó nélküli telefonos bejelentkezési ellenőrzéssel is tud bejelentkezni. Előfordulhat azonban, hogy a felhasználó újra megpróbál bejelentkezni. Ebben az esetben előfordulhat, hogy a felhasználó csak a jelszó beírni kívánt beállítását látja.

A forgatókönyv megoldásához a következő lépések használhatók:

1. Nyissa meg a Microsoft Authenticator alkalmazást.
2. Válaszadás az értesítési kérésekre.

Ezután a felhasználó továbbra is használhatja a jelszó nélküli telefonos bejelentkezést.

### <a name="federated-accounts"></a>Összevont fiókok

Ha egy felhasználó engedélyezte a jelszó nélküli hitelesítő adatokat, az Azure AD bejelentkezési folyamata nem használja tovább a bejelentkezési \_ tippet. Ezért a folyamat már nem gyorsítja fel a felhasználót az összevont bejelentkezési hely felé.

Ez a logika általában megakadályozza, hogy egy hibrid bérlő felhasználója a Active Directory szolgáltatásokhoz (AD FS) irányítva a bejelentkezés ellenőrzéséhez. A felhasználónak azonban továbbra is lehetősége van a **Jelszó használata gombra kattintni.**

### <a name="azure-mfa-server"></a>Azure MFA-kiszolgáló

A végfelhasználó egy helyszíni Azure MFA-kiszolgálón keresztül engedélyezhető a többtényezős hitelesítéshez (MFA). A felhasználó így is létrehozhat és felhasználhat egyetlen jelszó nélküli telefonos bejelentkezési hitelesítő adatokat.

Ha a felhasználó több telepítést (5+) próbál frissíteni a Microsoft Authenticator-alkalmazásból jelszó nélküli telefonos bejelentkezési hitelesítő adatokkal, ez a módosítás hibát eredményezhet.

### <a name="device-registration"></a>Eszközregisztráció

Mielőtt létrehozhatja ezt az új erős hitelesítő adatokat, előfeltételek is vannak. Az egyik előfeltétel az, hogy az Microsoft Authenticator alkalmazást az Azure AD-bérlőn belül regisztrálni kell egy egyéni felhasználó számára.

Jelenleg egy eszköz csak egy bérlőben regisztrálható. Ez a korlát azt jelenti, hogy a Microsoft Authenticator alkalmazásban csak egy munkahelyi vagy iskolai fiók engedélyezhető telefonos bejelentkezéshez.

> [!NOTE]
> Az eszközregisztráció nem ugyanaz, mint az eszközkezelés vagy a mobileszköz-kezelés (MDM). Az eszközregisztráció csak egy eszközazonosítót és egy felhasználói azonosítót társít az Azure AD-címtárban.

## <a name="next-steps"></a>Következő lépések

Az Azure AD-hitelesítéssel és a jelszó nélküli módszerekkel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A jelszó nélküli hitelesítés működése](concept-authentication-passwordless.md)
- [Tudnivalók az eszközregisztrációról](../devices/overview.md#getting-devices-in-azure-ad)
- [Tudnivalók az Azure AD Multi-Factor Authenticationről](../authentication/howto-mfa-getstarted.md)
