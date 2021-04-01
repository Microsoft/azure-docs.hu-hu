---
title: Kockázati Azure Active Directory Identity Protection vizsgálata
description: Ismerje meg, hogyan vizsgálhatja meg a kockázatos felhasználókat, az észleléseket és a bejelentkezéseket Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: be72c2152bdb8e1155d2dd29547f93ba3605d462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95251093"
---
# <a name="how-to-investigate-risk"></a>Útmutató: Kockázat vizsgálata

Az Identity Protection három jelentéssel rendelkező szervezeteket biztosít, amelyek segítségével megvizsgálhatja az identitás kockázatait a környezetében. Ezek a jelentések a **kockázatos felhasználók**, a **kockázatos bejelentkezések** és a **kockázati észlelések**. Az események vizsgálata kulcsfontosságú a biztonsági stratégiában található gyenge pontok jobb megismerése és azonosítása érdekében.

Mindhárom jelentés lehetővé teszi az események letöltését a alkalmazásban. CSV-formátum a Azure Portalon kívüli további elemzéshez. A kockázatos felhasználók és a kockázatos bejelentkezések jelentései lehetővé teszik a legfrissebb 2500-bejegyzések letöltését, míg a kockázati észlelési jelentés lehetővé teszi a legfrissebb 5000-rekordok letöltését.

A szervezetek kihasználhatják a Microsoft Graph API-integrációját, hogy más forrásokkal összesítsék az adatokat, és ez a szervezet számára is elérhető.

A három jelentés a **Azure Portal**  >  **Azure Active Directory**  >  **biztonsági** szolgáltatásban található.

## <a name="navigating-the-reports"></a>Navigálás a jelentésekben

Minden jelentés a jelentés tetején látható időszakra vonatkozó összes észlelési listával elindul. Minden jelentés lehetővé teszi az oszlopok hozzáadását vagy eltávolítását a rendszergazdai beállítások alapján. A rendszergazdák eldönthetik, hogy letöltik-e az adatfájlokat. CSV vagy. JSON-formátum. A jelentések a jelentés tetején lévő szűrők használatával szűrhetők.

Az egyes bejegyzések kijelölése további bejegyzéseket is engedélyezhet a jelentés tetején, például megerősítheti a bejelentkezést sérült vagy biztonságos módon, megerősítheti a felhasználót, hogy feltörték a felhasználót, vagy utasítsa el a felhasználói kockázatot.

Az egyes bejegyzések kijelölésekor a rendszer kibontja a részletek ablakot az észlelések alatt. A részletek nézet lehetővé teszi, hogy a rendszergazdák megvizsgálják és végrehajtsák az egyes észlelések műveleteit. 

![Példa Identity Protection-jelentésre, amely a kockázatos bejelentkezéseket és a részleteket tartalmazza](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Kockázatos felhasználók

A kockázatos felhasználók jelentés által biztosított információkkal a rendszergazdák a következőket kereshetik:

- Mely felhasználók vannak kitéve kockázatoknak, vagy kockázatos volt a kockázata?
- Az észlelések részletei
- Az összes kockázatos bejelentkezés előzményei
- Kockázati előzmények
 
A rendszergazdák ezután dönthetnek úgy, hogy műveleteket hajtanak végre ezekről az eseményekről. A rendszergazdák a következőket választhatják:

- Felhasználói jelszó alaphelyzetbe állítása
- Felhasználói biztonság megerősítése
- Felhasználói kockázat elvetése
- A felhasználó bejelentkezésének tiltása
- További vizsgálat az Azure ATP használatával

## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

A kockázatos bejelentkezések jelentés az elmúlt 30 nap (1 hónap) alapján szűrt adatértékeket tartalmaz.

A kockázatos bejelentkezési jelentés által biztosított információkkal a rendszergazdák a következőket kereshetik meg:

- A bejelentkezések a kockázat, a megerősítve, a megerősített biztonság, a kihagyott vagy a szervizelés alapján vannak osztályozva.
- A bejelentkezési kísérletekhez kapcsolódó valós idejű és összesített kockázati szintek.
- Aktivált észlelési típusok
- Alkalmazott feltételes hozzáférési szabályzatok
- MFA-részletek
- Eszköz adatai
- Alkalmazásadatok
- Hely adatai

A rendszergazdák ezután dönthetnek úgy, hogy műveleteket hajtanak végre ezekről az eseményekről. A rendszergazdák a következőket választhatják:

- Bejelentkezési kompromisszum megerősítése
- Biztonságos bejelentkezés megerősítése

> [!NOTE] 
> Az Identity Protection kiértékeli az összes hitelesítési folyamat kockázatát, függetlenül attól, hogy az interaktív vagy nem interaktív. A bejelentkezési jelentés azonban csak az interaktív bejelentkezéseket jeleníti meg. Előfordulhat, hogy a nem interaktív bejelentkezésekhez tartozó kockázatos bejelentkezések jelennek meg, de a bejelentkezés nem jelenik meg az Azure AD bejelentkezési jelentéseiben.

## <a name="risk-detections"></a>Kockázatészlelések

A kockázati észlelési jelentés az elmúlt 90 nap (3 hónap) során szűrt adattípusokat tartalmaz.

A kockázati észlelések jelentés által biztosított információkkal a rendszergazdák a következőket kereshetik:

- Az egyes kockázatok észlelésére vonatkozó információk, beleértve a típust.
- Az adott időpontban kiváltott egyéb kockázatok
- Bejelentkezési kísérlet helye
- A Microsoft Cloud App Security (MCAS) részletes adatainak csatolása.

A rendszergazdák ezután dönthetnek úgy, hogy visszatérnek a felhasználó kockázati vagy bejelentkezési jelentéséhez, hogy a gyűjtött információk alapján hajtsák végre a műveleteket.

> [!NOTE] 
> A rendszer észlelheti, hogy a kockázati felhasználói kockázati pontszámhoz hozzájáruló kockázati esemény hamis pozitív, vagy a felhasználói kockázat a házirend-kényszerítéssel lett szervizelve, például MFA-kérdés vagy biztonságos jelszó-módosítás elvégzése. Ezért a rendszer elveti a kockázati állapotot, és a "mesterséges intelligencia által megerősített bejelentkezések" kockázati részleteit felszínre hozza, és a továbbiakban nem járul hozzá a felhasználó kockázatához. 


## <a name="next-steps"></a>Következő lépések

- [A kockázatok enyhítésére rendelkezésre álló szabályzatok](concept-identity-protection-policies.md)

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)
