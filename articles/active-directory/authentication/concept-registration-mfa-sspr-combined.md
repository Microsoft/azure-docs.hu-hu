---
title: A SSPR és az Azure AD Multi-Factor Authentication együttes regisztrálása – Azure Active Directory
description: Ismerje meg a Azure Active Directory együttes regisztrációs élményét, amely lehetővé teszi a felhasználók számára az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási szolgáltatás regisztrálását
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 112ad0714c84cd3be08788b3277f52372f6d0373
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938478"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Összevont biztonsági információk regisztrálása Azure Active Directory – áttekintés

A kombinált regisztráció előtt a felhasználók az Azure AD-Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszereit külön-külön regisztrálták. Az emberek zavarosak voltak, hogy hasonló módszerek voltak használatban Multi-Factor Authentication és SSPR, de mindkét szolgáltatáshoz regisztrálniuk kellett őket. A kombinált regisztráció révén a felhasználók egyszer regisztrálhatnak, és a Multi-Factor Authentication és a SSPR előnyeit is igénybe vehetik.

> [!NOTE]
> Az Azure AD-bérlők augusztus 2020 15-én kezdődően automatikusan engedélyezve lesznek a kombinált regisztrációhoz. 

Ez a cikk a kombinált biztonsági regisztrációt ismerteti. A kombinált biztonsági regisztráció megkezdéséhez tekintse meg a következő cikket:

> [!div class="nextstepaction"]
> [Kombinált biztonsági regisztráció engedélyezése](howto-registration-mfa-sspr-combined.md)

