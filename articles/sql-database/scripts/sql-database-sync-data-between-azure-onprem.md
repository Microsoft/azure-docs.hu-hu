---
title: PowerShell példa-Szinkronizálás az SQL Database és az SQL Server között a helyszínen
description: Azure PowerShell-példaszkript egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: d8fa054bc418fc3230c1a69fb2767faadc59a9a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74421875"
---
# <a name="use-powershell-to-sync-between-a-sql-database-and-a-sql-server-on-premises-database"></a>A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz

Ez a PowerShell-példa egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz konfigurálja a Data Syncet. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az AZ PowerShell 1.4.0-s vagy újabb szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](../sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL Data Sync jelenleg nem támogatja az Azure SQL Database felügyelt példánya.

## <a name="prerequisites"></a>Előfeltételek

- Azure SQL-adatbázis létrehozása az AdventureWorksLT mintaadatbázisból központi adatbázisként
- Hozzon létre egy Azure SQL-adatbázist ugyanabban a régióban, mint a szinkronizálási adatbázis
- Helyszíni SQL Server-adatbázis létrehozása tagadatbázisként
- A paraméter helyőrzőinek frissítése a példa futtatása előtt

## <a name="example"></a>Példa

```powershell-interactive
using namespace Microsoft.Azure.Commands.Sql.DataSync.Model
using namespace System.Collections.Generic

# hub database info
$subscriptionId = "<subscriptionId>"
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$databaseName = "<databaseName>"

# sync database info
$syncDatabaseResourceGroupName = "<syncResourceGroupName>"
$syncDatabaseServerName = "<syncServerName>"
$syncDatabaseName = "<syncDatabaseName>"

# sync group info
$syncGroupName = "<syncGroupName>"
$conflictResolutionPolicy = "HubWin" # can be HubWin or MemberWin
$intervalInSeconds = 300 # sync interval in seconds (must be no less than 300)

# member database info
$syncMemberName = "<syncMemberName>"
$memberServerName = "<memberServerName>"
$memberDatabaseName = "<memberDatabaseName>"
$memberDatabaseType = "SqlServerDatabase" # can be AzureSqlDatabase or SqlServerDatabase
$syncDirection = "Bidirectional" # can be Bidirectional, Onewaymembertohub, Onewayhubtomember

# sync agent info
$syncAgentName = "<agentName>"
$syncAgentResourceGroupName = "<syncAgentResourceGroupName>"
$syncAgentServerName = "<syncAgentServerName>"

# temp file to save the sync schema
$tempFile = $env:TEMP+"\syncSchema.json"

# list of included columns and tables in quoted name
$includedColumnsAndTables =  "[SalesLT].[Address].[AddressID]",
                             "[SalesLT].[Address].[AddressLine2]",
                             "[SalesLT].[Address].[rowguid]",
                             "[SalesLT].[Address].[PostalCode]",
                             "[SalesLT].[ProductDescription]"
$metadataList = [System.Collections.ArrayList]::new($includedColumnsAndTables)

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

# use if it's safe to show password in script, otherwise use PromptForCredential
# $user = "username"
# $password = ConvertTo-SecureString -String "password" -AsPlainText -Force
# $credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$serverName+".database.windows.net",
              "",
              "")

# create a new sync agent
Write-Host "Creating new Sync Agent..."
New-AzSqlSyncAgent -ResourceGroupName $resourceGroupName -ServerName  $serverName -SyncDatabaseName $syncDatabaseName -SyncAgentName $syncAgentName

# generate agent key
Write-Host "Generating Agent Key..."
$agentKey = New-AzSqlSyncAgentKey -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName
Write-Host "Use your agent key to configure the sync agent. Do this before proceeding."
$agentkey

# DO THE FOLLOWING BEFORE THE NEXT STEP
# Install the on-premises sync agent on your machine and register the sync agent using the agent key generated above to bring the sync agent online.
# Add the SQL server database information including server name, database name, user name, password on the configuration tool within the sync agent.  

# create a new sync group
Write-Host "Creating Sync Group "$syncGroupName"..."
New-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Name $syncGroupName `
    -SyncDatabaseName $syncDatabaseName -SyncDatabaseServerName $syncDatabaseServerName -SyncDatabaseResourceGroupName $syncDatabaseResourceGroupName `
    -ConflictResolutionPolicy $conflictResolutionPolicy -DatabaseCredential $credential

# use if it's safe to show password in script, otherwise use PromptForCredential
#$user = "username"
#$password = ConvertTo-SecureString -String "password" -AsPlainText -Force
#$credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$memberServerName,
              "",
              "")

# get information from sync agent and confirm your SQL Server was configured (note the database ID to use for the sqlServerDatabaseID in the next step)
$syncAgentInfo = Get-AzSqlSyncAgentLinkedDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName

# add a new sync member
Write-Host "Adding member"$syncMemberName" to the sync group..."

New-AzSqlSyncMember -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
    -SyncGroupName $syncGroupName -Name $syncMemberName -MemberDatabaseType $memberDatabaseType -SyncAgentResourceGroupName $syncAgentResourceGroupName `
    -SyncAgentServerName $syncAgentServerName -SyncAgentName $syncAgentName  -SyncDirection $syncDirection -SqlServerDatabaseID  $syncAgentInfo.DatabaseId

# refresh database schema from hub database, specify the -SyncMemberName parameter if you want to refresh schema from the member database
Write-Host "Refreshing database schema from hub database..."
$startTime = Get-Date
Update-AzSqlSyncSchema -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# waiting for successful refresh
$startTime = $startTime.ToUniversalTime()
$timer=0
$timeout=90

# check the log and see if refresh has gone through
Write-Host "Check for successful refresh..."
$isSucceeded = $false
while ($isSucceeded -eq $false) {
    Start-Sleep -s 10
    $timer=$timer+10
    $details = Get-AzSqlSyncSchema -SyncGroupName $syncGroupName -ServerName $serverName -DatabaseName $databaseName -ResourceGroupName $resourceGroupName
    if ($details.LastUpdateTime -gt $startTime) {
        Write-Host "Refresh was successful"
        $isSucceeded = $true
    }
    if ($timer -eq $timeout) {
        Write-Host "Refresh timed out"
        break;
    }
}

# get the database schema
Write-Host "Adding tables and columns to the sync schema..."
$databaseSchema = Get-AzSqlSyncSchema -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
    -DatabaseName $DatabaseName -SyncGroupName $SyncGroupName `

$databaseSchema | ConvertTo-Json -depth 5 -Compress | Out-File "C:\Users\OnPremiseServer\AppData\Local\Temp\syncSchema.json"

$newSchema = [AzureSqlSyncGroupSchemaModel]::new()
$newSchema.Tables = [List[AzureSqlSyncGroupSchemaTableModel]]::new();

# add columns and tables to the sync schema
foreach ($tableSchema in $databaseSchema.Tables) {
    $newTableSchema = [AzureSqlSyncGroupSchemaTableModel]::new()
    $newTableSchema.QuotedName = $tableSchema.QuotedName
    $newTableSchema.Columns = [List[AzureSqlSyncGroupSchemaColumnModel]]::new();
    $addAllColumns = $false
    if ($MetadataList.Contains($tableSchema.QuotedName)) {
        if ($tableSchema.HasError) {
            $fullTableName = $tableSchema.QuotedName
            Write-Host "Can't add table $fullTableName to the sync schema" -foregroundcolor "Red"
            Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            continue;
        }
        else {
            $addAllColumns = $true
        }
    }
    foreach($columnSchema in $tableSchema.Columns) {
        $fullColumnName = $tableSchema.QuotedName + "." + $columnSchema.QuotedName
        if ($addAllColumns -or $MetadataList.Contains($fullColumnName)) {
            if ((-not $addAllColumns) -and $tableSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            }
            elseif ((-not $addAllColumns) -and $columnSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $columnSchema.ErrorId -foregroundcolor "Red"
            }
            else {
                Write-Host "Adding"$fullColumnName" to the sync schema"
                $newColumnSchema = [AzureSqlSyncGroupSchemaColumnModel]::new()
                $newColumnSchema.QuotedName = $columnSchema.QuotedName
                $newColumnSchema.DataSize = $columnSchema.DataSize
                $newColumnSchema.DataType = $columnSchema.DataType
                $newTableSchema.Columns.Add($newColumnSchema)
            }
        }
    }
    if ($newTableSchema.Columns.Count -gt 0) {
        $newSchema.Tables.Add($newTableSchema)
    }
}

# convert sync schema to JSON format
$schemaString = $newSchema | ConvertTo-Json -depth 5 -Compress

# workaround a powershell bug
$schemaString = $schemaString.Replace('"Tables"', '"tables"').Replace('"Columns"', '"columns"').Replace('"QuotedName"', '"quotedName"').Replace('"MasterSyncMemberName"','"masterSyncMemberName"')

# save the sync schema to a temp file
$schemaString | Out-File $tempFile

# update sync schema
Write-Host "Updating the sync schema..."
Update-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName `
    -DatabaseName $databaseName -Name $syncGroupName -Schema $tempFile

$syncLogStartTime = Get-Date

# trigger sync manually
Write-Host "Trigger sync manually..."
Start-AzSqlSyncGroupSync -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# check the sync log and wait until the first sync succeeded
Write-Host "Check the sync log..."
$isSucceeded = $false
for ($i = 0; ($i -lt 300) -and (-not $isSucceeded); $i = $i + 10) {
    Start-Sleep -s 10
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            if ($syncLog.Details.Contains("Sync completed successfully")) {
                Write-Host $syncLog.TimeStamp : $syncLog.Details
                $isSucceeded = $true
            }
        }
    }
}

