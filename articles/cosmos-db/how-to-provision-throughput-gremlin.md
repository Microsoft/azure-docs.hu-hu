---
title: Adatátviteli kapacitás kiépítése Azure Cosmos DB Gremlin API-erőforrásokon
description: Megtudhatja, hogyan hozhat létre tárolót, adatbázist és Azure Cosmos DB Gremlin API-erőforrásokban lévő adatátviteli teljesítményt. A Azure Portal, a CLI, a PowerShell és más SDK-k használatát fogja használni.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086159"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Adatbázis, tároló vagy méretezési teljesítmény kiépítése Azure Cosmos DB Gremlin API-erőforrásokon
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Ez a cikk azt ismerteti, hogyan kell kiépíteni az átviteli sebességet Azure Cosmos DB Gremlin API-ban. Egy tárolón vagy egy adatbázison is kiépítheti a standard (manuális) vagy az automatikus méretezési sebességet, és megoszthatja azt az adatbázisban lévő tárolók között. Az átviteli sebességet Azure Portal, Azure CLI vagy Azure Cosmos DB SDK-k használatával is kiépítheti.

Ha más API-t használ, az átviteli sebesség kiépítéséhez lásd: [SQL API](how-to-provision-container-throughput.md), [Cassandra API](how-to-provision-throughput-cassandra.md), [API MongoDB](how-to-provision-throughput-mongodb.md) cikkekhez.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új gráf** lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ. Válassza az **adatbázis átviteli sebességének kiépítése** lehetőséget, ha az átviteli sebességet az adatbázis szintjén szeretné kiépíteni.
   * Adja meg a gráf AZONOSÍTÓját.
   * Adja meg a partíciós kulcs értékét (például: `/ItemID` ).
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Képernyőkép a Adatkezelőról, amikor új gráfot hoz létre adatbázis-szintű átviteli sebességgel":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Az SQL API-hoz készült Cosmos SDK-k segítségével az összes Azure Cosmos DB API-ra kiépítheti az átviteli sebességet, a Cassandra és a MongoDB API kivételével.

### <a name="provision-container-level-throughput"></a>Tároló szintű átviteli sebesség kiépítése

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

### <a name="provision-database-level-throughput"></a>Adatbázis-szint átviteli sebességének kiépítése

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-sablonokkal az összes Azure Cosmos DB API-ra kiépíthető az adatbázison vagy a tároló szintjén található adatátviteli sebesség. Lásd: [Azure Resource Manager-sablonok Azure Cosmos db](templates-samples-gremlin.md) for Samples.

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A példákat lásd: [Azure CLI-minták Azure Cosmos DBhoz](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A mintáknál tekintse meg [a Azure Cosmos DB Azure PowerShell mintáit](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet a Azure Cosmos DBban:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)