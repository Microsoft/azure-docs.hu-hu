---
title: Elastic Database feladatok (előzetes verzió) létrehozása és kezelése a Transact-SQL (T-SQL) segítségével
description: A Transact-SQL (T-SQL) használatával számos adatbázison futtathat parancsfájlokat Elastic Database-feladatokkal.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: how-to
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/01/2021
ms.openlocfilehash: 11b94ba5bcedf56f0115b8730dc58f808aff5c58
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371600"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs-preview"></a>A Transact-SQL (T-SQL) használata Elastic Database feladatok létrehozásához és kezeléséhez (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk számos példát mutat be a rugalmas feladatok T-SQL használatával történő használatának megkezdésére.

A példák a [*feladatok adatbázisban*](job-automation-overview.md#elastic-job-database)elérhető [tárolt eljárásokat](#job-stored-procedures) és [nézeteket](#job-views) használják.

A Transact-SQL (T-SQL) feladatok létrehozására, konfigurálására, végrehajtására és kezelésére szolgál. A rugalmas feladatok ügynökének létrehozása nem támogatott a T-SQL-ben, ezért először létre kell hoznia egy *rugalmas feladatot* a portál vagy a [PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent)használatával.

## <a name="create-a-credential-for-job-execution"></a>Hitelesítő adat létrehozása a feladatok végrehajtásához

A hitelesítő adatok használatával csatlakozhat a megcélzott adatbázisokhoz a parancsfájlok futtatásához. A hitelesítő adatoknak megfelelő engedélyekkel kell rendelkezniük a célcsoport által megadott adatbázisokon a parancsfájl sikeres végrehajtásához. [Logikai SQL Server](logical-servers.md) és/vagy Pool célcsoport-tag használata esetén erősen javasolt a hitelesítő adatok létrehozása a hitelesítő adatok frissítéséhez a kiszolgáló és/vagy a készlet a feladatok végrehajtásakor történő bővítése előtt. Az adatbázis-hatókörrel rendelkező hitelesítő adatok létrejönnek a feladatra szolgáló ügynök adatbázisában. Ugyanezt a hitelesítő adatot kell használni a bejelentkezés *létrehozásához* és a *felhasználó bejelentkezési adatainak létrehozásához, hogy a bejelentkezési adatbázis engedélyei elérhetők legyenek* a cél adatbázisokra vonatkozóan.

```sql
--Connect to the new job database specified when creating the Elastic Job agent

-- Create a database master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create two database scoped credentials.  
-- The credential to connect to the Azure SQL logical server, to execute jobs
CREATE DATABASE SCOPED CREDENTIAL job_credential WITH IDENTITY = 'job_credential',
    SECRET = '<EnterStrongPasswordHere>';
GO
-- The credential to connect to the Azure SQL logical server, to refresh the database metadata in server
CREATE DATABASE SCOPED CREDENTIAL refresh_credential WITH IDENTITY = 'refresh_credential',
    SECRET = '<EnterStrongPasswordHere>';
GO
```

## <a name="create-a-target-group-servers"></a>Célcsoport létrehozása (kiszolgálók)

Az alábbi példa bemutatja, hogyan hajtható végre a feladatok a kiszolgálók összes adatbázisán.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1';

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name = 'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net';

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```

## <a name="exclude-an-individual-database"></a>Önálló adatbázis kizárása

Az alábbi példa bemutatja, hogyan hajtható végre a feladatok egy kiszolgáló összes adatbázisán, kivéve a *MappingDB* nevű adatbázist.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup';
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'refresh_credential', --credential required to refresh the databases in a server
@server_name = N'London.database.windows.net';
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server2.database.windows.net';
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name = N'MappingDB';
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```

## <a name="create-a-target-group-pools"></a>Célcsoport (készletek) létrehozása

Az alábbi példa bemutatja, hogyan célozhat meg egy vagy több rugalmas készletben lévő összes adatbázist.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup';

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name = 'refresh_credential', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net',
@elastic_pool_name = 'ElasticPool-1';

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```

## <a name="deploy-new-schema-to-many-databases"></a>Új séma üzembe helyezése számos adatbázison

Az alábbi példa bemutatja, hogyan helyezhet üzembe új sémát az összes adatbázison.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name = 'CreateTableTest', @description = 'Create Table Test';

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name = 'CreateTableTest',
@command = N'IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name = 'job_credential',
@target_group_name = 'PoolGroup';
```

## <a name="data-collection-using-built-in-parameters"></a>Adatgyűjtés beépített paraméterek használatával

Számos adatgyűjtési forgatókönyv esetében hasznos lehet néhány ilyen programozási változót felvenni a feladatok eredményeinek feldolgozásához.

- $ (job_name)
- $ (job_id)
- $ (job_version)
- $ (step_id)
- $ (step_name)
- $ (job_execution_id)
- $ (job_execution_create_time)
- $ (target_group_name)

Ha például az összes eredményt ugyanabból a feladatokból szeretné csoportosítani, használja a *$ (job_execution_id)* parancsot az alábbi parancsban látható módon:

```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```

## <a name="monitor-database-performance"></a>Adatbázis teljesítményének monitorozása

Az alábbi példa egy új feladatot hoz létre, amely több adatbázisból gyűjt teljesítményadatokat.

Alapértelmezés szerint a feladatok ügynöke létrehozza a kimeneti táblát a visszaadott eredmények tárolásához. Ezért a kimeneti hitelesítő adatokhoz tartozó rendszerbiztonsági tag számára legalább a következő engedélyekkel kell rendelkeznie: `CREATE TABLE` az adatbázison,,, `ALTER` , a `SELECT` `INSERT` `DELETE` kimeneti táblában vagy a sémájában, valamint a `SELECT` [sys. Indexes](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql) katalógus nézetben.

Ha az idő előtt manuálisan szeretné létrehozni a táblázatot, akkor a következő tulajdonságokkal kell rendelkeznie:

1. Az eredményhalmaz helyes nevét és adattípusait tartalmazó oszlopok.
2. A uniqueidentifier adattípusával internal_execution_id további oszlop.
3. A internal_execution_id oszlopban megnevezett nem fürtözött index `IX_<TableName>_Internal_Execution_ID` .
4. Az összes fent felsorolt engedély, kivéve az `CREATE TABLE` adatbázisra vonatkozó engedélyeket.

Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsokat:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name = 'ResultsJob',
@command = N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name = 'job_credential',
@target_group_name = 'PoolGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'job_credential',
@output_server_name = 'server1.database.windows.net',
@output_database_name = '<resultsdb>',
@output_table_name = '<resutlstable>';

--Create a job to monitor pool performance

--Connect to the job database specified when creating the job agent

-- Add a target group containing Elastic Job database
EXEC jobs.sp_add_target_group 'ElasticJobGroup';

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'ElasticJobGroup',
@target_type = 'SqlDatabase',
@server_name = 'server1.database.windows.net',
@database_name = 'master';

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name = 'ResultsPoolsJob',
@description = 'Demo: Collection Performance data from all pools',
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15;

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name = 'job_credential',
@target_group_name = 'ElasticJobGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'job_credential',
@output_server_name = 'server1.database.windows.net',
@output_database_name = 'resultsdb',
@output_table_name = 'resutlstable';
```

## <a name="view-job-definitions"></a>Feladatdefiníciók megtekintése

Az alábbi példa bemutatja, hogyan tekintheti meg az aktuális feladatdefiníciók.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs;

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j
  ON j.job_id = js.job_id AND j.job_version = js.job_version;

-- View the steps of all versions of all jobs
SELECT * FROM jobs.jobsteps;
```

## <a name="begin-unplanned-execution-of-a-job"></a>A feladatok nem tervezett végrehajtásának megkezdése

Az alábbi példa bemutatja, hogyan indíthat el azonnal egy feladatot.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest';

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier;
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output;
select @je;

select * from jobs.job_executions where job_execution_id = @je;

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1;
```

## <a name="schedule-execution-of-a-job"></a>Feladatok végrehajtásának ütemezve

Az alábbi példa bemutatja, hogyan ütemezhet egy feladatot a jövőbeli végrehajtáshoz.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name = 'ResultsJob',
@enabled=1,
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15;
```

## <a name="monitor-job-execution-status"></a>A feladatok végrehajtási állapotának figyelése

Az alábbi példa bemutatja, hogyan tekintheti meg az összes feladat végrehajtási állapotának részleteit.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC;

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC;

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob'
ORDER BY start_time DESC;

-- View all active executions
SELECT * FROM jobs.job_executions
WHERE is_active = 1
ORDER BY start_time DESC;
```

## <a name="cancel-a-job"></a>Feladat megszakítása

A következő példa egy feladat megszakítását mutatja be.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC;
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef';
```

## <a name="delete-old-job-history"></a>Régi feladatok előzményeinek törlése

Az alábbi példa bemutatja, hogyan törölheti a feladatok előzményeit egy adott dátum előtt.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job's executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00';

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>A feladatok és a hozzá tartozó feladatok előzményeinek törlése

Az alábbi példa bemutatja, hogyan törölhet egy feladatot és az összes kapcsolódó feladatot.  
Kapcsolódjon a [*feladatok adatbázisához*](job-automation-overview.md#elastic-job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob';

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="job-stored-procedures"></a>Feladatok tárolt eljárásai

A következő tárolt eljárások a [feladatok adatbázisban](job-automation-overview.md#elastic-job-database)találhatók.

|Tárolt eljárás  |Description  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Új feladatok hozzáadására szolgál.    |
|[sp_update_job](#sp_update_job)    |      Egy meglévő feladatot frissít.   |
|[sp_delete_job](#sp_delete_job)     |      Töröl egy meglévő feladatot.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Feltesz egy lépést egy feladatokba.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Frissíti a feladatok lépéseit.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Törli a feladatot.    |
|[sp_start_job](#sp_start_job)    |  A feladatok végrehajtásának megkezdése.       |
|[sp_stop_job](#sp_stop_job)     |     Leállítja a feladatok végrehajtását.   |
|[sp_add_target_group](#sp_add_target_group)    |     Célcsoport hozzáadására szolgál.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Egy célcsoport törlése.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Adatbázis vagy adatbázis-csoport hozzáadását egy célcsoporthoz.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     A célcsoport tagjainak eltávolítása egy célcsoportból.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Eltávolítja egy adott feladatokhoz tartozó előzményi rekordokat.     |

### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Új feladatok hozzáadására szolgál.
  
#### <a name="syntax"></a>Szintaxis  
  
```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
  [ , [ @job_id = ] job_id OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok  

[ **\@ job_name =** ] "job_name"  
A feladattípus neve. A névnek egyedinek kell lennie, és nem tartalmazhatja a százalékot (%) karakter. job_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ description =** ] "Description"  
A feladattípus leírása. a leírás a nvarchar (512), amelynek alapértelmezett értéke NULL. Ha a Leírás nincs megadva, a rendszer üres karakterláncot használ.

[ **\@ engedélyezve =** ] engedélyezve  
Azt jelzi, hogy engedélyezve van-e a feladatok ütemterve. Engedélyezve: bit, alapértelmezett értéke: 0 (letiltva). Ha a 0 érték van beállítva, a rendszer nem engedélyezi a feladatot, és nem a megfelelő ütemterv szerint fut. azonban manuálisan is futtatható. Ha 1, a rendszer az ütemterv szerint futtatja a feladatot, és manuálisan is futtatható.

[ **\@ schedule_interval_type =**] schedule_interval_type  
Az érték azt jelzi, hogy mikor kell végrehajtani a feladatot. schedule_interval_type a nvarchar (50), amelynek alapértelmezett értéke egyszer, és az alábbi értékek egyike lehet:

- Egyszer,
- Perc,
- Óra,
- Nap,
- "Weeks",
- Hónap

[ **\@ schedule_interval_count =** ] schedule_interval_count  
A feladatok egyes végrehajtásai közötti schedule_interval_count időszakok száma. a schedule_interval_count int, alapértelmezett értéke 1. Az értéknek 1-nél nagyobbnak vagy azzal egyenlőnek kell lennie.

[ **\@ schedule_start_time =** ] schedule_start_time  
A feladatok végrehajtásának megkezdésének dátuma. schedule_start_time DATETIME2, az alapértelmezett 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
A feladatütemezés leállításának dátuma. schedule_end_time DATETIME2, az alapértelmezett 9999-12-31 11:59:59.0000000.

[ **\@ job_id =** ] job_id kimenet  
Sikeres létrehozás esetén a feladathoz rendelt feladathoz tartozó azonosító szám. job_id uniqueidentifier típusú kimeneti változó.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

a sp_add_job a feladatok ügynökének létrehozásakor megadott feladatkártya-adatbázisból kell futtatni.
Ha sp_add_jobt hajtott végre egy feladathoz, sp_add_jobstep felhasználhatja azokat a lépéseket, amelyek a feladattal kapcsolatos tevékenységeket végzik. A művelet kezdeti verziószáma 0, amely az első lépés hozzáadásakor eggyel nő.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Egy meglévő feladatot frissít.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
  [ , [ @new_name = ] 'new_name' ]
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_name =** ] "job_name"  
A frissítendő feladatok neve. job_name nvarchar (128).

[ **\@ new_name =** ] "new_name"  
A feladatokhoz tartozó új név. new_name nvarchar (128).

[ **\@ description =** ] "Description"  
A feladattípus leírása. a Leírás nvarchar (512).

[ **\@ engedélyezve =** ] engedélyezve  
Megadja, hogy engedélyezve van-e a feladatok ütemezett értéke (1), vagy nincs engedélyezve (0). Engedélyezve: bit.

[ **\@ schedule_interval_type =** ] schedule_interval_type  
Az érték azt jelzi, hogy mikor kell végrehajtani a feladatot. schedule_interval_type nvarchar (50), és a következő értékek egyike lehet:

- Egyszer,
- Perc,
- Óra,
- Nap,
- "Weeks",
- Hónap

[ **\@ schedule_interval_count =** ] schedule_interval_count  
A feladatok egyes végrehajtásai közötti schedule_interval_count időszakok száma. a schedule_interval_count int, alapértelmezett értéke 1. Az értéknek 1-nél nagyobbnak vagy azzal egyenlőnek kell lennie.

[ **\@ schedule_start_time =** ] schedule_start_time  
A feladatok végrehajtásának megkezdésének dátuma. schedule_start_time DATETIME2, az alapértelmezett 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
A feladatütemezés leállításának dátuma. schedule_end_time DATETIME2, az alapértelmezett 9999-12-31 11:59:59.0000000.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

Ha sp_add_jobt hajtott végre egy feladathoz, sp_add_jobstep felhasználhatja azokat a lépéseket, amelyek a feladattal kapcsolatos tevékenységeket végzik. A művelet kezdeti verziószáma 0, amely az első lépés hozzáadásakor eggyel nő.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Töröl egy meglévő feladatot.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
  [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_name =** ] "job_name"  
A törlendő feladattípus neve. job_name nvarchar (128).

[ **\@ Force =** ] kényszerítés  
Megadja, hogy a rendszer törli-e a feladatot, ha a feladat végrehajtása folyamatban van, és megszakítja az összes folyamatban lévő végrehajtást (1), vagy ha a feladatok végrehajtása folyamatban van (0). a kényszerítés bit.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

A feladatok előzményeit a rendszer automatikusan törli a feladatok törlésekor.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Feltesz egy lépést egy feladatokba.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] step_name ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_name =** ] "job_name"  
Annak a feladattípusnak a neve, amelyhez hozzá szeretné adni a lépést. job_name nvarchar (128).

[ **\@ step_id =** ] step_id  
A feladatütemezés sorszáma. A Step Identification Numbers értéke 1, és hézagok nélkül növekszik. Ha egy meglévő lépés már rendelkezik ezzel az AZONOSÍTÓval, akkor a lépés és az összes következő lépés megnövekszik, hogy az új lépés beilleszthető legyen a sorba. Ha nincs megadva, a rendszer automatikusan hozzárendeli a step_id az utolsó lépésekhez a lépések sorrendjében. step_id egy int.

[ **\@ step_name =** ] step_name  
A lépés neve. Meg kell adni, kivéve egy olyan feladatokhoz tartozó első lépést, amely (a kényelem kedvéért) alapértelmezett neve "JobStep". step_name nvarchar (128).

[ **\@ command_type =** ] "command_type"  
A jobstep által végrehajtott parancs típusa. command_type a nvarchar (50), amely a TSql alapértelmezett értéke, ami azt jelenti, hogy a @command_type paraméter értéke egy T-SQL-szkript.

Ha meg van adva, az értéknek TSql kell lennie.

[ **\@ command_source =** ] "command_source"  
A parancs tárolási helyének típusa. command_source a nvarchar (50), amelynek alapértelmezett értéke a beágyazott, ami azt jelenti, hogy a paraméter értéke a @command_source parancs literális szövege.

Ha meg van adva, az értéknek inline értékűnek kell lennie.

[ **\@ Command =** ] "parancs"  
A parancsnak érvényes T-SQL-parancsfájlnak kell lennie, és ezt a feladatot a művelet hajtja végre. a parancs nvarchar (max), alapértelmezett értéke NULL.

[ **\@ credential_name =** ] "credential_name"  
A feladathoz tartozó vezérlő adatbázisban tárolt adatbázis-hatókörű hitelesítő adat neve, amelyet a rendszer a jelen lépés végrehajtásakor a célcsoporthoz tartozó egyes adatbázisokhoz való kapcsolódáshoz használ. credential_name nvarchar (128).

[ **\@ target_group_name =** ] "Target-group_name"  
Annak a célcsoportnak a neve, amely azokat a célként megadott adatbázisokat tartalmazza, amelyeken a feladatütemezés végre lesz hajtva. target_group_name nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Az első újrapróbálkozási kísérlet előtti késleltetés, ha a feladat lépése sikertelen a kezdeti végrehajtási kísérlet során. a initial_retry_interval_seconds int, alapértelmezett értéke 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Az újrapróbálkozási kísérletek közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb mértékben növekedni fog, mint ez az érték, akkor ez az érték nem éri el a határértéket. a maximum_retry_interval_seconds int, alapértelmezett értéke 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Az újrapróbálkozási késleltetésre alkalmazandó szorzó, ha több feladat-végrehajtási kísérlet meghiúsul. Ha például az első újrapróbálkozás késleltetése 5 másodperc, a leállítási-szorzó pedig 2,0, akkor a második újrapróbálkozás 10 másodperces késleltetéssel fog rendelkezni, a harmadik újrapróbálkozás pedig 20 másodperces késleltetéssel fog rendelkezni. a retry_interval_backoff_multiplier értéke valós, a 2,0-as alapértelmezett értékkel.

[ **\@ retry_attempts =** ] retry_attempts  
A végrehajtás újrapróbálkozásának száma, ha a kezdeti kísérlet meghiúsul. Ha például a retry_attempts értéke 10, akkor 1 kezdeti kísérlet és 10 újrapróbálkozás kísérlet, amely összesen 11 próbálkozást eredményez. Ha a végső újrapróbálkozási kísérlet meghiúsul, akkor a feladat végrehajtása nem sikerült. a retry_attempts int, alapértelmezett értéke pedig 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
A lépés végrehajtásához engedélyezett maximális időtartam. Ha túllépi az időkorlátot, a feladatok végrehajtása a időtúllépés életciklusával leáll. a step_timeout_seconds int, alapértelmezett értéke 43 200 másodperc (12 óra).

[ **\@ output_type =** ] "output_type"  
Ha nem null értékre van állítva, a parancs első eredményhalmaz-készletének típusa. output_type a nvarchar (50), amelynek alapértelmezett értéke NULL.

Ha meg van adva, az értéknek SqlDatabase kell lennie.

[ **\@ output_credential_name =** ] "output_credential_name"  
Ha nem null értékű, a kimeneti céladatbázis kapcsolódásához használt adatbázis-hatókörű hitelesítő adat neve. Meg kell adni, ha output_type egyenlő SqlDatabase. output_credential_name nvarchar (128), alapértelmezett értéke NULL.

[ **\@ output_subscription_id =** ] "output_subscription_id"  
Leírás szükséges.

[ **\@ output_resource_group_name =** ] "output_resource_group_name"  
Leírás szükséges.

[ **\@ output_server_name =** ] "output_server_name"  
Ha nem null értékű, a kimeneti céladatbázis-adatbázist tartalmazó kiszolgáló teljesen minősített DNS-neve. Meg kell adni, ha output_type egyenlő SqlDatabase. output_server_name a nvarchar (256), amelynek alapértelmezett értéke NULL.

[ **\@ output_database_name =** ] "output_database_name"  
Ha nem null értékű, a kimeneti célhelyet tartalmazó adatbázis neve. Meg kell adni, ha output_type egyenlő SqlDatabase. output_database_name a nvarchar (128), amelynek alapértelmezett értéke NULL.

[ **\@ output_schema_name =** ] "output_schema_name"  
Ha nem null értékű, a kimeneti célhelyet tartalmazó SQL-séma neve. Ha output_type egyenlő SqlDatabase, az alapértelmezett érték a dbo. output_schema_name nvarchar (128).

[ **\@ output_table_name =** ] "output_table_name"  
Ha nem null értékre van állítva, annak a táblának a neve, amelyet a parancs első eredményhalmaz-készlete fog írni. Ha a tábla még nem létezik, a rendszer a visszaadott eredményhalmaz sémája alapján hozza létre. Meg kell adni, ha output_type egyenlő SqlDatabase. output_table_name nvarchar (128), alapértelmezett értéke NULL.

[ **\@ job_version =** ] job_version kimenet  
A kimeneti paraméter, amely az új feladathoz tartozó verziószámot fogja rendelni. job_version int.

[ **\@ max_parallelism =** ] max_parallelism kimenet  
A rugalmas készlet maximális párhuzamossági szintje. Ha be van állítva, akkor a feladattípus csak legfeljebb ennyi adatbázison futhat, rugalmas készleten. Ez minden olyan rugalmas készletre vonatkozik, amely közvetlenül szerepel a célcsoportban, vagy a célcsoport részét képező kiszolgálón belül van. max_parallelism int.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

Ha sp_add_jobstep sikeres, a rendszer megnöveli a feladatokhoz tartozó aktuális verziószámot. A művelet következő futtatásakor a rendszer az új verziót fogja használni. Ha a feladatot jelenleg hajtja végre, a végrehajtás nem fogja tartalmazni az új lépést.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:  

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Frissíti a feladatok lépéseit.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @new_id = ] new_id ]
     [ , [ @new_name = ] 'new_name' ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_name =** ] "job_name"  
Annak a feladattípusnak a neve, amelyhez a lépés tartozik. job_name nvarchar (128).

[ **\@ step_id =** ] step_id  
A módosítandó feladatokhoz tartozó azonosító száma. Meg kell adni step_id vagy step_name értéket. step_id egy int.

[ **\@ step_name =** ] "step_name"  
A módosítandó lépés neve. Meg kell adni step_id vagy step_name értéket. step_name nvarchar (128).

[ **\@ new_id =** ] new_id  
A feladatütemezés új sorszáma. A Step Identification Numbers értéke 1, és hézagok nélkül növekszik. Ha egy lépés átrendezése megtörténik, a rendszer a többi lépést is automatikusan újraszámozza.

[ **\@ new_name =** ] "new_name"  
A lépés új neve. new_name nvarchar (128).

[ **\@ command_type =** ] "command_type"  
A jobstep által végrehajtott parancs típusa. command_type a nvarchar (50), amely a TSql alapértelmezett értéke, ami azt jelenti, hogy a @command_type paraméter értéke egy T-SQL-szkript.

Ha meg van adva, az értéknek TSql kell lennie.

[ **\@ command_source =** ] "command_source"  
A parancs tárolási helyének típusa. command_source a nvarchar (50), amelynek alapértelmezett értéke a beágyazott, ami azt jelenti, hogy a paraméter értéke a @command_source parancs literális szövege.

Ha meg van adva, az értéknek inline értékűnek kell lennie.

[ **\@ Command =** ] "parancs"  
A parancs (ok) nak érvényes T-SQL-parancsfájlnak kell lennie, és ezt a feladatot a művelet hajtja végre. a parancs nvarchar (max), alapértelmezett értéke NULL.

[ **\@ credential_name =** ] "credential_name"  
A feladathoz tartozó vezérlő adatbázisban tárolt adatbázis-hatókörű hitelesítő adat neve, amelyet a rendszer a jelen lépés végrehajtásakor a célcsoporthoz tartozó egyes adatbázisokhoz való kapcsolódáshoz használ. credential_name nvarchar (128).

[ **\@ target_group_name =** ] "Target-group_name"  
Annak a célcsoportnak a neve, amely azokat a célként megadott adatbázisokat tartalmazza, amelyeken a feladatütemezés végre lesz hajtva. target_group_name nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Az első újrapróbálkozási kísérlet előtti késleltetés, ha a feladat lépése sikertelen a kezdeti végrehajtási kísérlet során. a initial_retry_interval_seconds int, alapértelmezett értéke 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Az újrapróbálkozási kísérletek közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb mértékben növekedni fog, mint ez az érték, akkor ez az érték nem éri el a határértéket. a maximum_retry_interval_seconds int, alapértelmezett értéke 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Az újrapróbálkozási késleltetésre alkalmazandó szorzó, ha több feladat-végrehajtási kísérlet meghiúsul. Ha például az első újrapróbálkozás késleltetése 5 másodperc, a leállítási-szorzó pedig 2,0, akkor a második újrapróbálkozás 10 másodperces késleltetéssel fog rendelkezni, a harmadik újrapróbálkozás pedig 20 másodperces késleltetéssel fog rendelkezni. a retry_interval_backoff_multiplier értéke valós, a 2,0-as alapértelmezett értékkel.

[ **\@ retry_attempts =** ] retry_attempts  
A végrehajtás újrapróbálkozásának száma, ha a kezdeti kísérlet meghiúsul. Ha például a retry_attempts értéke 10, akkor 1 kezdeti kísérlet és 10 újrapróbálkozás kísérlet, amely összesen 11 próbálkozást eredményez. Ha a végső újrapróbálkozási kísérlet meghiúsul, akkor a feladat végrehajtása nem sikerült. a retry_attempts int, alapértelmezett értéke pedig 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
A lépés végrehajtásához engedélyezett maximális időtartam. Ha túllépi az időkorlátot, a feladatok végrehajtása a időtúllépés életciklusával leáll. a step_timeout_seconds int, alapértelmezett értéke 43 200 másodperc (12 óra).

[ **\@ output_type =** ] "output_type"  
Ha nem null értékre van állítva, a parancs első eredményhalmaz-készletének típusa. Ha vissza szeretné állítani a output_type értékét NULL értékre, állítsa a paraméter értékét "" értékre (üres karakterlánc). output_type a nvarchar (50), amelynek alapértelmezett értéke NULL.

Ha meg van adva, az értéknek SqlDatabase kell lennie.

[ **\@ output_credential_name =** ] "output_credential_name"  
Ha nem null értékű, a kimeneti céladatbázis kapcsolódásához használt adatbázis-hatókörű hitelesítő adat neve. Meg kell adni, ha output_type egyenlő SqlDatabase. Ha vissza szeretné állítani a output_credential_name értékét NULL értékre, állítsa a paraméter értékét "" értékre (üres karakterlánc). output_credential_name nvarchar (128), alapértelmezett értéke NULL.

[ **\@ output_server_name =** ] "output_server_name"  
Ha nem null értékű, a kimeneti céladatbázis-adatbázist tartalmazó kiszolgáló teljesen minősített DNS-neve. Meg kell adni, ha output_type egyenlő SqlDatabase. Ha vissza szeretné állítani a output_server_name értékét NULL értékre, állítsa a paraméter értékét "" értékre (üres karakterlánc). output_server_name a nvarchar (256), amelynek alapértelmezett értéke NULL.

[ **\@ output_database_name =** ] "output_database_name"  
Ha nem null értékű, a kimeneti célhelyet tartalmazó adatbázis neve. Meg kell adni, ha output_type egyenlő SqlDatabase. Ha vissza szeretné állítani a output_database_name értékét NULL értékre, állítsa a paraméter értékét "" értékre (üres karakterlánc). output_database_name a nvarchar (128), amelynek alapértelmezett értéke NULL.

[ **\@ output_schema_name =** ] "output_schema_name"  
Ha nem null értékű, a kimeneti célhelyet tartalmazó SQL-séma neve. Ha output_type egyenlő SqlDatabase, az alapértelmezett érték a dbo. Ha vissza szeretné állítani a output_schema_name értékét NULL értékre, állítsa a paraméter értékét "" értékre (üres karakterlánc). output_schema_name nvarchar (128).

[ **\@ output_table_name =** ] "output_table_name"  
Ha nem null értékre van állítva, annak a táblának a neve, amelyet a parancs első eredményhalmaz-készlete fog írni. Ha a tábla még nem létezik, a rendszer a visszaadott eredményhalmaz sémája alapján hozza létre. Meg kell adni, ha output_type egyenlő SqlDatabase. Ha vissza szeretné állítani a output_server_name értékét NULL értékre, állítsa a paraméter értékét "" értékre (üres karakterlánc). output_table_name nvarchar (128), alapértelmezett értéke NULL.

[ **\@ job_version =** ] job_version kimenet  
A kimeneti paraméter, amely az új feladathoz tartozó verziószámot fogja rendelni. job_version int.

[ **\@ max_parallelism =** ] max_parallelism kimenet  
A rugalmas készlet maximális párhuzamossági szintje. Ha be van állítva, akkor a feladattípus csak legfeljebb ennyi adatbázison futhat, rugalmas készleten. Ez minden olyan rugalmas készletre vonatkozik, amely közvetlenül szerepel a célcsoportban, vagy a célcsoport részét képező kiszolgálón belül van. Ha vissza szeretné állítani a max_parallelism értékét NULL értékre, állítsa a paraméter értékét-1 értékre. max_parallelism int.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

A rendszer nem érinti a feladatok folyamatban lévő végrehajtását. Ha sp_update_jobstep sikeres, a rendszer megnöveli a feladattípus verziószámát. A művelet következő futtatásakor a rendszer az új verziót fogja használni.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez

### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Eltávolít egy feladatot a feladatokból.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_name =** ] "job_name"  
Annak a feladattípusnak a neve, amelyből a lépés el lesz távolítva. job_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ step_id =** ] step_id  
A törlendő feladatokhoz tartozó azonosító száma. Meg kell adni step_id vagy step_name értéket. step_id egy int.

[ **\@ step_name =** ] "step_name"  
A törlendő lépés neve. Meg kell adni step_id vagy step_name értéket. step_name nvarchar (128).

[ **\@ job_version =** ] job_version kimenet  
A kimeneti paraméter, amely az új feladathoz tartozó verziószámot fogja rendelni. job_version int.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

A rendszer nem érinti a feladatok folyamatban lévő végrehajtását. Ha sp_update_jobstep sikeres, a rendszer megnöveli a feladattípus verziószámát. A művelet következő futtatásakor a rendszer az új verziót fogja használni.

A rendszer automatikusan újraszámozza a többi feladatot, hogy kitöltse a törölt feladatok lépésének bal oldali térközét.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

A feladatok végrehajtásának megkezdése.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_name =** ] "job_name"  
Annak a feladattípusnak a neve, amelyből a lépés el lesz távolítva. job_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ job_execution_id =** ] job_execution_id kimenet  
Kimeneti paraméter, amely a feladatok végrehajtásának AZONOSÍTÓját fogja rendelni. job_version a uniqueidentifier.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

Nincsenek.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Leállítja a feladatok végrehajtását.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_execution_id =** ] job_execution_id  
A leállítani kívánt feladatok azonosítójának száma. job_execution_id uniqueidentifier, alapértelmezett értéke NULL.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

Nincsenek.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Célcsoport hozzáadására szolgál.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ target_group_name =** ] "target_group_name"  
A létrehozandó célcsoport neve. target_group_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ target_group_id =** ] target_group_id a feladathoz hozzárendelt célcsoport-azonosító számot sikeresen létrehozva. target_group_id uniqueidentifier típusú kimeneti változó, amelynek alapértelmezett értéke NULL.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

A célcsoportok egyszerű módszert biztosítanak a feladatok adatbázis-gyűjteményen való megcélzására.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Egy célcsoport törlése.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```

#### <a name="arguments"></a>Argumentumok

[ **\@ target_group_name =** ] "target_group_name"  
A törlendő cél csoport neve. target_group_name nvarchar (128), alapértelmezés szerint nincs megadva.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

Nincsenek.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Adatbázis vagy adatbázis-csoport hozzáadását egy célcsoporthoz.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]
        [ , [ @target_type = ] 'target_type' ]
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]
        [ , [ @server_name = ] 'server_name' ]
        [ , [ @database_name = ] 'database_name' ]
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]
        [ , [ @shard_map_name = ] 'shard_map_name' ]
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok

[ **\@ target_group_name =** ] "target_group_name"  
Annak a célcsoportnak a neve, amelyhez a tag hozzá lesz adva. target_group_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ membership_type =** ] "membership_type"  
Megadja, hogy a célcsoport tagja belefoglalt vagy kizárva lesz-e. target_group_name a nvarchar (128), amelynek alapértelmezett értéke a "include". A membership_type érvényes értékei a következők: "include" vagy "kizár".

[ **\@ target_type =** ] "target_type"  
A céladatbázis vagy adatbázisok gyűjteményének típusa, beleértve a kiszolgálókon lévő összes adatbázist, a rugalmas készletben lévő összes adatbázist, a szegmensben található összes adatbázist vagy egy adott adatbázist. target_type nvarchar (128), alapértelmezés szerint nincs megadva. A target_type érvényes értékei: "SqlServer", "SqlElasticPool", "SqlDatabase" vagy "SqlShardMap".

[ **\@ refresh_credential_name =** ] "refresh_credential_name"  
Az adatbázis-hatókörű hitelesítő adat neve. refresh_credential_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ server_name =** ] "server_name"  
Annak a kiszolgálónak a neve, amelyet fel kell venni a megadott célcsoportba. server_name kell megadni, ha a target_type "SqlServer". server_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ database_name =** ] "database_name"  
Annak az adatbázisnak a neve, amelyet fel kell venni a megadott célcsoportba. database_name kell megadni, ha a target_type "SqlDatabase". database_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ elastic_pool_name =** ] "elastic_pool_name"  
Annak a rugalmas készletnek a neve, amelyet hozzá kell adni a megadott célcsoporthoz. elastic_pool_name kell megadni, ha a target_type "SqlElasticPool". elastic_pool_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ shard_map_name =** ] "shard_map_name"  
A megadott célcsoporthoz hozzáadandó szegmenses leképezési készlet neve. elastic_pool_name kell megadni, ha a target_type "SqlShardMap". shard_map_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ **\@ target_id =** ] target_group_id kimenet  
A célcsoport tagja számára hozzárendelt cél azonosító szám, ha a csoport hozzá lett adva. target_id uniqueidentifier típusú kimeneti változó, amelynek alapértelmezett értéke NULL.
Visszatérési kód értéke 0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

