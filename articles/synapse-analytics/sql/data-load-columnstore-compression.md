---
title: Az oszlopcentrikus index teljesítményének javítása
description: Csökkentse a memóriakövetelményeket, vagy növelje a rendelkezésre álló memóriát, hogy maximalizálja az oszlopcentrikus indexek által az egyes sorcsoportokba tömörített sorok számát.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a452808b1c349e2aec91759675e269e3680f0598
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567961"
---
# <a name="maximize-rowgroup-quality-for-columnstore-index-performance"></a>Sorcsoport minőségének maximalizálása az oszlopcentrikus index teljesítménye érdekében

A sorcsoport minőségét a sorcsoportok sorai száma határozza meg. A rendelkezésre álló memória növelésével maximalizálható az oszlopcentrikus index által az egyes sorcsoportokba tömörített sorok száma.  Ezekkel a módszerekkel javíthatja az oszlopcentrikus indexek tömörítési sebességét és lekérdezési teljesítményét.

## <a name="why-the-rowgroup-size-matters"></a>Miért fontos a sorcsoport mérete?

Mivel az oszlopcentrikus index az egyes sorcsoportok oszlopszegmensének vizsgálatával vizsgálja meg a táblát, az egyes sorcsoportok sorszámának maximalizálása javítja a lekérdezési teljesítményt. Ha a sorcsoportok sok sort tartalmaznak, az adattömörítés javul, ami azt jelenti, hogy kevesebb adatot kell beolvasni a lemezről.

További információ a sorcsoportokról: [Oszlopcentrikus indexek útmutatója.](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)

## <a name="target-size-for-rowgroups"></a>Sorcsoportok célmérete

A legjobb lekérdezési teljesítmény érdekében a cél az oszlopcentrikus index sorcsoportonkénti soronkénti számának maximalizálása. Egy sorcsoport legfeljebb 1 048 576 sorral lehet. Sorcsoportonként nem megengedett a sorok maximális száma. Az oszlopcentrikus indexek akkor érik el a megfelelő teljesítményt, ha a sorcsoportok legalább 100 000 sorból áll.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>A sorcsoportok vágása a tömörítés során

Tömeges betöltés vagy oszlopcentrikus index újraépítése során előfordulhat, hogy nincs elegendő memória az egyes sorcsoportokhoz kijelölt összes sor tömörítésére. Memórianyomás esetén az oszlopcentrikus indexek levágják a sorcsoportok méretét, így az oszlopcentrikus tömörítés sikeres lehet.

Ha nincs elegendő memória ahhoz, hogy legalább 10 000 sort tömörítsen az egyes sorcsoportokba, a rendszer hibát jelez.

További információ a tömeges betöltésről: Tömeges betöltés [fürtözött oszlopcentrikus indexbe.](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?view=azure-sqldw-latest#bulk&preserve-view=true)

## <a name="how-to-monitor-rowgroup-quality"></a>Sorcsoportok minőségének figyelése

A DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) tartalmazza az SQL DB-nek megfelelő nézetdefiníciót), amely hasznos információkat nyújt, például a sorcsoportok sorai számát és a vágás okát a vágás esetén. A következő nézetet a DMV lekérdezésének hasznos módjaként hozhatja létre a sorcsoportok levágása érdekében.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

A trim_reason_desc jelzi, hogy a sorcsoportot levágták-e (trim_reason_desc = NO_TRIM azt feltételezi, hogy nem volt vágás, és a sorcsoport optimális minőségű). Az alábbi vágási okok a sorcsoport korai vágását jelzik:

- BULKLOAD: Ezt a vágási okot akkor használja a rendszer, ha a terheléshez beérkező sorok kötege kevesebb mint 1 millió sorból áll. A motor tömörített sorcsoportokat hoz létre, ha több mint 100 000 sor van beszúrva (a Delta-tárolóba való beszúrás helyett), de a vágás okát BULKLOAD (TÖMEGES BETÖLTÉS) állapotra állítja. Ebben a forgatókönyvben érdemes lehet növelni a kötegelt terhelést, hogy több sort tartalmaztasunk. Továbbá újraértékelje a particionálási sémát, hogy az ne legyen túl részletes, mivel a sorcsoportok nem terjednek ki a partícióhatárok között.
- MEMORY_LIMITATION: 1 millió sort tartalmazó sorcsoportok létrehozásához a motornak bizonyos mennyiségű működő memóriára van szüksége. Ha a betöltési munkamenet szabad memóriája kisebb, mint a szükséges munkamemória, a sorcsoportok idő előtt levágnak. A következő szakaszok ismertetik, hogyan becsülheti meg a szükséges memóriát, és foglalhat le több memóriát.
- DICTIONARY_SIZE: Ez a vágási ok azt jelzi, hogy sorcsoport-vágás történt, mert legalább egy sztringoszlop széles és/vagy nagy számosságú sztringekkel volt. A szótár mérete a memóriában legfeljebb 16 MB lehet, és a korlát elérése után a sorcsoport tömörítve lesz. Ha ilyen helyzetbe kerül, fontolja meg a problémás oszlop külön táblába való elválasztása.

## <a name="how-to-estimate-memory-requirements"></a>Memóriakövetelmények becslése

Egy sorcsoport tömörítéséhez a maximálisan szükséges memória a következő:

- 72 MB +
- \#sorok \* \# \* oszlopai 8 bájt +
- \#rows \* \# short-string-columns \* 32 bájt +
- \#hosszú sztringoszlopok \* 16 MB a tömörítési szótárhoz

