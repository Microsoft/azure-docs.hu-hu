---
title: 'PowerShell: SQL Server migrálása a felügyelt SQL-példányra offline'
titleSuffix: Azure Database Migration Service
description: A Azure PowerShell és a Azure Database Migration Service használatával megtudhatja, hogyan lehet offline migrálni az SQL Serverról az Azure SQL felügyelt példányára.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697809"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>SQL Server migrálása az SQL felügyelt példányára a PowerShell-& kapcsolat nélkül Azure Database Migration Service

Ez a cikk azt ismerteti, hogy a **Adventureworks2016** -adatbázis a SQL Server 2005-es vagy újabb helyszíni példányára lett visszaállítva egy Azure SQL SQL felügyelt példányra Microsoft Azure PowerShell használatával. Az adatbázisokat áttelepítheti egy SQL Server példányból egy SQL felügyelt példányra a `Az.DataMigration` Microsoft Azure PowerShell moduljának használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Hozzon létre egy erőforráscsoportot.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service egy példányában.
> * Futtassa az áttelepítést offline állapotba.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk az offline áttelepítéssel kapcsolatos lépéseket ismerteti, de az is lehetséges, hogy [online](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md)is telepíthető.


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


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be Microsoft Azure-előfizetésbe

Jelentkezzen be az Azure-előfizetésbe a PowerShell használatával. További információkért tekintse meg a következő cikket: [bejelentkezés Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik.

Hozzon létre egy erőforráscsoportot a [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) parancs használatával.

A következő példában létrehozunk egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Egy Azure Database Migration Service-példány létrehozása

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Adatbázis-kapcsolati információs objektum létrehozása a forrás és a cél kapcsolatokhoz

A parancsmag használatával létrehozhat egy adatbázis-elérhetőségi adatobjektumot `New-AzDmsConnInfo` , amely a következő paramétereket várja:

* *ServerType*. A kért adatbázis-kapcsolatok típusa, például az SQL, az Oracle vagy a MySQL. Az SQL használata a SQL Server és az Azure SQL használatához.
* *Adatforrás*. SQL Server példány vagy Azure SQL Database példány neve vagy IP-címe.
* *AuthType*. A kapcsolatok hitelesítési típusa, amely lehet SqlAuthentication vagy WindowsAuthentication.
* *TrustServerCertificate*. Ezzel a paraméterrel megadható egy érték, amely jelzi, hogy a csatorna titkosítva van-e, miközben megkerüli a megbízhatósági kapcsolat ellenőrzéséhez szükséges tanúsítványláncot Az érték lehet `$true` vagy `$false` .

Az alábbi példa egy *MySourceSQLServer* nevű, a forráshoz SQL Server, SQL-hitelesítéssel elnevezett kapcsolattípus-objektumot hoz létre:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

A következő példa egy "targetmanagedinstance" nevű Azure SQL felügyelt példányhoz tartozó kapcsolatbiztonsági adatok létrehozását mutatja be:

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Adatbázisok biztosítása az áttelepítési projekthez

Hozzon létre egy olyan `AzDataMigrationDatabaseInfo` objektumot, amely az adatbázisokat a Azure Database Migration Service projekt részeként adja meg, amely a projekt létrehozására szolgáló paraméterként is megadható. A létrehozásához használhatja a parancsmagot `New-AzDataMigrationDatabaseInfo` `AzDataMigrationDatabaseInfo` .

A következő példa létrehozza a `AzDataMigrationDatabaseInfo` projektet a **AdventureWorks2016** -adatbázishoz, és hozzáadja azt a listához, amelyet paraméterként kíván megadni a projekt létrehozásához.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Projekt objektum létrehozása

Végezetül létrehozhat egy *MyDMSProject* nevű Azure Database Migration Service projektet, amely az *USA keleti* régiójában található, `New-AzDataMigrationProject` és hozzáadja a korábban létrehozott forrás-és cél-kapcsolatokat, valamint az áttelepítendő adatbázisok listáját.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Áttelepítési feladat létrehozása és elindítása

Ezután hozzon létre és indítson el egy Azure Database Migration Service feladatot. Ez a feladat a forrás és a cél kapcsolati hitelesítő adatait, valamint az áttelepítendő adatbázistáblák listáját, valamint az előfeltételként létrehozott projekttel már megadott adatokat is megköveteli.

### <a name="create-credential-parameters-for-source-and-target"></a>Hitelesítőadat-paraméterek létrehozása a forrás és a cél számára

Hozzon létre kapcsolatbiztonsági hitelesítő adatokat [PSCredential](/dotnet/api/system.management.automation.pscredential) objektumként.

Az alábbi példa az *PSCredential* objektumok létrehozását mutatja be mind a forrás-, mind a cél kapcsolathoz, és a jelszavakat karakterlánc-változókként *$sourcePassword* és *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Biztonsági mentési fájlmegosztás objektum létrehozása

Most hozzon létre egy fájlmegosztás objektumot, amely a helyi SMB hálózati megosztást jelképezi, amelyhez Azure Database Migration Service a parancsmag használatával elvégezheti a forrás-adatbázis biztonsági mentését `New-AzDmsFileShare` .

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Kiválasztott adatbázis-objektum létrehozása

A következő lépés a forrás-és cél-adatbázisok kiválasztása a parancsmag használatával `New-AzDmsSelectedDB` .

Az alábbi példa egy önálló adatbázis SQL Serverból egy Azure SQL felügyelt példányra való áttelepítését szemlélteti:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Ha egy teljes SQL Server-példánynak egy felügyelt Azure SQL-példányra van szüksége, akkor az összes adatbázis a forrásból való elvégzéséhez egy hurkot kell megadnia. A következő példában $Server, $SourceUserName és $SourcePassword esetében adja meg a forrás SQL Server részleteit.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>SAS URI az Azure Storage-tárolóhoz

Hozzon létre egy olyan változót, amely tartalmazza a SAS URI-t, amely hozzáférést biztosít a Azure Database Migration Service számára a Storage-fiók tárolójához, amelyhez a szolgáltatás feltölti a biztonságimásolat-fájlokat.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> A Azure Database Migration Service nem támogatja a fiók szintű SAS-token használatát. A Storage-fiók tárolóhoz SAS URI-t kell használnia. [Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>További konfigurációs követelmények

Néhány további követelményt is el kell végeznie:


* **Válassza a bejelentkezések lehetőséget**. Hozza létre az áttelepítendő bejelentkezések listáját az alábbi példában látható módon:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > A Azure Database Migration Service jelenleg csak az SQL-bejelentkezések áttelepítését támogatja.

* **Válassza ki az ügynök feladatait**. Az áttelepíteni kívánt ügynök-feladatok listájának létrehozása az alábbi példában látható módon:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > A Azure Database Migration Service jelenleg csak a T-SQL alrendszer feladat lépéseit támogató feladatokat támogatja.



### <a name="create-and-start-the-migration-task"></a>Az áttelepítési feladat létrehozása és elindítása

Az `New-AzDataMigrationTask` áttelepítési feladat létrehozásához és elindításához használja a parancsmagot.

#### <a name="specify-parameters"></a>Paraméterek megadása

A `New-AzDataMigrationTask` parancsmag a következő paramétereket várja:

* *TaskType*. A rendszer az Azure SQL felügyelt példány áttelepítési típusának *MigrateSqlServerSqlDbMi* SQL Server létrehozásához szükséges áttelepítési feladat típusát adja meg. 
* *Erőforráscsoport neve*. Azon Azure-erőforráscsoport neve, amelyben létre kívánja hozni a feladatot.
* *Szolgáltatásnév*. Azure Database Migration Service példány, amelyben létre kívánja hozni a feladatot.
* *Projektnév*. Azure Database Migration Service projekt neve, amelyben létre kívánja hozni a feladatot. 
* *Feladatnév*. A létrehozandó feladat neve. 
* *SourceConnection*. A forrás-SQL Server kapcsolatokat jelképező AzDmsConnInfo objektum.
* *TargetConnection*. A célként megadott Azure SQL felügyelt példány-kapcsolatokat jelképező AzDmsConnInfo objektum.
* *SourceCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential) objektum a forráskiszolgálón való csatlakozáshoz.
* *TargetCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential) objektum a célkiszolgálóra való csatlakozáshoz.
* *SelectedDatabase*. A forrás-és céladatbázis-leképezést jelképező AzDataMigrationSelectedDB objektum.
* *BackupFileShare*. Az a helyi hálózati megosztást jelképező fájlmegosztás objektum, amelyet a Azure Database Migration Service a forrás-adatbázis biztonsági másolatait felhasználva.
* *BackupBlobSasUri*. Az SAS URI-ja, amely hozzáférést biztosít a Azure Database Migration Service számára a Storage-fiók tárolójához, amelyhez a szolgáltatás feltölti a biztonságimásolat-fájlokat. Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját.
* *SelectedLogins*. Az áttelepítendő kiválasztott bejelentkezések listája.
* *SelectedAgentJobs*. Az áttelepítendő kiválasztott ügynök-feladatok listája.
* *SelectedLogins*. Az áttelepítendő kiválasztott bejelentkezések listája.
* *SelectedAgentJobs*. Az áttelepítendő kiválasztott ügynök-feladatok listája.



