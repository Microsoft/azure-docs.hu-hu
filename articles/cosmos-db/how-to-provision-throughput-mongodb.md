---
title: Átviteli sebesség biztosítása Azure Cosmos DB API-hoz a MongoDB-erőforrásokhoz
description: Megtudhatja, hogyan építhet ki tárolókat, adatbázisokat és Azure Cosmos DB API-t az MongoDB-erőforrások számára. A Azure Portal, a CLI, a PowerShell és más SDK-k használatát fogja használni.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086125"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Adatbázis, tároló vagy méretezési sebesség kiépítése a MongoDB-erőforrások Azure Cosmos DB API-ra
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a cikk azt ismerteti, hogyan kell kiépíteni az átviteli sebességet a MongoDB Azure Cosmos DB API-ban. Egy tárolón vagy egy adatbázison is kiépítheti a standard (manuális) vagy az automatikus méretezési sebességet, és megoszthatja azt az adatbázisban lévő tárolók között. Az átviteli sebességet Azure Portal, Azure CLI vagy Azure Cosmos DB SDK-k használatával is kiépítheti.

Ha más API-t használ, az átviteli sebesség kiépítéséhez lásd: [SQL API](how-to-provision-container-throughput.md), [CASSANDRA API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -cikkek.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új gyűjtemény** lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ. Válassza az **adatbázis átviteli sebességének kiépítése** lehetőséget, ha az átviteli sebességet az adatbázis szintjén szeretné kiépíteni.
   * Adja meg a gyűjtemény AZONOSÍTÓját.
   * Adja meg a partíciós kulcs értékét (például: `/ItemID` ).
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Képernyőkép a Adatkezelőról, amikor új gyűjteményt hoz létre adatbázis-szintű átviteli sebességgel":::

> [!Note]
> Ha a MongoDB Azure Cosmos DB API-val konfigurált Azure Cosmos-fiókban lévő tárolón való kiépítési átviteli sebességre van konfigurálva, használja a `/myShardKey` (z) partíciót a partíciós kulcs elérési útjára.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-sablonokkal az összes Azure Cosmos DB API-ra kiépíthető az adatbázison vagy a tároló szintjén található adatátviteli sebesség. Lásd: [Azure Resource Manager-sablonok Azure Cosmos db](templates-samples-mongodb.md) for Samples.

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A példákat lásd: [Azure CLI-minták Azure Cosmos DBhoz](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A mintáknál tekintse meg [a Azure Cosmos DB Azure PowerShell mintáit](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet a Azure Cosmos DBban:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)