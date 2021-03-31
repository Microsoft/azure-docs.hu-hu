---
title: StorSimple Virtual Array Service – összefoglalás panel | Microsoft Docs
description: Ismerteti a StorSimple Eszközkezelő szolgáltatás összefoglalási paneljét, és ismerteti, hogyan használható a StorSimple virtuális tömb állapotának figyelésére.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 31220a8f8b012e08d46195b60a26be9cfc261be5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514528"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz csatlakoztatott StorSimple Eszközkezelő szolgáltatás összefoglalási paneljének használata
## <a name="overview"></a>Áttekintés
A StorSimple Eszközkezelő szolgáltatás összefoglalási panelje a szolgáltatáshoz kapcsolódó StorSimple virtuális tömbök (más néven StorSimple virtuális eszközök vagy virtuális eszközök) összegző nézetét jeleníti meg, és kiemeli azokat, amelyeknek szükségük van a rendszergazdára. Ez az oktatóanyag bemutatja a szolgáltatás összegzése panelt, ismerteti a tartalmat és a függvényt, és leírja a panelen végrehajtható feladatokat.

![Szolgáltatás irányítópultja](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Felügyeleti parancsok és alapvető tudnivalók
A StorSimple összefoglaló paneljén láthatók a StorSimple Eszközkezelő szolgáltatás felügyeletének lehetőségei, valamint a szolgáltatáshoz regisztrált virtuális tömbök. A panel tetején és a bal oldalon láthatók a felügyeleti parancsok.

Ezekkel a beállításokkal különböző műveleteket végezhet el, például megosztásokat vagy köteteket vehet fel, illetve figyelheti a virtuális tömbökön futó különböző feladatokat.

Az Essentials terület a fontos tulajdonságok némelyikét rögzíti, például az erőforráscsoportot, a helyet és az előfizetést, amelyben a StorSimple Eszközkezelő létrejött.

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Eszközkezelő szolgáltatás összegzése
* A **riasztások** csempe az összes virtuális eszközön lévő összes aktív riasztást megjeleníti, a riasztás súlyossága szerint csoportosítva. A csempére kattintva megnyílik a **riasztások** panel, ahol egy adott riasztásra kattintva megtekintheti az adott riasztás további részleteit, beleértve az ajánlott műveleteket is. Ha a probléma megoldódott, törölheti is a riasztást.
* A **kapacitás** csempe megjeleníti az összes virtuális eszközön elérhető teljes tárterülethez képest kiépített elsődleges tárterületet. A **kiépített** tároló a felhasznált és a használatra kiosztott tárterület mennyiségét jelenti, és a **fennmaradó** kapacitás az összes virtuális eszközön kiépíthető fennmaradó kapacitásra utal. A **fennmaradó többszintű** kapacitás a rendelkezésre álló kapacitás, amelyet a felhőbe lehet kiépíteni, míg a **fennmaradó helyi** érték a virtuális tömbökhöz csatolt lemezeken fennmaradó kapacitás.
* A **használati** diagramon láthatja a virtuális eszközökhöz tartozó mérőszámokat. Megtekintheti az összes virtuális eszközön használt elsődleges tárterületet, valamint a virtuális eszközök által az elmúlt 7 napban felhasznált felhőalapú tárterületet, az alapértelmezett időtartamot. A diagram jobb felső sarkában található **Szerkesztés** lehetőséggel különböző időskálát választhat.
* Az **eszközök** csempe a StorSimple található virtuális tömbök számának összegzését jeleníti meg Eszközkezelő eszköz állapota szerint csoportosítva. Erre a csempére kattintva nyissa meg az **eszközök** listája panelt, és kattintson egy adott eszközre az eszközre vonatkozó összegzéshez. Egy adott eszköz összegző paneljén is elvégezheti az eszközre vonatkozó műveleteket. További információ az eszközök összegzése panelről: [eszköz összegzése](storsimple-virtual-array-device-summary.md)panel.

## <a name="view-the-activity-logs"></a>A tevékenység naplóinak megtekintése
Ha meg szeretné tekinteni a StorSimple belül végrehajtott különféle műveleteket Eszközkezelő, kattintson a StorSimple szolgáltatás összefoglaló paneljének bal oldalán található **tevékenység naplók** hivatkozásra. Ekkor megjelenik a tevékenység- **naplók** panel, ahol megtekintheti a legutóbbi végrehajtott műveletek összegzését.

![Tevékenységnaplók](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [használhatja a helyi webes felhasználói felületet a StorSimple virtuális tömb felügyeletére](storsimple-ova-web-ui-admin.md).

