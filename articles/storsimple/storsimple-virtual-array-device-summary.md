---
title: StorSimple virtuális tömb eszközének összegzése panel | Microsoft Docs
description: A StorSimple Eszközkezelő eszköz összefoglalási paneljét ismerteti, és ismerteti, hogyan használható a StorSimple virtuális tömb állapotának figyelésére.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005877"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz csatlakoztatott StorSimple-Eszközkezelő eszköz összefoglalási paneljének használata

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő-eszköz panel egy adott StorSimple-Eszközkezelő regisztrált StorSimple virtuális tömb összegző nézetét jeleníti meg, kiemelve azokat az eszközöket, amelyeknek rendszergazdai beavatkozásra van szükségük. Ez az oktatóanyag bemutatja az eszköz összegzése panelt, ismerteti a tartalmat és a függvényt, és leírja a panelen végrehajtható feladatokat.

Az eszköz összegzése panel a következő információkat jeleníti meg:

![Az eszköz irányítópultja](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Kezelés

A StorSimple-eszköz panelen láthatja a StorSimple-eszköz felügyeletének beállításait. A panel tetején és a bal oldalon láthatók a felügyeleti parancsok. Ezeket a beállításokat megosztások vagy kötetek hozzáadására, illetve a virtuális tömb frissítésére vagy feladatátvételére használhatja.

Az Essentials terület a fontos tulajdonságok némelyikét rögzíti, például az állapotot, a modellt, a szoftvert, valamint a tömb **webes felületére** mutató hivatkozást. Ha belső hálózatot használ, a [helyi webes felhasználói felületet](storsimple-ova-web-ui-admin.md) közvetlenül is elindíthatja a virtuális tömb felügyeletéhez.

![Eszköz Essentials](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple-eszköz összegzése

* A **riasztások** csempe tartalmazza a virtuális tömb összes aktív riasztását, a riasztás súlyossága szerint csoportosítva. A csempére kattintva nyissa meg a **riasztások** panelt, majd kattintson egy egyéni riasztásra, hogy megtekintse a riasztás további részleteit, beleértve az ajánlott műveleteket is. Ha a probléma megoldódott, törölheti is a riasztást.

* A **kapacitás** csempén látható az elsődleges tárterület, amelyet a rendszer a virtuális eszközön a rendelkezésre álló teljes tárterülethez viszonyítva kiépít és megmarad. A **kiépített** mennyiség a használatra előkészített és lefoglalt tárterületre utal, és a **fennmaradó** kapacitás az eszközön kiépíthető fennmaradó kapacitásra utal. A **fennmaradó többszintű** kapacitás a rendelkezésre álló kapacitás, amely tartalmazza a felhőt is, míg a **fennmaradó helyi** érték az ehhez a virtuális tömbhöz csatolt lemezeken fennmaradó kapacitás.

* A **használati** diagramon megtekintheti a virtuális tömbben használt elsődleges tárterületet, valamint az elmúlt 7 napban felhasznált Felhőbeli tárterületet, az alapértelmezett időtartamot. A diagram jobb felső sarkában található **Szerkesztés** lehetőséggel különböző időskálát választhat.

* A **megosztások** vagy **kötetek** csempe az eszközön az állapot szerint csoportosított megosztások vagy kötetek számának összegzését tartalmazza. A csempére kattintva nyissa meg a **megosztások**  vagy **kötetek** listája panelt, majd kattintson egy egyéni megosztásra vagy kötetre a tulajdonságainak megtekintéséhez vagy módosításához. További információ: a [megosztások kezelése](storsimple-virtual-array-manage-shares.md) vagy a [kötetek kezelése](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Következő lépések
Az alábbiak végrehajtásának módját ismerheti meg:
- [Megosztások kezelése StorSimple virtuális tömbben](storsimple-virtual-array-manage-shares.md)
    
- [Kötetek kezelése StorSimple virtuális tömbben](storsimple-virtual-array-manage-volumes.md)

