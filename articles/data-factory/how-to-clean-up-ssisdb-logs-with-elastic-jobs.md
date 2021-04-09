---
title: SSISDB-naplók karbantartása az Azure Elastic Database-feladatokkal
description: Ez a cikk azt ismerteti, hogyan lehet megtisztítani a SSISDB-naplókat az Azure rugalmas adatbázis-feladatok használatával az erre a célra használt tárolt eljárás elindításához
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ecabbf88e14d17ac912065c2ed4aa95316efaf9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387645"
---
# <a name="clean-up-ssisdb-logs-with-azure-elastic-database-jobs"></a>SSISDB-naplók karbantartása az Azure Elastic Database-feladatokkal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan használható az Azure Elastic Database-feladatok a SQL Server Integration Services Catalog-adatbázis naplófájljainak tisztítására szolgáló tárolt eljárás elindításához `SSISDB` .

Elastic Database feladatok egy Azure-szolgáltatás, amely megkönnyíti a feladatok automatizálását és futtatását egy adatbázison vagy egy adatbázis-csoporton. Ezeket a feladatokat a Azure Portal, a Transact-SQL, a PowerShell vagy a REST API-k használatával ütemezheti, futtathatja és figyelheti. A Elastic Database feladat használatával aktiválhatja a napló-karbantartási műveletet egy alkalommal vagy egy ütemezett időpontban. Az SSISDB erőforrás-használat alapján kiválaszthatja az ütemezett időközt, hogy elkerülje a nagy mennyiségű adatbázis terhelését.

További információ: [adatbázisok csoportjainak kezelése Elastic Database feladatokkal](../azure-sql/database/elastic-jobs-overview.md).

A következő szakaszok azt ismertetik, hogyan kell elindítani a tárolt eljárást `[internal].[cleanup_server_retention_window_exclusive]` , amely eltávolítja a rendszergazda által beállított adatmegőrzési időszakon kívüli SSISDB-naplókat.

## <a name="clean-up-logs-with-power-shell"></a>Naplók törlése a Power shellben

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Az alábbi PowerShell-parancsfájlok új rugalmas feladatot hoznak létre a SSISDB-napló karbantartásához tárolt eljárás elindításához. További információ: [rugalmas feladatok ügynökének létrehozása a PowerShell használatával](../azure-sql/database/elastic-jobs-powershell-create.md).

### <a name="create-parameters"></a>Paraméterek létrehozása

``` powershell
# Parameters needed to create the Job Database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing logical SQL server(for example, yhxserver) to hold the SSISDBLogCleanup job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for the Job Database to be created in the given SQL Server"),
# The Job Database should be a clean,empty,S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create the Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your new Elastic Job agent"),

# Parameters needed to create the job credential in the Job Database to connect to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for SSISDBLogCleanup job user to connect to SSISDB database for log cleanup"),
# Parameters needed to create a login and a user in the SSISDB of the target server
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of the target logical SQL server which contains SSISDB you need to cleanup, for example, myserver") + '.database.windows.net',
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

### <a name="trigger-the-cleanup-stored-procedure"></a>A karbantartási tárolt eljárás elindítása

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

## <a name="clean-up-logs-with-transact-sql"></a>Naplók törlése a Transact-SQL-sel

A következő példa Transact-SQL-szkriptek új rugalmas feladatot hoznak létre a SSISDB-napló karbantartására szolgáló tárolt eljárás elindításához. További információ: [a Transact-SQL (T-SQL) használata Elastic Database feladatok létrehozásához és kezeléséhez](../azure-sql/database/elastic-jobs-tsql-create-manage.md).

1. Hozzon létre vagy azonosítson egy üres S0 vagy magasabb Azure SQL Database, hogy a SSISDBCleanup-feladatok adatbázisa legyen. Ezután hozzon létre egy rugalmas feladatot a [Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent).

2. A feladathoz tartozó adatbázisban hozzon létre egy hitelesítő adatot a SSISDB napló-karbantartási feladathoz. Ezt a hitelesítő adatot a rendszer a SSISDB-adatbázishoz való kapcsolódáshoz használja a naplók tisztításához.

    ```sql
    -- Connect to the job database specified when creating the job agent
    -- Create a database master key if one does not already exist, using your own password.  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';  

    -- Create a credential for SSISDB log cleanup.  
    CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
    ```

3. Adja meg azt a SSISDB-adatbázist tartalmazó célcsoportot, amelynek a karbantartási tárolt eljárását futtatni kívánja.

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
4. Adja meg a megfelelő engedélyeket a SSISDB-adatbázishoz. A SSISDB-katalógusnak megfelelő engedélyekkel kell rendelkeznie ahhoz, hogy a tárolt eljárás sikeresen futtassa a SSISDB-napló karbantartását. Részletes útmutatásért lásd: [bejelentkezések kezelése](../azure-sql/database/logins-create-manage.md).

    ```sql
    -- Connect to the master database in the target server including SSISDB 
    CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

    -- Connect to SSISDB database in the target server to cleanup logs
    CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
    GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser
    ```
5. Hozza létre a feladatot, és adjon hozzá egy feladatot, amely elindítja a SSISDB-napló karbantartása tárolt eljárásának végrehajtását.

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
6. A folytatás előtt győződjön meg arról, hogy az adatmegőrzési időszak megfelelően be van állítva. Az ablakon kívüli SSISDB-naplók törlődnek, és nem állíthatók helyre.

   Ezután azonnal futtathatja a feladatot a SSISDB-napló karbantartásának megkezdéséhez.

    ```sql
    --Connect to the job database 
    --Run the job immediately to execute the stored procedure for SSISDB log cleanup
    declare @je uniqueidentifier
    exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

    --Watch the execution results for SSISDB log cleanup 
    select @je
    select * from jobs.job_executions where job_execution_id = @je
    ```
7. A feladatok végrehajtásának ütemezhető úgy is, hogy a SSISDB-naplókat egy ütemezett adatmegőrzési időszakon kívül is eltávolítsa. Használjon hasonló utasítást a feladatok paramétereinek frissítéséhez.

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

## <a name="monitor-the-cleanup-job-in-the-azure-portal"></a>A karbantartási feladatok figyelése a Azure Portal

A karbantartási feladatok végrehajtását a Azure Portal ellenőrizheti. Mindegyik végrehajtásnál megjelenik a feladatokhoz tartozó állapot, Kezdési idő és befejezési idő.

![A karbantartási feladatok figyelése a Azure Portal](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png)

## <a name="monitor-the-cleanup-job-with-transact-sql"></a>A karbantartási feladatok figyelése a Transact-SQL-sel

A Transact-SQL használatával is megtekintheti a karbantartási feladatok végrehajtási előzményeit.

```sql
--Connect to the job database 
--View all execution statuses for the job to cleanup SSISDB logs
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>Következő lépések

A Azure-SSIS Integration Runtime kapcsolatos felügyeleti és figyelési feladatokhoz tekintse meg a következő cikkeket. A Azure-SSIS IR a Azure SQL Database SSISDB tárolt SSIS-csomagok futásidejű motorja.

-   [Az Azure-SSIS Integration Runtime újrakonfigurálása](manage-azure-ssis-integration-runtime.md)

-   [Figyelje az Azure-SSIS integrációs](monitor-integration-runtime.md#azure-ssis-integration-runtime)modult.