if ($isSucceeded) {
    # enable scheduled sync
    Write-Host "Enable the scheduled sync with 300 seconds interval..."
    Update-AzSqlSyncGroup  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -Name $syncGroupName -IntervalInSeconds $intervalInSeconds
}
else {
    # output all log if sync doesn't succeed in 300 seconds
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            Write-Host $syncLog.TimeStamp : $syncLog.Details
        }
    }
}
```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Remove-AzResourceGroup -ResourceGroupName $syncDatabaseResourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzSQLSyncAgent](/powershell/module/az.sql/New-azSqlSyncAgent) |  Új szinkronizációs ügynök létrehozása |
| [Új-AzSQLSyncAgentKey](/powershell/module/az.sql/New-azSqlSyncAgentKey) |  A szinkronizációs ügynökhöz tartozó ügynökkulcs létrehozása |
| [Get-AzSQLSyncAgentLinkedDatabase](/powershell/module/az.sql/Get-azSqlSyncAgentLinkedDatabase) |  A szinkronizációs ügynökhöz kapcsolódó összes információ lekérése |
| [Új-AzSQLSyncMember](/powershell/module/az.sql/New-azSqlSyncMember) |  Új tag hozzáadása a szinkronizálási csoporthoz |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/Update-azSqlSyncSchema) |  Az adatbázisséma-információk frissítése |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/Get-azSqlSyncSchema) |  Az adatbázisséma-információk lekérése |
| [Update-AzSQLSyncGroup](/powershell/module/az.sql/Update-azSqlSyncGroup) |  A szinkronizálási csoport frissítése |
| [Start-AzSQLSyncGroupSync](/powershell/module/az.sql/Start-azSqlSyncGroupSync) | A szinkronizálás aktiválása |
| [Get-AzSQLSyncGroupLog](/powershell/module/az.sql/Get-azSqlSyncGroupLog) |  A szinkronizálási napló ellenőrzése |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről [az Azure PowerShell dokumentációjában](/powershell/azure/overview) talál további információt.

Az [Azure SQL Database PowerShell-szkriptekben](../sql-database-powershell-samples.md) további SQL Database PowerShell-szkriptminták találhatók.

További információ az SQL Data Syncről:

- Áttekintés – [Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az Azure SQL Data Sync segítségével](../sql-database-sync-data.md)
- Adatszinkronizálás beállítása
    - A portálon – [Oktatóanyag: Az SQL Data Sync beállítása az Adatok szinkronizálásához az Azure SQL Database és a helyszíni SQL Server között](../sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        - [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](sql-database-sync-data-between-sql-databases.md)
- Adatszinkronizálási ügynök – [Adatszinkronizálási ügynök az Azure SQL Data Sync szolgáltatáshoz](../sql-database-data-sync-agent.md)
- Gyakorlati tanácsok – [Gyakorlati tanácsok az Azure SQL Data Sync szolgáltatáshoz](../sql-database-best-practices-data-sync.md)
- Figyelő – [SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival](../sql-database-sync-monitor-oms.md)
- Hibaelhárítás – [Az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](../sql-database-troubleshoot-data-sync.md)
- A szinkronizálási séma frissítése
    - Transact-SQL - [A sémamódosítások replikációjának automatizálása az Azure SQL Data Sync-ben](../sql-database-update-sync-schema.md)
    - A PowerShell használatával – [A PowerShell használatával frissítse a szinkronizálási sémát egy meglévő szinkronizálási csoportban](sql-database-sync-update-schema.md)

További információ az SQL Database-ről:

- [Az SQL Database áttekintése](../sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
