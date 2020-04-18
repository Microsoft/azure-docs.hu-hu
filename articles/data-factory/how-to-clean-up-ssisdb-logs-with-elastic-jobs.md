---
title: SSISDB-naplók karbantartása az Azure rugalmas adatbázis-feladatokkal
description: Ez a cikk azt ismerteti, hogyan lehet az SSISDB-naplók at az Azure Elastic Database-feladatok használatával megtisztítani az erre a célra létező tárolt eljárást.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/13/2018
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 02952c3baea5d9089061b10f2429be57a9322398
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606180"
---
# <a name="clean-up-ssisdb-logs-with-azure-elastic-database-jobs"></a>SSISDB-naplók karbantartása az Azure rugalmas adatbázis-feladatokkal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogy miként aktiválható az Azure Elastic Database Jobs a tárolt `SSISDB`eljárás elindításához, amely törli az SQL Server Integration Services katalógusadatbázisának naplóit.

A rugalmas adatbázis-feladatok egy Azure-szolgáltatás, amely megkönnyíti a feladatok automatizálását és futtatását egy adatbázison vagy adatbázisok egy csoportján. Ezeket a feladatokat az Azure Portal, a Transact-SQL, a PowerShell vagy a REST API-k használatával ütemezheti, futtathatja és figyelheti. A rugalmas adatbázis-feladat segítségével indítsa el a tárolt eljárást a napló karbantartása egy alkalommal vagy ütemezés szerint. Az ssisdb-erőforrás-használat alapján kiválaszthatja az ütemezési időközt a nagy adatbázis-betöltés elkerülése érdekében.

További információ: [Adatbáziscsoportok kezelése rugalmas adatbázis-feladatokkal.](../sql-database/elastic-jobs-overview.md)

A következő szakaszok ismertetik, hogyan `[internal].[cleanup_server_retention_window_exclusive]`kell elindítani a tárolt eljárást , amely eltávolítja a rendszergazda által beállított megőrzési ablakon kívül lévő SSISDB-naplókat.

## <a name="clean-up-logs-with-power-shell"></a>Naplók karbantartása a Power Shell segítségével

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

A következő minta PowerShell-parancsfájlok hozzon létre egy új rugalmas feladat az SSISDB-napló karbantartása tárolt eljárás aktiválásához. További információ: [Rugalmas feladatügynök létrehozása a PowerShell használatával című témakörben.](../sql-database/elastic-jobs-powershell.md)

### <a name="create-parameters"></a>Paraméterek létrehozása

``` powershell
# Parameters needed to create the Job Database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL server(for example, yhxserver) to hold the SSISDBLogCleanup job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for the Job Database to be created in the given SQL Server"),
# The Job Database should be a clean,empty,S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create the Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your new Elastic Job agent"),

# Parameters needed to create the job credential in the Job Database to connect to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for SSISDBLogCleanup job user to connect to SSISDB database for log cleanup"),
# Parameters needed to create a login and a user in the SSISDB of the target server
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of the target Azure SQL server which contains SSISDB you need to cleanup, for example, myserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set job scheduling to trigger execution of cleanup stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run the job to cleanup SSISDB logs outside the log retention window immediately(Y/N). Make sure the retention window is set appropriately before running the following powershell scripts. Those removed SSISDB logs cannot be recoverd"),
$IntervalType = $(Read-Host "Please enter the interval type for the execution schedule of SSISDB log cleanup stored procedure. For the interval type, Year, Month, Day, Hour, Minute, Second can be supported."),
$IntervalCount = $(Read-Host "Please enter the detailed interval value in the given interval type for the execution schedule of SSISDB log cleanup stored procedure"),
# StartTime of the execution schedule is set as the current time as default. 
$StartTime = (Get-Date)
```

### <a name="trigger-the-cleanup-stored-procedure"></a>A karbantartás tárolt eljárásának aktiválása

