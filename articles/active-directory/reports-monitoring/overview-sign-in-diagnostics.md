---
title: Mi a Azure Active Directory bejelentkezési diagnosztika?
description: Általános áttekintést nyújt a Azure Active Directory bejelentkezési diagnosztika szolgáltatásáról.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdef3e1f1a60c9eb0c751855837e9cbe77e015e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98572289"
---
# <a name="what-is-the-sign-in-diagnostic-in-azure-ad"></a>Mi a bejelentkezési diagnosztika az Azure AD-ben?

Azure Active Directory (Azure AD) rugalmas biztonsági modellt biztosít, amellyel szabályozható, hogy a felhasználók hogyan végezhetik el a felügyelt erőforrásokat. Az ehhez az erőforrásokhoz való hozzáférést nem csak *azok végzik, hanem azok* elérésével is.  A rugalmas modell általában bizonyos fokú összetettséggel jár, mivel a konfigurációs beállítások száma megtörtént. A bonyolultság a hibák kockázatának növelésére is képes.

Rendszergazdaként olyan megoldásra van szüksége, amely betekintést nyújt a rendszer tevékenységeibe. Ez a láthatóság lehetővé teszi a problémák diagnosztizálását és megoldását. Az Azure AD-hez készült bejelentkezési diagnosztika példa erre a megoldásra. A diagnosztika segítségével elemezheti, hogy mi történt a bejelentkezési kísérlet során, és javaslatokat kaphat a problémák megoldásához anélkül, hogy a Microsoft-támogatást kellene bevonnia.

Ez a cikk áttekintést nyújt a megoldás működéséről, valamint arról, hogyan használhatja azt.

## <a name="requirements"></a>Követelmények

A bejelentkezési diagnosztika az Azure AD minden kiadásában elérhető.

Az Azure AD-ben globális rendszergazdának kell lennie ahhoz, hogy használhassa.

## <a name="how-it-works"></a>Működés

Az Azure AD-ben a bejelentkezési kísérletre adott válasz kötődik, hogy *ki* és *Hogyan* fér hozzá a bérlőhöz. A rendszergazdák például általában a bérlő minden aspektusát konfigurálhatják, amikor bejelentkeznek a vállalati hálózatról. Azonban előfordulhat, hogy az azonos felhasználó blokkolva van, amikor ugyanazzal a fiókkal jelentkezik be egy nem megbízható hálózatról.

Mivel a rendszer nagyobb rugalmasságot biztosít a bejelentkezési kísérletek megválaszolására, előfordulhat, hogy az olyan helyzetekben fejeződik be, ahol a bejelentkezések hibakeresése szükséges. A bejelentkezési diagnosztika szolgáltatás a következőket eredményezi:

- A bejelentkezési eseményekről származó adatok elemzése.

- Megjeleníti, hogy mi történt.

- Ajánlásokat nyújt a problémák megoldásához.

Az Azure AD bejelentkezési diagnosztika célja, hogy lehetővé tegye a bejelentkezési hibák öndiagnosztizálását. A diagnosztikai folyamat elvégzéséhez a következőket kell tennie:

![A bejelentkezési diagnosztika bemutató ábrája.](./media/overview-sign-in-diagnostics/process.png)

1. A bejelentkezési események hatókörének meghatározása.

2. Válassza ki az áttekinteni kívánt bejelentkezést.

3. Tekintse át a diagnosztikai eredményeket.

4. Végezze el a műveletet.

### <a name="define-scope"></a>Hatókör definiálása

Ennek a lépésnek a célja, hogy meghatározza a kivizsgáláshoz szükséges bejelentkezési események hatókörét. A hatókör vagy egy felhasználó vagy egy azonosító (correlationId, kérelemazonosító) és egy időtartomány alapján történik. Ha tovább szeretné szűkíteni a hatókört, megadhatja az alkalmazás nevét. Az Azure AD a hatókör-információkat használja a megfelelő események megkereséséhez.  

### <a name="select-sign-in"></a>Bejelentkezés kiválasztása  

A keresési feltételek alapján az Azure AD lekéri az összes egyező bejelentkezési eseményt, és egy hitelesítési összesítő listanézet-nézetben jeleníti meg azokat.

![Részleges képernyőkép a hitelesítési összefoglalás szakaszról.](./media/overview-sign-in-diagnostics/authentication-summary.png)

A nézetben megjelenített oszlopokat testre szabhatja.

### <a name="review-diagnostic"></a>Diagnosztika áttekintése

A kiválasztott bejelentkezési esemény esetén az Azure AD diagnosztikai eredményeket biztosít.

![A diagnosztikai eredmények szakaszt bemutató részleges képernyőkép.](./media/overview-sign-in-diagnostics/diagnostics-results.png)

Ezek az eredmények értékeléssel kezdődnek, ami elmagyarázza, hogy mi történt néhány mondatban. A magyarázat segít megérteni a rendszerviselkedést.

Ezután összefoglalja a kiválasztott bejelentkezési eseményre alkalmazott feltételes hozzáférési szabályzatok összegzését. A diagnosztikai eredmények a probléma megoldásához ajánlott szervizelési lépéseket is tartalmaznak. Mivel a problémák További segítség nélkül nem mindig oldhatók fel, egy javasolt lépés lehet egy támogatási jegy megnyitása.

### <a name="take-action"></a>Művelet elvégzése

Ezen a ponton meg kell adnia a probléma megoldásához szükséges információkat.