A feladatok a kiszolgálókon vagy egy rugalmas készleten belül, a végrehajtás időpontjában futnak, amikor egy kiszolgáló vagy rugalmas készlet szerepel a célcsoportban.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

#### <a name="examples"></a>Példák

A következő példa hozzáadja az összes adatbázist a londoni és a NewYork-kiszolgálóról a csoport kiszolgálóira az ügyféladatok fenntartása érdekében. A feladat-ügynök létrehozásakor megadott feladatok adatbázishoz kell csatlakoznia, ebben az esetben a ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs;
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information';
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'refresh_credential', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net';
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'refresh_credential', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net';
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

A célcsoport tagjainak eltávolítása egy célcsoportból.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
   [ , [ @target_id = ] 'target_id']
```

#### <a name="arguments"></a>Argumentumok

[ @target_group_name =] "target_group_name"  
Annak a célcsoportnak a neve, amelyből el szeretné távolítani a célcsoport tagját. target_group_name nvarchar (128), alapértelmezés szerint nincs megadva.

[ @target_id =] target_id  
 Az eltávolítandó célcsoport-tag számára hozzárendelt cél azonosító szám. target_id egy uniqueidentifier, amelynek alapértelmezett értéke NULL.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

A célcsoportok egyszerű módszert biztosítanak a feladatok adatbázis-gyűjteményen való megcélzására.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

#### <a name="examples"></a>Példák

A következő példa eltávolítja a London-kiszolgálót a csoport kiszolgálóiról az ügyféladatok fenntartásával. A feladat-ügynök létrehozásakor megadott feladatok adatbázishoz kell csatlakoznia, ebben az esetben a ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net';

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid;
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Eltávolítja egy adott feladatokhoz tartozó előzményi rekordokat.

#### <a name="syntax"></a>Szintaxis

```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentumok

