---
title: Hitelesítési módszerek tevékenység – Azure Active Directory
description: A szervezet által a bejelentkezéshez és a jelszó-visszaállításhoz használt hitelesítési módszerek áttekintése.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6566ee7e744411fc3b7005938f95181e5c5ec94d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645179"
---
# <a name="authentication-methods-activity"></a>Hitelesítési módszerek tevékenységei 

Az új hitelesítési módszerek tevékenység-irányítópult lehetővé teszi a rendszergazdáknak, hogy felügyeljék a hitelesítési módszer regisztrációját és használatát a szervezeten belül. Ez a jelentéskészítési funkció lehetővé teszi a szervezet számára, hogy megértse, milyen módszereket regisztrálnak, és hogyan használják őket.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A következő szerepkörök használhatják a használatot és az információkat:

- Jelentések olvasója
- Biztonsági olvasó
- Biztonsági rendszergazda
- Globális rendszergazda

 A használat és az adatok eléréséhez prémium szintű Azure AD P1 vagy P2 licenc szükséges. Az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási (SSPR) licencelési információk a [Azure Active Directory díjszabási webhelyen](https://azure.microsoft.com/pricing/details/active-directory/)találhatók.

## <a name="how-it-works"></a>Működés

A hitelesítési módszer használatának és az információknak a elérése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson **Azure Active Directory**  >  **biztonsági**  >  **hitelesítési módszerek**  >  **tevékenység** lehetőségre.
1. A jelentésnek két lapja van: **regisztráció** és **használat**.

   ![A hitelesítési módszerek tevékenységének áttekintése](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Regisztráció részletei

A [**regisztráció lapon**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) megtekintheti a többtényezős hitelesítésre, a passowordless hitelesítésre és az önkiszolgáló jelszó-visszaállításra képes felhasználók számát. 

Kattintson **Az Azure multi-Factor Authentication**, a **jelszó nélküli hitelesítésre** alkalmas felhasználók, illetve az önkiszolgáló **jelszó-visszaállításra** képes felhasználók lehetőségre, vagy a felhasználói regisztráció részleteinek előzetes szűrésére.

- Az **azure multi-Factor Authentication-t támogató felhasználók** az Azure ad-ben az MFA-ra képes felhasználók részletezését jelenítik meg. A felhasználók akkor tekinthetők megfelelőnek, ha egy erős hitelesítési módszerhez vannak regisztrálva, és a szabályzat lehetővé teszi, hogy ezt a módszert használja az MFA végrehajtásához. Ez a szám nem tükrözi az Azure AD-n kívüli MFA-hoz regisztrált felhasználókat. 
- A **jelszó nélküli hitelesítésre alkalmas felhasználók** a jelszó nélkül való bejelentkezésre alkalmas felhasználók lebontását mutatják. Ide tartoznak a FIDO2, a vállalati Windows Hello és a jelszó nélküli telefonos bejelentkezés a Microsoft Authenticator alkalmazással regisztrált felhasználók. 
- Az **önkiszolgáló jelszó-visszaállításra alkalmas felhasználók** az önkiszolgáló jelszó-visszaállításra képes felhasználók részletezését jelenítik meg. A felhasználók akkor SSPR, ha elegendő módszernek vannak regisztrálva ahhoz, hogy kielégítsék a szervezet SSPR-szabályzatát, és lehetővé tegye a SSPR végrehajtását. 

  ![A regisztrációra alkalmas felhasználók képernyőképe](media/how-to-authentication-methods-usage-insights/users-capable.png)

A **hitelesítési módszer által regisztrált felhasználók** azt mutatják be, hogy hány felhasználó van regisztrálva az egyes hitelesítési módszerekhez. A hitelesítési módszerre kattintva megtekintheti, hogy mely felhasználók vannak regisztrálva az adott metódushoz. 

![Képernyőfelvétel a regisztrált felhasználókról](media/how-to-authentication-methods-usage-insights/users-registered.png)

A hitelesítési módszer **legutóbbi regisztrációja** a hitelesítési módszer szerint a sikeres és sikertelen hitelesítési módszerek számát jeleníti meg. Kattintson egy hitelesítési módszerre az adott metódus legutóbbi regisztrációs eseményeinek megjelenítéséhez.

![Képernyőfelvétel a nemrégiben regisztrált](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Használat részletei

A **használati** jelentés megjeleníti, hogy a felhasználók mely hitelesítési módszereket használják a bejelentkezéshez és a jelszavuk alaphelyzetbe állításához.

![Képernyőkép a használati oldalról](media/how-to-authentication-methods-usage-insights/usage-page.png)

A **hitelesítési követelmények alapján a bejelentkezések** azon sikeres felhasználói interaktív bejelentkezések számát jeleníti meg, amelyek az Azure ad-ben a többtényezős hitelesítés végrehajtásához szükségesek. Ez nem tükrözi azokat a bejelentkezéseket, amelyekben az MFA-t egy harmadik féltől származó MFA-szolgáltató kényszeríti.

![A bejelentkezések képernyőképe a hitelesítési követelmény alapján](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

A **hitelesítési módszerekkel történő bejelentkezések** a felhasználói interaktív bejelentkezések (sikeres és sikertelen) számát jelenítik meg a használt hitelesítési módszer alapján. Nem tartalmazza azokat a bejelentkezéseket, amelyekben a jogkivonatban szereplő jogcím eleget tett a hitelesítési követelménynek.

![A bejelentkezések képernyőképe metódus alapján](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

A **jelszó alaphelyzetbe állítása és a fiók felszabadítása** során a rendszer az idő múlásával megjeleníti a sikeres jelszavak és jelszavak alaphelyzetbe állítása (önkiszolgáló és rendszergazda) számát.

![Képernyőkép az alaphelyzetbe állításról és a zárolás feloldásáról](media/how-to-authentication-methods-usage-insights/password-changes.png)

A jelszó-visszaállítás a **hitelesítési módszer** szerint a sikeres és sikertelen hitelesítések számát jeleníti meg a jelszó-átállítási folyamat során a hitelesítési módszer használatával.

![Képernyőkép az alaphelyzetbe állításról metódus alapján](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Felhasználói regisztráció részletei 

A lista tetején található vezérlők használatával megkeresheti a felhasználókat, és szűrheti a felhasználók listáját a megjelenített oszlopok alapján.

A regisztráció részletei jelentés az alábbi információkat jeleníti meg az egyes felhasználókra vonatkozóan:

- Egyszerű Felhasználónév
- Name
- MFA-kompatibilis (képes, nem alkalmas)
- Jelszó nélkül használható (képes, nem alkalmas)
- SSPR regisztrálva (regisztrált, nincs regisztrálva)
- SSPR engedélyezve (engedélyezve, nincs engedélyezve)
- SSPR-kompatibilis (alkalmas, nem kompatibilis) 
- Regisztrált metódusok (E-mail, mobiltelefon, alternatív mobiltelefon, irodai telefon, Microsoft Authenticator leküldéses, szoftveres egyszeri jelszó, FIDO2, biztonsági kulcs, biztonsági kérdések)

  ![Képernyőfelvétel a felhasználói regisztráció részleteiről](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Az események regisztrálása és alaphelyzetbe állítása 

Az **események regisztrálása és alaphelyzetbe állítása** az elmúlt 24 órában, az elmúlt hét napban vagy az elmúlt 30 napban az alábbiakat tartalmazza:

- Date
- Felhasználónév
- User 
- Szolgáltatás (regisztráció, alaphelyzetbe állítás)
- Használt módszer (alkalmazás-értesítés, alkalmazás kódja, telefonhívás, Office-hívás, alternatív mobil hívás, SMS, E-mail, biztonsági kérdések)
- Állapot (sikeres, sikertelen)
- Hiba oka (magyarázat)

  ![Képernyőkép a használati oldalról](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Korlátozások

Az ideiglenes hozzáférési pass (TAP) regisztrációja nem jelenik meg a jelentés regisztráció lapján, mert csak rövid ideig érvényesek.

## <a name="next-steps"></a>Következő lépések

- [A hitelesítési módszerek használati jelentésének API használata](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [A szervezet hitelesítési módszereinek kiválasztása](concept-authentication-methods.md)
- [Kombinált regisztrációs élmény](concept-registration-mfa-sspr-combined.md)