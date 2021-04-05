---
title: Adatátviteli kapacitás kiépítése Azure Cosmos DB Cassandra API erőforrásokon
description: Megtudhatja, hogyan hozhat létre tárolót, adatbázist és Azure Cosmos DB Cassandra API erőforrásokban lévő adatátviteli teljesítményt. A Azure Portal, a CLI, a PowerShell és más SDK-k használatát fogja használni.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086278"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Az adatbázis, a tároló vagy az autoskálázás átviteli sebességének kiépítése Azure Cosmos DB Cassandra API erőforrásokon
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ez a cikk azt ismerteti, hogyan kell kiépíteni az átviteli sebességet Azure Cosmos DB Cassandra API. Egy tárolón vagy egy adatbázison is kiépítheti a standard (manuális) vagy az automatikus méretezési sebességet, és megoszthatja azt az adatbázisban lévő tárolók között. Az átviteli sebességet Azure Portal, Azure CLI vagy Azure Cosmos DB SDK-k használatával is kiépítheti.

Ha más API-t használ, tekintse meg az [SQL API](how-to-provision-container-throughput.md)-t, az [MongoDB API](how-to-provision-throughput-mongodb.md)-t, az [Gremlin API](how-to-provision-throughput-gremlin.md) -cikkeket az átviteli sebesség kiépítéséhez.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új tábla** lehetőséget. Ezután adja meg a következő adatokat:

   * Jelezze, hogy új lemezterületet hoz létre, vagy egy meglévőt használ. Válassza az **adatbázis átviteli sebességének** kiosztása lehetőséget, ha a lemezterület szintjén szeretné kiépíteni az átviteli sebességet.
   * Adja meg a tábla AZONOSÍTÓját a CQL-parancson belül.
   * Adja meg az elsődleges kulcs értékét (például: `/userrID` ).
   * Adja meg a kiépíteni kívánt átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Képernyőkép a Adatkezelőról, amikor új gyűjteményt hoz létre adatbázis-szintű átviteli sebességgel":::

> [!Note]
> Ha az átviteli sebességet egy Cassandra API konfigurált Azure Cosmos-fiókban lévő tárolóban szeretné kiépíteni, használja `/myPrimaryKey` a partíciós kulcs elérési útját.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Egy Cassandra-tábla teljesítményének kiépítése

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Hasonló parancsok a CQL-kompatibilis illesztőprogramokon keresztül is kiállíthatók.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>A Cassandra Tables adatforgalmának módosítása vagy módosítása

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Hasonló parancs bármely CQL-kompatibilis illesztőprogrammal végrehajtható.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-sablonokkal az összes Azure Cosmos DB API-ra kiépíthető az adatbázison vagy a tároló szintjén található adatátviteli sebesség. Lásd: [Azure Resource Manager-sablonok Azure Cosmos db](templates-samples-cassandra.md) for Samples.

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A példákat lásd: [Azure CLI-minták Azure Cosmos DBhoz](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell használatával az összes Azure Cosmos DB API-ra kiépíthető az adatátviteli sebesség az adatbázison vagy a tároló szintű erőforrásokon. A mintáknál tekintse meg [a Azure Cosmos DB Azure PowerShell mintáit](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet a Azure Cosmos DBban:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)