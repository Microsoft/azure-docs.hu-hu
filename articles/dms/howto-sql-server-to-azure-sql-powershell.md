---
title: 'PowerShell: SQL Server migrálása SQL Database'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan telepíthet át egy adatbázist SQL Serverról Azure SQL Databasere a Azure Database Migration Service Azure PowerShell használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a8f7e14500fb377b46f651b53e2704d8477aea7a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520659"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>SQL Server-adatbázis migrálása Azure SQL Databasera a Azure PowerShell használatával

Ebben a cikkben a **Adventureworks2012** -adatbázist a SQL Server 2016 vagy újabb rendszerű helyszíni példányra telepíti át a Microsoft Azure PowerShell használatával Azure SQL Database. Az adatbázisokat áttelepítheti egy SQL Server-példányról a Azure SQL Databasere a `Az.DataMigration` Microsoft Azure PowerShell moduljának használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Hozzon létre egy erőforráscsoportot.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Hozzon létre egy áttelepítési projektet egy Azure Database Migration Service-példányban.
> * A migrálás futtatása.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a következőkre lesz szüksége:

* [SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (bármely kiadás)
* A TCP/IP protokoll engedélyezéséhez, amely alapértelmezés szerint le van tiltva SQL Server Express telepítéssel. Engedélyezze a TCP/IP protokollt a [kiszolgáló hálózati protokolljának engedélyezése vagy letiltása](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)című cikkben leírtak szerint.
* A [Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Egy Azure SQL Database-példány. Azure SQL Database-példány létrehozásához kövesse az [Azure Portal-adatbázis létrehozása a Azure SQL Databaseban](../azure-sql/database/single-database-create-quickstart.md)című cikk részleteit.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3-as vagy újabb verzió.
* Ahhoz, hogy a Azure Resource Manager üzemi modellel létrehozott egy Microsoft Azure Virtual Network, amely Azure Database Migration Service a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával biztosítja a helyek közötti kapcsolatot a helyszíni forráskiszolgálóról.
* A helyszíni adatbázis és a séma áttelepítésének felmérése a Data Migration Assistant használatával a [SQL Server áttelepítési felmérés végrehajtása](/sql/dma/dma-assesssqlonprem) című cikkben leírtak szerint
* Az az. DataMigration modul letöltése és telepítése a PowerShell-galéria az [install-Module PowerShell-parancsmag](/powershell/module/powershellget/Install-Module)használatával; Győződjön meg arról, hogy a PowerShell-parancssorablakot a Futtatás rendszergazdaként paranccsal nyitja meg.
* Annak biztosítása érdekében, hogy a forrás SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok a [vezérlési kiszolgáló](/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel rendelkezzenek.
* Annak ellenőrzéséhez, hogy az Azure SQL DB-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkeznek-e a vezérlési adatbázis engedélyével a cél Azure SQL Database adatbázisain.
* Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be Microsoft Azure-előfizetésbe

Az Azure-előfizetéshez a PowerShell használatával történő bejelentkezéshez használja a [Azure PowerShell bejelentkezve](/powershell/azure/authenticate-azureps) található cikk utasításait.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot a virtuális gép létrehozása előtt.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *EastUS* régióban.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Egy Azure Database Migration Service-példány létrehozása

Azure Database Migration Service új példányát a parancsmag használatával hozhatja létre `New-AzDataMigrationService` . Ez a parancsmag a következő szükséges paramétereket várja:

* *Azure-erőforráscsoport neve*. A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal Azure-erőforráscsoportot is létrehozhat a korábban megjelenített módon, és paraméterként megadhatja a nevét.
* *Szolgáltatás neve*. A Azure Database Migration Service kívánt egyedi szolgáltatásnév megfelelő karakterlánc 
* *Hely*. Megadja a szolgáltatás helyét. Itt adhatja meg az Azure-beli adatközpont helyét, például az USA nyugati régióját vagy Délkelet-Ázsiában
* *SKU*. Ez a paraméter a DMS SKU-nevének felel meg. A jelenleg támogatott SKU-név *GeneralPurpose_4vCores*.
* *Virtuális alhálózati azonosító*. A [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancsmag segítségével alhálózatot hozhat létre. 

A következő példa egy *MyDMS* nevű szolgáltatást hoz létre az *USA keleti* régiójában, a *MyVNET* nevű virtuális hálózat és a *MySubnet* nevű alhálózat használatával. 

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

A parancsmag használatával létrehozhat egy adatbázis-elérhetőségi adatobjektumot `New-AzDmsConnInfo` . Ez a parancsmag a következő paramétereket várja:

* *ServerType*. A kért adatbázis-kapcsolatok típusa, például az SQL, az Oracle vagy a MySQL. Az SQL használata a SQL Server és az Azure SQL használatához.
* *Adatforrás*. SQL Server példány vagy Azure SQL Database neve vagy IP-címe.
* *AuthType*. A kapcsolatok hitelesítési típusa, amely lehet SqlAuthentication vagy WindowsAuthentication.
* A *TrustServerCertificate* paraméter egy olyan értéket állít be, amely jelzi, hogy a csatorna titkosítva van-e, miközben megkerüli a megbízhatósági kapcsolat ellenőrzéséhez szükséges tanúsítványláncot. Az érték lehet igaz vagy hamis.

Az alábbi példa az SQL-hitelesítéssel létrehozott MySourceSQLServer nevű forrás-SQL Serverhoz hoz létre egy kapcsolattípus-objektumot:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

A következő példa azt mutatja be, hogyan kell létrehozni az SQL-hitelesítés használatával a SQLAzureTarget nevű kiszolgálóhoz tartozó kapcsolatok adatait:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Adatbázisok biztosítása az áttelepítési projekthez

Hozzon létre egy olyan `AzDataMigrationDatabaseInfo` objektumot, amely a projekt létrehozásához paraméterként megadható Azure Database áttelepítési projekt részeként adja meg az adatbázisokat. A parancsmag használatával `New-AzDataMigrationDatabaseInfo` AzDataMigrationDatabaseInfo hozhatók létre. 

A következő példa létrehoz egy `AzDataMigrationDatabaseInfo` projektet a **AdventureWorks2016** adatbázishoz, és hozzáadja azt a listához, amelyet paraméterként kíván megadni a projekt létrehozásához.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Projekt objektum létrehozása

Végül létrehozhat egy *MyDMSProject* nevű Azure Database áttelepítési projektet, amely az *USA keleti* régiójában található `New-AzDataMigrationProject` , és hozzáadja a korábban létrehozott forrás-és cél kapcsolatokat, valamint az áttelepítendő adatbázisok listáját.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Áttelepítési feladat létrehozása és elindítása

Végül hozza létre és indítsa el az Azure Database áttelepítési feladatot. Az Azure Database áttelepítési feladathoz kapcsolati hitelesítő adatokra van szükség a forráshoz és a célhelyhez, valamint az áttelepítendő adatbázistáblák listájához, valamint az előfeltételként létrehozott projekthez már megadott információ mellett.

### <a name="create-credential-parameters-for-source-and-target"></a>Hitelesítőadat-paraméterek létrehozása a forrás és a cél számára

A kapcsolatbiztonsági hitelesítő adatok létrehozhatók [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) objektumként.

Az alábbi példa a *PSCredential* objektumok létrehozását mutatja be mind a forrás-, mind a célként megadott kapcsolatokhoz, és a jelszavakat karakterlánc-változókként *$sourcePassword* és *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Táblázatos Térkép létrehozása és a forrás és a cél paramétereinek kiválasztása az áttelepítéshez

Az áttelepítéshez egy másik paraméter szükséges, amely a forrásról a célhelyre irányuló táblák leképezését áttelepíti. Hozzon létre olyan tábla szótárát, amely leképezést biztosít a forrás-és a céltábla közötti áttelepítéshez. Az alábbi példa azt szemlélteti, hogyan használható a forrás-és a céltábla emberi erőforrások sémája a AdventureWorks 2016-adatbázishoz.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A következő lépés a forrás-és a cél-adatbázisok kiválasztása, valamint a tábla-hozzárendelés megadása paraméterként a `New-AzDmsSelectedDB` parancsmag használatával, az alábbi példában látható módon:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Hozza létre az áttelepítési feladatot, és indítsa el

Az `New-AzDataMigrationTask` áttelepítési feladat létrehozásához és elindításához használja a parancsmagot. Ez a parancsmag a következő paramétereket várja:

* *TaskType*. A SQL Server számára létrehozandó áttelepítési feladat típusa Azure SQL Database áttelepítési típus *MigrateSqlServerSqlDb* várható. 
* *Erőforráscsoport neve*. Azon Azure-erőforráscsoport neve, amelyben létre kívánja hozni a feladatot.
* *Szolgáltatásnév*. Azure Database Migration Service példány, amelyben létre kívánja hozni a feladatot.
* *Projektnév*. Azure Database Migration Service projekt neve, amelyben létre kívánja hozni a feladatot. 
* *Feladatnév*. A létrehozandó feladat neve. 
* *SourceConnection*. A forrás-SQL Server kapcsolatokat jelképező AzDmsConnInfo objektum.
* *TargetConnection*. A célként Azure SQL Database kapcsolatokat jelölő AzDmsConnInfo objektum.
* *SourceCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) objektum a forráskiszolgálón való csatlakozáshoz.
* *TargetCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) objektum a célkiszolgálóra való csatlakozáshoz.
* *SelectedDatabase*. A forrás-és céladatbázis-leképezést jelképező AzDataMigrationSelectedDB objektum.
* *SchemaValidation*. (nem kötelező, kapcsoló paraméter) Az áttelepítés után a a séma információinak összehasonlítását végzi a forrás és a cél között.
* *DataIntegrityValidation*. (nem kötelező, kapcsoló paraméter) Az áttelepítés után a a forrás és a cél között ellenőrzőösszeg-alapú adatintegritás-ellenőrzést hajt végre.
* *QueryAnalysisValidation*. (nem kötelező, kapcsoló paraméter) Az áttelepítés után egy gyors és intelligens lekérdezési elemzést hajt végre, ha lekéri a lekérdezéseket a forrás-adatbázisból, és végrehajtja azokat a célhelyen.

Az alábbi példa egy myDMSTask nevű áttelepítési feladatot hoz létre és indít el:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

A következő példa létrehozza és elindítja ugyanezt az áttelepítési feladatot a fentiek szerint, de mindhárom érvényesítést is végrehajtja:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>A migrálás monitorozása

A (z) rendszerű áttelepítési feladat figyeléséhez a feladat State (állapot) tulajdonságát kell lekérdezni, ahogy az az alábbi példában is látható:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>A DMS-példány törlése

Az áttelepítés befejezése után törölheti az Azure DMS-példányt:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Következő lépés

* Tekintse át az áttelepítési útmutatót a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/).