---
title: Szinapszis SQL-erőforrások felhasználása
description: Ismerkedjen meg a szinapszis SQL-felhasználási modelljeivel az Azure szinapszis Analytics szolgáltatásban.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 03144d478be0053ac77709132b08cdf17b062fb0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666299"
---
# <a name="synapse-sql-resource-consumption"></a>Szinapszis SQL-erőforrások felhasználása

Ez a cikk a szinapszis SQL erőforrás-felhasználási modelljeit ismerteti.

## <a name="serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet

A kiszolgáló nélküli SQL-készlet olyan fizetési szolgáltatás, amely nem igényli a megfelelő méret kiválasztását. A rendszer a követelmények alapján automatikusan beállítja az infrastruktúra kezelését és a megoldás megfelelő méretének kiválasztását.

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Dedikált SQL-készlet – adatraktár-egységek (DWU-EK) és számítási adattárház-egységek (cDWUs-EK)

Javaslatok az adatraktár-egységek (DWU) ideális számának kiválasztásához az árak és a teljesítmény optimalizálásához, valamint az egységek számának módosításához.

### <a name="data-warehouse-units"></a>Adattárházegységek

A szinapszis SQL-készlet a kiépített analitikai erőforrások gyűjteményét jelöli. Az analitikai erőforrások a CPU, a memória és az IO kombinációja vannak meghatározva. Ez a három erőforrás az adatraktár-egységek (DWU) számítási skálázási egységei között van. A DWU a számítási erőforrások és teljesítmény absztrakt, normalizált mértéke. A szolgáltatási szint módosítása megváltoztatja a rendszer számára elérhető DWU számát. Ez a változás pedig módosítja a rendszere teljesítményét és költségeit.

A nagyobb teljesítmény érdekében növelheti az adatraktár-egységek számát. Kevesebb teljesítmény esetén csökkentse az adatraktár-egységeket. A tár és a számítási feladatok költségeinek számlázása külön történik, ezért az adattárházegységek számának módosítása nem befolyásolja a tárolási költségeket.

Az adatraktár-egységek teljesítménye a következő adatraktár-számítási feladatok mérőszámán alapul:

- A szabványos adattárház-lekérdezések gyors vizsgálata nagy mennyiségű sort vizsgál, majd összetett összesítést hajt végre. Ez a művelet I/O-és CPU-igényes.
- Az adattárház az Azure Storage-Blobokból vagy Azure Data Lakeokból származó adatok betöltésének gyorsasága. A művelet a hálózati és a CPU-igényes.
- A [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) T-SQL-parancs által a tábla másolásának gyorsasága. A művelet magában foglalja az adatok tárolásból való beolvasását, a berendezés csomópontjain való terjesztését és a tárolóba való írást. Ez a művelet CPU-, IO-és hálózati igényű.

Növekvő DWU:

- A rendszer lineárisan módosítja a vizsgálatok, összesítések és CTAS utasításokhoz tartozó rendszerek teljesítményét
- Növeli az olvasók és az írók számát a kiinduló betöltési műveletekhez
- Növeli az egyidejű lekérdezések és a párhuzamossági bővítőhelyek maximális számát.

### <a name="service-level-objective"></a>Service Level Objective

A szolgáltatási szint célkitűzése (SLO) a méretezhetőségi beállítás, amely meghatározza az adattárház költségeit és teljesítményét. A Gen2 szolgáltatási szintjeit számítási adattárház-egységek (cDWU-EK) mérik, például DW2000c. A Gen1 szolgáltatási szintjei a DWU-ben vannak mérve, például DW2000.

A szolgáltatási szint célkitűzése (SLO) a méretezhetőségi beállítás, amely meghatározza az adattárház költségeit és teljesítményét. A Gen2 dedikált SQL-készlet szolgáltatási szintjeit az adatraktár-egységek (DWU) mérik, például DW2000c.

