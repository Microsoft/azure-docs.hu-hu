---
title: Tároló létrehozása Azure Cosmos DB Cassandra API
description: Megtudhatja, hogyan hozhat létre tárolót Azure Cosmos DB-Cassandra API a Azure Portal, a .NET, a Java, a Python, a Node.js és más SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 01030a563c15eee7786058c2eae30d23803dfe42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101646"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>Tároló létrehozása Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ez a cikk ismerteti a tárolók létrehozásának különböző módszereit Azure Cosmos DB Cassandra API. Bemutatja, hogyan hozhat létre egy tárolót a Azure Portal, az Azure CLI, a PowerShell vagy a támogatott SDK-k használatával. Ez a cikk bemutatja, hogyan hozhat létre tárolót, hogyan adhatja meg a partíciós kulcsot, és hogyan lehet kiépíteni az átviteli sebességet.

Ez a cikk ismerteti a tárolók létrehozásának különböző módszereit Azure Cosmos DB Cassandra API. Ha más API-t használ, tekintse meg a tároló létrehozásához a [MongoDB, a](how-to-create-container-mongodb.md) [Gremlin api](how-to-create-container-gremlin.md), a [Table API](how-to-create-container-table.md)és az [SQL API](how-to-create-container.md) -cikkek API-ját.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>Létrehozás az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-cassandra-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tábla** lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új lemezterületet hoz létre, vagy egy meglévőt használ.
   * Adja meg egy tábla nevét.
   * Adja meg a tulajdonságokat, és adjon meg egy elsődleges kulcsot.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Képernyőkép a Cassandra APIről, tábla hozzáadása párbeszédpanel":::

> [!NOTE]
> A Cassandra API esetében a rendszer az elsődleges kulcsot használja partíciókulcsként.

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>Létrehozás a .NET SDK használatával

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

Ha a gyűjtemény létrehozásakor időtúllépési kivételt tapasztal, végezzen el egy olvasási műveletet annak ellenőrzéséhez, hogy a gyűjtemény létrehozása sikeres volt-e. Az olvasási művelet kivételt jelez, amíg a gyűjtemény-létrehozási művelet nem sikerült. A létrehozási művelet által támogatott állapotkódok listájának megtekintéséhez tekintse [meg Azure Cosmos db cikk HTTP-állapotkódot](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Létrehozás az Azure CLI használatával

[Cassandra-tábla létrehozása az Azure CLI-vel](./scripts/cli/cassandra/create.md). Az összes Azure Cosmos DB API-val kapcsolatos összes Azure CLI-minta listáját lásd: [Azure CLI-minták a Azure Cosmos DBhoz](cli-samples.md).

## <a name="create-using-powershell"></a>Létrehozás a PowerShell használatával

[Hozzon létre egy Cassandra táblát a PowerShell használatával](./scripts/powershell/cassandra/create.md). Az összes Azure Cosmos DB API-val kapcsolatos PowerShell-minta listázásához lásd: [PowerShell-minták](powershell-samples.md)

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](./account-databases-containers-items.md)