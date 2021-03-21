---
title: 'PowerShell: az adatszinkronizálás SQL Database és SQL Server között'
description: Azure SQL Database és SQL Server közötti adatszinkronizáláshoz használjon Azure PowerShell példa parancsfájlt.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 443232bb41ba73b5bd02d45c542e555904f539db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792871"
---
# <a name="use-powershell-to-sync-data-between-sql-database-and-sql-server"></a>Az SQL Database és SQL Server közötti adatszinkronizálás a PowerShell használatával

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a Azure PowerShell például úgy konfigurálja az adatszinkronizálást, hogy szinkronizálja az adatAzure SQL Database és a SQL Server közötti adatszinkronizálást. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az az PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

A SQL-adatszinkronizálás áttekintését lásd: az [adatszinkronizálás több Felhőbeli és helyszíni adatbázis között a SQL-adatszinkronizálás im Azure-](../sql-data-sync-data-sql-server-sql-database.md)ban.

> [!IMPORTANT]
> A SQL-adatszinkronizálás jelenleg nem támogatja az Azure SQL felügyelt példányát.

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre egy adatbázist a Azure SQL Database egy AdventureWorksLT-mintaadatbázis központi adatbázisként.
- Hozzon létre egy adatbázist a Azure SQL Databaseban ugyanabban a régióban, mint a szinkronizálási adatbázist.
- Adatbázis létrehozása SQL Server-példányban tagként-adatbázisként.
- A példa futtatása előtt frissítse a paraméter helyőrzőit.

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
# Add the SQL Server database information including server name, database name, user name, password on the configuration tool within the sync agent.  

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

# get information from sync agent and confirm your SQL Server instance was configured (note the database ID to use for the sqlServerDatabaseID in the next step)
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

| Parancs | Jegyzetek |
|---|---|
| [Új – AzSqlSyncAgent](/powershell/module/az.sql/New-azSqlSyncAgent) |  Létrehoz egy új szinkronizáló ügynököt. |
| [Új – AzSqlSyncAgentKey](/powershell/module/az.sql/New-azSqlSyncAgentKey) |  Létrehozza a Szinkronizáló ügynökhöz társított ügynök kulcsát. |
| [Get-AzSqlSyncAgentLinkedDatabase](/powershell/module/az.sql/Get-azSqlSyncAgentLinkedDatabase) |  A szinkronizálási ügynök összes adatának beolvasása. |
| [Új – AzSqlSyncMember](/powershell/module/az.sql/New-azSqlSyncMember) |  Vegyen fel egy új tagot a szinkronizálási csoportba. |
| [Frissítés – AzSqlSyncSchema](/powershell/module/az.sql/Update-azSqlSyncSchema) |  Frissíti az adatbázis-séma információit. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/Get-azSqlSyncSchema) |  Az adatbázis-séma adatainak beolvasása. |
| [Frissítés – AzSqlSyncGroup](/powershell/module/az.sql/Update-azSqlSyncGroup) |  Frissíti a szinkronizálási csoportot. |
| [Start – AzSqlSyncGroupSync](/powershell/module/az.sql/Start-azSqlSyncGroupSync) | Elindítja a szinkronizálást. |
| [Get-AzSqlSyncGroupLog](/powershell/module/az.sql/Get-azSqlSyncGroupLog) |  Ellenőrzi a szinkronizálási naplót. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről [az Azure PowerShell dokumentációjában](/powershell/azure/) talál további információt.

Az [Azure SQL Database PowerShell-szkriptekben](../powershell-script-content-guide.md) további SQL Database PowerShell-szkriptminták találhatók.

További információ a SQL-adatszinkronizálásról:

- Áttekintés – az [adatszinkronizálás több felhőalapú és helyszíni adatbázis között az Azure SQL-adatszinkronizálás](../sql-data-sync-data-sql-server-sql-database.md)
- Adatszinkronizálás beállítása
    - A Azure Portal- [oktatóanyag: az SQL-adatszinkronizálás beállítása a Azure SQL Database és egy SQL Server helyszíni adatbázis közötti adatszinkronizáláshoz](../sql-data-sync-sql-server-configure.md)
    - A PowerShell használata a [Azure SQL Database több adatbázisa közötti szinkronizáláshoz a PowerShell](sql-data-sync-sync-data-between-sql-databases.md) használatával
- Adatszinkronizálási ügynök – [adatszinkronizálási ügynök SQL-adatszinkronizálás az Azure-ban](../sql-data-sync-agent-overview.md)
- Ajánlott eljárások – [ajánlott eljárások az Azure](../sql-data-sync-best-practices.md) -beli SQL-adatszinkronizálás
- Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](../monitor-tune-overview.md)
- Hibaelhárítás – [Az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](../sql-data-sync-troubleshoot.md)
- A szinkronizálási séma frissítése
    - Transact-SQL – [a séma változásainak automatizálása a SQL-adatszinkronizálás az Azure-ban](../sql-data-sync-update-sync-schema.md)
    - A PowerShell használata [a szinkronizálási séma egy meglévő szinkronizálási csoportban való frissítéséhez a PowerShell használatával](update-sync-schema-in-sync-group.md)

További információ a Azure SQL Databaseról:

- [SQL Database áttekintése](../sql-database-paas-overview.md)
- [Az adatbázis életciklusának felügyelete](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))