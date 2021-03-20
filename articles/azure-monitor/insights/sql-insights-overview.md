---
title: SQL-alapú üzemelő példányok figyelése SQL-ellenőrzésekkel (előzetes verzió)
description: Az SQL-információk áttekintése Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d01f80a803c5b0f9da067dd23ab8cdb4cc591a79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609387"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>SQL-alapú üzemelő példányok figyelése SQL-ellenőrzésekkel (előzetes verzió)
Az SQL-áttekintések az SQL-környezetek teljesítményét és állapotát figyelik.  A teljesítménnyel kapcsolatos szűk keresztmetszetek és problémák azonosításával segítheti az SQL-háttérrendszer által létrehozott létfontosságú számítási feladatok kiszámítható teljesítményét és rendelkezésre állását. Az SQL-elemzések [Azure monitor naplókban](../logs/data-platform-logs.md)tárolják az adattárakat, ami lehetővé teszi, hogy hatékony összesítést és szűrést nyújtson, és az adattrendeket az idő múlásával elemezze. Ezek az adatok a Azure Monitorban tekinthetők meg az ajánlat részeként szállított nézetekben, és a lekérdezések futtatásához és a trendek elemzéséhez közvetlenül is elvégezheti a naplózási adatok megtekintését.

Az SQL-alapú adatellenőrzések nem telepítenek semmit az SQL-IaaS üzemelő példányokon. Ehelyett dedikált figyelő virtuális gépeket használ az SQL Péter és az SQL IaaS üzemelő példányok adatainak távoli összegyűjtéséhez.  Az SQL-elemzések figyelési profilja segítségével kezelheti az összegyűjtött adatkészleteket az SQL típusa alapján, beleértve az Azure SQL DB-t, az Azure SQL felügyelt példányát és az Azure virtuális gépen futó SQL Servert.

## <a name="pricing"></a>Díjszabás

