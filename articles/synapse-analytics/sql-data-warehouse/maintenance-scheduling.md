---
title: A szinapszis SQL-készlet karbantartási Ütemtervei
description: A karbantartási ütemezés lehetővé teszi, hogy az ügyfelek megtervezzék az Azure szinapszis Analytics által az új funkciók, frissítések és javítások elvégzéséhez szükséges ütemezett karbantartási eseményeket.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 886e99d6f56a008c694b3bcb366cfb4049f3528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590213"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Karbantartási ütemtervek használata a szolgáltatások frissítéseinek és karbantartásának kezeléséhez

A karbantartási ütemezési funkció integrálja a Service Health tervezett karbantartási értesítéseket, Resource Health ellenőrzési és karbantartási ütemezési szolgáltatást a szinapszis SQL-készlethez (adattárház) az Azure szinapszis Analyticsen belül.

A karbantartási ütemezés használatával válassza ki az időablakot, amikor az új funkciók, frissítések és javítások fogadására alkalmas. Egy hét napos időszakon belül ki kell választania egy elsődleges és egy másodlagos karbantartási időszakot, minden egyes ablaknak külön napi tartományon belül kell lennie.

Megadhatja például, hogy a 22:00-es és a Sunday 01:00-es elsődleges ablak is beütemezzen, majd egy másodlagos 19:00 ablakot is ütemezzen a 22:00-re. Ha a karbantartás nem hajtható végre az elsődleges karbantartási időszakban, akkor a másodlagos karbantartási időszak alatt újra próbálkozik a karbantartással. A szolgáltatás karbantartása esetenként az elsődleges és a másodlagos Windows rendszerben is előfordulhat. Az összes karbantartási művelet gyors befejezésének biztosítása érdekében a DW400c és az alacsonyabb adattárház-rétegek a kijelölt karbantartási időszakon kívül is elvégezhetik a karbantartást.

Minden újonnan létrehozott adattárház-példányhoz a rendszer által meghatározott karbantartási ütemterv lesz alkalmazva az üzembe helyezés során. Az ütemtervet az üzembe helyezés befejeződése után is szerkesztheti.

Bár a karbantartási időszak három és nyolc óra között lehet, ez nem jelenti azt, hogy az adattárház az időtartam alatt offline állapotba kerül. A karbantartás az adott 5 -6 időszakon belül bármikor elvégezhető, és az adott időszakban egy leválasztást kell várnia, mivel a szolgáltatás új kódot helyez üzembe az adattárházban. A DW400c és az alacsonyabb szint esetében a karbantartási időszak során több rövid veszteség is tapasztalható a kapcsolódáskor. A karbantartás megkezdésekor az összes aktív munkamenet meg lesz szakítva, a nem véglegesített tranzakciók pedig vissza lesznek állítva. A példányok leállásának minimalizálásához győződjön meg arról, hogy az adattárház nem rendelkezik hosszan futó tranzakciókkal a választott karbantartási időszak előtt.

Minden karbantartási műveletnek a megadott karbantartási időszakon belül kell lennie, kivéve, ha szükség van az időérzékeny frissítés üzembe helyezésére. Ha az adattárház egy ütemezett karbantartás során szünetel, a rendszer a folytatási művelet során frissíti. Az adatraktár-karbantartás befejeződése után azonnal értesítést kap.

## <a name="alerts-and-monitoring"></a>Riasztások és figyelés

Service Health értesítésekkel való integráció és a Resource Health-ellenőrzési figyelő lehetővé teszi, hogy az ügyfelek tájékoztassanak a közelgő karbantartási tevékenységekről. Ez az automatizálás kihasználja Azure Monitor. Eldöntheti, hogyan szeretné értesíteni a közelgő karbantartási eseményekről. Emellett kiválaszthatja, hogy mely automatizált folyamatok segítenek az állásidő kezelésében és a működési hatás minimalizálásában.

A 24 órás előzetes értesítés megelőzi a DWC400c és az alacsonyabb rétegekhez nem tartozó karbantartási eseményeket.

> [!NOTE]
> Abban az esetben, ha egy kritikus idejű frissítést kell telepíteni, a speciális értesítési idők jelentősen csökkenthetők. Ez a frissítés kritikus jellege miatt egy azonosított karbantartási időszakon kívül is előfordulhat.

Ha a karbantartás megkezdése előtt értesítést kapott, de a karbantartás nem hajtható végre az értesítésben megadott időszakban, a rendszer lemondási értesítést fog kapni. A karbantartás ekkor folytatódik a következő ütemezett karbantartási időszakban.

