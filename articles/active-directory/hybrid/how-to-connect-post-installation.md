---
title: 'Azure AD Connect: további lépések és a Azure AD Connect kezelése | Microsoft Docs'
description: Megtudhatja, hogyan bővítheti Azure AD Connect alapértelmezett konfigurációját és működési feladatait.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d6689b4b67b8462e983ae9b111e0fbc60c422b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657110"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>További lépések és a Azure AD Connect kezelése
A jelen cikkben ismertetett működési eljárások segítségével testreszabhatja Azure Active Directory (Azure AD) kapcsolódását a szervezet igényeinek és követelményeinek megfelelően.  

## <a name="add-additional-sync-admins"></a>További szinkronizálási rendszergazdák hozzáadása
Alapértelmezés szerint csak az a felhasználó, aki a telepítést és a helyi rendszergazdákat is képes kezelni a telepített szinkronizáló motort. Ahhoz, hogy a felhasználók hozzáférjenek és felügyelhetők legyenek a Szinkronizáló motorban, keresse meg a ADSyncAdmins nevű csoportot a helyi kiszolgálón, és adja hozzá őket ehhez a csoporthoz.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Licencek kiosztása prémium szintű Azure AD és nagyvállalati mobilitási csomag felhasználói számára
Most, hogy a felhasználók már szinkronizálva lettek a felhővel, hozzá kell rendelnie a licenceket, hogy azok a felhőalapú alkalmazások, például a Microsoft 365 számára is elérhetők legyenek.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>prémium szintű Azure AD vagy nagyvállalati mobilitási csomag licencének kiosztása

1. Jelentkezzen be a Azure Portalba rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. A **Active Directory** lapon kattintson duplán arra a könyvtárra, amelyen be szeretné állítani a felhasználókat.
4. A könyvtárlap tetején válassza a **Licencek** elemet.
5. A **licencek** lapon válassza a **Active Directory Premium** vagy a **nagyvállalati mobilitási csomag** lehetőséget, majd kattintson a **hozzárendelés** elemre.
6. A párbeszédpanelen válassza ki azokat a felhasználókat, akikhez licenceket szeretne rendelni, majd kattintson a pipa ikonra a módosítások mentéséhez.

## <a name="verify-the-scheduled-synchronization-task"></a>Az ütemezett szinkronizálási feladat ellenőrzése
A szinkronizálás állapotának vizsgálatához használja a Azure Portal.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Az ütemezett szinkronizálási feladat ellenőrzése
1. Jelentkezzen be a Azure Portalba rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. A bal oldalon válassza a **Azure ad Connect**
4. A lap tetején jegyezze fel a legutóbbi szinkronizálást.

![Címtár-szinkronizálás ideje](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Ütemezett szinkronizálási feladat elindítása
Ha szinkronizálási feladatot kell futtatnia, a következőket teheti:

1. A varázsló elindításához kattintson duplán a Azure AD Connect asztali parancsikonra.
2. Kattintson a **Configure** (Konfigurálás) elemre.
3. A feladatok képernyőn válassza a **szinkronizálási beállítások testreszabása lehetőséget** , majd kattintson a **tovább** gombra.
4. Adja meg Azure AD hitelesítő adatait
5. Kattintson a **Tovább** gombra. Kattintson a **Tovább** gombra.  Kattintson a **Tovább** gombra.
5.  A **konfigurálásra kész** képernyőn győződjön meg arról, hogy be van jelölve a **szinkronizálási folyamat elindítása a konfiguráció befejeződése után** jelölőnégyzet.
6.  Kattintson a **Configure** (Konfigurálás) elemre.

További információ a Azure AD Connect Sync Schedulerről: [Azure ad Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>A Azure AD Connectban elérhető további feladatok
A Azure AD Connect első telepítése után a varázslót bármikor elindíthatja a Azure AD Connect Kezdőlap vagy az asztal parancsikonjának használatával.  Megfigyelheti, hogy a varázsló újbóli megadásával további feladatok formájában új lehetőségek is rendelkezésre állnak.  

Az alábbi táblázat a feladatok összegzését és az egyes feladatok rövid leírását tartalmazza.

![További feladatok listája](./media/how-to-connect-post-installation/addtasks2.png)

| További feladat | Leírás |
| --- | --- |
|**Adatvédelmi beállítások**|Megtekintheti, hogy milyen telemetria-adatmegosztások vannak a Microsofttal.|
|**Aktuális konfiguráció megtekintése**|Tekintse meg az aktuális Azure AD Connect megoldást.  Ide tartozik az általános beállítások, a szinkronizált könyvtárak és a szinkronizálási beállítások. |
| **Szinkronizálási beállítások testreszabása** |Módosítsa az aktuális konfigurációt, például további Active Directory erdők hozzáadását a konfigurációhoz, vagy engedélyezze a szinkronizálási beállításokat, például a felhasználó, csoport, eszköz vagy jelszó visszaírását. |
|**Eszközbeállítások konfigurálása**|Szinkronizáláshoz elérhető eszközbeállítások|
|**Címtár-séma frissítése**|Lehetővé teszi új helyszíni címtárobjektumok hozzáadását a szinkronizáláshoz|
|**Átmeneti üzemmód konfigurálása** |Nem azonnal szinkronizált és nem az Azure AD-ba vagy a helyszíni Active Directoryba exportált fázis-információk.  Ezzel a funkcióval előzetesen megtekintheti a szinkronizálásokat, mielőtt azok bejelentkeznek. |
|**Felhasználói bejelentkezés módosítása**|A felhasználó által a bejelentkezéshez használt hitelesítési módszer módosítása|
|**Összevonás kezelése**|AD FS-infrastruktúra kezelése, tanúsítványok megújítása és AD FS-kiszolgálók hozzáadása|
|**Hibaelhárítás**|Súgó a Azure AD Connect problémák elhárításához|

## <a name="next-steps"></a>Következő lépések
További információ a helyszíni [identitások és a Azure Active Directory integrálásáról](whatis-hybrid-identity.md).
