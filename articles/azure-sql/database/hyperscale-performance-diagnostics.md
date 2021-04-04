---
title: Teljesítmény-diagnosztika a nagy kapacitású-ben
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure SQL Database nagy kapacitású kapcsolatos problémákat.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: ed31ff5d77b258d141a77fc174c2d5452adf7d01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791715"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL nagy kapacitású Performance hibaelhárítási diagnosztika
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A nagy kapacitású-adatbázisok teljesítményével kapcsolatos problémák elhárításához a Azure SQL Database számítási csomópont [általános teljesítmény-hangolási módszerei](monitor-tune-overview.md) a teljesítmény vizsgálatának kiindulópontja. A nagy kapacitású [elosztott architektúrája](service-tier-hyperscale.md#distributed-functions-architecture) miatt azonban további diagnosztika lett hozzáadva a segítséghez. Ez a cikk a nagy kapacitású-specifikus diagnosztikai információkat ismerteti.

## <a name="log-rate-throttling-waits"></a>A naplózási sebesség szabályozása vár

Minden Azure SQL Database szolgáltatási szinten a naplózási [ráta irányításával](resource-limits-logical-server.md#transaction-log-rate-governance)kényszerített, a log Generation díjszabási korlátai érvényesek. A nagy kapacitású-ben a log generálási korlát jelenleg 100 MB/s-ra van állítva, a szolgáltatási szinttől függetlenül. Vannak azonban olyan időpontok, amikor az elsődleges számítási replika log generálási sebességét szabályozni kell a helyreállító SLA-kat fenntartásához. Ez a szabályozás akkor fordul elő, ha egy [lapozófájl vagy egy másik számítási replika](service-tier-hyperscale.md#distributed-functions-architecture) jelentősen megmarad az új naplóbejegyzések a log szolgáltatásban való alkalmazása mögött.

A következő várakozási típusok ( [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) leírják, hogy az elsődleges számítási replikán miért lehet szabályozni a naplózási sebességet:

|Várakozás típusa    |Leírás                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Akkor következik be, amikor a nagy kapacitású-adatbázis elsődleges számítási csomópontjának napló-generálási sebessége a kiszolgáló (k) késleltetett naplójának használata miatt van szabályozva.         |
|RBIO_RG_DESTAGE        | Akkor következik be, amikor a nagy kapacitású-adatbázis számítási csomópontjának naplójának generálási arányát a rendszer a hosszú távú napló-tárolás késleltetett naplózása miatt szabályozza.         |
|RBIO_RG_REPLICA        | Akkor következik be, amikor a nagy kapacitású-adatbázis számítási csomópontjának naplójának generálási arányát a rendszer az olvasható másodlagos replika (ok) késleltetett naplójának használata miatt szabályozza.         |
|RBIO_RG_LOCALDESTAGE   | Akkor következik be, amikor a naplózási szolgáltatás késleltetett naplózása miatt a rendszer a nagy kapacitású adatbázis számítási csomópontjának naplójának generálási sebességét szabályozza.         |

## <a name="page-server-reads"></a>Az oldal kiszolgálójának olvasása

A számítási replikák nem gyorsítótárazzák helyileg az adatbázis teljes másolatát. A számítási replikához tartozó adatok tárolása a pufferben (a memóriában) és a helyi rugalmas puffer-tároló bővítmény (RBPEX) gyorsítótárában történik, amely az adatlapok részleges (nem lefedő) gyorsítótára. A helyi RBPEX gyorsítótár mérete arányos a számítási mérettel, és a számítási rétegek memóriájának háromszorosa. A RBPEX hasonló ahhoz a puffer-készlethez, amelyben a leggyakrabban használt információ található. A más lapokon található kiszolgálók esetében a RBPEX gyorsítótár található az adatbázis által karbantartott részhez.

Ha az olvasás egy számítási replikán van kiadva, ha az adatok nem léteznek a puffer-készletben vagy a helyi RBPEX-gyorsítótárban, a rendszer getPage (pageId, LSN) függvény hívását állítja ki, és a lapot beolvassa a megfelelő oldal-kiszolgálóról. Az egyoldalas kiszolgálókról érkező olvasások távoli olvasások, így lassabbak, mint a helyi RBPEX beolvasása. Az IO-vel kapcsolatos teljesítményproblémák hibaelhárításakor tudnia kell, hogy hány IOs-t tettek elérhetővé viszonylag lassabb távoli oldal-kiszolgáló olvasásával.

Több dinamikus felügyelt nézet (DMV) és bővített eseménynek van olyan oszlopa és mezője, amely meghatározza a kiszolgálóoldali kiszolgálóról érkező távoli olvasások számát, ami összehasonlítható az összes olvasási művelettel. A Query Store a lekérdezés futási idejének statisztikájának részeként rögzíti a távoli olvasásokat is.

- A jelentéskészítő lap kiszolgálójának olvasási oszlopai a végrehajtás DMV és a katalógusok nézeteiben érhetők el, például:

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- A Page Server olvasások a következő kiterjesztett eseményekhez lesznek hozzáadva:
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - lekérdezés – store_execution_runtime_info
- A rendszer a ActualPageServerReads/ActualPageServerReadAheads adja hozzá a lekérdezési terv XML-kódjához a tényleges tervekhez. Például:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Ha ezeket az attribútumokat a lekérdezési terv tulajdonságai ablakban szeretné megtekinteni, a SSMS 18,3-es vagy újabb verziójára van szükség.

## <a name="virtual-file-stats-and-io-accounting"></a>Virtuális fájl statisztikái és IO-nyilvántartása

Azure SQL Database a [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF az elsődleges módszer a SQL Database IO figyelésére. A nagy kapacitású IO-jellemzői eltérnek az [elosztott architektúrája](service-tier-hyperscale.md#distributed-functions-architecture)miatt. Ebben a szakaszban az IO (olvasás és írás) adatfájlokra koncentrálunk, ahogy az ebben a DMF látható. A nagy kapacitású-ben az ebben a DMF látható összes adatfájl egy távoli oldal-kiszolgálónak felel meg. Az itt említett RBPEX cache egy helyi SSD-alapú gyorsítótár, amely a számítási replika nem a gyorsítótára.

### <a name="local-rbpex-cache-usage"></a>Helyi RBPEX cache-használat

Helyi RBPEX gyorsítótár létezik a számítási replikán a helyi SSD-tárolón. Így a gyorsítótárhoz tartozó IO gyorsabb, mint az IO a távoli lapok kiszolgálóin. A nagy kapacitású-adatbázisban jelenleg [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) egy külön sorban jelent meg az IO-t a számítási REPLIKA helyi RBPEX gyorsítótára. Ebben a sorban a 0 érték szerepel mindkettő `database_id` és `file_id` oszlop esetében. Az alábbi lekérdezés például a RBPEX-használati statisztikát adja vissza az adatbázis indítása óta.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

A RBPEX által az összes többi adatfájlon végzett összesített olvasások aránya a RBPEX cache találati arányát biztosítja.

### <a name="data-reads"></a>Adatolvasások

- Ha az olvasásokat a SQL Server adatbázismotor állítja ki egy számítási replikán, akkor azokat a helyi RBPEX-gyorsítótár vagy a távoli lapok kiszolgálói vagy a kettő kombinációja, ha több oldalt olvas be.
- Ha a számítási replika egy adott fájl egyes lapjait olvassa be, például file_id 1 értéket, ha ezek az adatok kizárólag a helyi RBPEX-gyorsítótárban találhatók, az olvasáshoz tartozó összes IO-t file_id 0 (RBPEX) értékre kell kiszámítani. Ha az adatok némelyike a helyi RBPEX-gyorsítótárban található, és néhány rész egy távoli oldalon található, akkor az i/o-t a rendszer a RBPEX szolgáltatásból kiszolgált rész file_id felé haladva a 0 értékre veszi figyelembe, és a távoli oldal kiszolgálója által kiszolgált rész file_id 1.
- Ha egy számítási replika egy adott [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) lévő oldalt kér le, ha a lap kiszolgálója nem a kért LSN, a rendszer a számítási replikán lévő olvasás után megvárja, amíg a kiszolgáló felveszi a lapot a számítási replikába. A számítási replikán lévő egyik oldalról beolvasott adatok esetén a PAGEIOLATCH_ * WAIT típus jelenik meg, ha az IO-ra várakozik. A nagy kapacitású-ben ez a várakozási idő magában foglalja a LSN kért oldalának begyűjtéséhez szükséges időt, valamint a lap kiszolgálóról a számítási replikára történő átviteléhez szükséges időt.
- A nagyméretű olvasások, például az olvasás előtt gyakran a ["Scatter-gather" olvasások](/sql/relational-databases/reading-pages/)használatával történik. Így egyszerre akár 4 MB-nyi lapot is beolvashat, így egyetlen beolvasható a SQL Server-adatbázismotor. Ha azonban a beolvasott adatokat RBPEX-ban végzik, akkor ezek az olvasások több egyéni 8 KB-os olvasásnak vannak elküldve, mivel a puffer-készlet és a RBPEX mindig 8 KB-os lapokat használ. Ennek eredményeképpen a RBPEX által látott olvasási IOs-szám nagyobb lehet, mint a motor által elvégzett IOs tényleges száma.

### <a name="data-writes"></a>Adatírások

- Az elsődleges számítási replika nem ír közvetlenül a lapok kiszolgálójára. Ehelyett a naplózási szolgáltatásból származó bejegyzéseket a rendszer visszajátssza a megfelelő kiszolgálókon.
- A számítási replikán megjelenő írásokat a rendszer túlnyomórészt a helyi RBPEX írja (file_id 0). A 8 KB-nál nagyobb logikai fájlokra történő írások esetén, azaz a [gather-Write](/sql/relational-databases/writing-pages/)használatával végzett írások esetében az egyes írási műveletek több 8 KB-os egyéni írásra vannak lefordítva a RBPEX, mivel a puffer-készlet és a RBPEX mindig 8 KB-os lapokat használ. Ennek eredményeképpen a RBPEX által látott írási IOs-szám nagyobb lehet, mint a motor által végrehajtott IOs-szám.
- A nem RBPEX fájlok, illetve a file_id 0-tól eltérő adatfájlok, amelyek a lapozófájloknak felelnek meg, az írásokat is megjelenítik. A nagy kapacitású szolgáltatási szinten ezek az írások szimulálva vannak, mivel a számítási replikák soha nem írnak közvetlenül a lapozófájlokra. Az írási IOPS és az átviteli sebesség a számítási replikán megjelenő módon történik, de a file_id 0-tól eltérő adatfájlok késése nem tükrözi a kiszolgálóoldali írások tényleges késését.

### <a name="log-writes"></a>Naplók írása

- Az elsődleges számításban a napló írását a sys.dm_io_virtual_file_stats file_id 2. részében kell elszámolni. Az elsődleges számítási naplóba való írás egy írás a naplózási zónába.
- A naplózási rekordok nincsenek megerősítve a véglegesítő másodlagos replikán. A nagy kapacitású-ben a log szolgáltatás aszinkron módon alkalmazza a naplót a másodlagos replikára. Mivel a naplózási írások valójában nem fordulnak elő másodlagos replikákban, a log IOs a másodlagos replikán való könyvelése csak követési célokat szolgál.

## <a name="data-io-in-resource-utilization-statistics"></a>Adat IO az erőforrás-kihasználtsági statisztikában

A nem nagy kapacitású adatbázisokban az adatfájlok összevont olvasási és írási IOPS az [erőforrás-irányítási](./resource-limits-logical-server.md#resource-governance) adatIOPSi korláthoz viszonyítva az oszlopban [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nézetek szerepelnek `avg_data_io_percent` . Ugyanezt az értéket jelenti a Azure Portal _Adatio százalékként_.

A nagy kapacitású-adatbázisokban ez az oszlop csak a számítási replikán lévő helyi tárterületre vonatkozó korláttal, kifejezetten a RBPEX és a értékekkel kapcsolatos IOPS-kihasználtságot jelenti `tempdb` . Ebben az oszlopban az 100% érték azt jelzi, hogy az erőforrás-szabályozás korlátozza a helyi tárterület IOPS. Ha ez egy teljesítménnyel kapcsolatos problémával összefügg, állítsa be úgy a számítási feladatot, hogy kevesebb IO-t állítson elő, vagy növelje az adatbázis-szolgáltatás célkitűzését, hogy növelje az erőforrás-szabályozás _maximális IOPS_ [korlátját](resource-limits-vcore-single-databases.md). A RBPEX-olvasások és-írások erőforrás-szabályozása esetében a rendszer az egyes 8 KB-os IOs-et, nem pedig a SQL Server adatbázismotor által kiállított nagyobb IOs-t számítja fel.

A távoli lapokon tárolt adatio-kiszolgálók nem szerepelnek az erőforrás-felhasználási nézetekben vagy a portálon, de a [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF jelennek meg, ahogy azt korábban említettük.

## <a name="additional-resources"></a>További források

- A nagy kapacitású-virtuális mag erőforrás-korlátaival kapcsolatban lásd: [nagy kapacitású szolgáltatási réteg virtuális mag korlátai](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)
- A Azure SQL Database teljesítményének finomhangolását lásd: [a lekérdezési teljesítmény a Azure SQL Database](performance-guidance.md)
- A Query Store-t használó teljesítmény-hangolással kapcsolatban lásd: [Teljesítményfigyelés a Query Store használatával](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- A DMV-figyelési parancsfájlok esetében lásd: [teljesítmény monitorozása Azure SQL Database dinamikus felügyeleti nézetek használatával](monitoring-with-dmvs.md)