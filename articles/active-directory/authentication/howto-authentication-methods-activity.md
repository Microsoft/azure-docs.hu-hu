---
title: Hitelesítési módszerek tevékenység – Azure Active Directory
description: A felhasználók által a bejelentkezéshez és a jelszavak visszaállításához használt hitelesítési módszerek áttekintése.
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
ms.openlocfilehash: 699ff88e4181dada5eacaa3f13469722cdf7ceaa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530442"
---
# <a name="authentication-methods-activity"></a>Hitelesítési módszerek tevékenységei 

Az új hitelesítési módszerek tevékenység-irányítópultja lehetővé teszi a rendszergazdák számára a hitelesítési módszerek regisztrációjának és használatának megfigyelését a szervezeten belül. Ez a jelentéskészítési funkció lehetővé teszi a szervezet számára a regisztrált módszerek és azok használata áttekintését.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

Az alábbi engedélyekkel rendelkező beépített és egyéni szerepkörök hozzáférhetnek a Hitelesítési módszerek tevékenység panelhez és AZ API-khoz:

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

A következő szerepkörök rendelkezik a szükséges engedélyekkel:

- Jelentések olvasója
- Biztonsági olvasó
- Globális olvasó
- Biztonsági operátor
- Biztonsági rendszergazda
- Globális rendszergazda

 A prémium szintű Azure AD P1 vagy P2 licenc szükséges a használat és az elemzések eléréséhez. Az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) licencelési információit a következő [díjszabási Azure Active Directory talál:](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Működés

A hitelesítési módszer használatának és elemzésének elérése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson **a Azure Active Directory** hitelesítési  >  **módszerek**  >  **tevékenységének**  >  **gombra.**
1. A jelentésben két lap található: **Regisztráció és** **Használat.**

   ![Hitelesítési módszerek tevékenységének áttekintése](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Regisztráció részletei

A Regisztráció lapon [**látható**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) a többtényezős hitelesítésre, a jelszó nélküli hitelesítésre és az önkiszolgáló jelszó-visszaállításra képes felhasználók száma. 

A felhasználóregisztráció részleteinek előzetes szűréséhez kattintson a következő lehetőségek bármelyikének egyikéhez:

- **Az Azure Multi-Factor Authenticationre** képes felhasználók a következő két felhasználó lebontását jelenítik meg:
  - Erős hitelesítési módszerre regisztrálva 
  - Szabályzat által engedélyezett az MFA ezen módszerének használatára 
  
  Ez a szám nem tükrözi az Azure AD-n kívül az MFA-hoz regisztrált felhasználókat. 
- A **jelszó** nélküli hitelesítésre képes felhasználók azon felhasználók lebontását jeleníti meg, akik jelszó nélkül, a FIDO2, Vállalati Windows Hello vagy jelszó nélküli telefonos bejelentkezés használatával jelentkeznek be az Microsoft Authenticator alkalmazással. 
- **Az önkiszolgáló jelszó-visszaállításra** képes felhasználók a jelszavukat alaphelyzetbe állítható felhasználók lebontását jelenítik meg. A felhasználók visszaállíthatja a jelszavukat, ha mindkettő:
  - Elég módszer van regisztrálva ahhoz, hogy megfeleljen a szervezet önkiszolgáló jelszó-visszaállítási szabályzatának 
  - A jelszó visszaállításának engedélyezése 

  ![Képernyőkép a regisztráló felhasználókról](media/how-to-authentication-methods-usage-insights/users-capable.png)

**A hitelesítési módszerrel regisztrált felhasználók** azt mutatják meg, hogy hány felhasználó van regisztrálva az egyes hitelesítési módszerekhez. Kattintson egy hitelesítési módszerre annak nevére, hogy ki regisztrált erre a módszerre.

![Képernyőkép a regisztrált felhasználókról](media/how-to-authentication-methods-usage-insights/users-registered.png)

**A legutóbbi, hitelesítési módszerrel** végzett regisztráció azt mutatja, hogy hány regisztráció volt sikeres és sikertelen, hitelesítési módszer szerint rendezve. Kattintson egy hitelesítési módszerre az erre a módszerre vonatkozó legutóbbi regisztrációs eseményekhez.

![A nemrégiben regisztráltak képernyőképe](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Használat részletei

A **Használati jelentés** megmutatja, hogy mely hitelesítési módszerek használhatók a bejelentkezéshez és a jelszavak visszaállításához.

![Képernyőkép a Használat lapról](media/how-to-authentication-methods-usage-insights/usage-page.png)

**A hitelesítési követelmények** alapján való bejelentkezések azt mutatják meg, hogy hány sikeres interaktív felhasználói bejelentkezésre volt szükség az egytényezős és a többtényezős hitelesítéshez az Azure AD-ban. Azok a bejelentkezések, amelyekben az MFA-t egy külső MFA-szolgáltató kényszerítette, nem tartalmazzák.

![Képernyőkép a bejelentkezésről hitelesítési követelmények szerint](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**A hitelesítési módszerrel** való bejelentkezések a felhasználói interaktív bejelentkezések (sikeres és sikertelen) számát jeleníti meg a használt hitelesítési módszer szerint. Nem tartalmazza az olyan bejelentkezéseket, ahol a hitelesítési követelményt egy jogcím kielégíti a jogkivonatban.

![Képernyőkép a bejelentkezések metódusról](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**A jelszó-visszaállítások és** a fiókfeloldási zárolások száma a sikeres jelszóváltozások és új jelszó-visszaállítások (önkiszolgáló és rendszergazdai) számát jeleníti meg az idő alatt.

![Képernyőkép az alaphelyzetbe állításról és a zárolás feloldásáról](media/how-to-authentication-methods-usage-insights/password-changes.png)

**A jelszó-visszaállítás hitelesítési módszerrel** a sikeres és sikertelen hitelesítések számát mutatja a hitelesítési módszerrel a jelszó-visszaállítási folyamat során.

![Képernyőkép az alaphelyzetbe állításról metódus szerint](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Felhasználói regisztráció részletei 

A lista tetején található vezérlőkkel rákereshet egy felhasználóra, és a megjelenő oszlopok alapján szűrheti a felhasználók listáját.

A regisztrációs adatokról szóló jelentés az alábbi információkat jeleníti meg az egyes felhasználókról:

- Egyszerű felhasználónév
- Name
- MFA-kompatibilis (képes, nem alkalmas)
- Jelszó nélküli kompatibilis (képes, nem képes)
- Regisztrált SSPR (Regisztrált, Nincs regisztrálva)
- SSPR engedélyezve (engedélyezve, nincs engedélyezve)
- SSPR-kompatibilis (kompatibilis, nem kompatibilis) 
- Regisztrált metódusok (e-mail, mobiltelefon, alternatív mobiltelefon, irodai telefon, Microsoft Authenticator Push, Szoftver egy alkalommal PIN-kód, FIDO2, Biztonsági kulcs, Biztonsági kérdések)

  ![Képernyőkép a felhasználóregisztráció részleteiről](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Regisztrációs és alaphelyzetbe állítási események 

**A regisztrációs és visszaállítási események** az elmúlt 24 óra, az elmúlt hét nap vagy az elmúlt 30 nap regisztrációs és visszaállítási eseményeit jelenítik meg, beleértve a következőket:

- Date
- Felhasználónév
- Felhasználó 
- Szolgáltatás (regisztráció, alaphelyzetbe állítás)
- Használt módszer (alkalmazásértesítés, alkalmazáskód, telefonhívás, Office-hívás, alternatív mobilhívás, SMS, e-mail, biztonsági kérdések)
- Állapot (sikeres, sikertelen)
- A hiba oka (magyarázat)

  ![Képernyőkép a regisztrációs és visszaállítási eseményekről](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Korlátozások

- A jelentésben az adatok nem valós időben frissülnek, és akár néhány órás késést is tükrözhetnek.
- ideiglenes hozzáférési kód nem jelennek meg a jelentés regisztrációs lapján, mert csak rövid ideig érvényesek.
- A felhasználó által konfigurált **PhoneAppNotification** vagy **PhoneAppOTP** metódusok nem jelennek meg az irányítópulton. 

## <a name="next-steps"></a>Következő lépések

- [A hitelesítési módszerek használati jelentési API-jának használata](/graph/api/resources/authenticationmethods-usage-insights-overview)
- [Hitelesítési módszerek kiválasztása a szervezet számára](concept-authentication-methods.md)
- [Kombinált regisztrációs élmény](concept-registration-mfa-sspr-combined.md)