#### <a name="create-and-start-a-migration-task"></a>Áttelepítési feladat létrehozása és elindítása

Az alábbi példa egy **myDMSTask** nevű offline áttelepítési feladatot hoz létre és indít el:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```


## <a name="monitor-the-migration"></a>A migrálás monitorozása

Az áttelepítés figyeléséhez hajtsa végre a következő feladatokat.

1. Összevonja az összes áttelepítési részletet egy $CheckTask nevű változóba.

    Az áttelepítéssel kapcsolatos adatok, például a tulajdonságok, az állapot és az adatbázis-információk összevonásához használja az alábbi kódrészletet:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Használja a `$CheckTask` változót az áttelepítési feladat aktuális állapotának lekéréséhez.

    Ha a `$CheckTask` változót az áttelepítési feladat aktuális állapotának lekéréséhez szeretné használni, a feladat állapot tulajdonságának lekérdezésével figyelheti a futtatott áttelepítési feladatot, ahogy az az alábbi példában is látható:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```


## <a name="delete-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service példányának törlése

Az áttelepítés befejezése után törölheti az Azure Database Migration Service példányt:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>Következő lépések

Tudjon meg többet a Azure Database Migration Serviceről a cikk [Mi a Azure Database Migration Service?](./dms-overview.md)című cikkben.

További információkat a további áttelepítési forgatókönyvekről (forrás/cél párok) a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/)talál.