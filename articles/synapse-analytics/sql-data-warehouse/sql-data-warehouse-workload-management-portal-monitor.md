---
title: Munkaterhelés-kezelési portál figyelése
description: Útmutató a munkaterhelés-kezelési portál monitorozásához az Azure szinapszis Analyticsben.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/01/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 702cc1fc8b135b2eb4af9106a81946873918c4fa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694393"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure szinapszis Analytics – munkaterhelés felügyeleti portál monitorozás

Ez a cikk azt ismerteti, hogyan lehet figyelni a [munkaterhelés-csoport](sql-data-warehouse-workload-isolation.md#workload-groups) erőforrásainak kihasználtságát és lekérdezési tevékenységeit.
Az Azure-Metrikaböngésző konfigurálásával kapcsolatos további információkért tekintse meg az [azure Metrikaböngésző első lépései](../../azure-monitor/essentials/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) című cikket.  A rendszererőforrás-használat figyelésével kapcsolatos részletekért tekintse meg az Azure szinapszis Analytics figyelési dokumentációjának [erőforrás-felhasználás](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) szakaszát.
A munkaterhelés-kezelés figyeléséhez két különböző kategóriájú munkaterhelés-csoport metrikája van megadva: erőforrás-elosztási és lekérdezési tevékenység.  Ezek a metrikák a munkaterhelés csoport alapján bonthatók és szűrhetők.  A metrikák az alapján bonthatók és szűrhetők, ha a rendszer definiálva van (erőforrás-osztály munkaterhelési csoportok) vagy felhasználó által definiált (a [munkaterhelés-csoport létrehozása](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) szintaxissal létrehozott felhasználó által létrehozva).

## <a name="workload-management-metric-definitions"></a>Munkaterhelés-kezelési metrika definíciói

|Metrika neve                    |Leírás  |Aggregáció típusa |
|-------------------------------|-------------|-----------------|
|Érvényes Cap-erőforrás százaléka | A *tényleges Cap-erőforrás százalékos* értéke a munkaterhelés-csoport által elérhető erőforrások százalékos arányának korlátozása, amely az egyéb munkaterhelés-csoportok számára kiosztott *tényleges minimális erőforrás-százalékos arányt* veszi figyelembe. A *tényleges Cap-erőforrás százalékos* mérőszáma a `CAP_PERCENTAGE_RESOURCE` [munkaterhelés-csoport létrehozása](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) szintaxisban található paraméterrel konfigurálható.  A hatályos érték itt van leírva.<br><br>Ha például egy munkaterhelés-csoport a `DataLoads` `CAP_PERCENTAGE_RESOURCE` = 100-as és egy másik munkaterhelés-csoporttal jön létre, és a tényleges minimális erőforrás-százalékos érték 25%, akkor a munkaterhelés-csoport *tényleges maximális erőforrás* -százalékos `DataLoads` értéke 75%.<br><br>A *tényleges Cap-erőforrás százaléka* határozza meg a munkaterhelés (és így lehetséges átviteli sebesség) felső határát.  Ha további átviteli sebességre van szükség a jelenlegi *Cap-erőforrás százalékos* mérőszáma által aktuálisan jelentettnél, növelje a t `CAP_PERCENTAGE_RESOURCE` , csökkentse a `MIN_PERCENTAGE_RESOURCE` többi munkaterhelés-csoportot, vagy bővítse a példányt további erőforrások hozzáadásával.  A csökkentheti a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` párhuzamosságot, de előfordulhat, hogy a teljes átviteli sebesség nem növekszik.| Minimum, átlag, maximum |
|Effektív minimális erőforrás százaléka |A *tényleges minimális erőforrás-százalékos* érték a munkaterhelés csoport számára fenntartott és elkülönített erőforrások minimális százalékaránya, amely a szolgáltatási szint minimumát veszi figyelembe.  A tényleges minimális erőforrás-százalékos metrika a `MIN_PERCENTAGE_RESOURCE` [MUNKATERHELÉS-csoport létrehozása](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) szintaxisban megadott paraméterrel konfigurálható.  A hatályos érték [itt](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)van leírva.<br><br>A Sum aggregációs típust akkor használja, ha ez a metrika nem szűrt, és nincs szétosztva a rendszeren konfigurált teljes munkaterhelés-elkülönítés figyeléséhez.<br><br>A *tényleges minimális erőforrás-százalékos* érték határozza meg, hogy a munkaterhelés-csoport a garantált Egyidejűség (és így garantált átviteli sebesség) alsó határát is elérheti.  Ha további garantált erőforrásokra van szükség a *tényleges minimális erőforrás-százalékos* metrika által jelenleg jelentettnél, növelje a `MIN_PERCENTAGE_RESOURCE` munkaterhelés-csoporthoz konfigurált paramétert.  A csökkentheti a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` párhuzamosságot, de előfordulhat, hogy a teljes átviteli sebesség nem növekszik. |Minimum, átlag, maximum|
|Munkaterhelési csoport aktív lekérdezései  |Ez a metrika a munkaterhelés csoporton belüli aktív lekérdezéseket jelenti.  Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|Sum         |
|Munkaterhelés-csoport lefoglalása a Cap-erőforrás százaléka alapján |Ez a metrika az erőforrások százalékos kiosztását jeleníti meg a munkaterhelési csoport *tényleges maximális erőforrásához* viszonyítva.  Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja.<br><br>Vegyünk fel egy munkaterhelési csoportot a `DataLoads` 75%-os *effektív korlát-erőforrás százalékával* , és egy `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 25%-os konfigurált értékkel.  A *munkaterhelési csoport kiosztása a Cap-erőforrás százalékos* értékének alapján szűrve: `DataLoads` 33% (25%/75%) Ha egyetlen lekérdezés futott ebben a munkaterhelés-csoportban.<br><br>Ezzel a metrikával azonosíthatók a munkaterhelés-csoportok kihasználtsága.  A 100%-ra közelítő érték azt jelzi, hogy a munkaterhelés-csoport számára elérhető összes erőforrás használatban van.  Emellett a *munkaterhelés-csoport várólistán lévő lekérdezési metrikája* ugyanarra a munkaterhelés-csoportra vonatkozóan, amely a nullánál nagyobb értéket mutat, azt jelzi, hogy a munkaterhelés-csoport további erőforrásokat fog használni, ha lefoglalt.  Ezzel szemben, ha ez a metrika konzisztensen alacsony, és a *munkaterhelési csoport aktív lekérdezései* alacsonyak, a munkaterhelés-csoport nincs használatban.  Ez a helyzet különösen akkor hasznos, ha az effektív határérték- *erőforrás százaléka* meghaladja a nullánál nagyobb értéket, mivel ez a számítási [feladatok elkülönítését](#underutilized-workload-isolation)jelzi.|Minimum, átlag, maximum |
|Munkaterhelés-csoport kiosztása rendszerszázalékkal | Ez a metrika az erőforrások százalékos kiosztását mutatja a teljes rendszerrel viszonyítva.<br><br>Vegyünk fel egy munkaterhelési csoportot `DataLoads` , amelynek a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` beállítása 25%.  A *munkaterhelési csoport kiosztása* a rendszerértékek szerinti kiszűrve értéke `DataLoads` 25% lenne (25%/100%) Ha egyetlen lekérdezés futott ebben a munkaterhelés-csoportban.|Minimum, átlag, maximum |
|Munkaterhelés-csoport lekérdezési időtúllépései |Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit.  A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozások miatt).<br><br>A lekérdezés időtúllépése a `QUERY_EXECUTION_TIMEOUT_SEC` [MUNKATERHELÉS-csoport létrehozása](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) szintaxisban található paraméterrel konfigurálható.  Az érték növelésével csökkentheti a lekérdezési időtúllépések számát.<br><br>Vegye fontolóra a számítási `REQUEST_MIN_RESOURCE_GRANT_PERCENT` feladatok csoportjának paraméterének növelését, hogy csökkentse az időtúllépések mennyiségét, és több erőforrást foglaljon le lekérdezésként.  Vegye figyelembe, hogy `REQUEST_MIN_RESOURCE_GRANT_PERCENT` a növelés csökkenti a munkaterhelés-csoport egyidejűségének mennyiségét. |Sum |
|Munkaterhelési csoport várólistán lévő lekérdezések | Azon munkaterhelés-csoport lekérdezései, amelyek várólistára várnak a végrehajtás megkezdésére.  A lekérdezések várólistára helyezhetők, mert az erőforrásokra vagy zárolásokra várnak.<br><br>A lekérdezések több okból is várnak.  Ha a rendszer túl van terhelve, és az egyidejűségi igény nagyobb, mint ami elérhető, a lekérdezések várólistára kerülnek.<br><br>Vegye fontolóra további erőforrások hozzáadását a munkaterhelés-csoporthoz azáltal, hogy növeli a `CAP_PERCENTAGE_RESOURCE` paramétert a [MUNKATERHELÉS-csoport létrehozása](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasításban.  Ha a `CAP_PERCENTAGE_RESOURCE` érték nagyobb, mint a *tényleges Cap-erőforrás százalékos* mérőszáma, akkor a számítási feladatok elkülönítése más munkaterhelés-csoport számára is hatással van a munkaterhelési csoport számára lefoglalt erőforrásokra.  `MIN_PERCENTAGE_RESOURCE`További erőforrások hozzáadásához vegye fontolóra a más munkaterhelés-csoportok csökkentését vagy a példány vertikális felskálázását. |Sum |

## <a name="monitoring-scenarios-and-actions"></a>Figyelési forgatókönyvek és műveletek

Az alábbiakban számos diagram-konfigurációt talál, amelyekkel kiemelheti a számítási feladatok felügyeleti metrikájának használatát a hibaelhárításhoz és a probléma megoldásához szükséges kapcsolódó műveletekhez.

### <a name="underutilized-workload-isolation"></a>Kihasználatlan számítási feladatok elkülönítése

Vegye figyelembe a következő munkaterhelési csoportot és az osztályozó konfigurációt, ahol létrejön egy nevű munkaterhelési csoport, `wgPriority` és a *TheCEO* `membername` a munkaterhelés-osztályozó használatával van leképezve `wcCEOPriority` .  A `wgPriority` munkaterhelési csoport számára 25%-os munkaterhelés-elkülönítés van konfigurálva ( `MIN_PERCENTAGE_RESOURCE` = 25).  A *TheCEO* által küldött összes lekérdezés a rendszererőforrások 5%-át ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5) adja meg.

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Az alábbi diagram a következőképpen van konfigurálva:<br>
Metrika 1: *effektív minimális erőforrás-százalék* (AVG összesítés, `blue line` )<br>
Metrika 2: *munkaterhelési csoport kiosztása rendszerszázalékkal* (AVG összesítés, `purple line` )<br>
Filter: [munkaterhelés-csoport] = `wgPriority`<br>
![A képernyőképen egy diagram látható a két metrikával és szűrővel.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
A diagram azt mutatja, hogy a munkaterhelések 25%-os elkülönítése átlagosan csak 10%-ot használ.  Ebben az esetben a `MIN_PERCENTAGE_RESOURCE` paraméter értéke 10 vagy 15 közötti értékre csökkenthető, és lehetővé teszi, hogy a rendszer más számítási feladatait használja az erőforrások felhasználásához.

### <a name="workload-group-bottleneck"></a>Munkaterhelés-csoport szűk keresztmetszete

Vegye figyelembe a következő munkaterhelés-csoportot és az osztályozó konfigurációt, ahol létrejön egy nevű munkaterhelési csoport, `wgDataAnalyst` és a *DataAnalyst* `membername` a munkaterhelés-osztályozó használatával van leképezve `wcDataAnalyst` .  A `wgDataAnalyst` munkaterhelés-csoport 6%-os munkaterhelés-elkülönítést ( `MIN_PERCENTAGE_RESOURCE` = 6) és egy 9%-os erőforrás-korlátot ( `CAP_PERCENTAGE_RESOURCE` = 9) tartalmaz.  A *DataAnalyst* által küldött összes lekérdezés a rendszererőforrások 3%-át ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3) adja meg.

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Az alábbi diagram a következőképpen van konfigurálva:<br>
1. metrika: *effektív Cap-erőforrás százaléka* (AVG összesítés, `blue line` )<br>
2. metrika: *munkaterhelési csoport lefoglalása a Cap erőforrás százalékában* (AVG összesítés, `purple line` )<br>
3. metrika: *munkaterhelési csoport várólistán lévő lekérdezések* (Sum összesítés, `turquoise line` )<br>
Filter: [munkaterhelés-csoport] = `wgDataAnalyst`<br>
![A képernyőfelvételen egy diagram látható a három metrikával és szűrővel.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
A diagram azt mutatja, hogy az erőforrásokra vonatkozó 9%-os korláttal a munkaterhelési csoport 90% + használatban van (a *munkaterhelési csoport kiosztása a Cap-erőforrás százalékos mérőszáma alapján*).  A lekérdezések állandó sorba állítása a *munkaterhelés-csoport várólistán lévő lekérdezések metrikájában* látható.  Ebben az esetben a `CAP_PERCENTAGE_RESOURCE` (z) 9%-nál magasabb értékre való növelése lehetővé teszi, hogy a több lekérdezés egyidejűleg legyen végrehajtva.  Az `CAP_PERCENTAGE_RESOURCE` azt feltételezi, hogy elegendő erőforrás áll rendelkezésre, és más munkaterhelés-csoportok nem elkülönítettek.  Győződjön meg róla, hogy a sapka megnőtt a *tényleges Cap-erőforrás százalékos metrikájának* ellenőrzésével.  Ha további átviteli sebességre van szükség, vegye fontolóra a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` értékét 3-nál nagyobb értékre.  Az `REQUEST_MIN_RESOURCE_GRANT_PERCENT` lehetővé teszi, hogy a lekérdezések gyorsabban fussanak.

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása T-SQL használatával](quickstart-configure-workload-isolation-tsql.md)<br>
- [MUNKATERHELÉS-csoport létrehozása (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Számítási feladatok BESOROLÁSának létrehozása (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Erőforrás-használat figyelése](sql-data-warehouse-concept-resource-utilization-query-activity.md)
