---
title: A Service Summary panel használata a StorSimple 8000 Series eszközhöz
description: Ismerteti a StorSimple szolgáltatás összegzése panelt, és ismerteti, hogyan használható a StorSimple-megoldás állapotának figyelésére.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 7b4b697b3d27b57212fc59396e1f8111e297d6cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514941"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>A StorSimple 8000 Series-eszközhöz készült Service Summary panel használata

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás összegzése panel a StorSimple Eszközkezelő szolgáltatáshoz csatlakozó összes eszköz összefoglaló nézetét tartalmazza, és kiemeli azokat az eszközöket, amelyeknek rendszergazdai beavatkozásra van szükségük. Ez az oktatóanyag bemutatja a szolgáltatás összegzése panelt, ismerteti az irányítópult tartalmát és funkcióját, és leírja az ezen a lapon végrehajtható feladatokat.

![Szolgáltatás összegzése](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Felügyeleti parancsok

A StorSimple szolgáltatás összefoglalása panelen megtekintheti a StorSimple Eszközkezelő szolgáltatás és a StorSimple 8000 sorozatú, a szolgáltatáshoz regisztrált eszközök felügyeletének beállításait. A panel tetején és a bal oldalon láthatók a felügyeleti parancsok.

![Parancssáv](./media/storsimple-8000-service-dashboard/service-summary2.png)

Ezekkel a beállításokkal különböző műveleteket végezhet el, például megosztásokat vagy köteteket vehet fel, illetve figyelheti a StorSimple-eszközökön futó különböző feladatokat.


## <a name="essentials"></a>Alapvető erőforrások

Az Essentials terület a fontos tulajdonságok némelyikét rögzíti, például az erőforráscsoportot, a helyet és az előfizetést, amelyben a StorSimple Eszközkezelő létrejött.

![Alapvető erőforrások](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Eszközkezelő szolgáltatás összegzése

* A **riasztások** csempe az összes eszközön lévő összes aktív riasztást megjeleníti, a riasztás súlyossága szerint csoportosítva.

    ![Riasztások csempe](./media/storsimple-8000-service-dashboard/service-summary4.png)

    A csempére kattintva megnyílik a **riasztások** panel, ahol egy adott riasztásra kattintva megtekintheti az adott riasztás további részleteit, beleértve az ajánlott műveleteket is. Ha a probléma megoldódott, törölheti is a riasztást.

    ![Kattintson a riasztások csempére](./media/storsimple-8000-service-dashboard/service-summary8.png)

* A **kapacitás** csempe megjeleníti az elsődleges tárolót, amely az összes eszközön elérhető teljes tárterülethez képest kiépített és megmaradt. A **kiépített** mennyiség a használatra előkészített és lefoglalt tárterületre utal, és a **fennmaradó** kapacitás az összes eszközön kiépíthető fennmaradó kapacitásra utal.

    ![Kapacitás csempe](./media/storsimple-8000-service-dashboard/service-summary6.png)

    A **fennmaradó többszintű** kapacitás a rendelkezésre álló kapacitás, amely a felhőben is kiépíthető, míg a **fennmaradó helyi** érték a StorSimple 8000 Series-eszközökhöz csatolt lemezek kapacitása.


* A **használati** diagramon megtekintheti az eszközök vonatkozó mérőszámait. Megtekintheti az összes eszközön használt elsődleges tárterületet, valamint az eszközök által az elmúlt 7 napban felhasznált Felhőbeli tárterületet, az alapértelmezett időtartamot. 

    ![Használati csempe](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Egy másik időskála kiválasztásához használja a diagram jobb felső sarkában található **Szerkesztés** lehetőséget.

     ![Kattintson a használati csempe](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Diagram adatexportálása](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Az **eszközök** csempe összefoglalja a Eszközkezelő StorSimple StorSimple 8000 sorozatú eszközeinek számát az eszköz állapota szerint csoportosítva. 

    ![Eszközök csempe](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Erre a csempére kattintva nyissa meg az **eszközök** listája panelt, és kattintson egy adott eszközre az eszközre vonatkozó összegzéshez. Az eszközre vonatkozó műveleteket az adott eszköz összefoglaló paneljéről is végrehajthatja. További információ az eszközök összegzése panelről: [eszköz összegzése](storsimple-8000-device-dashboard.md)panel.

    ![Kattintson az eszközök csempére](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>A tevékenység naplóinak megtekintése

Ha meg szeretné tekinteni a StorSimple belül végrehajtott különféle műveleteket Eszközkezelő, kattintson a StorSimple szolgáltatás összefoglaló paneljének bal oldalán található **tevékenység naplók** hivatkozásra. Ekkor megjelenik a tevékenység- **naplók** panel, ahol megtekintheti a legutóbbi végrehajtott műveletek összegzését.

![Tevékenységnaplók](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Következő lépések

* További információ arról, hogyan [használható a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

