---
title: 'PowerShell: SQL Server migrálása az SQL felügyelt példányára online'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan telepítheti át a SQL Serverról az Azure SQL felügyelt példányára Azure PowerShell és a Azure Database Migration Service használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697796"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>SQL Server migrálása az SQL felügyelt példányára online a PowerShell-& Azure Database Migration Service

Ebben a cikkben online áttelepíti a **Adventureworks2016** -adatbázist a SQL Server 2005-es vagy újabb helyszíni példányára egy Azure SQL SQL felügyelt példányra Microsoft Azure PowerShell használatával. Az adatbázisokat áttelepítheti egy SQL Server példányból egy SQL felügyelt példányra a `Az.DataMigration` Microsoft Azure PowerShell moduljának használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Hozzon létre egy erőforráscsoportot.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service egy példányában.
> * Futtassa az áttelepítést online.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk egy online áttelepítés lépéseit ismerteti, de az is lehetséges, hogy [kapcsolat nélküli módban](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)is telepíthető.


## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a következőkre lesz szüksége:

* [SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (bármely kiadás).
* A **AdventureWorks2016** -adatbázis egy helyi példánya, amely [innen](/sql/samples/adventureworks-install-configure)tölthető le.
* A TCP/IP protokoll engedélyezéséhez, amely alapértelmezés szerint le van tiltva SQL Server Express telepítéssel. Engedélyezze a TCP/IP protokollt a [kiszolgáló hálózati protokolljának engedélyezése vagy letiltása](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)című cikkben leírtak szerint.
* A [Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* A felügyelt SQL-példányok. A felügyelt SQL-példányok létrehozásához kövesse a [ASQL felügyelt példány létrehozása](../azure-sql/managed-instance/instance-create-quickstart.md)című cikk részleteit.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 vagy újabb verzió letöltése és telepítése.
* A Azure Resource Manager üzemi modellel létrehozott Microsoft Azure Virtual Network, amely a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával biztosítja Azure Database Migration Service a helyek közötti kapcsolatot a helyszíni forráskiszolgálóról.
* A helyszíni adatbázis és a séma áttelepítésének befejezett értékelése Data Migration Assistant használatával, a [SQL Server áttelepítési felmérés végrehajtása](/sql/dma/dma-assesssqlonprem)című cikkben leírtak szerint.
* A `Az.DataMigration` modul (0.7.2 vagy újabb verzió) letöltése és telepítése a PowerShell-Galéria az [install-Module PowerShell-parancsmag](/powershell/module/powershellget/Install-Module)használatával.
* Győződjön meg arról, hogy a forrás SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkeznek a [vezérlési kiszolgáló](/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel.
* Annak biztosítása érdekében, hogy a cél SQL felügyelt példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek a VEZÉRLÉSi adatbázis engedéllyel a célként megadott SQL felügyelt példány adatbázisain.

    > [!IMPORTANT]
    > Online áttelepítéshez már be kell állítania a Azure Active Directory hitelesítő adatait. További információkért tekintse meg az [erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatás létrehozása a portál használatával](../active-directory/develop/howto-create-service-principal-portal.md)című cikket.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik.

Hozzon létre egy erőforráscsoportot a [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) parancs használatával.

A következő példában létrehozunk egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>DMS-példány létrehozása

Azure Database Migration Service új példányát a parancsmag használatával hozhatja létre `New-AzDataMigrationService` .
Ez a parancsmag a következő szükséges paramétereket várja:

* *Azure-erőforráscsoport neve*. A [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) paranccsal létrehozhat egy Azure-erőforráscsoportot a korábban bemutatott módon, és paraméterként megadhatja a nevét.
* *Szolgáltatás neve*. A Azure Database Migration Service kívánt egyedi szolgáltatásnév megfelelő karakterlánc.
* *Hely*. Megadja a szolgáltatás helyét. Itt adhatja meg az Azure-beli adatközpont helyét, például az USA nyugati régióját vagy Délkelet-Ázsiában.
* *SKU*. Ez a paraméter a DMS SKU-nevének felel meg. A jelenleg támogatott SKU-nevek *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Virtuális alhálózati azonosító*. Alhálózat létrehozásához használhatja a parancsmagot [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

A következő példa egy *MyDMS* nevű szolgáltatást hoz létre az *USA keleti* régiójában, a *MyVNET* nevű virtuális hálózat és egy *MySubnet* nevű alhálózat használatával. 


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Azure Database Migration Service példány létrehozása után hozzon létre egy áttelepítési projektet. Egy Azure Database Migration Service projekthez szükség van a forrás-és a célhely kapcsolati adataira, valamint a projekt részeként áttelepíteni kívánt adatbázisok listájára.
Forrás-és cél kapcsolati karakterláncok definiálása.

A következő parancsfájl a forrás-SQL Server kapcsolat részleteit definiálja: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

A következő parancsfájl a célként megadott SQL felügyelt példány kapcsolati részleteit határozza meg: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Forrás-és céladatbázis-hozzárendelés definiálása

Az áttelepítési projektben áttelepítendő adatbázisok megadása

A következő parancsfájl leképezi a forrás-adatbázist a megfelelő új adatbázisra a cél SQL felügyelt példányon a megadott névvel.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

Több adatbázis esetében adja hozzá az adatbázisok listáját a fenti szkripthez a következő formátumban:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>DMS-projekt létrehozása

Létrehozhat egy Azure Database Migration Service projektet a DMS-példányon belül.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Biztonsági mentési fájlmegosztás objektum létrehozása

Most hozzon létre egy fájlmegosztás objektumot, amely a helyi SMB hálózati megosztást jelképezi, amelyhez Azure Database Migration Service a New-AzDmsFileShare parancsmag használatával elvégezheti a forrás-adatbázis biztonsági mentését.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Az Azure Storage megadása 

Válassza ki az áttelepítéshez használni kívánt Azure Storage-tárolót: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Azure Active Directory alkalmazás konfigurálása

Adja meg az online SQL felügyelt példányok áttelepítésének Azure Active Directory szükséges adatait: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Áttelepítési feladat létrehozása és elindítása

Ezután hozzon létre és indítson el egy Azure Database Migration Service feladatot. Meghívja a forrást és a célt változók használatával, és listázza az áttelepítendő adatbázis-táblákat: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

Az alábbi példa egy online áttelepítési feladatot hoz létre és indít el: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

További információ: [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>A migrálás monitorozása

Az áttelepítés figyeléséhez hajtsa végre a következő feladatokat.

### <a name="check-the-status-of-task"></a>A feladat állapotának megkeresése

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

A következő paranccsal kérheti le a hibák listáját: –

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>A átváltás végrehajtása 

Az online áttelepítés során az adatbázisok teljes biztonsági mentését és visszaállítását hajtja végre, majd a BackupFileShare tárolt tranzakciónaplók visszaállításával folytatja a munkát.

Ha az Azure SQL felügyelt példányának adatbázisa frissül a legújabb adatokkal, és szinkronizálva van a forrás-adatbázissal, átváltás is elvégezheti.

A következő példa végrehajtja a cutover\migration. A felhasználó saját belátása szerint hívja meg ezt a parancsot.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service példányának törlése

Az áttelepítés befejezése után törölheti az Azure Database Migration Service példányt:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>További források

További információkat a további áttelepítési forgatókönyvekről (forrás/cél párok) a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/)talál.

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet a Azure Database Migration Serviceről a cikk [Mi a Azure Database Migration Service?](./dms-overview.md)című cikkben.