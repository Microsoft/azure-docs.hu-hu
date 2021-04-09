---
title: Hitelesítési módszerek tevékenység – Azure Active Directory
description: A felhasználók által a bejelentkezéshez és a jelszavak alaphelyzetbe állításához regisztrált hitelesítési módszerek áttekintése.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a1cb71917fdb30ffccda21bedffe1c7f2a428c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557955"
---
# <a name="authentication-methods-activity"></a>Hitelesítési módszerek tevékenységei 

Az új hitelesítési módszerek tevékenység-irányítópult lehetővé teszi a rendszergazdáknak, hogy felügyeljék a hitelesítési módszer regisztrációját és használatát a szervezeten belül. Ez a jelentéskészítési funkció lehetővé teszi a szervezet számára, hogy megértse, milyen módszereket regisztrálnak, és hogyan használják őket.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A beépített és egyéni szerepkörök a következő engedélyekkel férhetnek hozzá a hitelesítési módszerek tevékenység paneljéhez és API-khoz:

- Microsoft. Directory/auditLogs/allProperties/READ
- Microsoft. Directory/signInReports/allProperties/READ

A következő szerepkörök rendelkeznek a szükséges engedélyekkel:

- Jelentések olvasója
- Biztonsági olvasó
- Globális olvasó
- Biztonsági operátor
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

Kattintson a következő beállítások bármelyikére a felhasználói regisztráció részleteinek megtekintéséhez:

- Az **Azure multi-Factor Authentication-t támogató felhasználók** a két felhasználó részletezését jelenítik meg:
  - Erős hitelesítési módszerhez regisztrálva 
  - Házirend által engedélyezett módszer az MFA használatára 
  
  Ez a szám nem tükrözi az Azure AD-n kívüli MFA-hoz regisztrált felhasználókat. 
- A **jelszó nélküli hitelesítésre alkalmas felhasználók** a FIDO2, a vállalati Windows Hello vagy a jelszó nélküli telefonos bejelentkezéshez használt felhasználók lebontását jelenítik meg a Microsoft Authenticator alkalmazással. 
- Az **önkiszolgáló jelszó-visszaállításra alkalmas felhasználók** a jelszavuk alaphelyzetbe állítására jogosult felhasználók részletezését jelenítik meg. Ha mindkettő a következőket állítja be, a felhasználók is visszaállíthatják a jelszavukat:
  - Elegendő módszer van regisztrálva ahhoz, hogy megfeleljenek a szervezeti szabályzatnak az önkiszolgáló jelszó-visszaállításhoz 
  - Engedélyezve a jelszó alaphelyzetbe állítása 

  ![A regisztrálni képes felhasználók képernyőképe](media/how-to-authentication-methods-usage-insights/users-capable.png)

A **hitelesítési módszer által regisztrált felhasználók** azt mutatják be, hogy hány felhasználó van regisztrálva az egyes hitelesítési módszerekhez. A hitelesítési módszerre kattintva megtekintheti, hogy ki van regisztrálva az adott metódushoz.

![Képernyőfelvétel a regisztrált felhasználókról](media/how-to-authentication-methods-usage-insights/users-registered.png)

**A hitelesítési módszer legutóbbi regisztrációja** azt mutatja, hogy hány regisztráció sikeres és sikertelen volt, a hitelesítési módszer szerint rendezve. Kattintson egy hitelesítési módszerre az adott metódus legutóbbi regisztrációs eseményeinek megjelenítéséhez.

![Képernyőfelvétel a nemrégiben regisztrált](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Használat részletei

A **használati** jelentés megjeleníti, hogy mely hitelesítési módszerek használhatók a bejelentkezéshez és a jelszavak alaphelyzetbe állításához.

![Képernyőkép a használati oldalról](media/how-to-authentication-methods-usage-insights/usage-page.png)

A **hitelesítési követelmény szerinti bejelentkezések** az Azure ad-ben a többtényezős hitelesítéshez szükséges, sikeres felhasználói interaktív bejelentkezések számát jelenítik meg. Nem tartoznak bele azok a bejelentkezések, ahol az MFA-t egy harmadik féltől származó MFA-szolgáltató kényszeríti.

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

  ![Képernyőfelvétel a regisztrációról és az események alaphelyzetbe állításáról](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Korlátozások

- A jelentésben szereplő adat nem frissül valós időben, és akár néhány óra késéssel is járhat.
- Az ideiglenes hozzáférési pass-regisztrációk nem tükröződnek a jelentés regisztráció lapján, mert csak rövid ideig érvényesek.
- A felhasználó által konfigurálható **PhoneAppNotification** -vagy **PhoneAppOTP** -metódusok nem jelennek meg az irányítópulton. 

## <a name="next-steps"></a>Következő lépések

- [A hitelesítési módszerek használati jelentésének API használata](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [A szervezet hitelesítési módszereinek kiválasztása](concept-authentication-methods.md)
- [Kombinált regisztrációs élmény](concept-registration-mfa-sspr-combined.md)
