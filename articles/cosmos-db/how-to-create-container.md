---
title: Tároló létrehozása az Azure Cosmos DB-ben
description: Megtudhatja, hogyan hozhat létre tárolót Azure Cosmos DBban Azure Portal, .NET, Java, Python, Node.js és más SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2362326bccd90af997aa9237ec5f14e39ae62c85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019997"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos-tároló létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Cosmos-tárolót (gyűjtemény, tábla vagy gráf) a Azure Portal, az Azure CLI, a PowerShell vagy a támogatott SDK-k használatával. Ez a cikk bemutatja, hogyan hozhat létre tárolót, hogyan adhatja meg a partíciós kulcsot, és hogyan lehet kiépíteni az átviteli sebességet.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="create-a-container-using-azure-portal"></a>Tároló létrehozása az Azure Portal használatával

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tároló**elemet. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló AZONOSÍTÓját.
   * Adjon meg egy partíciós kulcsot.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Képernyőkép a Adatkezelő panelről, új tároló kiemelve":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tároló**elemet. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a tároló AZONOSÍTÓját.
   * Adja meg a szegmens kulcsát.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Képernyőkép a Adatkezelő panelről, új tároló kiemelve":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-cassandra-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tábla**lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új lemezterületet hoz létre, vagy egy meglévőt használ.
   * Adja meg egy tábla nevét.
   * Adja meg a tulajdonságokat, és adjon meg egy elsődleges kulcsot.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Képernyőkép a Adatkezelő panelről, új tároló kiemelve":::

> [!NOTE]
> A Cassandra API esetében a rendszer az elsődleges kulcsot használja partíciókulcsként.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-graph-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új gráf**lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új adatbázist hoz létre, vagy egy meglévőt használ.
   * Adja meg a gráf AZONOSÍTÓját.
   * Válassza a **Korlátlan** tárolási kapacitás lehetőséget.
   * Adja meg a csúcspontok partíciós kulcsát.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Képernyőkép a Adatkezelő panelről, új tároló kiemelve":::

### <a name="table-api"></a><a id="portal-table"></a>Table API

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-table-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tábla**lehetőséget. Ezután adja meg a következő adatokat:

   * Adja meg a tábla AZONOSÍTÓját.
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Kattintson az **OK** gombra.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Képernyőkép a Adatkezelő panelről, új tároló kiemelve":::

> [!Note]
> A Table API esetében a rendszer minden új sor hozzáadásakor megad egy partíciókulcsot.

## <a name="create-a-container-using-azure-cli"></a>Tároló létrehozása az Azure CLI-vel<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Az alábbi hivatkozások bemutatják, hogyan hozhat létre Azure Cosmos DB tároló-erőforrásokat az Azure CLI használatával.

Az összes Azure Cosmos DB API-val kapcsolatos összes Azure CLI-minta listáját lásd: [Azure CLI-minták a Azure Cosmos DBhoz](cli-samples.md).

* [Tároló létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-container)
* [Azure Cosmos DB gyűjtemény létrehozása a MongoDB API-hoz az Azure CLI-vel](./scripts/cli/mongodb/create.md)
* [Cassandra-tábla létrehozása az Azure CLI-vel](./scripts/cli/cassandra/create.md)
* [Gremlin gráf létrehozása az Azure CLI-vel](./scripts/cli/gremlin/create.md)
* [Table API tábla létrehozása az Azure CLI-vel](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>Tároló létrehozása a PowerShell használatával

Az alábbi hivatkozások bemutatják, hogyan hozhat létre Azure Cosmos DB tároló-erőforrásokat a PowerShell használatával.

Az összes Azure Cosmos DB API-val kapcsolatos PowerShell-minta listázásához lásd: [PowerShell-minták](powershell-samples.md)

* [Tároló létrehozása a PowerShell-lel](manage-with-powershell.md#create-container)
* [Azure Cosmos DB gyűjtemény létrehozása a MongoDB API-hoz a PowerShell-lel](./scripts/powershell/mongodb/create.md)
* [Cassandra-tábla létrehozása a PowerShell-lel](./scripts/powershell/cassandra/create.md)
* [Gremlin gráf létrehozása a PowerShell-lel](./scripts/powershell/gremlin/create.md)
* [Table API tábla létrehozása a PowerShell-lel](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>Tároló létrehozása .NET SDK használatával

Ha a gyűjtemény létrehozásakor időtúllépési kivételt tapasztal, végezzen el egy olvasási műveletet annak ellenőrzéséhez, hogy a gyűjtemény létrehozása sikeres volt-e. Az olvasási művelet kivételt jelez, amíg a gyűjtemény-létrehozási művelet nem sikerült. A létrehozási művelet által támogatott állapotkódok listájának megtekintéséhez tekintse [meg Azure Cosmos db cikk HTTP-állapotkódot](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL API és Gremlin API

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> A MongoDB huzal protokoll nem ismeri a [kérelmek egységének](request-units.md)fogalmát. Ha olyan új gyűjteményt szeretne létrehozni, amely számára kiépített átviteli sebességgel rendelkezik, használja a Azure Portal vagy Cosmos DB SDK-kat az SQL API-hoz.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](account-overview.md)
