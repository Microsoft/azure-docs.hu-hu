---
title: Jelszó nélküli bejelentkezés a Microsoft Authenticator app-Azure Active Directory
description: Jelszó nélküli bejelentkezés engedélyezése az Azure AD-be az Microsoft Authenticator alkalmazással (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62dea67f139857befc7ca24b8b8a105241b5c949
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626155"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással (előzetes verzió)

A Microsoft Authenticator alkalmazás jelszó használata nélkül is bejelentkezhet bármely Azure AD-fiókba. Microsoft Authenticator a kulcs-alapú hitelesítés használatával engedélyezhető egy olyan eszközhöz kötött felhasználói hitelesítő adat, amelyben az eszköz PIN-kódot vagy biometrikus adatokat használ. A [vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) hasonló technológiát használ.

Ez a hitelesítési technológia bármilyen eszköz platformon használható, beleértve a mobilt is. Ez a technológia olyan alkalmazásokkal vagy webhelyekkel is használható, amelyek integrálva vannak a Microsoft Authentication librarys használatával.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Böngészőbeli bejelentkezés – példa arra, hogy a felhasználó jóváhagyja a bejelentkezést.":::

Azok a felhasználók, akik engedélyezték a telefonos bejelentkezést a Microsoft Authenticator alkalmazásból, egy üzenet jelenik meg, amely arra kéri őket, hogy koppintson egy számra az alkalmazásban. A rendszer nem kér felhasználónevet vagy jelszót. Az alkalmazásban a bejelentkezési folyamat befejezéséhez a felhasználónak a következő műveleteket kell végrehajtania:

1. Egyezik a számmal.
2. Válassza a **Jóváhagyás** lehetőséget.
3. Adja meg a PIN-kódját vagy a biometrikus adatokat.

## <a name="prerequisites"></a>Előfeltételek

Ha jelszó nélküli telefonos bejelentkezést szeretne használni a Microsoft Authenticator alkalmazással, a következő előfeltételek teljesülése szükséges:

- Az Azure AD Multi-Factor Authentication ellenőrzési módszerként engedélyezett leküldéses értesítésekkel.
- Az Microsoft Authenticator legújabb verziója iOS 8,0 vagy újabb, vagy Android 6,0 vagy újabb rendszert futtató eszközökön.

> [!NOTE]
> Ha az Azure AD PowerShell használatával engedélyezte Microsoft Authenticator jelszó nélküli bejelentkezési előnézetet, akkor az a teljes címtárhoz engedélyezve volt. Ha engedélyezi ezt az új metódust, az supercedes a PowerShell-házirendet. Azt javasoljuk, hogy a bérlő összes felhasználója számára engedélyezze az új *hitelesítési módszerek* menüt, ellenkező esetben az új szabályzatban nem szereplő felhasználók nem tudnak többé jelszó nélkül bejelentkezni.

## <a name="enable-passwordless-authentication-methods"></a>Jelszóval nem rendelkező hitelesítési módszerek engedélyezése

A jelszó nélküli hitelesítés az Azure AD-ben való használatához először engedélyezze a kombinált regisztrációs élményt, majd engedélyezze a felhasználók számára a kevesebb jelszót.

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztráció használatának engedélyezése

A jelszóval nem rendelkező hitelesítési módszerek regisztrációs funkciói a kombinált regisztráció funkcióra támaszkodnak. Ha szeretné, hogy a felhasználók maguk végezzék el a közös regisztrációt, kövesse a következő lépéseket a [kombinált biztonsági információk regisztrálásának engedélyezéséhez](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Jelszó nélküli telefonos bejelentkezési hitelesítési módszerek engedélyezése

Az Azure AD segítségével kiválaszthatja, hogy mely hitelesítési módszereket lehet használni a bejelentkezési folyamat során. A felhasználók ezután regisztrálhatják a használni kívánt metódusokat.

A jelszó nélküli telefonos bejelentkezés hitelesítési módszerének engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) *globális rendszergazdai* fiókkal.
1. Keresse meg és válassza ki *Azure Active Directory*, majd keresse meg a **biztonsági**  >  **hitelesítési módszerek**  >  **házirendjeit**.
1. A **Microsoft Authenticator (előzetes verzió)** alatt válassza a következő beállításokat:
   1. **Engedélyezés** – igen vagy nem
   1. **Cél** – az összes felhasználó vagy a felhasználók kiválasztása
1. Alapértelmezés szerint minden hozzáadott csoport vagy felhasználó számára engedélyezve van a Microsoft Authenticator használata a jelszó nélküli és a leküldéses értesítési módokban ("minden" mód). Ennek módosításához minden sorra:
   1. Tallózással keresse meg a következőt: **..**  >  . **Konfigurálás**.
   1. **Hitelesítési mód** esetén – bármilyen, jelszó nélküli vagy leküldéses
1. Az új szabályzat alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>A Microsoft Authenticator felhasználói regisztrációja és kezelése

A felhasználók a következő lépések végrehajtásával regisztrálhatják magukat az Azure AD-ban használt jelszóval nem rendelkező hitelesítési módszerként:

1. Lépjen a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) lapra.
1. Jelentkezzen be, majd adja hozzá a hitelesítő alkalmazást a **method > hitelesítő alkalmazás hozzáadása** lehetőség kiválasztásával, majd **adja hozzá** a elemet.
1. Az eszközön a Microsoft Authenticator alkalmazás telepítéséhez és konfigurálásához kövesse az utasításokat.
1. A hitelesítő konfigurációjának befejezéséhez válassza a **kész** lehetőséget.
1. **Microsoft Authenticator** a regisztrált fiók legördülő menüjében válassza a **telefonos bejelentkezés engedélyezése** lehetőséget.
1. Kövesse az alkalmazás utasításait a fiók jelszavas telefonos bejelentkezéshez való regisztrálásának befejezéséhez.

