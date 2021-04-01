---
title: Azure Cosmos DB Core (SQL) API-erőforrások kezelése az Azure CLI-vel
description: Azure Cosmos DB Core (SQL) API-erőforrások kezelése az Azure CLI-vel.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: b13f5bfffced9afd80663d606e30e028e52643ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563836"
---
# <a name="manage-azure-cosmos-core-sql-api-resources-using-azure-cli"></a>Azure Cosmos Core-(SQL-) API-erőforrások kezelése az Azure CLI-vel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az alábbi útmutató az Azure Cosmos DB-fiókok, -adatbázisok és -tárolók felügyeletének Azure CLI-vel történő automatizálásához használható általános parancsokat ismerteti. Az összes Azure Cosmos DB CLI-parancsra vonatkozó hivatkozási lapok az [Azure CLI-referenciában](/cli/azure/cosmosdb)érhetők el. Az [Azure CLI-mintákban](cli-samples.md)további példákat is találhat Azure Cosmos DBhoz, beleértve Cosmos db fiókok, adatbázisok és tárolók létrehozását és kezelését a MongoDB, a Gremlin, a Cassandra és a Table APIhoz.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

Azure CLI-minták más API-khoz lásd: [CLI-minták a Cassandra](cli-samples-cassandra.md), CLI-minták a [MongoDB API](cli-samples-mongodb.md)-hoz, CLI-minták a [Gremlin](cli-samples-gremlin.md), CLI-minták [táblázathoz](cli-samples-table.md)

> [!IMPORTANT]
> Azure Cosmos DB erőforrásokat nem lehet átnevezni, mivel ez megsérti, hogyan működik az Azure Resource Manager erőforrás-URI-k.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-fiókok

Az alábbi szakaszban bemutatjuk, hogyan kezelheti az Azure Cosmos-fiókot, beleértve a következőket:

* [Azure Cosmos-fiók létrehozása](#create-an-azure-cosmos-db-account)
* [Régiók hozzáadása vagy eltávolítása](#add-or-remove-regions)
* [Többrégiós írások engedélyezése](#enable-multiple-write-regions)
* [Regionális feladatátvételi prioritás beállítása](#set-failover-priority)
* [Automatikus feladatátvétel engedélyezése](#enable-automatic-failover)
* [Manuális feladatátvétel indítása](#trigger-manual-failover)
* [Fiók kulcsainak listázása](#list-account-keys)
* [Csak olvasható fiókok kulcsainak listázása](#list-read-only-account-keys)
* [Kapcsolatok karakterláncok listázása](#list-connection-strings)
* [Fiók kulcsának újralétrehozása](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos DB fiókot SQL API-val, munkamenet-konzisztencia az USA 2. nyugati régiójában és az USA 2. keleti régiójában:

> [!IMPORTANT]
> Az Azure Cosmos-fiók nevének kisbetűnek kell lennie, és 44 karakternél rövidebbnek kell lennie.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>Régiók hozzáadása vagy eltávolítása

Hozzon létre egy Azure Cosmos-fiókot két régióval, adjon hozzá egy régiót, és távolítson el egy régiót.

> [!NOTE]
> Nem lehet egyszerre hozzáadni vagy eltávolítani a régiókat `locations` , és módosítani az Azure Cosmos-fiók egyéb tulajdonságait. A régiók módosításait külön műveletként kell végrehajtani, mint a fiók erőforrásának egyéb módosításait.
> [!NOTE]
> Ezzel a paranccsal hozzáadhat és eltávolíthat régiókat, de nem teszi lehetővé a feladatátvételi prioritások módosítását, illetve manuális feladatátvételt is indíthat. Lásd: a [feladatátvételi prioritás beállítása](#set-failover-priority) és a [manuális feladatátvétel elindítása](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>Több írási régió engedélyezése

Többrégiós írások engedélyezése Cosmos-fiókhoz

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>Feladatátvételi prioritás beállítása

Az automatikus feladatátvételhez konfigurált Azure Cosmos-fiók feladatátvételi prioritásának beállítása

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a>Automatikus feladatátvétel engedélyezése

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>Manuális feladatátvétel indítása

> [!CAUTION]
> Ha a Priority = 0 prioritású régiót módosítja, manuális feladatátvételt indít egy Azure Cosmos-fiókhoz. A többi prioritási változás nem indít el feladatátvételt.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> Az összes fiók kulcsainak listázása

Egy Cosmos-fiókhoz tartozó összes kulcs beolvasása.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>Csak olvasható fiókok kulcsainak listázása

Egy Cosmos-fiók írásvédett kulcsainak beolvasása.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>Kapcsolatok karakterláncok listázása

A Cosmos-fiókhoz tartozó kapcsolatok karakterláncának beolvasása.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>Fiók kulcsának újralétrehozása

Új kulcs újralétrehozása Cosmos-fiókhoz.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Adatbázis Azure Cosmos DB

A következő szakaszban bemutatjuk, hogyan kezelheti a Azure Cosmos DB-adatbázist, beleértve a következőket:

* [Adatbázis létrehozása](#create-a-database)
* [Megosztott átviteli sebességgel rendelkező adatbázis létrehozása](#create-a-database-with-shared-throughput)
* [Adatbázis migrálása az átméretezési sebességre](#migrate-a-database-to-autoscale-throughput)
* [Adatbázis átviteli sebességének módosítása](#change-database-throughput)
* [Adatbázis törlésének megakadályozása](#prevent-a-database-from-being-deleted)

### <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy Cosmos-adatbázist.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>Megosztott átviteli sebességgel rendelkező adatbázis létrehozása

Hozzon létre egy Cosmos-adatbázist közös átviteli sebességgel.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="migrate-a-database-to-autoscale-throughput"></a>Adatbázis migrálása az átméretezési sebességre

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

# Migrate to autoscale throughput
az cosmosdb sql database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="change-database-throughput"></a>Adatbázis átviteli sebességének módosítása

Egy Cosmos-adatbázis átviteli sebességének növelése 1000 RU/s használatával.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="prevent-a-database-from-being-deleted"></a>Adatbázis törlésének megakadályozása

Az adatbázis törlésének megakadályozása érdekében helyezzen el egy Azure-erőforrást törlő zárolást. Ez a funkció megköveteli, hogy a Cosmos-fiókot a adatsík SDK-k módosítsák. További információ: az SDK-k [változásainak megakadályozása](role-based-access-control.md#prevent-sdk-changes). Az Azure-erőforrások zárolása azt is megakadályozhatja, hogy az erőforrást egy zárolási típus megadásával is meg lehessen változtatni `ReadOnly` . A Cosmos-adatbázisok esetében az átviteli sebesség módosításának megakadályozása érdekében használható.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB tároló

Az alábbi részben bemutatjuk, hogyan kezelheti a Azure Cosmos DB tárolót, beleértve a következőket:

* [Tároló létrehozása](#create-a-container)
* [Tároló létrehozása az autoscale paranccsal](#create-a-container-with-autoscale)
* [Hozzon létre egy olyan tárolót, amelynek ÉLETTARTAMa engedélyezve van](#create-a-container-with-ttl)
* [Egyéni index-házirenddel rendelkező tároló létrehozása](#create-a-container-with-a-custom-index-policy)
* [Tároló átviteli sebességének módosítása](#change-container-throughput)
* [Tároló migrálása az átméretezési átviteli sebességre](#migrate-a-container-to-autoscale-throughput)
* [Tároló törlésének megakadályozása](#prevent-a-container-from-being-deleted)

### <a name="create-a-container"></a>Tároló létrehozása

Hozzon létre egy Cosmos-tárolót az alapértelmezett index-házirenddel, a partíciós kulccsal és a 400-es RU/s-val.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>Tároló létrehozása az autoscale paranccsal

Hozzon létre egy Cosmos-tárolót az alapértelmezett index-házirenddel, a partíciós kulccsal és az 4000-es Automatikus méretezéssel.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>Hozzon létre egy tárolót az ÉLETTARTAMmal

Hozzon létre egy Cosmos-tárolót, amelyen engedélyezve van az élettartam.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>Egyéni index-házirenddel rendelkező tároló létrehozása

Hozzon létre egy Cosmos-tárolót egyéni index-házirenddel, térbeli indexszel, összetett indexszel, a 400-es partíciós kulccsal és RU/s-vel.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>Tároló átviteli sebességének módosítása

Növelje egy Cosmos-tároló átviteli sebességét 1000 RU/s használatával.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="migrate-a-container-to-autoscale-throughput"></a>Tároló migrálása az átméretezési átviteli sebességre

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

# Migrate to autoscale throughput
az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="prevent-a-container-from-being-deleted"></a>Tároló törlésének megakadályozása

Egy tárolóban lévő Azure-erőforrás törlési zárolásával megakadályozható a törlés. Ez a funkció megköveteli, hogy a Cosmos-fiókot a adatsík SDK-k módosítsák. További információ: az SDK-k [változásainak megakadályozása](role-based-access-control.md#prevent-sdk-changes). Az Azure-erőforrások zárolása azt is megakadályozhatja, hogy az erőforrást egy zárolási típus megadásával is meg lehessen változtatni `ReadOnly` . A Cosmos-tárolók esetében ez felhasználható az átviteli sebesség vagy bármely más tulajdonság módosításának megakadályozására.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információkért lásd:

* [Telepítse az Azure CLI-t](/cli/azure/install-azure-cli)
* [Azure CLI-dokumentáció](/cli/azure/cosmosdb)
* [További Azure CLI-minták a Azure Cosmos DB](cli-samples.md)