## <a name="scenarios"></a>Forgatókönyvek

A bejelentkezési diagnosztika a következő forgatókönyvekre vonatkozik:

- Feltételes hozzáférés által blokkolva

- Sikertelen feltételes hozzáférés

- Többtényezős hitelesítés (MFA) a feltételes hozzáférésből

- MFA más követelmények alapján

- MFA-ellenőrzés szükséges

- MFA-ellenőrzés szükséges (kockázatos bejelentkezési hely)

- Sikeres bejelentkezés

### <a name="blocked-by-conditional-access"></a>Feltételes hozzáférés által blokkolva

Ebben a forgatókönyvben egy feltételes hozzáférési szabályzat blokkolta a bejelentkezési kísérletet.

![Képernyőfelvétel: hozzáférés-konfiguráció kijelölve a blokkolási hozzáféréssel.](./media/overview-sign-in-diagnostics/block-access.png)

A forgatókönyv diagnosztikai szakasza a felhasználói bejelentkezési esemény és az alkalmazott szabályzatok részleteit jeleníti meg.

### <a name="failed-conditional-access"></a>Sikertelen feltételes hozzáférés

Ez a forgatókönyv általában egy olyan bejelentkezési kísérlet eredménye, amely nem sikerült, mert a feltételes hozzáférési szabályzat követelményei nem teljesültek. Néhány gyakori példa:

![Képernyőfelvétel: hozzáférés-konfiguráció általános házirend-példákkal, és hozzáférés engedélyezése kiválasztva.](./media/overview-sign-in-diagnostics/require-controls.png)

- Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése

- Jóváhagyott ügyfélalkalmazás megkövetelése

- Alkalmazásvédelmi szabályzat megkövetelése

A forgatókönyv diagnosztikai szakasza a felhasználói bejelentkezési kísérlet és az alkalmazott szabályzatok részleteit jeleníti meg.

### <a name="mfa-from-conditional-access"></a>MFA a feltételes hozzáférésből

Ebben az esetben a feltételes hozzáférési szabályzatnak a többtényezős hitelesítési készlettel való bejelentkezésre van szükségük.

![Képernyőfelvétel: a hozzáférés konfigurálása a többtényezős hitelesítés megkövetelése beállítással.](./media/overview-sign-in-diagnostics/require-mfa.png)

A forgatókönyv diagnosztikai szakasza a felhasználói bejelentkezési kísérlet és az alkalmazott szabályzatok részleteit jeleníti meg.

### <a name="mfa-from-other-requirements"></a>MFA más követelmények alapján

Ebben a forgatókönyvben egy feltételes hozzáférési szabályzat nem kényszerített egy többtényezős hitelesítési követelményt. Például többtényezős hitelesítés felhasználónkénti alapon.

![Képernyőfelvétel: többtényezős hitelesítés felhasználónkénti konfiguráció alapján.](./media/overview-sign-in-diagnostics/mfa-per-user.png)

Ennek a diagnosztikai forgatókönyvnek a célja, hogy további részletekkel lássa el a következőket:

- A többtényezős hitelesítés megszakításának forrása
- Az ügyfél interakciójának eredménye

Megtekintheti a felhasználó bejelentkezési kísérletének összes részletét is.

### <a name="mfa-proof-up-required"></a>MFA-ellenőrzés szükséges

Ebben az esetben a bejelentkezési kísérleteket a többtényezős hitelesítés beállítására irányuló kérelmek megszakították. Ennek a beállításnak a használata is ismert.

A többtényezős hitelesítés akkor fordul elő, ha a felhasználónak többtényezős hitelesítést kell használnia, de még nincs konfigurálva, vagy a rendszergazdának be kell állítania a felhasználót a konfigurálásához.

Ennek a diagnosztikai forgatókönyvnek a célja, hogy feltárja, hogy a többtényezős hitelesítés megszakítása a felhasználói konfiguráció hiánya miatt történt. Az ajánlott megoldás a felhasználó számára az ellenőrzés befejezésére szolgál.

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>MFA-ellenőrzés szükséges (kockázatos bejelentkezési hely)

Ebben a forgatókönyvben a bejelentkezési kísérleteket megszakította egy, a kockázatos bejelentkezési hely többtényezős hitelesítésének beállítására irányuló kérelem.

Ennek a diagnosztikai forgatókönyvnek a célja, hogy feltárja, hogy a többtényezős hitelesítés megszakítása a felhasználói konfiguráció hiánya miatt történt. Az ajánlott megoldás az, hogy a felhasználó végrehajtsa a vizsgálatot, különösen olyan hálózati helyről, amely nem kockázatos.

Ha például egy vállalati hálózat elnevezett helyként van meghatározva, a felhasználónak Ehelyett a vállalati hálózatról kell próbálkoznia.

### <a name="successful-sign-in"></a>Sikeres bejelentkezés

Ebben a forgatókönyvben a bejelentkezési eseményeket a feltételes hozzáférés vagy a többtényezős hitelesítés nem szakította meg.

Ez a diagnosztikai forgatókönyv a feltételes hozzáférési házirendek vagy a többtényezős hitelesítés miatt várhatóan megszakított felhasználói bejelentkezési események részleteit tartalmazza.

## <a name="next-steps"></a>Következő lépések

- [Mik azok az Azure Active Directory-jelentések?](overview-reports.md)
- [Mi az Azure Active Directory-figyelés?](overview-monitoring.md)
