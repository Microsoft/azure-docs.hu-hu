---
title: Azure Cosmos DB Core (SQL) API-erőforrások kezelése a PowerShell használatával
description: Azure Cosmos DB Core (SQL) API-erőforrások kezelése a PowerShell használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333426"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Azure Cosmos DB Core (SQL) API-erőforrások kezelése a PowerShell használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az alábbi útmutató ismerteti, hogyan használható a PowerShell a Azure Cosmos DB Core (SQL) API-erőforrások (például a Cosmos-fiók, az adatbázis, a tároló és az átviteli sebesség) felügyeletére és automatizálására. Más API-khoz készült PowerShell-parancsmagok esetében lásd: [PowerShell-minták a Cassandra](powershell-samples-cassandra.md), PowerShell-minták a [MongoDB API](powershell-samples-mongodb.md)-hoz, PowerShell-minták a [Gremlin](powershell-samples-gremlin.md), PowerShell-minták [táblázathoz](powershell-samples-table.md)

> [!NOTE]
> A cikkben szereplő minták az [az. CosmosDB](/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használják. A legújabb változásokért tekintse meg az az [. CosmosDB](/powershell/module/az.cosmosdb) API-referenciát ismertető oldalt.

A Azure Cosmos DB platformfüggetlen felügyeletéhez használhatja a `Az` és a `Az.CosmosDB` parancsmagot a [platformfüggetlen PowerShell](/powershell/scripting/install/installing-powershell)-lel, valamint az [Azure CLI](manage-with-cli.md)-vel, a [Rest APIval][rp-rest-api]vagy a [Azure Portalsal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Első lépések

Kövesse a [Azure PowerShell telepítésének és konfigurálásának][powershell-install-configure] lépéseit a PowerShell Azure-fiókjába való telepítéséhez és bejelentkezéséhez.

> [!IMPORTANT]
> Azure Cosmos DB erőforrásokat nem lehet átnevezni, mivel ez megsérti, hogyan működik az Azure Resource Manager erőforrás-URI-k.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-fiókok

Az alábbi szakaszban bemutatjuk, hogyan kezelheti az Azure Cosmos-fiókot, beleértve a következőket:

* [Azure Cosmos-fiók létrehozása](#create-account)
* [Azure Cosmos-fiók frissítése](#update-account)
* [Az előfizetésben lévő összes Azure Cosmos-fiók listázása](#list-accounts)
* [Azure Cosmos-fiók beszerzése](#get-account)
* [Azure Cosmos-fiók törlése](#delete-account)
* [Azure Cosmos-fiókhoz tartozó címkék frissítése](#update-tags)
* [Azure Cosmos-fiók kulcsainak listázása](#list-keys)
* [Kulcsok újragenerálása Azure Cosmos-fiókhoz](#regenerate-keys)
* [Azure Cosmos-fiókhoz tartozó kapcsolatok karakterláncok listázása](#list-connection-strings)
* [Azure Cosmos-fiók feladatátvételi prioritásának módosítása](#modify-failover-priority)
* [Manuális feladatátvétel indítása Azure Cosmos-fiókhoz](#trigger-manual-failover)
* [Erőforrás-zárolások listázása egy Azure Cosmos DB fiókban](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Azure Cosmos-fiók létrehozása

Ez a parancs egy Azure Cosmos DB adatbázis-fiókot hoz létre [több régióval][distribute-data-globally], az [automatikus feladatátvételsel](how-to-manage-database-account.md#automatic-failover) és a határos állandó [konzisztencia-házirenddel](consistency-levels.md).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` Az Azure-erőforráscsoport, amelybe telepíteni kívánja a Cosmos-fiókot. Már léteznie kell.
* `$locations` Az adatbázis-fiók régiói, a régió, amely `FailoverPriority 0` az írási régió.
* `$accountName` Az Azure Cosmos-fiók neve. Egyedinek, kisbetűsnek kell lennie, csak alfanumerikus és "-" karaktereket tartalmazhat, és 3 – 31 karakter hosszú lehet.
* `$apiKind` A létrehozandó Cosmos-fiók típusa. További információ: [Cosmos db API-](introduction.md#simplified-application-development)k.
* `$consistencyPolicy`, `$maxStalenessInterval` és `$maxStalenessPrefix` Az Azure Cosmos-fiók alapértelmezett konzisztencia-szintje és beállításai. További információ: [Azure Cosmos db egységességi szintjei](consistency-levels.md).

Az Azure Cosmos-fiókok IP-tűzfallal, Virtual Network szolgáltatási végpontokkal és privát végpontokkal konfigurálhatók. További információ a Azure Cosmos DB IP-tűzfalának konfigurálásáról: az [IP-tűzfal konfigurálása](how-to-configure-firewall.md). További információ a Azure Cosmos DB szolgáltatási végpontjának engedélyezéséről: [virtuális hálózatok elérésének konfigurálása](how-to-configure-vnet-service-endpoint.md). További információ a Azure Cosmos DB magánhálózati végpontjának engedélyezéséről: [hozzáférés konfigurálása privát végpontokról](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Egy erőforráscsoport összes Azure Cosmos-fiókjának listázása

Ez a parancs az erőforráscsoport összes Azure Cosmos-fiókját listázza.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Azure Cosmos-fiók tulajdonságainak beolvasása

Ez a parancs lehetővé teszi egy meglévő Azure Cosmos-fiók tulajdonságainak beszerzését.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Azure Cosmos-fiók frissítése

Ezzel a paranccsal frissítheti Azure Cosmos DB adatbázis-fiókjának tulajdonságait. A frissíthető tulajdonságok közé tartoznak a következők:

* Régiók hozzáadása vagy eltávolítása
* Alapértelmezett konzisztencia-házirend módosítása
* IP-címtartomány módosítása
* Virtual Network konfigurációk módosítása
* Többrégiós írások engedélyezése

> [!NOTE]
> Nem lehet egyszerre hozzáadni vagy eltávolítani a régiókat ( `locations` ), és módosítani az Azure Cosmos-fiók egyéb tulajdonságait. A régiók módosításait külön műveletként kell végrehajtani a fiók bármely egyéb változása alapján.
> [!NOTE]
> Ezzel a paranccsal hozzáadhat és eltávolíthat régiókat, de nem teszi lehetővé a feladatátvételi prioritások módosítását, illetve manuális feladatátvételt is indíthat. Lásd: a [feladatátvételi prioritás módosítása](#modify-failover-priority) és a [manuális feladatátvétel elindítása](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Több írási régió engedélyezése Azure Cosmos-fiókhoz

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Azure Cosmos-fiók törlése

Ez a parancs töröl egy meglévő Azure Cosmos-fiókot.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Azure Cosmos-fiók címkék frissítése

Ezzel a paranccsal az Azure Cosmos-fiókhoz tartozó [Azure-erőforrás-címkék][azure-resource-tags] állíthatók be. A címkék a fiók létrehozásakor és a használatával is megadhatók `New-AzCosmosDBAccount` `Update-AzCosmosDBAccount` .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Fiók kulcsainak listázása

Azure Cosmos-fiók létrehozásakor a szolgáltatás két elsődleges hozzáférési kulcsot hoz létre, amelyek hitelesítésre használhatók az Azure Cosmos-fiók elérésekor. A csak olvasási jogosultságú kulcsok a csak olvasási műveletek hitelesítéséhez is létrejönnek.
A két hozzáférési kulcs megadásával a Azure Cosmos DB lehetővé teszi, hogy egyszerre egy kulcsot újrageneráljon és forgasson el, megszakítás nélkül az Azure Cosmos-fiókjába.
Cosmos DB fiókoknak két írható-olvasható kulcsa van (elsődleges és másodlagos) és két írásvédett kulcs (elsődleges és másodlagos).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Kapcsolatok karakterláncok listázása

A következő parancs lekérdezi a kapcsolati karakterláncokat az alkalmazások Cosmos DB-fiókhoz való csatlakoztatásához.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Fiók kulcsainak újragenerálása

Az Azure Cosmos-fiókhoz való hozzáférési kulcsokat rendszeresen újra kell generálni a kapcsolatok biztonságossá tételéhez. Az elsődleges és másodlagos hozzáférési kulcsok hozzá vannak rendelve a fiókhoz. Ez lehetővé teszi az ügyfelek számára a hozzáférés fenntartását, miközben az egyes kulcsok újbóli létrehozása történik.
Egy Azure Cosmos-fiókhoz négy típusú kulcs létezik (elsődleges, másodlagos, PrimaryReadonly és SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Automatikus feladatátvétel engedélyezése

A következő parancs egy Cosmos DB fiókot állít be, amely a feladatátvételt automatikusan átadja a másodlagos régiójának, ha az elsődleges régió elérhetetlenné válik.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Feladatátvétel prioritásának módosítása

Az automatikus Feladatátvételsel konfigurált fiókok esetében megváltoztathatja azt a sorrendet, amelyben a Cosmos a másodlagos replikákat az elsődlegesnek fogja előléptetni, és az elsődleges elérhetetlenné válik.

Az alábbi példában feltételezzük, hogy az aktuális feladatátvételi prioritás a következő:, `West US 2 = 0` `East US 2 = 1` `South Central US = 2` . A parancs ezt a következőre fogja módosítani: `West US 2 = 0` `South Central US = 1` , `East US 2 = 2` .

> [!CAUTION]
> A helyének módosítása elindítja `failoverPriority=0` a manuális feladatátvételt egy Azure Cosmos-fiókhoz. A többi prioritási változás nem indít el feladatátvételt.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Manuális feladatátvétel indítása

A manuális Feladatátvételsel konfigurált fiókok esetében a módosításával átadhatja a másodlagos replikát az elsődlegesnek `failoverPriority=0` . Ezzel a művelettel vészhelyzeti helyreállítási gyakorlatot indíthat a vész-helyreállítási tervezés teszteléséhez.

Az alábbi példában feltételezzük, hogy a fiók aktuális feladatátvételi prioritásával rendelkezik `West US 2 = 0` , `East US 2 = 1` és megfordítja a régiókat.

> [!CAUTION]
> A `locationName` szolgáltatásra való váltáskor `failoverPriority=0` a rendszer manuális feladatátvételt indít el egy Azure Cosmos-fiókhoz. A többi prioritási változás nem indít el feladatátvételt.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Erőforrás-zárolások listázása egy Azure Cosmos DB fiókban

Az erőforrás-zárolások Azure Cosmos DB erőforrásokra, például adatbázisokra és gyűjteményekre is helyezhetők. Az alábbi példa bemutatja, hogyan listázhatja az összes Azure-erőforrás zárolását egy Azure Cosmos DB fiókban.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Adatbázis Azure Cosmos DB

A következő szakaszban bemutatjuk, hogyan kezelheti a Azure Cosmos DB-adatbázist, beleértve a következőket:

* [Létrehoz egy Azure Cosmos DB-adatbázist](#create-db)
* [Megosztott átviteli sebességgel rendelkező Azure Cosmos DB-adatbázis létrehozása](#create-db-ru)
* [Azure Cosmos DB adatbázis átviteli sebességének beolvasása](#get-db-ru)
* [Adatbázis átviteli sebességének átmigrálása az autoscalere](#migrate-db-ru)
* [Egy fiók összes Azure Cosmos DB adatbázisának listázása](#list-db)
* [Egyetlen Azure Cosmos DB adatbázis beolvasása](#get-db)
* [Azure Cosmos DB-adatbázis törlése](#delete-db)
* [Erőforrás-zárolás létrehozása Azure Cosmos DB adatbázison a törlés megakadályozása érdekében](#create-db-lock)
* [Erőforrás-zárolás eltávolítása egy Azure Cosmos DB adatbázison](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Létrehoz egy Azure Cosmos DB-adatbázist

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Megosztott átviteli sebességgel rendelkező Azure Cosmos DB-adatbázis létrehozása

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Azure Cosmos DB adatbázis átviteli sebességének beolvasása

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Adatbázis átviteli sebességének átmigrálása az autoscalere

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Összes Azure Cosmos DB adatbázis beolvasása egy fiókban

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Egyetlen Azure Cosmos DB adatbázis beolvasása

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Azure Cosmos DB-adatbázis törlése

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Erőforrás-zárolás létrehozása Azure Cosmos DB adatbázison a törlés megakadályozása érdekében

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Erőforrás-zárolás eltávolítása egy Azure Cosmos DB adatbázison

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB tároló

Az alábbi részben bemutatjuk, hogyan kezelheti a Azure Cosmos DB tárolót, beleértve a következőket:

* [Azure Cosmos DB tároló létrehozása](#create-container)
* [Azure Cosmos DB tároló létrehozása az autoscale paranccsal](#create-container-autoscale)
* [Azure Cosmos DB tároló létrehozása nagyméretű partíciós kulccsal](#create-container-big-pk)
* [Azure Cosmos DB tároló átviteli sebességének beolvasása](#get-container-ru)
* [Tároló átviteli sebességének átmigrálása az autoscalere](#migrate-container-ru)
* [Azure Cosmos DB-tároló létrehozása egyéni indexeléssel](#create-container-custom-index)
* [Kikapcsolt indexeléssel rendelkező Azure Cosmos DB-tároló létrehozása](#create-container-no-index)
* [Egyedi kulccsal és TTL-vel rendelkező Azure Cosmos DB-tároló létrehozása](#create-container-unique-key-ttl)
* [Ütközéses feloldású Azure Cosmos DB-tároló létrehozása](#create-container-lww)
* [Az összes Azure Cosmos DB-tároló listázása egy adatbázisban](#list-containers)
* [Egyetlen Azure Cosmos DB tároló beszerzése egy adatbázisban](#get-container)
* [Azure Cosmos DB tároló törlése](#delete-container)
* [Erőforrás-zárolás létrehozása Azure Cosmos DB-tárolón a törlés megakadályozása érdekében](#create-container-lock)
* [Erőforrás-zárolás eltávolítása Azure Cosmos DB tárolón](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Azure Cosmos DB tároló létrehozása

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Azure Cosmos DB tároló létrehozása az autoscale paranccsal

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Azure Cosmos DB tároló létrehozása nagyméretű partíciós kulcs méretével

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Azure Cosmos DB tároló átviteli sebességének beolvasása

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Tároló átviteli sebességének átmigrálása az autoscalere

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Egyéni index-házirenddel rendelkező Azure Cosmos DB-tároló létrehozása

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Kikapcsolt indexeléssel rendelkező Azure Cosmos DB-tároló létrehozása

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Azure Cosmos DB tároló létrehozása egyedi kulcsokra vonatkozó szabályzattal és TTL-vel

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Ütközéses feloldású Azure Cosmos DB-tároló létrehozása

Ha az összes ütközést a ConflictsFeed szeretné írni, és külön kezeli a leírót, adja meg a következőt: `-Type "Custom" -Path ""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Ha egy tárolt eljárás használatára vonatkozó ütközés-feloldási szabályzatot szeretne létrehozni, hívja meg `New-AzCosmosDBSqlConflictResolutionPolicy` és adja át a paramétereket és a paramétert `-Type` `-ConflictResolutionProcedure` .

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Az összes Azure Cosmos DB-tároló listázása egy adatbázisban

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Egyetlen Azure Cosmos DB tároló beszerzése egy adatbázisban

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Azure Cosmos DB tároló törlése

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Erőforrás-zárolás létrehozása Azure Cosmos DB-tárolón a törlés megakadályozása érdekében

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Erőforrás-zárolás eltávolítása Azure Cosmos DB tárolón

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Következő lépések

* [Minden PowerShell-minta](powershell-samples.md)
* [Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Azure Cosmos DB tároló létrehozása](how-to-create-container.md)
* [A Azure Cosmos DB élettartamának konfigurálása](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/