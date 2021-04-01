---
title: Eszköz összegzésének használata a StorSimple Eszközkezelő szolgáltatásban
description: Ismerteti a StorSimple Eszközkezelő a szolgáltatás eszközének összegzését, valamint azt, hogy miként használható a tárolási metrikák és a csatlakoztatott kezdeményezők megtekintésére, valamint a sorozatszám és a IQN megkeresésére.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017151"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Az eszköz összegzésének használata a StorSimple Eszközkezelő szolgáltatásban

## <a name="overview"></a>Áttekintés
Az StorSimple-eszköz összegzése panel áttekintést nyújt egy adott StorSimple-eszközről, szemben a szolgáltatás összefoglalási paneljével, amely információt nyújt a Microsoft Azure StorSimple-megoldásban foglalt összes eszközről.

Az eszköz összegzése panel egy összegző nézetet biztosít egy StorSimple 8000 sorozatú eszközről, amely egy adott Eszközkezelő StorSimple van regisztrálva, és kiemeli azokat a problémákat, amelyeknek szükségük van a rendszergazda beavatkozására. Ez az oktatóanyag bemutatja az eszköz összegzése panelt, ismerteti a tartalmat és a függvényt, és leírja a panelen végrehajtható feladatokat.

Az eszköz összegzése panel a következő információkat jeleníti meg:

![Eszköz összegzése panel](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Felügyeleti parancssáv

A StorSimple-eszköz panelen láthatja a StorSimple-eszköz felügyeletének beállításait. A panel tetején és a bal oldalon láthatók a felügyeleti parancsok. Ezeket a beállításokat megosztások vagy kötetek hozzáadására, illetve az eszköz frissítésére vagy feladatátvételére használhatja.

![Felügyeleti parancssáv](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Alapvető erőforrások

Az Essentials terület a fontos tulajdonságok, például az állapot, a modell, a cél IQN és a szoftver verziószámát rögzíti. 

![Eszköz Essentials](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Figyelés

* A **riasztások** csempéje az eszközre vonatkozó összes aktív riasztást tartalmazza, a riasztás súlyossága szerint csoportosítva.

    ![Riasztási csempe](./media/storsimple-8000-device-dashboard/device-summary4.png)

    A csempére kattintva nyissa meg a **riasztások** panelt, majd kattintson egy egyéni riasztásra, hogy megtekintse a riasztás további részleteit, beleértve az ajánlott műveleteket is. Ha a probléma megoldódott, törölheti is a riasztást.

    ![Kattintson a riasztás csempére](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Az **állapot és** az állapot csempe betekintést nyújt az eszköz hardver-összetevő állapotára, beleértve az eszköz állapotát. Előfordulhat, hogy az eszköz offline, online állapotban van, inaktiválva van, vagy készen áll a beállításra.

    ![Állapot és állapot csempe](./media/storsimple-8000-device-dashboard/device-summary5.png)

* A **kötetek** csempe összefoglalja az eszközön az állapot szerint csoportosított kötetek számát.

    ![Kötetek csempe](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kattintson a csempére a **kötetek** listája panel megnyitásához, majd kattintson egy adott kötetre a tulajdonságainak megtekintéséhez vagy módosításához.
    
    ![Kattintson a kötetek csempe](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    További információ: a [kötetek kezelése](storsimple-8000-manage-volumes-u2.md).

* A **használati** diagramon megtekintheti az eszközön használt elsődleges tárterületet, valamint az elmúlt 7 napban felhasznált Felhőbeli tárterületet, az alapértelmezett időszakot.

     ![Használati csempe](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Egy másik időskála kiválasztásához használja a diagram jobb felső sarkában található **Szerkesztés** lehetőséget.

     ![Használati diagram szerkesztése](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Ebben a diagramban megtekintheti a teljes elsődleges tárterület mérőszámait (a gazdagépek által az eszközhöz írt adatok mennyiségét), valamint az eszköz által az adott időszakban felhasznált teljes felhőalapú tárterületet.
  
     Ebben a kontextusban az *elsődleges tárterület* a gazdagép által írt összes adatmennyiségre utal, és a kötet típusa szerint bontható: az *elsődleges többplatformos tárolás* magában foglalja mind a helyileg tárolt, mind a felhőbe irányuló adatfeldolgozást. Az *elsődleges helyileg rögzített tárterület* csak a helyileg tárolt adattárolást foglalja magában. A *felhőalapú tárolás* másfelől a felhőben tárolt adatmennyiség mértékének mérése. Ez a tároló a rétegekből származó adatokat és biztonsági másolatokat tartalmaz. A felhőben tárolt adatmennyiség deduplikált és tömörítve van, míg az elsődleges tároló az deduplikálása és tömörítése előtt felhasznált tárterületet jelzi. (Összehasonlíthatja ezt a két számot a tömörítési sebesség megismeréséhez.) Az elsődleges és a Felhőbeli tárolás esetében a megjelenő összegek a beállított követési gyakoriságon alapulnak. Ha például egy hetes gyakoriságot választ, akkor a diagram az előző hét egyes napjain jeleníti meg az összes adatát.

     Ha meg szeretné tekinteni a Felhőbeli tárhely mennyiségét az idő múlásával, válassza ki a **Felhőbeli tároláshoz használt** lehetőséget. Ha meg szeretné tekinteni a gazdagép által írt összes tárterületet, válassza ki a **felhasznált elsődleges** többhelyes tárolót és az **elsődleges helyileg rögzített tárolási** beállításokat. 
     További információ: [a StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszközök figyelésére](./storsimple-8000-monitor-device.md).


* A **kapacitás** csempe az eszközön az összes rendelkezésre álló tárterülethez képest kiépített elsődleges tárterületet jeleníti meg. A **kiépített** mennyiség a használatra előkészített és lefoglalt tárterületre utal, és a **fennmaradó** kapacitás az eszközön kiépíthető fennmaradó kapacitásra utal. 

    ![Használat csempe 2](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Erre a csempére kattintva megtekintheti, hogyan legyen kiépítve a kapacitás a többszintű és a helyileg rögzített kötetek között. A **fennmaradó többszintű** kapacitás a rendelkezésre álló kapacitás, amelyet a felhőbe lehet kiépíteni, míg a **fennmaradó helyi** érték az eszközhöz csatolt lemezek kapacitása marad.

    ![Kattintson a használati diagram elemre.](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple szolgáltatás összefoglalási](storsimple-8000-service-dashboard.md)paneljéről.
* További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).