> [!NOTE]
> Az Azure szinapszis Analytics Gen2 nemrégiben hozzáadott további méretezési képességeket a számítási rétegek támogatásához, ami 100 cDWU. A jelenleg a Gen1-on található meglévő adattárházak, amelyek az alacsonyabb számítási szinteket igénylik, mostantól a Gen2-ra frissíthetik azokat a régiókat, amelyek jelenleg elérhetők a további díjak nélkül.  Ha a régiója még nem támogatott, akkor továbbra is frissíthet egy támogatott régióra. További információ: [verziófrissítés a Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

A T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg a szolgáltatási szintet és a teljesítmény szintjét a dedikált SQL-készlethez.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Teljesítmény szintjei és adattárház-egységek

Minden teljesítményszint némileg eltérő mértékegységet használ az adattárház-egységek számára. Ez a különbség a számlán jelenik meg, mivel a skálázási egység közvetlenül a számlázásra van lefordítva.

- A Gen1 adattárházak mérése adatraktár-egységekben történik (DWU).
- A Gen2 adattárházak mérése számítási adattárház-egységekben történik (cDWUs).

Mind a DWU, mind a cDWUs támogatja a méretezési számítási kapacitást, és szünetelteti a számítást, ha nem kell használnia az adattárházat. Ezek a műveletek mind igény szerint használhatók. A Gen2 egy helyi lemezes gyorsítótárat használ a számítási csomópontokon a teljesítmény javítása érdekében. A rendszer skálázása vagy szüneteltetése után a gyorsítótár érvénytelenné válik, így az optimális teljesítmény elérése előtt szükség van egy gyorsítótár-felmelegedésre.  

Az adatraktár-egységek növelése során lineárisan növelheti a számítástechnikai erőforrásokat. A Gen2 a legjobb lekérdezési teljesítményt és a legmagasabb szintű méretezést biztosítja. A Gen2 rendszerek a gyorsítótár legtöbbjét is használják.

#### <a name="capacity-limits"></a>Kapacitási korlátok

Minden SQL-kiszolgáló (például myserver.database.windows.net) rendelkezik egy [adatbázis-tranzakciós egység (DTU)](../../azure-sql/database/service-tiers-dtu.md) kvótával, amely egy adott számú adattárház-egységet engedélyez. További információ: [munkaterhelés-kezelési kapacitás korlátai](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>A szükséges adatraktár-egységek számának felmérése

Az adatraktár-egységek ideális száma a munkaterheléstől és a rendszerbe betöltött adatok mennyiségétől függ.

A számítási feladatok legjobb DWU megkeresésének lépései:

1. Először válasszon kisebb DWU.
2. Az alkalmazások teljesítményének figyelése az adatterhelések a rendszeren való tesztelésekor, a kiválasztott DWU számának megjelölésével.
3. Azonosítsa a maximális tevékenység időszakos időszakára vonatkozó további követelményeket. Előfordulhat, hogy a jelentős csúcsokat és a tevékenységekben lévő vályúkat bemutató munkaterheléseket gyakran kell méretezni.

Az SQL Pool egy kibővíthető rendszer, amely nagy mennyiségű számítási és lekérdezési mennyiségű adatmennyiséget tud kiépíteni. Ha szeretné megtekinteni a skálázás valódi képességeit, különösen nagyobb DWU esetében, javasoljuk, hogy az adatkészletet méretezéssel méretezheti, hogy elegendő mennyiségű adattal rendelkezzen a processzorok megadásához. A méretezési teszteléshez legalább 1 TB-ot ajánlott használni.

> [!NOTE]
>
> A lekérdezési teljesítmény csak akkor nő további párhuzamos, ha a munka felosztható a számítási csomópontok között. Ha azt tapasztalja, hogy a méretezés nem változik a teljesítményen, előfordulhat, hogy a tábla kialakítását és/vagy a lekérdezéseit is meg kell adnia. A lekérdezések hangolásával kapcsolatos útmutatásért lásd: [felhasználói lekérdezések kezelése](../overview-terminology.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

### <a name="permissions"></a>Engedélyek

Az adatraktár-egységek módosításához az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true)utasításban ismertetett engedélyek szükségesek.

Az Azure beépített szerepkörei, például az SQL-adatbázis közreműködői és SQL Server közreműködők módosíthatják a DWU beállításait.

#### <a name="view-current-dwu-settings"></a>Aktuális DWU-beállítások megtekintése

Az aktuális DWU-beállítás megtekintése:

1. Nyissa meg SQL Server Object Explorer a Visual Studióban.
2. Kapcsolódjon a logikai SQL-kiszolgálóhoz társított Master adatbázishoz.
3. Válasszon a sys.database_service_objectives dinamikus felügyeleti nézetből. Alább bemutatunk egy példát:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Adatraktár-egységek módosítása

#### <a name="azure-portal"></a>Azure Portal

DWU módosítása:

1. Nyissa meg a [Azure Portal](https://portal.azure.com), nyissa meg az adatbázist, és válassza a **skála** lehetőséget.

2. A **skála** alatt mozgassa a csúszkát balra vagy jobbra a DWU beállítás módosításához.

3. Kattintson a **Mentés** gombra. Ekkor megjelenik egy megerősítő üzenet. Válassza az **Igen** lehetőséget a megerősítéshez **, vagy a Mégse gombra.**

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A DWU módosításához használja a [set-AzSqlDatabase PowerShell-](/powershell/module/az.sql/set-azsqldatabase) parancsmagot. A következő példa beállítja a szolgáltatási szint célkitűzését, hogy DW1000 a Server MyServer üzemeltetett adatbázis-MySQLDW.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

További információ: PowerShell- [parancsmagok az Azure szinapszis analyticshez](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

A T-SQL használatával megtekintheti az aktuális DWUsettings, módosíthatja a beállításokat, és megtekintheti a folyamatot.

A DWU módosítása:

1. Kapcsolódjon a kiszolgálóhoz társított Master adatbázishoz.
2. Használja az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) TSQL utasítást. A következő példa a szolgáltatási szint célkitűzését állítja be a DW1000c adatbázis-MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST API-k

A DWU módosításához használja az [adatbázis létrehozása vagy frissítése](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) REST API. A következő példa a szolgáltatási szint célkitűzését állítja be a DW1000c adatbázis-MySQLDW, amely a kiszolgáló MyServer található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

További REST API Példákért lásd: [REST API-k az Azure szinapszis analyticshez](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>DWU-változások állapotának ellenõrzése

A DWU módosításai több percet is igénybe vehetnek. Ha automatikusan végzi a méretezést, érdemes megfontolnia a logikát, hogy a művelet végrehajtása előtt bizonyos műveleteket végre lehessen hajtani.

Az adatbázis állapotának ellenőrzése különböző végpontokon keresztül lehetővé teszi az automatizálás megfelelő megvalósítását. A portál értesítést küld egy művelet és az adatbázisok aktuális állapotának befejezéséről, de nem teszi lehetővé az állapot programozott ellenőrzését.

Az adatbázis állapota nem ellenőrizhető a kibővíthető műveletekhez a Azure Portal.

A DWU változásainak állapotának ellenõrzése:

1. Kapcsolódjon a kiszolgálóhoz társított Master adatbázishoz.
2. Az adatbázis állapotának vizsgálatához küldje el a következő lekérdezést.

```sql
SELECT    *
FROM      sys.databases
;
```

1. A művelet állapotának vizsgálatához küldje el a következő lekérdezést

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Ez a DMV a dedikált SQL-készlet különböző felügyeleti műveleteivel, például a művelettel és a művelet állapotával kapcsolatos információkat ad vissza, amely vagy IN_PROGRESS vagy kész.

### <a name="the-scaling-workflow"></a>A skálázási munkafolyamat

Amikor elindít egy méretezési műveletet, a rendszer először az összes nyitott munkamenetet megöli, és minden nyitott tranzakciót visszagörget, hogy konzisztens állapotot biztosítson. A skálázási műveletek esetében a méretezés csak a tranzakciós visszaállítás befejeződése után történik meg.  

- A skálázási műveletek esetében a rendszer leválasztja az összes számítási csomópontot, kiépíti a további számítási csomópontokat, majd újracsatlakoztatja a tárolási réteghez.
- A leskálázási művelethez a rendszer leválasztja az összes számítási csomópontot, majd csak a szükséges csomópontokat csatlakoztatja a tárolási réteghez.

## <a name="next-steps"></a>Következő lépések

A teljesítmény kezelésével kapcsolatos további információkért lásd: [erőforrás-osztályok a számítási feladatok kezeléséhez](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , valamint a [memória és a Egyidejűség korlátai](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).