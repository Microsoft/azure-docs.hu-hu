---
title: SQL-alapú üzemelő példányok figyelése SQL-ellenőrzésekkel (előzetes verzió)
description: Az SQL-információk áttekintése Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567831"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>SQL-alapú üzemelő példányok figyelése SQL-ellenőrzésekkel (előzetes verzió)
Az SQL-elemzések átfogó megoldást nyújtanak az [Azure SQL-családban](../../azure-sql/index.yml)található termékek figyelésére. Az SQL-elemzések [dinamikus felügyeleti nézetek](../../azure-sql/database/monitoring-with-dmvs.md) használatával teszik elérhetővé az állapot figyeléséhez, a problémák diagnosztizálásához és a teljesítmény finomhangolásához szükséges adatkezelési nézeteket.  

Az SQL-elemzések távolról végzik az összes figyelést. A dedikált virtuális gépek figyelési ügynökei csatlakoznak az SQL-erőforrásokhoz, és távolról gyűjtenek adatokat. Az összegyűjtött adatokat [Azure monitor naplókban](../logs/data-platform-logs.md)tárolja a rendszer, így egyszerű összesítést, szűrést és trend-elemzést tesz lehetővé. Megtekintheti az összegyűjtött adatokat az SQL-elemzések [munkafüzet-sablonból](../visualize/workbooks-overview.md), vagy közvetlenül is kioszthatja az adatokat a [naplók](../logs/get-started-queries.md)használatával.

## <a name="pricing"></a>Díjszabás
Az SQL-bepillantást nem lehet közvetlen kifizetni. Minden költséget az adatokat gyűjtő virtuális gépek, az adatokat tároló Log Analytics munkaterületek és az adatokon konfigurált riasztási szabályok okoznak. 

**Virtuális gépek**

