---
title: SQL Server migrálása az Azure SQL Database a Database Migration Service és a PowerShell segítségével |} A Microsoft Docs
description: Megismerheti, hogyan kell áttelepíteni a helyszíni SQL Serverről az Azure SQL Database Azure PowerShell használatával.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 599fc7e1eb021e3c519047a14145c292623d7508
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533843"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>A helyszíni SQL Server migrálása az Azure SQL Database, Azure PowerShell-lel
Ebben a cikkben telepít át a **Adventureworks2012** adatbázis egy helyi példányára, az SQL Server 2016 vagy újabb visszaállítása egy Azure SQL Database a Microsoft Azure PowerShell használatával. Telepíthet át adatbázisokat egy helyszíni SQL Server-példány az Azure SQL Database használatával a `Az.DataMigration` a Microsoft Azure PowerShell-modulja.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service-példányában.
> * A migrálás futtatása.

## <a name="prerequisites"></a>Előfeltételek
A lépések elvégzéséhez szüksége:

- [Az SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (bármely kiadás esetén)
- Ahhoz, hogy a TCP/IP protokoll, amely az SQL Server Express telepítése alapértelmezés szerint le van tiltva. Engedélyezze a TCP/IP protokollt a következő cikk [engedélyezheti vagy tilthatja le a kiszolgálói hálózati protokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Konfigurálhatja a [hozzáféréshez a Windows tűzfal](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Az Azure SQL Database-példány. Létrehozhat egy Azure SQL Database-példány a következő a részletek a cikkben [egy Azure SQL database létrehozása az Azure Portalon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 vagy újabb.
- Az Azure Resource Manager üzembe helyezési modell, amely biztosítja az Azure Database Migration Service-helyek közötti kapcsolatok a helyszíni adatforrás-kiszolgálók használatával a virtuális hálózat létrehozva [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- A Data Migration Assistant szolgáltatást használ, a cikkben leírtak szerint a helyi adatbázis és séma migrálási felmérést elvégzése [egy SQL Server migrálási felmérést végez](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Töltse le és telepítse a Az.DataMigration modul PowerShell-galériából történő használatával való [Install-Module PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); ügyeljen arra, hogy nyissa meg a powershell-parancsablakot a Futtatás rendszergazdaként paranccsal.
- SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatokat ahhoz, hogy rendelkezik a [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel.
- Annak érdekében, hogy a cél Azure SQL DB-hez való kapcsolódáshoz használt hitelesítő adatok példány az adatbázis VEZÉRLÉSE engedéllyel rendelkezik a cél Azure SQL Database-adatbázisok.
- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiók előkészületek.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be a Microsoft Azure-előfizetés
A cikk utasításokat használja [bejelentkezés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/authenticate-azureps) a PowerShell használatával jelentkezzen be az Azure-előfizetésében.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, egy virtuális gép létrehozása előtt.

Hozzon létre egy erőforráscsoportot a használatával a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsot. 

A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* a a *EastUS* régióban.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Az Azure Database Migration Service-példány létrehozása 
Azure Database Migration Service új példányának használatával hozhat létre a `New-AzDataMigrationService` parancsmagot. Ennek a parancsmagnak a következő szükséges paraméterek:
- *Azure erőforráscsoport-nevet*. Használhat [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsot hozhat létre Azure-erőforráscsoporton előzőekben ismertetettek szerint, és a name paramétert adja meg.
- *Szolgáltatásnév*. Karakterlánc, amely megfelel a kívánt egyedi szolgáltatás nevének, Azure Database Migration Service 
- *Hely*. A szolgáltatás helyét adja meg. Adja meg az Azure data center egy helyen, például az USA nyugati RÉGIÓJA vagy Délkelet-Ázsia
- *Termékváltozat*. Ez a paraméter DMS Sku-név tartozik. A jelenleg támogatott Sku-név *GeneralPurpose_4vCores*.
- *Virtuális alhálózati azonosító*. Parancsmagot használhatja [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) alhálózat létrehozását. 

A következő példában létrehozunk egy elnevezett szolgáltatás *MyDMS* erőforráscsoportban *MyDMSResourceGroup* található a *USA keleti Régiójában* nevűvirtuálishálózatothasználórégió *MyVNET* és nevű alhálózat *MySubnet*.

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
Miután létrehozott egy Azure Database Migration Service-példányt, hozzon létre egy migrálási projektet. Az Azure Database Migration Service-projekt kapcsolatadatok mind a forrás és cél-példányok, valamint az, hogy a projekt részeként migrálni kívánt adatbázisok listájának kell rendelkeznie.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Adatbázis-kapcsolati adatok objektum a forrás- és kapcsolatok létrehozása
Egy adatbázis-kapcsolati adatok objektum használatával is létrehozhat a `New-AzDmsConnInfo` parancsmagot. Ennek a parancsmagnak a következő paraméterekkel:
- *ServerType*. Adatbázis-kapcsolatot kért, például az SQL, Oracle vagy MySQL típusú. SQL az SQL Server és az Azure SQL használata.
- *Adatforrás*. Név vagy IP-címét egy SQL Server-példányt vagy az Azure SQL database.
- *Érvénytelen AuthType*. A kapcsolat, amely lehet SqlAuthentication vagy WindowsAuthentication hitelesítési típusa.
- *TrustServerCertificate* paraméter, amely azt jelzi, hogy a csatorna során a rendszer megkerüli a tanúsítványlánc érvényesítése megbízhatósági walking titkosítva legyen-e értéket állítja be. Érték a true vagy FALSE (hamis) lehet.

Az alábbi példában a forrás sql-hitelesítéssel MySourceSQLServer nevű SQL Server kapcsolati adatok objektumot hoz létre: 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

A következő példa bemutatja egy Azure SQL adatbázis-kiszolgáló sql-hitelesítéssel SQLAzureTarget nevű kapcsolati adatok létrehozása:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Adja meg az adatbázisokat a migrálási projekt
Hozzon létre egy `AzDataMigrationDatabaseInfo` , amely megadja az adatbázisok az Azure adatbázis-Migrálás részeként a projekt, amely objektumok adható meg paraméterként, a projekt létrehozásához. A parancsmag `New-AzDataMigrationDatabaseInfo` AzDataMigrationDatabaseInfo létrehozására használható. 

A következő példában létrehozunk `AzDataMigrationDatabaseInfo` project számára a **AdventureWorks2016** adatbázis, és hozzáadja azt a listát adható meg paraméterként a projekt létrehozásához.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Projekt-objektum létrehozása
Végül hoz létre az Azure Database Migration-projekt nevű *MyDMSProject* található *USA keleti Régiójában* használatával `New-AzDataMigrationProject` és a korábban létrehozott forrás- és kapcsolatok és listájának hozzáadása migrálni kívánt adatbázisok.

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

## <a name="create-and-start-a-migration-task"></a>Hozzon létre, és indítsa el áttelepítési feladatot
Végül hozzon létre, és indítsa el az Azure adatbázis-Migrálási feladat. Az Azure adatbázis-Migrálási feladat kapcsolat hitelesítő adatokat igényel forrás és cél- és az előfeltételként létrehozott projekt már megadott információk mellett az áttelepítendő adatbázistáblák listája. 

### <a name="create-credential-parameters-for-source-and-target"></a>Forrás és a cél hitelesítő adat paramétereinek létrehozása
Kapcsolat biztonsági hitelesítő adatokat hozható létre egy [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum. 

Az alábbi példa bemutatja *PSCredential* objektumokat biztosít a jelszavak, karakterlánc típusú változót a forrás- és cél kapcsolatok *$sourcePassword* és *$ targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Hozzon létre egy tábla hozzárendelése és migráláshoz válassza forrás- és paraméterek
Az áttelepítéshez szükséges egy másik paraméter feltérképezése a forrásból táblák áttelepíteni kívánt cél. Hozzon létre táblák szótár, amely tartalmazza az áttelepítéshez a forrás- és táblák között. Az alábbi példában látható a forrás- és táblák közötti megfeleltetés az AdventureWorks 2016 adatbázis emberi erőforrások sémáját.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A következő lépés az, hogy válassza ki a forrás- és adatbázisokat, és adja meg a tábla hozzárendelése használatával paraméterként áttelepíteni a `New-AzDmsSelectedDB` parancsmag, az alábbi példában látható módon:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Hozzon létre, és indítsa el áttelepítési feladatot

Használja a `New-AzDataMigrationTask` parancsmag segítségével hozzon létre egy áttelepítési feladat elindításához. Ennek a parancsmagnak a következő paraméterekkel:
- *TaskType*. Áttelepítési feladat létrehozása az SQL Server Azure SQL Database-áttelepítési típushoz típusú *MigrateSqlServerSqlDb* várt. 
- *Erőforráscsoport-nevet*. Azure-erőforráscsoportot, amelyben létrehozza a feladat nevét.
- *Szolgáltatásnév*. Azure Database Migration Service példány, amelyben a feladat létrehozásához.
- *Projektnév*. Azure Database Migration Service-projekt, amelyben létrehozza a feladat neve. 
- *Feladatnév*. A létrehozandó feladat neve. 
- *SourceConnection*. AzDmsConnInfo a forrás SQL Server közötti kapcsolat képviselő objektum.
- *TargetConnection*. AzDmsConnInfo a cél Azure SQL Database-kapcsolatot képviselő objektum.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum forrás-kiszolgálójához való kapcsolódás.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a célkiszolgálóhoz való kapcsolódáshoz.
- *SelectedDatabase*. A forrás- és adatbázisok közötti képviselő AzDataMigrationSelectedDB objektum.

Az alábbi példa létrehoz és nevű myDMSTask áttelepítési feladat elindítása:

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

## <a name="monitor-the-migration"></a>A migrálás monitorozása
Az áttelepítési feladat fut a feladat a következő példában látható módon állapot tulajdonságának lekérdezésével figyelheti:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>A DMS-példány törlése
Az áttelepítés befejezése után törölheti az Azure DMS-példány:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>További lépések
- Tekintse át a migrálási útmutató segítséget nyújt a Microsoft [adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).