Nem számítunk fel közvetlen költséget az SQL-számításokhoz, de a tevékenységért a Log Analytics munkaterületen kell fizetnie. A [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján az SQL-megállapítások számlázása a következő:

- Az ügynököktől betöltött és a munkaterületen tárolt adatok.
- Riasztási szabályok a naplófájlok alapján.
- Riasztási szabályokból küldött értesítések.

A napló mérete az összegyűjtött adatok karakterlánc-hosszával változik, és az adatbázis-tevékenység mennyiségét is növelheti. 

## <a name="supported-versions"></a>Támogatott verziók 
Az SQL-ismeretek a következő SQL Server-verziókat támogatják:

- SQL Server 2012 és újabb

Az SQL-alapú adatáttekintések a következő környezetekben futó SQL Servereket támogatják:

- Azure SQL Database
- Felügyelt Azure SQL-példány
- Azure SQL virtuális gépek
- Azure-beli virtuális gépek

Az SQL-bepillantást nem támogatja vagy nem támogatja a következő:

- Az Azure-on kívüli virtuális gépeken futó SQL Server jelenleg nem támogatottak.
- Rugalmas készletek Azure SQL Database: korlátozott támogatás a nyilvános előzetes verzióban. Az általános elérhetősége teljes mértékben támogatott lesz.  
- Azure SQL Server nélküli központi telepítések: az aktív geo-DR-hez hasonlóan a jelenlegi figyelési ügynökök nem fogják tudni, hogy a kiszolgáló nélküli telepítés alvó állapotba kerüljön. Ez a kiszolgáló nélküli üzemelő példányok esetében a vártnál magasabb költségekkel járhat.  
- Olvasható formátumú másodlagos zónák: jelenleg csak egyetlen olvasható másodlagos végponttal (üzletileg kritikus vagy nagy kapacitású) rendelkező központi telepítési típusok lesznek támogatottak. Ha a nagy kapacitású üzemelő példányok elnevezett replikákat támogatnak, több olvasható másodlagos végpontot is támogatni fogunk egyetlen logikai adatbázishoz.  
- Azure Active címtárak: jelenleg csak a figyelési ügynökhöz tartozó SQL-bejelentkezések támogatottak. Azt tervezzük, hogy egy közelgő kiadásban támogassa az Azure Active könyvtárakat, és jelenleg nem támogatja az SQL VM-hitelesítés használatát egy testre szabott tartományvezérlőn.  


## <a name="open-sql-insights"></a>SQL-ismeretek megnyitása
Az SQL-adatok megnyitásához válassza az **SQL (előzetes verzió)** lehetőséget a Azure Portal **Azure monitor** menüjének elemzése szakaszában.  Kattintson egy csempére, és töltse be a figyelt SQL-típushoz tartozó élményt.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Az SQL-ismeretek Azure Portal.":::


## <a name="enable-sql-insights"></a>SQL-ismeretek engedélyezése 
A hibaelhárítás lépésein felül tekintse meg az SQL-információk [engedélyezése](sql-insights-enable.md) című témakört az SQL-információk engedélyezéséhez.


## <a name="data-collected-by-sql-insights"></a>SQL-elemzések által összegyűjtött adatok
A nyilvános előzetes verzióban az SQL-adatvizsgálatok csak a távoli figyelési módszert támogatják. A Graf ügynök nincs telepítve a SQL Server. A szolgáltatás a SQL Server bemeneti beépülő modulját használja a (z)-hez, és három különböző típusú SQL-figyelő lekérdezéseit használja: Azure SQL DB, Azure SQL felügyelt példány, Azure-beli virtuális gépen futó SQL Server. 

A következő táblázatok összefoglalják a következőket:

- A lekérdezés neve a SQLServer-ben-Graf beépülő modulban
- Dinamikus felügyelt nézetek a lekérdezési hívások
- Névtér az adat a *InsighstMetrics* táblában jelenik meg
- Azt határozza meg, hogy az adatok gyűjtése alapértelmezés szerint történjen-e
- Az adatok gyűjtésének gyakorisága alapértelmezés szerint
 
A figyelési profil létrehozásakor módosíthatja a futtatott lekérdezéseket és az adatgyűjtés gyakoriságát. 

### <a name="azure-sql-db-data"></a>Azure SQL DB-beli adatszolgáltatások 

| Lekérdezés neve | DMV | Névtér | Alapértelmezés szerint engedélyezve | Alapértelmezett gyűjtés gyakorisága |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | No | NA |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Yes | 60 másodperc |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Yes | 60 másodperc |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | Yes | 60 másodperc |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>sys. adatbázisok<br>sys. [database_service_objectives] | sqlserver_server_properties | Yes | 60 másodperc |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 másodperc |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 másodperc |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Yes | 60 másodperc |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA  |

### <a name="azure-sql-managed-instance-data"></a>Az Azure SQL felügyelt példányainak adatvédelme 

| Lekérdezés neve | DMV | Névtér | Alapértelmezés szerint engedélyezve | Alapértelmezett gyűjtés gyakorisága |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Yes | 60 másodperc |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Yes | 60 másodperc |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Yes | 60 másodperc |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Yes | 60 másodperc |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 másodperc |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 másodperc |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Yes | 60 másodperc |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA |

### <a name="sql-server-data"></a>SQL Serveri az adatgyűjtést

| Lekérdezés neve | DMV | Névtér | Alapértelmezés szerint engedélyezve | Alapértelmezett gyűjtés gyakorisága |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Yes | 60 másodperc |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 másodperc | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Yes | 60 másodperc |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | Yes | 60 másodperc |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 másodperc |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Yes | 60 másodperc |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Yes | 60 másodperc |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 másodperc |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 másodperc |




## <a name="next-steps"></a>Következő lépések

Lásd: az SQL-információk [engedélyezése](sql-insights-enable.md) a részletes eljáráshoz az SQL-információk engedélyezéséhez.
Tekintse meg az SQL-információkra vonatkozó gyakori kérdések [gyakori kérdéseit](../faq.md#sql-insights-preview) .
