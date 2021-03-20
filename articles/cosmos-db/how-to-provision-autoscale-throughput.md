---
title: Az adatméretezési teljesítmény kiépítése a Azure Cosmos DB SQL API-ban
description: Megtudhatja, hogyan építhet ki az Azure Cosmos DB SQL API-k tároló-és adatbázis-szintjén található, az Azure Portal, a CLI, a PowerShell és más SDK-k használatával történő autoskálázási teljesítményt.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 52904296df77d9097a6180345388e8e702e2bca0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97357624"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>A Azure Cosmos DB-SQL API-ban lévő adatbázison vagy tárolón lévő autoskálázási teljesítmény kiépítése
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azt ismerteti, hogyan lehet kiépíteni az adatátviteli sebességet egy adatbázison vagy tárolón (gyűjtemény, gráf vagy tábla) Azure Cosmos DB SQL API-ban. Engedélyezheti az autoskálázást egyetlen tárolón, vagy kiépítheti az adatátviteli sebességet egy adatbázison, és megoszthatja azt az adatbázis összes tárolója között.

Ha más API-t használ, tekintse meg a [MongoDB API](how-to-provision-throughput-mongodb.md)-t, [CASSANDRA API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -cikkeket az átviteli sebesség kiépítéséhez.

## <a name="azure-portal"></a>Azure Portal

### <a name="create-new-database-or-container-with-autoscale"></a>Új adatbázis vagy tároló létrehozása az autoscale paranccsal

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) vagy a [Azure Cosmos db Explorerben.](https://cosmos.azure.com/)

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza az **új tároló elemet.** Adja meg az adatbázis, a tároló és a partíciós kulcs nevét. Az **átviteli sebesség** területen válassza az **autoskálázás** lehetőséget, és állítsa be azt a [maximális átviteli sebességet (ru/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) , amelyet az adatbázisnak vagy a tárolónak méreteznie kell.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Tároló létrehozása és az autoscale kiépített átviteli sebességének konfigurálása":::

1. Válassza az **OK** lehetőséget.

Ha a megosztott átviteli sebességű adatbázison szeretné kiépíteni az autoskálázást, válassza az **adatbázis átviteli sebességének kiépítése** lehetőséget új adatbázis létrehozásakor. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Az autoskálázás engedélyezése meglévő adatbázison vagy tárolón

> [!IMPORTANT]
> A jelenlegi kiadásban a Azure Portal az automatikus méretezés és a standard (manuális) kiépített átviteli sebesség közötti áttelepítés egyetlen módja. 

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) vagy a [Azure Cosmos db Explorerben.](https://cosmos.azure.com/)

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza ki a tároló **méretezését és beállításait** , vagy méretezze át az adatbázis **méretét** .

1. A **skála** területen válassza az **autoskálázás** lehetőséget, és **mentse**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Az autoskálázás engedélyezése egy meglévő tárolón":::

> [!NOTE]
> Ha engedélyezi az automatikus méretezést egy meglévő adatbázison vagy tárolón, a maximális RU/s értékét a rendszer határozza meg, az aktuális manuális kiépített átviteli sebesség beállításaitól és tárterülettől függően. A művelet befejezése után szükség esetén módosíthatja a maximális RU/mp-t. [Részletek](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB .NET v3 SDK

A Azure Cosmos DB .NET SDK for SQL API [3,9-es vagy újabb verzióját](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) használja az autoscale-erőforrások kezeléséhez. 

> [!IMPORTANT]
> Használhatja a .NET SDK-t új autoskálázási erőforrások létrehozásához. Az SDK nem támogatja az automatikus skálázás és a standard (manuális) átviteli sebesség közötti áttelepítést. Az áttelepítési forgatókönyv jelenleg csak a Azure Portal támogatott. 

### <a name="create-database-with-shared-throughput"></a>Adatbázis létrehozása megosztott átviteli sebességgel

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Tároló létrehozása dedikált átviteli sebességgel

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Az aktuális átviteli sebesség (RU/s) olvasása

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Az autoskálázás maximális átviteli sebességének módosítása (RU/s)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB Java v4 SDK

A Azure Cosmos DB Java SDK for SQL API [4,0-es vagy újabb verzióját](https://mvnrepository.com/artifact/com.azure/azure-cosmos) használhatja az autoscale-erőforrások kezeléséhez.

> [!IMPORTANT]
> A Java SDK használatával új autoscale-erőforrásokat hozhat létre. Az SDK nem támogatja az automatikus skálázás és a standard (manuális) átviteli sebesség közötti áttelepítést. Az áttelepítési forgatókönyv jelenleg csak a Azure Portal támogatott.

### <a name="create-database-with-shared-throughput"></a>Adatbázis létrehozása megosztott átviteli sebességgel

#### <a name="async"></a>[Aszinkron](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Tároló létrehozása dedikált átviteli sebességgel

#### <a name="async"></a>[Aszinkron](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Az aktuális átviteli sebesség (RU/s) olvasása

#### <a name="async"></a>[Aszinkron](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Az autoskálázás maximális átviteli sebességének módosítása (RU/s)

#### <a name="async"></a>[Aszinkron](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-sablonokkal az összes Azure Cosmos DB API-ra kiépíthető az adatbázison vagy a tároló szintjén található adatátviteli sebesség. Lásd: [Azure Resource Manager-sablonok Azure Cosmos db](./templates-samples-sql.md) for Samples.

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A példákat lásd: [Azure CLI-minták Azure Cosmos DBhoz](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A mintáknál tekintse meg [a Azure Cosmos DB Azure PowerShell mintáit](powershell-samples.md).

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogy milyen [előnyökkel jár a kiépített átviteli sebesség az autoskálázással](provision-throughput-autoscale.md#benefits-of-autoscale).
* Megtudhatja, hogyan [választhat a manuális és az automatikus skálázási sebesség közül](how-to-choose-offer.md).
* Tekintse át az [autoscale – gyakori kérdések](autoscale-faq.md)című szakaszt.