```powershell
# Install the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force
# You may need to restart the powershell session
# Install the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Place AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create a Job Database which is used for defining jobs of triggering SSISDB log cleanup stored procedure and tracking cleanup history of jobs
Write-Output "Creating a blank SQL database to be used as the SSISDBLogCleanup  Job Database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable the Elastic Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create the Elastic Job agent
Write-Output "Creating the Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create the job credential in the Job Database to connect to SSISDB database in the target server for log cleanup
Write-Output "Creating job credential to connect to SSISDB database..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# In the master database of the target SQL server which contains SSISDB to cleanup 
# - Create the job user login
Write-Output "Grant permissions on the master database of the target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# For SSISDB database of the target SQL server
# - Create the SSISDBLogCleanup user from the SSISDBlogCleanup user login
# - Grant permissions for the execution of SSISDB log cleanup stored procedure 
Write-Output "Grant appropriate permissions on SSISDB database..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser"

$TargetDatabase | % {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create a target group which includes SSISDB database needed to cleanup
Write-Output "Creating the target group including only SSISDB database needed to cleanup ..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create the job to trigger execution of SSISDB log cleanup stored procedure
Write-Output "Creating a new job to trigger execution of the stored procedure for SSISDB log cleanup"
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add the job step to execute internal.cleanup_server_retention_window_exclusive
Write-Output "Adding the job step for the cleanup stored procedure execution"
$SqlText = "EXEC internal.cleanup_server_retention_window_exclusive"
$Job | Add-AzureRmSqlElasticJobStep -Name "step to execute cleanup stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run the job to immediately start cleanup stored procedure execution for once
IF(($RunJobOrNot = "Y") -Or ($RunJobOrNot = "y"))
{
Write-Output "Start a new execution of the stored procedure for SSISDB log cleanup immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule the job running to trigger stored procedure execution on schedule for removing SSISDB logs outside the retention window
Write-Output "Start the execution schedule of the stored procedure for SSISDB log cleanup..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

## <a name="clean-up-logs-with-transact-sql"></a>Naplók karbantartása a Transact-SQL-rel

A következő minta Transact-SQL parancsfájlok hozzon létre egy új rugalmas feladat, hogy indítsa el a tárolt eljárás SSISDB log cleanup. További információ: [A Transact-SQL (T-SQL) használata rugalmas adatbázis-feladatok létrehozásához és kezeléséhez.](../sql-database/elastic-jobs-tsql.md)

1. Hozzon létre vagy azonosítson egy üres S0 vagy újabb Azure SQL-adatbázist az SSISDBCleanup feladat-adatbázisként. Ezután hozzon létre egy rugalmas feladatügynök az [Azure Portalon.](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent)

2. A feladat-adatbázisban hozzon létre hitelesítő adatokat az SSISDB naplótisztítási feladathoz. Ez a hitelesítő adat az SSISDB-adatbázishoz való csatlakozáshoz használható a naplók karbantartásához.

    ```sql
    -- Connect to the job database specified when creating the job agent
    -- Create a database master key if one does not already exist, using your own password.  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';  

    -- Create a credential for SSISDB log cleanup.  
    CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
    ```

3. Adja meg azt a célcsoportot, amely tartalmazza azt az SSISDB-adatbázist, amelynek a karbantartástárolt eljárását futtatni szeretné.

    ```sql
    -- Connect to the job database 
    -- Add a target group 
    EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

    -- Add SSISDB database into the target group
    EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
    @target_type = 'SqlDatabase',
    @server_name = '<EnterSSISDBTargetServerName>',
    @database_name = '<EnterSSISDBName>'

    --View the recently created target group and target group members
    SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
    SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
    ```
4. Adja meg a megfelelő engedélyeket az SSISDB adatbázishoz. Az SSISDB-katalógusnak megfelelő engedélyekkel kell rendelkeznie ahhoz, hogy a tárolt eljárás sikeresen lefuttassa az SSISDB-napló karbantartását. Részletes útmutatásért [lásd: Bejelentkezések kezelése.](../sql-database/sql-database-manage-logins.md)

    ```sql
    -- Connect to the master database in the target server including SSISDB 
    CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

    -- Connect to SSISDB database in the target server to cleanup logs
    CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
    GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser
    ```
5. Hozza létre a feladatot, és adjon hozzá egy feladatlépést az SSISDB-napló karbantartásához a tárolt eljárás végrehajtásának elindításához.

    ```sql
    --Connect to the job database 
    --Add the job for the execution of SSISDB log cleanup stored procedure.
    EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs which are outside the retention window'

    --Add a job step to execute internal.cleanup_server_retention_window_exclusive
    EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
    @command=N'EXEC internal.cleanup_server_retention_window_exclusive',
    @credential_name='SSISDBLogCleanupCred',
    @target_group_name='SSISDBTargetGroup'
    ```
6. Mielőtt folytatna, győződjön meg arról, hogy a megőrzési ablak megfelelően van beállítva. Az ablakon kívüli SSISDB-naplók törlődnek, és nem lehet visszaőket tetszetős helyretenni.

   Ezután azonnal futtathatja a feladatot az SSISDB naplókarbantartás megkezdéséhez.

    ```sql
    --Connect to the job database 
    --Run the job immediately to execute the stored procedure for SSISDB log cleanup
    declare @je uniqueidentifier
    exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

    --Watch the execution results for SSISDB log cleanup 
    select @je
    select * from jobs.job_executions where job_execution_id = @je
    ```
7. Szükség esetén ütemezheti a feladatvégrehajtásokat, hogy az SSISDB-naplókat az adatmegőrzési időszakon kívül, ütemezés szerint távolítsa el. Hasonló utasítás használatával frissítheti a feladat paramétereit.

    ```sql
    --Connect to the job database 
    EXEC jobs.sp_update_job
    @job_name='CleanupSSISDBLog',
    @enabled=1,
    @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
    @schedule_interval_count='<EnterDetailedIntervalValue>',
    @schedule_start_time='<EnterProperStartTimeForSchedule>',
    @schedule_end_time='<EnterProperEndTimeForSchedule>'
    ```

## <a name="monitor-the-cleanup-job-in-the-azure-portal"></a>A törlési feladat figyelése az Azure Portalon

Figyelheti a törlési feladat végrehajtását az Azure Portalon. Minden egyes végrehajtásnál láthatja a feladat állapotát, kezdési és befejezési idejét.

![A törlési feladat figyelése az Azure Portalon](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png)

## <a name="monitor-the-cleanup-job-with-transact-sql"></a>A törlési feladat figyelése a Transact-SQL-el

A Transact-SQL használatával is megtekintheti a törlési feladat végrehajtási előzményeit.

```sql
--Connect to the job database 
--View all execution statuses for the job to cleanup SSISDB logs
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>További lépések

Az Azure-SSIS-integrációs futásidejű felügyeleti és figyelési feladatokról az alábbi cikkekben. Az Azure-SSIS IR az SSISDB-ben az Azure SQL Database-ben tárolt SSIS-csomagok futásidejű motorja.

-   [Az Azure-SSIS Integration Runtime újrakonfigurálása](manage-azure-ssis-integration-runtime.md)

-   [Figyelje az Azure-SSIS-integrációs futásidőt.](monitor-integration-runtime.md#azure-ssis-integration-runtime)
