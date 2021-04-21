---
title: 'PowerShell: Offline migrálás futtatása MySQL-adatbázisból Azure Database for MySQL DMS használatával'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan telepítheti át a helyi MySQL-adatbázist Azure Database for MySQL powershell-Azure Database Migration Service használatával.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 424443288dddcb09d15b7d00ffe9a2840f602959
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819688"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>MySQL áttelepítése offline Azure Database for MySQL PowerShell-& Azure Database Migration Service

Ebben a cikkben egy helyszíni példányra visszaállított MySQL-adatbázist fog migrálni a Azure Database for MySQL-be a Azure Database Migration Service offline migrálási képességének használatával a Microsoft Azure PowerShell. A cikk a MySQL-adatbázis Azure-ba való offline migrálásának végrehajtása érdekében egymás után végrehajtható PowerShell-szkriptek gyűjteményét írja le.

> [!NOTE]
> Jelenleg nem lehet teljes adatbázis-migrálást futtatni az Az.DataMigration modullal. Addig is a minta PowerShell-szkriptet a rendszer "adott ként" biztosítja, amely a [DMS REST API-t](https://docs.microsoft.com/rest/api/datamigration/tasks/get) használja, és lehetővé teszi a migrálás automatizálását. Ezt a szkriptet a rendszer módosítja vagy kiajátssa, miután az Az.DataMigration modulban és az Azure CLI-ben hivatalos támogatást adott hozzá. 

> [!IMPORTANT]
> A "MySQL to Azure Database for MySQL" online migrálási forgatókönyvet egy párhuzamos, nagy mértékben teljesítő offline migrálási forgatókönyv váltja fel 2021. június 1-től. Online migrálások esetén használhatja ezt az új ajánlatot és [a beható adatreplikációt.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Másik lehetőségként használjon nyílt forráskódú eszközöket, például a [MyDumpert vagy a MyLoadert](https://centminmod.com/mydumper.html) az online migrálások során a beható adatreplikációval. 

A cikk segít automatizálni azt a forgatókönyvet, amelyben a forrás és a céladatbázis neve azonos vagy eltérő lehet, és a migrálás részeként a céladatbázisban lévő táblák mindegyikét vagy csak néhányat kell migrálni, azonos névvel és táblastruktúrával. Bár a cikkek feltételezik, hogy a forrás egy MySQL-adatbázispéldány, és Azure Database for MySQL célként szolgál, használható az egyik Azure Database for MySQL-ból egy másikba való áttelepítéshez csupán a forráskiszolgáló nevének és hitelesítő adatainak módosításával. Emellett az alacsonyabb verziójú MySQL-kiszolgálókról (5.6-os és újabb verziók) magasabb verziókra való migrálás is támogatott.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Adatbázisséma mi áttelepítése.
> * Hozzon létre egy erőforráscsoportot.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása egy Azure Database Migration Service példányban.
> * Konfigurálja a migrálási projektet a MySQL offline migrálási képességének használatára.
> * A migrálás futtatása.

## <a name="prerequisites"></a>Előfeltételek

A lépések befejezéséhez a következőre lesz szüksége:

* Aktív előfizetéssel rendelkezik egy Azure-fiókkal. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free)
* Helyszíni MySQL-adatbázis 5.6-os vagy újabb verzióval. Ha nem, töltse le és telepítse a [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6-os vagy korábbi kiadását.
* [Példány létrehozása az Azure Database for MySQL-ben](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Az adatbázisok Workbench alkalmazással való csatlakoztatásával és létrehozásával kapcsolatos részletekért tekintse meg a Csatlakozás és adatok lekérdezése a [MySQL Workbench](../mysql/connect-workbench.md) használatával cikket. A Azure Database for MySQL verziónak a helyszíni MySQL-verzióval egyenlőnek vagy annál magasabbnak kell lennie. A MySQL 5.7-es verzió például át lehet Azure Database for MySQL 5.7-es verzióra, vagy frissíthető a 8-as verzióra.  
* Hozzon létre Microsoft Azure Virtual Network-Azure Database Migration Service-kiszolgálóhoz egy üzembe helyezési modellel, amely [expressRoute](../expressroute/expressroute-introduction.md) vagy VPN használatával biztosítja Azure Resource Manager helyszíni forráskiszolgálókhoz való [hely–hely kapcsolatot.](../vpn-gateway/vpn-gateway-about-vpngateways.md) A virtuális hálózatok létrehozásával kapcsolatos további [](../virtual-network/index.yml)információkért tekintse meg a Virtual Network dokumentációját, és különösen a részletes információkat tartalmazó gyorsútmutató-cikkeket.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute-et a Microsofttal való hálózati társviszony-létesítés során használja, adja hozzá a *Microsoft.Sql* szolgáltatásvégpontot ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti. [](../virtual-network/virtual-network-service-endpoints-overview.md) Ez a konfiguráció azért szükséges, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem blokkolják a ServiceTag 443-as kimenő portját a Storage és az AzureMonitor számára. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részletekért tekintse meg a Hálózati forgalom szűrése hálózati [biztonsági csoportokkal cikket.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Nyissa meg a Windows tűzfalat, hogy engedélyezze a Virtual Network for Azure Database Migration Service a mySQL-kiszolgáló forráshoz való hozzáférést, amely alapértelmezés szerint a 3306-os TCP-port.
* Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy engedélyezze a Virtual Network-ból származó kapcsolatokat, hogy az Azure Database Migration Service hozzáférjen a forrásadatbázis(okkal) a migráláshoz.
* Hozzon létre [](../azure-sql/database/firewall-configure.md) egy kiszolgálószintű tűzfalszabályt, vagy konfigurálja a VNET-szolgáltatásvégpontokat a Azure Database for MySQL számára, hogy Virtual Network hozzáférést Azure Database Migration Service a céladatbázishoz. [](../mysql/howto-manage-vnet-using-portal.md)
* A forrásként szolgáló MySQL-nek a MySQL közösségi kiadásának kell lennie. A MySQL-példány verziójának megállapításához futtassa a következő parancsot a MySQL segédprogramban vagy a MySQL Workbenchben:

    ```
    SELECT @@version;
    ```

* Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. Ha a MyISAM-táblákat InnoDB-táblákká szeretné alakítani, tekintse meg a [táblák MyISAM-ból InnoDB-vé konvertálását ismertető](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) részt
* A felhasználónak jogosultsággal kell lennie a forrásadatbázis adatainak olvasása érdekében.
* Az útmutató a PowerShell 7.1-es és PSEdition Core-t használja, amely a telepítési [útmutatónak megfelelően telepíthető](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)
* Töltse le és telepítse a következő modulokat a PowerShell-galéria [Install-Module PowerShell-parancsmag használatával;](/powershell/module/powershellget/Install-Module) Mindenképpen nyissa meg a PowerShell-parancsablakot a futtatás rendszergazdaként paranccsal:
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Adatbázisséma áttelepítése

Az összes adatbázis-objektum, például a táblasémák, indexek és tárolt eljárások átviteléhez ki kell bontanunk a sémát a forrásadatbázisból, és alkalmaznunk kell a céladatbázisra. A séma kibontásához használhatja a mysqldump segédprogramot a `--no-data` paraméterrel. Ehhez olyan gépre van szükség, amely a forrás MySQL-adatbázishoz és a céladatbázishoz is Azure Database for MySQL.

A séma mysqldump használatával történő exportáláshoz futtassa a következő parancsot:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Például:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

A séma importálása célként Azure Database for MySQL futtassa a következő parancsot:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Például:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Ha a sémában vannak idegen kulcsok, a párhuzamos adatbetöltést a migrálási feladat fogja kezelni. A sémaáttelepítés során nem szükséges elejteni a idegen kulcsokat.

Ha az adatbázisban eseményindítók vannak, az kikényszeríteni fogja az adatok integritását a célban, még a forrásból való teljes adatáttelepítés előtt. Javasoljuk, hogy a migrálás során tiltsa le az eseményindítókat a célban lévő összes táblán, majd engedélyezze az eseményindítókat a migrálás után.

Hajtsa végre az alábbi szkriptet a MySQL Workbenchben a céladatbázison az eseményindító-szkript kibontása és az eseményindító-szkript hozzáadása érdekében.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Futtassa a létrehozott drop trigger lekérdezést (DropQuery oszlop) az eredményben az eseményindítók céladatbázisban való eldobása érdekében. Az eseményindító hozzáadása lekérdezés menthető, és az adatáttelepítés befejezése után használható.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be az Microsoft Azure előfizetésbe

A [Connect-AzAccount PowerShell-paranccsal](/powershell/module/az.accounts/connect-azaccount) jelentkezzen be az Azure-előfizetésbe a PowerShell használatával, a Bejelentkezés a következővel: [Azure PowerShell.](/powershell/azure/authenticate-azureps)

A következő szkript beállítja a PowerShell-munkamenet alapértelmezett előfizetését a bejelentkezés után, és létrehoz egy segítőnaplózási függvényt a formázott konzolnaplókhoz.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

Az erőforrás-szolgáltató regisztrációját minden Azure-előfizetésben csak egyszer kell regisztrálni. A regisztráció nélkül nem hozhat létre példányokat a **Azure Database Migration Service.**

Regisztrálja az erőforrás-szolgáltatót a [Register-AzResourceProvider paranccsal.](/powershell/module/az.resources/register-azresourceprovider) A következő szkript regisztrálja a szolgáltatáshoz szükséges **erőforrás-Azure Database Migration Service**

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot a DMS-erőforrások létrehozása előtt.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup paranccsal.](/powershell/module/az.resources/new-azresourcegroup)

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *USA 2.* nyugati régiójában az alapértelmezett *mySubscription előfizetés alatt.*

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Egy Azure Database Migration Service-példány létrehozása

A [New-AzDataMigrationService](/powershell/module/az.datamigration/new-azdatamigrationservice) Azure Database Migration Service új példányát hozhatja létre. Ez a parancs a következő kötelező paramétereket várja:
* *Azure-erőforráscsoport neve.* A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal a korábban bemutatott módon hozhat létre Azure-erőforráscsoportot, és paraméterként meg kell adnia a nevét.
* *Szolgáltatásnév.* A kívánt egyedi szolgáltatásnévnek megfelelő sztring a Azure Database Migration Service 
* *Hely*. Megadja a szolgáltatás helyét. Adjon meg egy Azure-adatközpontot, például az USA nyugati régiója vagy Délkelet-Ázsia
* *Termékváltozat.* Ez a paraméter a DMS termékváltozat nevének felel meg. A jelenleg támogatott termékváltozatok neve *a Standard_1vCore*, *Standard_2vCores*, *Standard_4vCores*, *Premium_4vCores.*
* *Virtuális alhálózat azonosítója.* Az alhálózatok információit a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) paranccsal kaphatja meg. 

A következő szkript azt várja, hogy a *myVirtualNetwork* virtuális hálózat létezik egy *default* nevű alhálózattal, majd létrehoz egy *myDmService* nevű Database Migration Service-t a **3.** lépésben létrehozott erőforráscsoportban és ugyanabban a régióban.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Miután létrehozott egy Azure Database Migration Service, létre fog hozni egy migrálási projektet. A migrálási projekt határozza meg a migrálás szükséges típusát.

A következő szkript létrehoz egy *myfirstmysqlofflineproject* nevű migrálási projektet a MySQL Azure Database for MySQL-ről a **4.** lépésben és ugyanabban a régióban létrehozott Database Migration Service-példányba való offline migráláshoz.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Adatbázis-kapcsolati információ objektum létrehozása a forrás- és célkapcsolathoz

A migrálási projekt létrehozása után létre fogja hozni az adatbázis-kapcsolat adatait. Ezek a kapcsolati adatok lesznek használva a forrás- és célkiszolgálókhoz való csatlakozáshoz az áttelepítési folyamat során.

A következő szkript a forrás- és cél MySQL-példány kiszolgálónevét, felhasználónevét és jelszavát használja, és létrehozza a kapcsolati információk objektumait. A szkript arra kéri a felhasználót, hogy adja meg a forrás- és cél MySQL-példány jelszavát. Csendes szkriptek használata után a hitelesítő adatok lekért adatok a Azure Key Vault. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>Táblanevek listájának kinyerása a céladatbázisból

Az adatbázistábla-lista egy áttelepítési feladattal és kapcsolati adatokkal bontható ki. A táblalista a forrás- és a céladatbázisból is ki lesz bontva, így a megfelelő leképezés és ellenőrzés is megjelenik. 

A következő szkript a forrás- és céladatbázisok nevét használja, majd kinyeri a táblalistát az adatbázisokból a *GetUserTablesMySql migrálási* feladattal. 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Táblaleképezés összeállítása felhasználói konfiguráció alapján

Az áttelepítési feladat konfigurálása során létre fog hozni egy leképezést a forrás- és a céltáblák között. A leképezés a táblanév szintjén történik, de a feltételezés az, hogy a leképezett táblák táblaszerkezete (oszlopszám, oszlopnevek, adattípusok stb.) pontosan megegyezik.

A következő szkript létrehoz egy leképezést a **7.** lépésben kinyert cél- és forrástáblalista alapján. Részleges adatbetöltés esetén a felhasználó meg tud adni egy táblázatlistát a táblák kiszűréséhez. Ha nem ad meg felhasználói bevitelt, akkor a rendszer az összes céltáblát leképezi. A szkript azt is ellenőrzi, hogy létezik-e a forrásban azonos nevű tábla. Ha a tábla neve nem létezik a forrásban, akkor a céltábla figyelmen kívül lesz hagyva a migrálás során. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>Az áttelepítési feladat bemenetének létrehozása és konfigurálása

A táblaleképezés létrehozása után létrehozza a *Migrate.MySql.AzureDbForMySql* típusú migrálási feladat bemenetét, és konfigurálja a tulajdonságokat.

A következő szkript létrehozza az áttelepítési feladatot, és beállítja a kapcsolatokat, az adatbázisneveket és a táblaleképezést.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Teljesítmény-finomhangolási paraméterek konfigurálása

A PowerShell-modultól függően elérhető néhány választható paraméter, amelyek a környezet alapján hangolhatóak. Ezekkel a paraméterekkel javítható az áttelepítési feladat teljesítménye. Ezek a paraméterek nem kötelezőek, és az alapértelmezett értékük NULL.

> [!NOTE]
> Az alábbi teljesítménykonfigurációk nagyobb átviteli sebességet mutatnak a prémium termékváltozat migrálása során.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 másodperc
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

A következő szkript a paraméterek felhasználói értékeit veszi fel, és beállítja a paramétereket az áttelepítési feladat tulajdonságaiban.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>Az áttelepítési feladat létrehozása és futtatása

Miután konfigurálta a bemenetet a feladathoz, a rendszer most már létre lesz hozva és végre lesz hajtva az ügynökben. A szkript elindítja a feladat végrehajtását, és megvárja, amíg az áttelepítés befejeződik.

A következő szkript meghívja a konfigurált áttelepítési feladatot, és megvárja, amíg befejeződik.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>A Database Migration Service

Ugyanaz a Database Migration Service több migráláshoz is használható, így a létrehozott példány ismét felhasználható. Ha nem folytatja a Database Migration Service, törölheti a szolgáltatást a [Remove-AzDataMigrationService paranccsal.](/powershell/module/az.datamigration/remove-azdatamigrationservice?)

A következő szkript törli a Azure Database Migration Service példányt és a társított projekteket.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Következő lépések

* A DMS-sel való migrálás ismert problémáival és korlátozásával kapcsolatos további információkért lásd: Gyakori problémák – [Azure Database Migration Service.](./known-issues-troubleshooting-dms.md)
* A forrásadatbázis DMS-sel való kapcsolati hibáinak elhárításához tekintse meg a forrásadatbázisok csatlakoztatásával [kapcsolatos problémákat](./known-issues-troubleshooting-dms-source-connectivity.md)bemutató cikket.
* További információt a Azure Database Migration Service a Mi a Azure Database Migration Service? cikkben [talál.](./dms-overview.md)
* További információt a Azure Database for MySQL a Mi a Azure Database for MySQL? cikkben [talál.](../mysql/overview.md)
* A DMS portálon keresztüli használatával kapcsolatos oktatóanyagért lásd: [Oktatóanyag: MySQL](./tutorial-mysql-azure-mysql-offline-portal.md) migrálva offline Azure Database for MySQL DMS használatával