![Saját fiók, amely egy felhasználó regisztrált biztonsági adatait tartalmazza](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

Az új felület engedélyezése előtt tekintse át ezt a rendszergazda által irányított dokumentációt és a felhasználó által irányított dokumentációt, hogy megértse a funkció funkcióit és hatását. A [felhasználói dokumentáció](../user-help/security-info-setup-signin.md) alapján felkészítheti a felhasználókat az új élményre, és segítheti a sikeres bevezetést.

Az Azure AD kombinált biztonsági információinak regisztrációja jelenleg nem érhető el olyan nemzeti felhők számára, mint az Azure Germany vagy az Azure China 21Vianet. Az Azure USA kormányzati szervei számára érhető el.

> [!IMPORTANT]
> Azok a felhasználók, akik számára engedélyezve van az eredeti előzetes verzió és a bővített kombinált regisztrációs élmény, az új viselkedést láthatják. Azok a felhasználók, akik mindkét élmény esetében engedélyezve vannak, csak a saját fiókkal kapcsolatos felhasználói élményt látják. A *fiókom a* közös regisztráció megjelenésével és működésével igazodik, és zökkenőmentes felhasználói élményt nyújt a felhasználóknak. A felhasználók a következő helyen tekinthetik meg a fiókomat: [https://myaccount.microsoft.com](https://myaccount.microsoft.com) .
>
> A biztonsági adatok elérésére tett kísérlet során hibaüzenet jelenhet meg, például: "sajnos nem lehet bejelentkezni". Győződjön meg arról, hogy nincs olyan konfigurációs vagy csoportházirend-objektuma, amely blokkolja a harmadik féltől származó cookie-kat a böngészőben.

A *saját fiók* oldalai honosítva vannak az oldalt elérő számítógép nyelvi beállításai alapján. A Microsoft a böngésző gyorsítótárában használt legújabb nyelvet tárolja, így a lapok elérésére irányuló próbálkozások továbbra is az utolsó használt nyelven jelennek meg. Ha törli a gyorsítótárat, az oldalak ismételt renderelése megtörténjen.

Ha egy adott nyelvet szeretne kényszeríteni, hozzáadhat az `?lng=<language>` URL végéhez, ahol a a `<language>` megjeleníteni kívánt nyelv kódja.

![SSPR vagy egyéb biztonsági ellenőrzési módszerek beállítása](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>A kombinált regisztrációban elérhető módszerek

A kombinált regisztráció a következő hitelesítési módszereket és műveleteket támogatja:

| Metódus | Regisztráció | Módosítás | Törlés |
| --- | --- | --- | --- |
| Microsoft Authenticator | Igen (legfeljebb 5) | Nem | Igen |
| Egyéb hitelesítő alkalmazás | Igen (legfeljebb 5) | Nem | Igen |
| Hardver jogkivonata | Nem | Nem | Igen |
| Telefon | Igen | Igen | Yes |
| Másik telefon | Igen | Igen | Yes |
| Munkahelyi telefon | Igen | Igen | Yes |
| E-mail | Igen | Igen | Yes |
| Biztonsági kérdések | Igen | Nem | Igen |
| Alkalmazásjelszavak. | Igen | Nem | Igen |
| FIDO2 biztonsági kulcsok<br />*Felügyelt mód csak a [biztonsági adatok](https://mysignins.microsoft.com/security-info) lapról*| Igen | Igen | Yes |

> [!NOTE]
> Az alkalmazás jelszavai csak az Multi-Factor Authentication számára kikényszerített felhasználók számára érhetők el. Az alkalmazás jelszavai nem érhetők el azokhoz a felhasználókhoz, akik számára engedélyezett a Multi-Factor Authentication egy feltételes hozzáférési szabályzaton keresztül.

A felhasználók a következő beállítások egyikét állíthatják be alapértelmezett Multi-Factor Authentication metódusként:

- Microsoft Authenticator – értesítés.
- Hitelesítő alkalmazás vagy hardver-jogkivonat – kód.
- Telefonhívás.
- Szöveges üzenet.

Mivel továbbra is további hitelesítési módszereket adunk az Azure AD-hez, ezek a módszerek a kombinált regisztrációban is elérhetők.

## <a name="combined-registration-modes"></a>Kombinált regisztrációs módok

A kombinált regisztrációnak két módja van: megszakítás és felügyelet.

- A **megszakítási mód** egy varázsló-szerű élmény, amely a felhasználók számára jelenik meg, amikor regisztrálnak vagy frissítenek biztonsági adataikat a bejelentkezéskor.
- A **kezelési mód** a felhasználói profil része, és lehetővé teszi a felhasználók számára a biztonsági adatok kezelését.

Mindkét mód esetében azok a felhasználók, akik korábban már regisztráltak egy olyan metódust, amelyet a Multi-Factor Authentication használhatnak Multi-Factor Authentication ahhoz, hogy hozzáférhessenek a biztonsági adataihoz. A felhasználóknak meg kell erősíteniük az adataikat, mielőtt továbbra is használni tudják a korábban regisztrált módszereiket. 

### <a name="interrupt-mode"></a>Megszakítási mód

A kombinált regisztráció a Multi-Factor Authentication és a SSPR szabályzatot egyaránt figyelembe veszi, ha mindkettő engedélyezve van a bérlő számára. Ezek a szabályzatok határozzák meg, hogy a rendszer megszakítja-e a felhasználók regisztrációját a bejelentkezés során, és hogy mely módszerek érhetők el a regisztrációhoz.

A következő példák olyan forgatókönyveket mutatnak be, amelyekben a rendszer a biztonsági adatok regisztrálását vagy frissítését kéri a felhasználóknak:

- *Multi-Factor Authentication regisztráció a személyazonosság védelme révén kényszerítve:* A rendszer megkéri a felhasználókat, hogy regisztráljanak a bejelentkezés során. Regisztrálják Multi-Factor Authentication metódusokat és SSPR metódusokat (ha a felhasználó engedélyezve van a SSPR).
- *Multi-Factor Authentication regisztráció a felhasználónkénti multi-Factor Authentication használatával:* A rendszer megkéri a felhasználókat, hogy regisztráljanak a bejelentkezés során. Regisztrálják Multi-Factor Authentication metódusokat és SSPR metódusokat (ha a felhasználó engedélyezve van a SSPR).
- *Multi-Factor Authentication a regisztrációt a feltételes hozzáférés vagy más szabályzatok segítségével érvényesítjük:* A rendszer akkor kéri a felhasználókat, hogy regisztráljanak, ha Multi-Factor Authenticationt igénylő erőforrást használnak. Regisztrálják Multi-Factor Authentication metódusokat és SSPR metódusokat (ha a felhasználó engedélyezve van a SSPR).
- *SSPR-regisztráció érvénybe léptetése:* A rendszer megkéri a felhasználókat, hogy regisztráljanak a bejelentkezés során. Csak SSPR metódusokat regisztráljanak.
- *SSPR-frissítés kényszerítve:* A felhasználóknak a biztonsági adataikat a rendszergazda által beállított időközönként kell áttekinteniük. A felhasználók megtekinthetik az adataikat, és szükség esetén ellenőrizhetik a jelenlegi adatokat, vagy módosíthatják azokat.

A regisztráció érvénybe léptetése esetén a felhasználók a lehető legkevesebb módszert mutatják be a Multi-Factor Authentication és a SSPR szabályzatoknak való megfeleléshez.

Vegye figyelembe a következő példát:

- A SSPR engedélyezve van egy felhasználó. A SSPR házirendben két módszer szükséges az alaphelyzetbe állításhoz, és engedélyezte a Mobile App Code, az e-mail és a Phone használatát.
- Ez a felhasználó két módszer regisztrálásához szükséges.
   - A felhasználó alapértelmezés szerint a hitelesítő alkalmazás és a telefon beállítást jeleníti meg.
   - A felhasználó megadhatja, hogy a hitelesítő alkalmazás vagy a telefon helyett e-maileket regisztráljon.

A következő folyamatábra azt ismerteti, hogy mely metódusok jelennek meg a felhasználó számára a bejelentkezés során megszakított regisztráció során:

![Kombinált biztonsági adatok folyamatábrája](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Ha a Multi-Factor Authentication és a SSPR is engedélyezve van, javasoljuk, hogy érvényesítse Multi-Factor Authentication regisztrációját.

Ha a SSPR-szabályzat megköveteli, hogy a felhasználók rendszeres időközönként vizsgálják felül a biztonsági adataikat, a rendszer a bejelentkezés során megszakítja a felhasználókat, és megjeleníti az összes regisztrált módszerét. Megerősítik az aktuális információt, ha naprakészek, vagy ha szükséges, módosításokat végezhetnek. Ezen oldal elérésekor a felhasználóknak többtényezős hitelesítést kell végezniük.

### <a name="manage-mode"></a>Kezelés módja

A felhasználók a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) fiók **biztonsági adatainak** kiválasztásával érhetik el a kezelési módot. A felhasználók hozzáadhatnak metódusokat, törölhetik vagy módosíthatják a meglévő metódusokat, módosíthatják az alapértelmezett metódust és egyebeket.

## <a name="key-usage-scenarios"></a>Kulcshasználat forgatókönyvek

### <a name="set-up-security-info-during-sign-in"></a>Biztonsági adatok beállítása a bejelentkezés során

A rendszergazda kényszerített regisztrációt.

Egy felhasználó nem állította be az összes szükséges biztonsági adatot, és a Azure Portalra kerül. A Felhasználónév és a jelszó megadása után a rendszer felszólítja a felhasználót a biztonsági adatok beállítására. A felhasználó ezután a varázslóban látható lépéseket követve beállíthatja a szükséges biztonsági adatokat. Ha a beállítások lehetővé teszik, a felhasználó dönthet úgy, hogy az alapértelmezés szerint nem látható módszereket is beállít. A varázsló befejezése után a felhasználók áttekintik az általuk beállított metódusokat és a Multi-Factor Authentication alapértelmezett módszerét. A telepítési folyamat befejezéséhez a felhasználó megerősíti az adatokat, és folytatja a Azure Portal.

### <a name="set-up-security-info-from-my-account"></a>Biztonsági adatok beállítása a fiókomból

Egy rendszergazda nem kényszerített regisztrációt.

Egy olyan felhasználó, aki még nem állította be az összes szükséges biztonsági adatot, a következőre lép: [https://myaccount.microsoft.com](https://myaccount.microsoft.com) . A felhasználó a bal oldali panelen kiválasztja a **biztonsági adatokat** . Onnan a felhasználó úgy dönt, hogy hozzáad egy metódust, kiválasztja az elérhető módszerek bármelyikét, és az adott metódus beállításának lépéseit követi. Ha elkészült, a felhasználó látja a biztonsági adatok lapon beállított metódust.

### <a name="delete-security-info-from-my-account"></a>Biztonsági adatok törlése a fiókomból

Az a felhasználó, aki korábban beállított legalább egy metódust, navigál a következőhöz: [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . A felhasználó úgy dönt, hogy törli a korábban regisztrált módszerek egyikét. Ha elkészült, a felhasználó már nem látja ezt a metódust a biztonsági adatok lapon.

### <a name="change-the-default-method-from-my-account"></a>A fiókom alapértelmezett módszerének módosítása

Az a felhasználó, aki korábban beállított legalább egy olyan metódust, amelyet Multi-Factor Authentication használhat a alkalmazásban [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . A felhasználó az aktuális alapértelmezett metódust egy másik alapértelmezett metódusra módosítja. Ha elkészült, a felhasználó az új alapértelmezett metódust látja a biztonsági adatok lapon.

## <a name="next-steps"></a>Következő lépések

Első lépésként tekintse meg az oktatóanyagokat az [önkiszolgáló jelszó-visszaállítás engedélyezéséhez](tutorial-enable-sspr.md) és az [Azure ad-multi-Factor Authentication engedélyezéséhez](tutorial-enable-azure-mfa.md).

Megtudhatja, hogyan [engedélyezheti a kombinált regisztrációt a bérlőben](howto-registration-mfa-sspr-combined.md) , vagy [kényszerítheti a felhasználókat a hitelesítési módszerek ismételt regisztrálására](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Áttekintheti az [Azure AD multi-Factor Authentication és a SSPR elérhető metódusait](concept-authentication-methods.md)is.