Az összes aktív karbantartási esemény megjelenik a **Service Health tervezett karbantartási** szakaszban. A Service Health előzményei a múltbeli események teljes számát tartalmazzák. Az aktív esemény során a Azure Service Health-ellenőrzés portál irányítópultján figyelheti a karbantartást.

### <a name="maintenance-schedule-availability"></a>Karbantartási ütemterv rendelkezésre állása

Még ha a karbantartási ütemezés nem érhető el a kiválasztott régióban, bármikor megtekintheti és szerkesztheti a karbantartási ütemtervet. Ha a karbantartási ütemezés elérhetővé válik a régióban, az azonosított ütemezés azonnal aktív lesz a szinapszis SQL-készletben.

## <a name="view-a-maintenance-schedule"></a>Karbantartási ütemterv megtekintése

Alapértelmezés szerint az összes újonnan létrehozott adattárház-példány 8 órás elsődleges és másodlagos karbantartási időszakot alkalmaz az üzembe helyezés során. A fentiekben leírtak szerint a Windowst a közeljövőben is megváltoztathatja. A megadott karbantartási időszakokon kívül nem fog karbantartás zajlani előzetes értesítés nélkül.

A szinapszis SQL-készletre alkalmazott karbantartási ütemterv megtekintéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a megtekinteni kívánt szinapszis SQL-készletet.
3. Megnyílik a kiválasztott szinapszis SQL-készlet az Áttekintés panelen. Az adatraktárra alkalmazott karbantartási ütemterv a **karbantartási ütemterv** alatt jelenik meg.

![Áttekintés panel](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Karbantartási ütemterv módosítása

A karbantartási ütemtervet bármikor frissítheti vagy módosíthatja. Ha a kiválasztott példány aktív karbantartási cikluson halad át, a rendszer menti a beállításokat. A következő azonosított karbantartási időszak alatt lesznek aktívak. [További](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) információ az adattárház figyeléséről aktív karbantartási esemény során.

## <a name="identifying-the-primary-and-secondary-windows"></a>Az elsődleges és a másodlagos ablakok azonosítása

Az elsődleges és a másodlagos Windowsnak külön napi tartományokkal kell rendelkeznie. Ilyen például a kedd – csütörtök elsődleges ablak és a szombat – vasárnap egy másodlagos ablak.

A szinapszis SQL-készlet karbantartási ütemtervének módosításához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a frissíteni kívánt szinapszis SQL-készletet. Megnyílik az oldal az Áttekintés panelen.
Nyissa meg a karbantartási ütemterv beállításainak lapját a **karbantartási ütemterv összegzése** hivatkozásra kattintva az Áttekintés panelen. Vagy válassza a **karbantartási ütemterv** lehetőséget a bal oldali erőforrás menüjében.

    ![Áttekintő panel beállításai](./media/maintenance-scheduling/maintenance-change-option.png)

3. Azonosítsa az elsődleges karbantartási időszak előnyben részesített napját az oldal tetején található beállítások használatával. Ez a beállítás határozza meg, hogy az elsődleges ablak a hétköznapokon vagy a hétvégén fog történni. A kiválasztott érték frissíti a legördülő listát.
Az előzetes verzió során előfordulhat, hogy egyes régiók még nem támogatják a rendelkezésre álló **napi** beállítások teljes készletét.

   ![Karbantartási beállítások panel](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Válassza ki a kívánt elsődleges és másodlagos karbantartási időszakokat a legördülő lista használatával:
   - **Nap**: az előnyben részesített nap a kiválasztott ablak karbantartásának végrehajtásához.
   - **Kezdési idő**: a karbantartási időszak előnyben részesített kezdési ideje.
   - **Időablak**: az időtartomány előnyben részesített időtartama.

   A panel alján található **ütemezett összefoglalás** terület a kiválasztott értékek alapján frissül.
  
5. Kattintson a **Mentés** gombra. Megjelenik egy üzenet, amely megerősíti, hogy most már aktív az új ütemterv.

   Ha olyan régióban ment egy ütemezést, amely nem támogatja a karbantartási ütemezést, akkor a következő üzenet jelenik meg. A rendszer menti a beállításokat, és aktívvá válik, ha a funkció elérhetővé válik a kiválasztott régióban.

   ![A régió elérhetőségével kapcsolatos üzenet](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Következő lépések

- [További](../../azure-monitor/alerts/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) információ a riasztások létrehozásáról, megtekintéséről és kezeléséről Azure monitor használatával.
- [További](../..//azure-monitor/alerts/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) információ a napló riasztási szabályaival kapcsolatos webhook-műveletekről.
- [További információ](../..//azure-monitor/alerts/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Műveleti csoportok létrehozása és kezelése.
- [További](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) információ a Azure Service Healthról.