[ **\@ job_name =** ] "job_name"  
Annak a feladattípusnak a neve, amelyre vonatkozóan törölni szeretné az előzmények rekordjait. job_name a nvarchar (128), amelynek alapértelmezett értéke NULL. Meg kell adni job_id vagy job_nameot, de mindkettő nem adható meg.

[ **\@ job_id =** ] job_id  
 A törölni kívánt rekordok feladatainak feladata. job_id a uniqueidentifier, amelynek alapértelmezett értéke NULL. Meg kell adni job_id vagy job_nameot, de mindkettő nem adható meg.

[ **\@ oldest_date =** ] oldest_date  
 Az előzményekben megőrizni kívánt legrégebbi rekord. oldest_date a DATETIME2, amelynek alapértelmezett értéke NULL. Ha oldest_date van megadva, sp_purge_jobhistory csak a megadott értéknél régebbi rekordokat távolítja el.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések

A célcsoportok egyszerű módszert biztosítanak a feladatok adatbázis-gyűjteményen való megcélzására.

#### <a name="permissions"></a>Engedélyek

Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai futtathatják ezt a tárolt eljárást. Korlátozzák a felhasználókat, hogy csak a feladatok figyelésére legyenek képesek, a feladat ügynökének létrehozásakor megadott feladat-ügynök adatbázisában megadhatja a következő adatbázis-szerepkört:

