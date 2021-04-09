---
title: Tároló létrehozása Azure Cosmos DB SQL API-ban
description: Megtudhatja, hogyan hozhat létre tárolót Azure Cosmos DB SQL API-ban Azure Portal, .NET, Java, Python, Node.js és más SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006848"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Tároló létrehozása Azure Cosmos DB SQL API-ban
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk ismerteti a tárolók létrehozásának különböző módszereit Azure Cosmos DB SQL API-ban. Bemutatja, hogyan hozható létre tároló a Azure Portal, az Azure CLI, a PowerShell vagy a támogatott SDK-k használatával. Ez a cikk bemutatja, hogyan hozhat létre tárolót, hogyan adhatja meg a partíciós kulcsot, és hogyan lehet kiépíteni az átviteli sebességet.

Ez a cikk ismerteti a tárolók létrehozásának különböző módszereit Azure Cosmos DB SQL API-ban. Ha más API-t használ, tekintse meg a tároló létrehozásához a MongoDB, [Cassandra API](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)és [Table API](how-to-create-container-table.md) cikkek [API](how-to-create-container-mongodb.md)-ját.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Tároló létrehozása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tároló** elemet. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló AZONOSÍTÓját.
   * Adjon meg egy partíciós kulcsot.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Képernyőkép a Adatkezelő panelről, új tároló kiemelve":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Tároló létrehozása az Azure CLI használatával

[Hozzon létre egy tárolót az Azure CLI-vel](manage-with-cli.md#create-a-container). Az összes Azure Cosmos DB API-val kapcsolatos összes Azure CLI-minta listáját lásd: [Azure CLI-minták a Azure Cosmos DBhoz](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Tároló létrehozása a PowerShell használatával

[Hozzon létre egy tárolót a PowerShell használatával](manage-with-powershell.md#create-container). Az összes Azure Cosmos DB API-val kapcsolatos PowerShell-minta listázásához lásd: [PowerShell-minták](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Tároló létrehozása .NET SDK használatával

Ha a gyűjtemény létrehozásakor időtúllépési kivételt tapasztal, végezzen el egy olvasási műveletet annak ellenőrzéséhez, hogy a gyűjtemény létrehozása sikeres volt-e. Az olvasási művelet kivételt jelez, amíg a gyűjtemény-létrehozási művelet nem sikerült. A létrehozási művelet által támogatott állapotkódok listájának megtekintéséhez tekintse [meg Azure Cosmos db cikk HTTP-állapotkódot](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](./account-databases-containers-items.md)