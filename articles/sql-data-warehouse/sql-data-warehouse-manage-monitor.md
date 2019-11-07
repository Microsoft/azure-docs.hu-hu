---
title: Monitor your workload using DMVs
description: Megtudhatja, hogyan figyelheti a munkaterhelést a DMV használatával.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/23/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e1a754747ae5c0fb7c50653f4881b67a81e011ef
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645673"
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitor your workload using DMVs
Ez a cikk azt ismerteti, hogyan használhatók a dinamikus felügyeleti nézetek (DMV) a számítási feladatok figyelésére. Ide tartozik a lekérdezés végrehajtásának vizsgálata Azure SQL Data Warehouseban.

## <a name="permissions"></a>Engedélyek
A cikkben szereplő DMV lekéréséhez meg kell TEKINTENIe az adatbázis ÁLLAPOTÁT vagy a VEZÉRLÉSi engedélyt. Általában a MEGTEKINTÉSi adatbázis ÁLLAPOTának megadása az előnyben részesített engedély, mert sokkal szigorúbb.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Kapcsolatok figyelése
Az SQL Data Warehouse összes bejelentkezését a rendszer naplózza a [sys. DM _pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Ez a DMV tartalmazza az utolsó 10 000 bejelentkezést.  A session_id az elsődleges kulcs, amely minden új bejelentkezéshez egymás után van rendelve.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Lekérdezés végrehajtásának figyelése
Az SQL Data Warehouseon végrehajtott összes lekérdezést a [sys. DM _pdw_exec_requests][sys.dm_pdw_exec_requests]naplózza a rendszer.  Ez a DMV tartalmazza az utolsó 10 000-lekérdezést.  A request_id egyedileg azonosítja az egyes lekérdezéseket, és ez a DMV elsődleges kulcsa.  A request_id minden egyes új lekérdezéshez egymás után van rendelve, és a QID előtaggal van ellátva, amely a lekérdezés AZONOSÍTÓját jelöli.  A DMV egy adott session_id való lekérdezése az adott bejelentkezés összes lekérdezését megjeleníti.

> [!NOTE]
> A tárolt eljárások több kérelem azonosítóját használják.  A kérelmek azonosítói sorrendben vannak hozzárendelve. 
> 
> 

Az alábbi lépéseket követve vizsgálhatja meg a lekérdezés-végrehajtási terveket és időpontokat egy adott lekérdezés esetében.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>1\. lépés: a vizsgálni kívánt lekérdezés azonosítása
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

```

A fenti lekérdezési eredményekből jegyezze fel a vizsgálni kívánt lekérdezés **azonosítóját** .

A **felfüggesztett** állapotú lekérdezések a nagy számú aktív futó lekérdezés miatt várólistára helyezhetők. Ezek a lekérdezések a [sys. DM _pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) is megjelennek a lekérdezés UserConcurrencyResourceType. A párhuzamossági korlátokkal kapcsolatos információkért lásd: [teljesítmény](performance-tiers.md) -és [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md). A lekérdezések más okokat is várhatnak, például az objektumok zárolását.  Ha a lekérdezés egy erőforrásra vár, tekintse meg a jelen cikk további [erőforrásaira váró lekérdezések kivizsgálását][Investigating queries waiting for resources] ismertető cikket.

A [sys. DM _pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) táblában lévő lekérdezések keresésének egyszerűbbé tételéhez a [label][LABEL] paranccsal rendeljen hozzá egy olyan megjegyzést a lekérdezéshez, amely a sys. DM _pdw_exec_requests nézetben kereshető.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>2\. lépés: a lekérdezési terv vizsgálata
A kérelem azonosítója segítségével kérje le a lekérdezés elosztott SQL-(DSQL-) tervét a [sys. DM _pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Ha egy DSQL-csomag a vártnál hosszabb időt vesz igénybe, az ok lehet egy összetett csomag, amely sok DSQL lépést vagy csak egy lépést tart hosszú idő alatt.  Ha a csomag számos lépésből áll, több áthelyezési művelettel, érdemes lehet optimalizálni a táblázatok eloszlását az adatmozgatás csökkentése érdekében. A [táblázat terjesztési][Table distribution] cikke leírja, hogy miért kell áthelyezni az adatátvitelt a lekérdezés megoldásához, és elmagyarázza néhány terjesztési stratégiát az adatáthelyezés minimalizálásához.

Egy adott lépés további részleteinek vizsgálatához a hosszan futó lekérdezési lépés *operation_type* oszlopát, és jegyezze fel a **lépés indexét**:

* Folytassa a 3a. lépéssel az **SQL-műveletekhez**: OnOperation, RemoteOperation, ReturnOperation.
* Folytassa a 3b lépéssel az **adatáthelyezési műveletekhez**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>3a. lépés: az SQL vizsgálata az elosztott adatbázisokon
A Request ID és a Step index használatával kérhet le részleteket a [sys. DM _pdw_sql_requests][sys.dm_pdw_sql_requests], amely az összes elosztott adatbázis lekérdezési lépésének végrehajtási információit tartalmazza.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Ha a lekérdezési lépés fut, a [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] segítségével lekérheti a SQL Server becsült tervét a SQL Server terv gyorsítótárból egy adott disztribúción futó lépéshez.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>3b. lépés: az adatáthelyezés vizsgálata az elosztott adatbázisokon
A Request ID és a Step index használatával adatokat kérhet le egy, a [sys. DM _pdw_dms_workers][sys.dm_pdw_dms_workers]származó egyes eloszláson futó adatáthelyezési lépésről.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Tekintse meg a *total_elapsed_time* oszlopot, és ellenőrizze, hogy egy adott eloszlás jelentősen hosszabb időt vesz igénybe, mint a többi adatáthelyezés.
* A hosszú ideig futó eloszlásnál tekintse meg a *rows_processed* oszlopot, és ellenőrizze, hogy az adott eloszlásból áthelyezett sorok száma lényegesen nagyobb-e, mint a többi. Ha igen, ez a megállapítás a mögöttes adatokat is jelezheti.

Ha a lekérdezés fut, a [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] segítségével lekérheti a SQL Server becsült tervét a SQL Server terv gyorsítótárból a jelenleg futó SQL-lépéshez egy adott eloszláson belül.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Várakozó lekérdezések figyelése
Ha azt tapasztalja, hogy a lekérdezés nem halad előre, mert egy erőforrásra vár, itt látható egy lekérdezés, amely megjeleníti az összes olyan erőforrást, amely a lekérdezésre vár.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Ha a lekérdezés aktívan várakozik egy másik lekérdezés erőforrásaira, akkor az állapot **AcquireResources**válik.  Ha a lekérdezés rendelkezik az összes szükséges erőforrással, akkor a rendszer megadjaaz állapotot.

## <a name="monitor-tempdb"></a>Tempdb figyelése
A tempdb a közbenső eredmények tárolására szolgál a lekérdezés végrehajtása során. A tempdb-adatbázis magas kihasználtsága lassú lekérdezési teljesítményt eredményezhet. Azure SQL Data Warehouse minden csomópontja körülbelül 1 TB-nyi lemezterülettel rendelkezik a tempdb. Az alábbiakban a tempdb használatának figyelésére, valamint a lekérdezésekben a tempdb használatának csökkenésére vonatkozó tippek találhatók. 

### <a name="monitoring-tempdb-with-views"></a>Tempdb figyelése nézetekkel
A tempdb-használat figyeléséhez először telepítse a [Microsoft. vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) nézetet a [Microsoft eszközkészletből a SQL Data Warehouse](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Ezután végrehajthatja a következő lekérdezést, hogy az összes végrehajtott lekérdezés esetében megjelenjen a tempdb használata.

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```

Ha olyan lekérdezéssel rendelkezik, amely nagy mennyiségű memóriát használ, vagy a tempdb kiosztásával kapcsolatos hibaüzenetet kapott, annak oka az lehet, hogy egy nagyon nagy [CREATE TABLE a Select (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) vagy a [Insert Select](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) utasítás fut, amely nem a végső adatáthelyezési művelet. Ez általában ShuffleMove műveletként azonosítható az elosztott lekérdezési tervben közvetlenül a végső Beszúrás kiválasztása előtt.  A [sys. DM _pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) segítségével figyelje a ShuffleMove műveleteket. 

A leggyakoribb megoldás a CTAS vagy a SELECT utasítás beillesztése több betöltési utasításba, így az adatmennyiség nem haladja meg az 1 TB/csomópont tempdb korlátot. A fürtöt nagyobb méretre is méretezheti, amely a tempdb méretét több csomóponton is eloszthatja, ami csökkenti az egyes csomópontok tempdb.

A CTAS és az INSERT SELECT utasítások mellett a nem elegendő memóriával futó nagyméretű, összetett lekérdezések a tempdb-sel való meghibásodást okozhatnak.  Érdemes lehet nagyobb erőforrás- [osztállyal](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management) futtatni, hogy elkerülje a tempdb való kilépést.

## <a name="monitor-memory"></a>Memória figyelése

A memória okozhatja a lassú teljesítményt és a memóriával kapcsolatos problémák okát. Ha úgy találja, hogy SQL Server memóriahasználat a lekérdezés végrehajtása során, érdemes megfontolnia az adattárház skálázását.

A következő lekérdezés visszaadja SQL Server memóriahasználat és a memória terhelését egy csomóponton:   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>Tranzakciós napló méretének figyelése
A következő lekérdezés az egyes eloszlások tranzakciós naplójának méretét adja vissza. Ha az egyik naplófájl eléri a 160 GB-ot, érdemes megfontolnia a példány méretezését vagy a tranzakció méretének korlátozását. 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>Tranzakciós napló visszaállításának figyelése
Ha a lekérdezések sikertelenek, vagy a folytatáshoz hosszú időt vesznek fel, ellenőrizheti és megfigyelheti, hogy vannak-e visszagörgethető tranzakciók.
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>A kiinduló terhelés figyelése
A következő lekérdezés a terhelés előrehaladásának becslését tartalmazza. A lekérdezés csak a jelenleg feldolgozás alatt álló fájlokat jeleníti meg. 

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>További lépések
További információ a DMV: [rendszernézetek][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