Virtuális gépek esetén a [virtuális gépek díjszabási oldalán](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/)közzétett díjszabás alapján számítjuk fel a díjat. A szükséges virtuális gépek száma a figyelni kívánt kapcsolódási karakterláncok számától függően változhat. Javasoljuk, hogy minden 100-os kapcsolódási karakterlánchoz 1 virtuális gépet foglaljon le Standard_B2s. További részletekért tekintse meg az Azure-beli [virtuális gépekre vonatkozó követelményeket](sql-insights-enable.md#azure-virtual-machine-requirements) .

**Log Analytics-munkaterületek**

A Log Analytics munkaterületek esetében a díjszabás a [Azure monitor díjszabási oldalon](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján történik. Az SQL-elemzések által használt Log Analytics munkaterületek az adatok betöltésére, az adatmegőrzésre és (opcionálisan) adatok exportálására vonatkozó költségekkel járnak. A pontos díjak a betöltött, megőrzött és exportált adatok mennyiségétől függően változnak. Az ilyen adatmennyiség az adatbázis-tevékenységtől és a [figyelési profilokban](sql-insights-enable.md#create-sql-monitoring-profile)meghatározott gyűjtési beállításoktól függően változhat.

**Riasztási szabályok**

A Azure Monitor riasztási szabályai a [Azure monitor díjszabási oldalon](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján számítjuk fel a díjat. Ha úgy dönt, hogy SQL-alapú [riasztásokat hoz létre](sql-insights-alerts.md), akkor a létrehozott riasztási szabályokért és az összes elküldött értesítésért díjat számítunk fel.

## <a name="supported-versions"></a>Támogatott verziók 
Az SQL-ismeretek a következő SQL Server-verziókat támogatják:
- SQL Server 2012 és újabb

Az SQL-alapú adatáttekintések a következő környezetekben futó SQL Servereket támogatják:
- Azure SQL Database
- Felügyelt Azure SQL-példány
- SQL Server az Azure Virtual Machines-on (az SQL-alapú [virtuális gép](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) szolgáltatójának regisztrált virtuális gépeken futó SQL Server)
- Azure-beli virtuális gépek (SQL Server az SQL-alapú [virtuálisgép](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) -szolgáltatónál nem regisztrált virtuális gépeken futnak)

Az SQL-bepillantást nem támogatja vagy nem támogatja a következő:
- **Nem Azure-beli példányok**: az Azure-on kívüli virtuális gépeken futó SQL Server nem támogatottak
- **Rugalmas készletek Azure SQL Database**: a metrikák nem gyűjthetők össze rugalmas készletekhez. A rugalmas készletekben lévő adatbázisok esetében nem lehet mérőszámokat összegyűjteni.
- **Alacsony szolgáltatási szintek Azure SQL Database**: az alapszintű, S0, S1 és S2 [szolgáltatási](../../azure-sql/database/resource-limits-dtu-single-databases.md) szinten lévő adatbázisok esetében nem gyűjthetők a metrikák.
- **Azure SQL Database kiszolgáló** nélküli szinten: a metrikák összegyűjthetők az adatbázisok kiszolgáló nélküli számítási szintjével. A metrikák összegyűjtésének folyamata azonban alaphelyzetbe állítja az automatikus szüneteltetés késleltetésének időzítőjét, ami megakadályozza, hogy az adatbázis automatikusan szüneteltetett állapotba lépjen
- **Másodlagos replikák**: a metrikák csak egyetlen másodlagos replika esetében gyűjthetők össze adatbázisként. Ha egy adatbázis több mint 1 másodlagos replikával rendelkezik, csak 1 lehet figyelni.
- **Hitelesítés Azure Active Directorysal**: az SQL-hitelesítés egyetlen támogatott [hitelesítési](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) módszer a figyeléshez. Az Azure-beli virtuális gépeken a SQL Server az egyéni tartományvezérlők Active Directory használatával történő hitelesítés nem támogatott.  

## <a name="open-sql-insights"></a>SQL-ismeretek megnyitása
Az SQL-adatok megnyitásához válassza az **SQL (előzetes verzió)** lehetőséget a Azure Portal **Azure monitor** menüjének elemzése szakaszában.  Kattintson egy csempére, és töltse be a figyelt SQL-típushoz tartozó élményt.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Az SQL-ismeretek Azure Portal.":::

## <a name="enable-sql-insights"></a>SQL-ismeretek engedélyezése 
Az SQL-információk engedélyezésével kapcsolatos utasításokért lásd: SQL-információk [engedélyezése](sql-insights-enable.md) .

## <a name="troubleshoot-sql-insights"></a>SQL-alapú adatáttekintések – problémamegoldás 
Az SQL-információk hibaelhárításával kapcsolatos utasításokért tekintse meg az SQL-információk [hibaelhárítását](sql-insights-troubleshoot.md) ismertető témakört.

## <a name="data-collected-by-sql-insights"></a>SQL-elemzések által összegyűjtött adatok
Az SQL-elemzések távolról végzik az összes figyelést. A SQL Server rendszert futtató virtuális gépeken nem telepítünk ügynököt. 

Az SQL-elemzések dedikált figyelő virtuális gépeket használnak az SQL-erőforrások adatainak távoli összegyűjtéséhez. Minden figyelési virtuális gépen telepítve lesz a [Azure monitor ügynök](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) és a számítási feladatok beolvasása (WLI) bővítmény. A WLI-bővítmény tartalmazza a nyílt forráskódú- [Graf Agent ügynököt](https://www.influxdata.com/time-series-platform/telegraf/). Az SQL- [elemzések adatgyűjtési szabályokat](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) használ a következő adatgyűjtési beállítások megadásához: [SQL Server-beépülő modul](https://www.influxdata.com/integration/microsoft-sql-server/).

A Azure SQL Database, az Azure SQL felügyelt példánya és a SQL Server számára különböző adathalmazok érhetők el. Az alábbi táblázatok az elérhető adatértékeket írják le. Testre szabhatja, hogy mely adatkészletek legyenek összegyűjtve, és a gyűjtemény gyakorisága a [figyelési profil létrehozásakor](sql-insights-enable.md#create-sql-monitoring-profile).

Az alábbi táblázatokban a következő oszlopok szerepelnek:
- **Felhasználóbarát név**: a lekérdezés neve, ahogyan az a Azure Portal a figyelési profil létrehozásakor látható
- **Konfiguráció neve**: a lekérdezés neve a Azure Portal a figyelési profil szerkesztése során látható módon
- **Névtér**: a lekérdezés neve log Analytics munkaterületen található. Ez az azonosító megjelenik az  `Namespace` oszlop tulajdonságának InsighstMetrics táblájában. `Tags`
- **DMV**: az adathalmaz létrehozásához használt dinamikus felügyelt nézetek
- **Alapértelmezés szerint engedélyezve**: azt határozza meg, hogy az adatok gyűjtése alapértelmezés szerint történjen-e
- **Alapértelmezett gyűjtemény gyakorisága**: az adatok gyűjtésének gyakorisága alapértelmezés szerint

### <a name="data-for-azure-sql-database"></a>Azure SQL Database 
| Felhasználóbarát név | Konfiguráció neve | Névtér | Dinamikus felügyeleti nézetek | Alapértelmezés szerint engedélyezve | Alapértelmezett gyűjtés gyakorisága |
|:---|:---|:---|:---|:---|:---|
| ADATBÁZIS várakozási statisztikái | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | NA |
| DBO várakozási statisztikái | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Yes | 60 másodperc |
| Memória-nyilvántartók | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 másodperc |
| Adatbázis-IO | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Yes | 60 másodperc |
| Kiszolgáló tulajdonságai | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys. adatbázisok<br>sys. [database_service_objectives] | Yes | 60 másodperc |
| Teljesítményszámlálók | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Yes | 60 másodperc |
| Erőforrás-statisztika | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Yes | 60 másodperc |
| Erőforrások szabályozása | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Yes | 60 másodperc |
| Kérelmek | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Táblázat| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Az Azure SQL felügyelt példányainak adatvédelme 

| Felhasználóbarát név | Konfiguráció neve | Névtér | Dinamikus felügyeleti nézetek | Alapértelmezés szerint engedélyezve | Alapértelmezett gyűjtés gyakorisága |
|:---|:---|:---|:---|:---|:---|
| Várakozási statisztikák | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 másodperc |
| Memória-nyilvántartók | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 másodperc |
| Adatbázis-IO | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 másodperc |
| Kiszolgáló tulajdonságai | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Yes | 60 másodperc |
| Teljesítményszámlálók | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Yes | 60 másodperc |
| Erőforrás-statisztika | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Yes | 60 másodperc |
| Erőforrások szabályozása | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Yes | 60 másodperc |
| Kérelmek | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Táblázat | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |

### <a name="data-for-sql-server"></a>SQL Server

| Felhasználóbarát név | Konfiguráció neve | Névtér | Dinamikus felügyeleti nézetek | Alapértelmezés szerint engedélyezve | Alapértelmezett gyűjtés gyakorisága |
|:---|:---|:---|:---|:---|:---|
| Várakozási statisztikák | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 másodperc | 
| Memória-nyilvántartók | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 másodperc |
| Adatbázis-IO | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 másodperc |
| Kiszolgáló tulajdonságai | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Yes | 60 másodperc |
| Teljesítményszámlálók | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Yes | 60 másodperc |
| Kötet területe | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Yes | 60 másodperc |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Yes | 60 másodperc |
| Táblázat | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |
| Kérelmek | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Rendelkezésre állási replika állapota | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 másodperc |
| Rendelkezésre állási adatbázis replikái | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 másodperc |

## <a name="next-steps"></a>Következő lépések

- Lásd: az SQL-információk [engedélyezése](sql-insights-enable.md) az SQL-megállapítások engedélyezéséhez
- Tekintse meg az SQL-információkra vonatkozó gyakori kérdések [gyakori kérdéseit](../faq.md#sql-insights-preview) .