A vállalatok jelszó használata nélkül is elérhetik a felhasználókat a telefonnal való bejelentkezéshez. Ha további segítségre van a Microsoft Authenticator alkalmazás konfigurálásához és a telefonos bejelentkezés engedélyezéséhez, olvassa el a következőt: [Bejelentkezés a fiókba a Microsoft Authenticator alkalmazás használatával](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Azok a felhasználók, akik nem engedélyezték a telefonos bejelentkezést, a továbbiakban nem tudják engedélyezni a Microsoft Authenticator alkalmazáson belül.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

A felhasználók a következő műveletek elvégzése után megkezdhetik a jelszó nélküli bejelentkezés használatát:

- A rendszergazda engedélyezte a felhasználó bérlőjét.
- A felhasználó frissítette Microsoft Authenticator alkalmazást a telefonos bejelentkezés engedélyezéséhez.

Amikor a felhasználó először indítja el a telefonos bejelentkezési folyamatot, a felhasználó a következő lépéseket hajtja végre:

1. Írja be a nevét a bejelentkezési oldalon.
2. Válassza a **tovább** lehetőséget.
3. Ha szükséges, válassza **ki a bejelentkezés egyéb módjait**.
4. Kiválasztja a **kérelem jóváhagyása a Microsoft Authenticator alkalmazásban** lehetőséget.

A felhasználó ezután egy számmal jelenik meg. Az alkalmazás a megfelelő szám kiválasztásával kéri a felhasználót a hitelesítéshez a jelszó beírása helyett.

Miután a felhasználó felhasználta a jelszó nélküli telefonos bejelentkezést, az alkalmazás továbbra is végigvezeti a felhasználót ezzel a módszerrel. A felhasználó azonban megtekintheti a másik módszer kiválasztásának lehetőségét is.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Böngészőbeli bejelentkezés – példa a Microsoft Authenticator alkalmazás használatával.":::

## <a name="known-issues"></a>Ismert problémák

Az aktuális előzetes verzióban a következő ismert problémák léteznek.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Nem jelenik meg a jelszó nélküli telefonos bejelentkezés beállítása

Az egyik forgatókönyvben a felhasználók megválaszolatlan, jelszó nélküli telefonos bejelentkezési ellenőrzést végeznek. A felhasználó mégis megpróbálhat újra bejelentkezni. Ha ez történik, előfordulhat, hogy a felhasználó csak a jelszó megadására szolgáló lehetőséget látja.

A forgatókönyv megoldásához a következő lépéseket lehet használni:

1. Nyissa meg a Microsoft Authenticator alkalmazást.
2. Válaszoljon bármely értesítési kérésre.

Ezután a felhasználó továbbra is használhatja a jelszó nélküli telefonos bejelentkezést.

### <a name="federated-accounts"></a>Összevont fiókok

Ha egy felhasználó engedélyezte a jelszó nélküli hitelesítő adatokat, az Azure AD bejelentkezési folyamata leáll a bejelentkezési útmutató használatával \_ . Ezért a folyamat már nem gyorsítja fel a felhasználót egy összevont bejelentkezési hely felé.

Ez a logika általában megakadályozza, hogy egy hibrid bérlő felhasználója Active Directory összevont szolgáltatások (AD FS) számára legyen átirányítva a bejelentkezési ellenőrzéshez. A felhasználó azonban megtartja a **jelszó használata** lehetőséget.

### <a name="azure-mfa-server"></a>Azure MFA-kiszolgáló

A végfelhasználó engedélyezhető a többtényezős hitelesítés (MFA) számára egy helyszíni Azure MFA-kiszolgálón keresztül. A felhasználó továbbra is létrehozhat és használhat egyszeri jelszó nélküli telefonos bejelentkezési hitelesítő adatokat.

Ha a felhasználó a jelszó nélküli telefonos bejelentkezési hitelesítő adatokkal próbálja meg frissíteni a Microsoft Authenticator alkalmazás több telepítését (5 +), akkor ez a változás hibát eredményezhet.

### <a name="device-registration"></a>Eszközregisztráció

Ahhoz, hogy az új, erős hitelesítő adatokat létre tudja hozni, előfeltételei vannak. Az egyik előfeltétel, hogy az eszközt, amelyre az Microsoft Authenticator alkalmazást telepíti, regisztrálni kell az Azure AD-bérlőn belül az egyes felhasználók számára.

Az eszközök jelenleg csak egyetlen bérlőben regisztrálhatók. Ez a korlát azt jelenti, hogy a Microsoft Authenticator alkalmazásban csak egy munkahelyi vagy iskolai fiók engedélyezhető a telefonos bejelentkezéshez.

> [!NOTE]
> Az eszköz regisztrálása nem ugyanaz, mint az eszközkezelés vagy a mobileszköz-kezelés (MDM). Az eszközök regisztrálása csak az eszköz AZONOSÍTÓját és a felhasználói azonosítót társítja az Azure AD-címtárban.

## <a name="next-steps"></a>Következő lépések

Az Azure AD-hitelesítéssel és a jelszóval nem rendelkező módszerekkel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Ismerje meg, hogyan működik a jelszavas hitelesítés](concept-authentication-passwordless.md)
- [Tudnivalók az eszközök regisztrálásáról](../devices/overview.md#getting-devices-in-azure-ad)
- [További tudnivalók az Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
