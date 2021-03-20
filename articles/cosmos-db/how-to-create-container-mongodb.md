---
title: Tároló létrehozása a MongoDB Azure Cosmos DB API-ban
description: Megtudhatja, hogyan hozhat létre tárolót a MongoDB Azure Cosmos DB API-ban a Azure Portal, a .NET, a Java, a Node.js és más SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101595"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Tároló létrehozása a MongoDB Azure Cosmos DB API-ban
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a cikk bemutatja, hogyan hozhat létre tárolót a MongoDB Azure Cosmos DB API-ban. Bemutatja, hogyan hozhat létre egy tárolót a Azure Portal, az Azure CLI, a PowerShell vagy a támogatott SDK-k használatával. Ez a cikk bemutatja, hogyan hozhat létre tárolót, hogyan adhatja meg a partíciós kulcsot, és hogyan lehet kiépíteni az átviteli sebességet.

Ez a cikk bemutatja, hogyan hozhat létre tárolót a MongoDB Azure Cosmos DB API-ban. Ha más API-t használ, tekintse meg az [SQL API](how-to-create-container.md)-t, a [Cassandra APIt](how-to-create-container-cassandra.md), a [Gremlin API](how-to-create-container-gremlin.md)-t és a [Table API](how-to-create-container-table.md) cikkeket a tároló létrehozásához.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Létrehozás az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tároló** elemet. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló AZONOSÍTÓját.
   * Adja meg a szegmens kulcsát.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Képernyőkép a MongoDB Azure Cosmos DB API-ról, tároló hozzáadása párbeszédpanel":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Létrehozás a .NET SDK használatával

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> A MongoDB huzal protokoll nem ismeri a [kérelmek egységének](request-units.md)fogalmát. Ha olyan új gyűjteményt szeretne létrehozni, amely számára kiépített átviteli sebességgel rendelkezik, használja a Azure Portal vagy Cosmos DB SDK-kat az SQL API-hoz.

Ha a gyűjtemény létrehozásakor időtúllépési kivételt tapasztal, végezzen el egy olvasási műveletet annak ellenőrzéséhez, hogy a gyűjtemény létrehozása sikeres volt-e. Az olvasási művelet kivételt jelez, amíg a gyűjtemény-létrehozási művelet nem sikerült. A létrehozási művelet által támogatott állapotkódok listájának megtekintéséhez tekintse [meg Azure Cosmos db cikk HTTP-állapotkódot](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Létrehozás az Azure CLI használatával

[Hozzon létre egy gyűjteményt Azure Cosmos db for MONGODB API-hoz az Azure CLI-vel](./scripts/cli/mongodb/create.md). Az összes Azure Cosmos DB API-val kapcsolatos összes Azure CLI-minta listáját lásd: [Azure CLI-minták a Azure Cosmos DBhoz](cli-samples.md).

## <a name="create-using-powershell"></a>Létrehozás a PowerShell használatával

[Hozzon létre egy gyűjteményt Azure Cosmos db for MONGODB API-hoz a PowerShell](./scripts/powershell/mongodb/create.md)-lel. Az összes Azure Cosmos DB API-val kapcsolatos PowerShell-minta listázásához lásd: [PowerShell-minták](powershell-samples.md)

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Tároló létrehozása Azure Resource Manager sablonok használatával

[Hozzon létre egy gyűjteményt Azure Cosmos db for MONGODB API-hoz Resource Manager-sablonnal](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](./account-databases-containers-items.md)