> [!NOTE]
> Ahol a rövid sztringoszlopok <= 32 bájtos sztring adattípust, a hosszú sztringoszlopok pedig 32 bájtos > használják.

A hosszú sztringek egy szöveg tömörítésére tervezett tömörítési módszerrel vannak tömörítve. Ez a tömörítési metódus egy *szótárral tárolja* a szövegmintákat. A szótárak maximális mérete 16 MB. A sorcsoport minden hosszú sztringoszlopa csak egy szótárban található.

Az oszlopcentrikus memóriakövetelmények részletes tárgyalását a méretezést bemutató videóban [Synapse SQL: konfigurálás és útmutatás.](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)

## <a name="ways-to-reduce-memory-requirements"></a>A memóriakövetelmények csökkentésének módjai

Az alábbi technikákkal csökkentheti a sorcsoportok oszlopcentrikus indexekbe való tömörítésének memóriakövetelményét.

### <a name="use-fewer-columns"></a>Kevesebb oszlop használata

Ha lehetséges, tervezd meg a kevesebb oszlopot tartalmazó táblát. Amikor egy sorcsoportot tömörít az oszlopcentrikusba, az oszlopcentrikus index külön tömöríti az egyes oszlopszegmenseket. Ezért a sorcsoportok tömörítésére vonatkozó memóriakövetelmények az oszlopok számának növekedésével növekednek.

### <a name="use-fewer-string-columns"></a>Kevesebb sztringoszlop használata

A sztring adattípusok több memóriát igényelnek, mint a numerikus és a dátum adattípusok. A memóriakövetelmények csökkentése érdekében érdemes lehet eltávolítani a sztringoszlopokat a ténytáblákból, és kisebb dimenziótáblákba őket.

A sztringtömörítés további memóriakövetelményei:

- A legfeljebb 32 karakter hosszúságú sztring adattípusok értékenként 32 további bájtot is tartalmazhatnak.
- A 32 karakternél hosszabb sztring adattípusok szótári metódusokkal vannak tömörítve.  A szótár létrehozásához a sorcsoport minden oszlopa akár további 16 MB-ot is megkövetelhet.

### <a name="avoid-over-partitioning"></a>Kerülje a túl particionálást

Az oszlopcentrikus indexek partíciónként egy vagy több sorcsoportot hoznak létre. Az adatraktározási Azure Synapse Analytics a partíciók száma gyorsan növekszik, mivel az adatok el vannak osztva, és minden elosztás particionálva van. Ha a tábla túl sok partícióval rendelkezik, előfordulhat, hogy nincs elegendő sor a sorcsoportok kitöltéshez. A sorok hiánya nem eredményez memórianyomást a tömörítés során, de olyan sorcsoportokhoz vezet, amelyek nem érik el a legjobb oszlopcentrikus lekérdezési teljesítményt.

A túl particionálás elkerülésének egy másik oka az, hogy a sorok egy particionált tábla oszlopcentrikus indexbe való betöltése memóriaterhelést okoz. A betöltés során számos partíció fogadhatta a bejövő sorokat, amelyeket a rendszer a memóriában tart egészen addig, amíg az egyes partíciók nem tartalmaznak elég sort ahhoz, hogy tömörítve legyen. A túl sok partíció további memórianyomást okoz.

### <a name="simplify-the-load-query"></a>A betöltési lekérdezés egyszerűsítése

Az adatbázis a lekérdezés összes operátora között megosztja a lekérdezés memória-engedélyét. Ha egy terheléslekérdezés összetett rendezésekkel és illesztésekkel rendelkezik, a tömörítéshez rendelkezésre álló memória csökken.

Úgy tervezheti meg a terheléses lekérdezést, hogy csak a lekérdezés betöltésére összpontosítson. Ha átalakításokat kell futtatnia az adatokon, a betöltési lekérdezéstől elkülönítve futtassa őket. Az adatokat például egy halomtáblában kell előkészítésre venni, futtatni az átalakításokat, majd betölteni az előkészítési táblát az oszlopcentrikus indexbe. 

### <a name="adjust-maxdop"></a>A MAXDOP beállítása

Minden elosztás párhuzamosan tömöríti a sorcsoportokat az oszloptárba, ha elosztásonként több processzormag áll rendelkezésre. A párhuzamosság további memória-erőforrásokat igényel, ami memórianyomáshoz és sorcsoportok vágásához vezethet.

A memóriaterhelés csökkentése érdekében a MAXDOP lekérdezési tipp használatával kényszerítheti a terhelési művelet soros módban való futtatását az egyes disztribúciókban.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Több memória lefoglalásának módjai

A DWU mérete és a felhasználói erőforrásosztály együttesen határozzák meg, hogy mennyi memória áll rendelkezésre egy felhasználói lekérdezéshez. A terheléses lekérdezések memóriabeli engedélyének növeléséhez növelheti a DWUS-okat, vagy növelheti az erőforrásosztályt.

- A DWUs növelésével kapcsolatos további Hogyan [teljesítményről.](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Egy lekérdezés erőforrásosztályának a módosítása: Felhasználói erőforrásosztály [módosítása példa.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class)

## <a name="next-steps"></a>Következő lépések

A teljesítmény javításának további módjait a Teljesítmény áttekintése Synapse SQL [találja.](../overview-terminology.md)