- jobs_reader

A szerepkörök engedélyeivel kapcsolatos részletekért tekintse meg a jelen dokumentum engedélyek szakaszát. Csak a sysadmin (rendszergazda) tagok használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztéséhez.

#### <a name="examples"></a>Példák

A következő példa hozzáadja az összes adatbázist a londoni és a NewYork-kiszolgálóról a csoport kiszolgálóira az ügyféladatok fenntartása érdekében. A feladat-ügynök létrehozásakor megadott feladatok adatbázishoz kell csatlakoznia, ebben az esetben a ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```

## <a name="job-views"></a>Feladatok nézetei

A [feladatok adatbázisban](job-automation-overview.md#elastic-job-database)a következő nézetek érhetők el.

|Nézet  |Description  |
|---------|---------|
|[job_executions](#job_executions-view)     |  A feladatok végrehajtási előzményeit jeleníti meg.      |
|[feladatok](#jobs-view)     |   Megjeleníti az összes feladatot.      |
|[job_versions](#job_versions-view)     |   Megjeleníti az összes feladattípust.      |
|[feladatlépésnél](#jobsteps-view)     |     Megjeleníti az egyes feladatok aktuális verziójának összes lépését.    |
|[jobstep_versions](#jobstep_versions-view)     |     Megjeleníti az egyes feladatok összes verziójának összes lépését.    |
|[target_groups](#target_groups-view)     |      Megjeleníti az összes célcsoportot.   |
|[target_group_members](#target_group_members-view)     |   Megjeleníti az összes célcsoport összes tagját.      |

### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions nézet

[feladatok]. [job_executions]

A feladatok végrehajtási előzményeit jeleníti meg.

|Oszlop neve | Adattípus | Leírás |
|---------|---------|---------|
|**job_execution_id** | uniqueidentifier | A feladatok végrehajtásának egy példányának egyedi azonosítója.
|**job_name** | nvarchar (128) | A feladattípus neve.
|**job_id** | uniqueidentifier | A feladatokhoz tartozó egyedi azonosító.
|**job_version** | int | A feladatokhoz tartozó verzió (a feladatok minden módosításakor automatikusan frissülnek).
|**step_id** |int | A lépéshez tartozó egyedi (ehhez a feladathoz) azonosító. NULL érték azt jelzi, hogy ez a fölérendelt feladatok végrehajtása.
|**is_active** | bit | Azt jelzi, hogy az adatok aktívak vagy inaktívak-e. 1 az aktív feladatokat jelöli, a 0 pedig inaktív értéket jelez.
|**életciklus** | nvarchar (50) | A (z) "létrehozva", "folyamatban", "sikertelen", "sikeres", "kihagyott", "SucceededWithSkipped" művelet állapotát jelző érték.|
|**create_time**| datetime2 (7) | A feladatok létrehozásának dátuma és időpontja.
|**start_time** | datetime2 (7) | A feladatok végrehajtásának dátuma és időpontja. NULL, ha a feladatot még nem hajtották végre.
|**end_time** | datetime2 (7) | A feladatok végrehajtásának dátuma és időpontja. NULL, ha a feladatot még nem hajtották végre, vagy még nem végezte el a végrehajtást.
|**current_attempts** | int | A lépés újrapróbálkozásának száma. A fölérendelt feladatoknak 0, a alárendelt feladatok végrehajtása a végrehajtási házirend alapján 1 vagy nagyobb lesz.
|**current_attempt_start_time** | datetime2 (7) | A feladatok végrehajtásának dátuma és időpontja. NULL érték azt jelzi, hogy ez a fölérendelt feladatok végrehajtása.
|**last_message** | nvarchar (max.) | A feladatok vagy lépések előzményeinek üzenete.
|**target_type** | nvarchar (128) | A céladatbázis vagy adatbázisok gyűjteményének típusa, beleértve a kiszolgáló összes adatbázisát, egy rugalmas készletben vagy adatbázisban lévő összes adatbázist. A target_type érvényes értékei: "SqlServer", "SqlElasticPool" vagy "SqlDatabase". NULL érték azt jelzi, hogy ez a fölérendelt feladatok végrehajtása.
|**target_id** | uniqueidentifier | A célcsoport-tag egyedi azonosítója.  NULL érték azt jelzi, hogy ez a fölérendelt feladatok végrehajtása.
|**target_group_name** | nvarchar (128) | A célcsoport neve. NULL érték azt jelzi, hogy ez a fölérendelt feladatok végrehajtása.
|**target_server_name** | nvarchar (256)  | A célcsoportban található kiszolgáló neve. Csak akkor van megadva, ha target_type "SqlServer". NULL érték azt jelzi, hogy ez a fölérendelt feladatok végrehajtása.
|**target_database_name** | nvarchar (128) | A célcsoportban található adatbázis neve. Csak akkor van megadva, ha target_type "SqlDatabase". NULL érték azt jelzi, hogy ez a fölérendelt feladatok végrehajtása.

### <a name="jobs-view"></a>feladatok nézet

[feladatok]. feladatok

Megjeleníti az összes feladatot.

|Oszlop neve | Adattípus |Leírás|
|------|------|-------|
|**job_name** | nvarchar (128) | A feladattípus neve.|
|**job_id**| uniqueidentifier |A feladatokhoz tartozó egyedi azonosító.|
|**job_version** |int |A feladatokhoz tartozó verzió (a feladatok minden módosításakor automatikusan frissülnek).|
|**Leírás** |nvarchar (512)| A feladatokhoz tartozó Leírás Engedélyezett bit: azt jelzi, hogy a feladatok engedélyezve vagy le vannak-e tiltva. 1 az engedélyezett feladatokat jelzi, a 0 pedig letiltott feladatokat jelez.|
|**schedule_interval_type**|nvarchar (50) |Az érték, amely azt jelzi, hogy mikor kell végrehajtani a feladatot: "ONCE", "Minutes", "Hours", "Days", "Weeks", "months"
|**schedule_interval_count**|int|A feladatok egyes végrehajtásai közötti schedule_interval_type időszakok száma.|
|**schedule_start_time**|datetime2 (7)|A művelet utolsó indításának dátuma és időpontja.|
|**schedule_end_time**|datetime2 (7)|A művelet utolsó befejezett végrehajtásának dátuma és időpontja.|

### <a name="job_versions-view"></a><a name="job_versions-view"></a>job_versions nézet

[feladatok]. [job_versions]

Megjeleníti az összes feladattípust.

|Oszlop neve|Adattípus|Leírás|
|------|------|-------|
|**job_name**|nvarchar (128)|A feladattípus neve.|
|**job_id**|uniqueidentifier|A feladatokhoz tartozó egyedi azonosító.|
|**job_version**|int|A feladatokhoz tartozó verzió (a feladatok minden módosításakor automatikusan frissülnek).|

### <a name="jobsteps-view"></a>feladatlépésnél nézet

[feladatok]. feladatlépésnél

Megjeleníti az egyes feladatok aktuális verziójának összes lépését.

|Oszlop neve|Adattípus|Leírás|
|------|------|-------|
|**job_name**|nvarchar (128)|A feladattípus neve.|
|**job_id**|uniqueidentifier|A feladatokhoz tartozó egyedi azonosító.|
|**job_version**|int|A feladatokhoz tartozó verzió (a feladatok minden módosításakor automatikusan frissülnek).|
|**step_id**|int|A lépéshez tartozó egyedi (ehhez a feladathoz) azonosító.|
|**step_name**|nvarchar (128)|Egyedi (ehhez a feladatokhoz) a lépéshez tartozó név.|
|**command_type**|nvarchar (50)|A feladattípusban végrehajtandó parancs típusa. A v1 esetében az értéknek és az alapértelmezett értéknek a "TSql" értéket kell megadnia.|
|**command_source**|nvarchar (50)|A parancs helye. A v1 esetében a "inline" az alapértelmezett és az egyetlen elfogadott érték.|
|**parancs**|nvarchar (max.)|A rugalmas feladatokkal command_type használatával végrehajtandó parancsok.|
|**credential_name**|nvarchar (128)|A feladatok végrehajtásához használt adatbázis-hatókörű hitelesítő adat neve.|
|**target_group_name**|nvarchar (128)|A célcsoport neve.|
|**target_group_id**|uniqueidentifier|A célcsoport egyedi azonosítója.|
|**initial_retry_interval_seconds**|int|Az első újrapróbálkozási kísérlet előtti késleltetés. Az alapértelmezett érték 1.|
|**maximum_retry_interval_seconds**|int|Az újrapróbálkozási kísérletek közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb mértékben növekedni fog, mint ez az érték, akkor ez az érték nem éri el a határértéket. Az alapértelmezett érték a 120.|
|**retry_interval_backoff_multiplier**|valós szám|Az újrapróbálkozási késleltetésre alkalmazandó szorzó, ha több feladat-végrehajtási kísérlet meghiúsul. Az alapértelmezett érték a 2,0.|
|**retry_attempts**|int|Az újrapróbálkozási kísérletek száma, ha ez a lépés meghiúsul. Az alapértelmezett érték 10, amely nem kísérli meg az újrapróbálkozási kísérleteket.|
|**step_timeout_seconds**|int|Az újrapróbálkozási kísérletek közötti idő (percben). Az alapértelmezett érték 0, amely 0 perces intervallumot jelez.|
|**output_type**|nvarchar (11)|A parancs helye. Az aktuális előzetes verzióban a "inline" az alapértelmezett és az egyetlen elfogadott érték.|
|**output_credential_name**|nvarchar (128)|A célkiszolgálóra való kapcsolódáshoz használandó hitelesítő adatok neve az eredményhalmaz tárolásához.|
|**output_subscription_id**|uniqueidentifier|A lekérdezés végrehajtásának eredményeihez megadott cél server\database előfizetés egyedi azonosítója.|
|**output_resource_group_name**|nvarchar (128)|Az erőforráscsoport neve, ahol a célkiszolgáló található.|
|**output_server_name**|nvarchar (256)|A célkiszolgáló neve az eredmény-készlethez.|
|**output_database_name**|nvarchar (128)|Az eredményhalmaz céladatbázis neve.|
|**output_schema_name**|nvarchar (max.)|A célként megadott séma neve. Ha nincs megadva, az alapértelmezett érték a dbo.|
|**output_table_name**|nvarchar (max.)|Annak a táblának a neve, amely az eredményeket a lekérdezés eredményei alapján tárolja. A tábla automatikusan létrejön, az eredmények készletének sémája alapján, ha még nem létezik. A sémának meg kell egyeznie az eredmények készletének sémájával.|
|**max_parallelism**|int|Egy rugalmas készletben lévő adatbázisok maximális száma, amelyet a feladattípus egyszerre fog futni. Az alapértelmezett érték NULL, ami nincs korlátozva. |

### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions nézet

[feladatok]. [jobstep_versions]

Megjeleníti az egyes feladatok összes verziójának összes lépését. A séma megegyezik a [feladatlépésnél](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups nézet

[feladatok]. [target_groups]

Felsorolja az összes célcsoportot.

|Oszlop neve|Adattípus|Leírás|
|-----|-----|-----|
|**target_group_name**|nvarchar (128)|A célcsoport neve, adatbázisok gyűjteménye.
|**target_group_id**|uniqueidentifier|A célcsoport egyedi azonosítója.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members nézet

[feladatok]. [target_group_members]

Megjeleníti az összes célcsoport összes tagját.

|Oszlop neve|Adattípus|Leírás|
|-----|-----|-----|
|**target_group_name**|nvarchar (128|A célcsoport neve, adatbázisok gyűjteménye. |
|**target_group_id**|uniqueidentifier|A célcsoport egyedi azonosítója.|
|**membership_type**|int|Megadja, hogy a célcsoport tagja szerepel-e a célcsoportban, vagy ki van-e zárva. A target_group_name érvényes értékei a következők: "include" vagy "kizár".|
|**target_type**|nvarchar (128)|A céladatbázis vagy adatbázisok gyűjteményének típusa, beleértve a kiszolgáló összes adatbázisát, egy rugalmas készletben vagy adatbázisban lévő összes adatbázist. A target_type érvényes értékei: "SqlServer", "SqlElasticPool", "SqlDatabase" vagy "SqlShardMap".|
|**target_id**|uniqueidentifier|A célcsoport-tag egyedi azonosítója.|
|**refresh_credential_name**|nvarchar (128)|A célcsoport tagjához való kapcsolódáshoz használt adatbázis-hatókörű hitelesítő adat neve.|
|**subscription_id**|uniqueidentifier|Az előfizetés egyedi azonosítója.|
|**resource_group_name**|nvarchar (128)|Azon erőforráscsoport neve, amelyben a célcsoport tagja található.|
|**server_name**|nvarchar (128)|A célcsoportban található kiszolgáló neve. Csak akkor van megadva, ha target_type "SqlServer". |
|**database_name**|nvarchar (128)|A célcsoportban található adatbázis neve. Csak akkor van megadva, ha target_type "SqlDatabase".|
|**elastic_pool_name**|nvarchar (128)|A célcsoportban található rugalmas készlet neve. Csak akkor van megadva, ha target_type "SqlElasticPool".|
|**shard_map_name**|nvarchar (128)|A célcsoportban lévő szegmens térképek neve. Csak akkor van megadva, ha target_type "SqlShardMap".|

## <a name="resources"></a>Források

- ![Témakör hivatkozás ikon](/sql/database-engine/configure-windows/media/topic-link.gif "Témakör hivatkozásának ikonja") [Transact-SQL szintaxisának konvenciói](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  

## <a name="next-steps"></a>Következő lépések

- [Rugalmas feladatok létrehozása és kezelése a PowerShell-lel](elastic-jobs-powershell-create.md)
- [Engedélyezés és engedélyek](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
