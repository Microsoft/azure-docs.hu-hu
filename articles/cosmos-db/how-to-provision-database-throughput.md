---
title: Adatbázis átviteli sebességének kiépítése Azure Cosmos DB SQL API-ban
description: Megtudhatja, hogyan építhet ki adatátviteli sebességet Azure Cosmos DB SQL API adatbázis szintjén Azure Portal, CLI, PowerShell és számos más SDK használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341994"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Standard (manuális) átviteli sebesség kiépítése egy adatbázison Azure Cosmos DB-SQL API-ban
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azt ismerteti, hogyan lehet a standard (manuális) átviteli sebességet Azure Cosmos DB SQL API-ban található adatbázison kiépíteni. Az átviteli sebességet egyetlen [tárolóhoz](how-to-provision-container-throughput.md)vagy egy adatbázishoz is kiépítheti, és megoszthatja az átviteli sebességet a tárolóban lévő tárolók között. A tárolók szintjének és az adatbázis szintű átviteli sebesség használatának megismeréséhez tekintse [meg a tárolók és adatbázisok átviteli sebességének használati esetei](set-throughput.md) című cikket. Az adatbázis-szint átviteli sebességét az Azure Portal vagy a Azure Cosmos DB SDK-k használatával is kiépítheti.

Ha más API-t használ, tekintse meg a [MongoDB API](how-to-provision-throughput-mongodb.md)-t, [CASSANDRA API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -cikkeket az átviteli sebesség kiépítéséhez.

## <a name="provision-throughput-using-azure-portal"></a>Átviteli sebesség kiosztása az Azure Portallal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account), vagy válasszon ki egy meglévő Azure Cosmos-fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza az **új adatbázis** lehetőséget. Adja meg a következő adatokat:

   * Adja meg az adatbázis AZONOSÍTÓját.
   * Válassza az **adatbázis-átviteli sebesség** beállítása lehetőséget.
   * Adja meg az átviteli sebességet (például 1000 RUs).
   * Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Képernyőkép az új adatbázis párbeszédpanelről":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Átviteli sebesség kiépítése az Azure CLI vagy a PowerShell használatával

Megosztott átviteli sebességgel rendelkező adatbázis létrehozása:

* [Adatbázis létrehozása az Azure CLI-vel](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Adatbázis létrehozása a PowerShell használatával](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Átviteli sebesség kiosztása a .NET SDK használatával

> [!Note]
> Az SQL API-hoz az Azure Cosmos SDK-k használatával kiépítheti az átviteli sebességet az összes API-hoz. Az alábbi példát is használhatja Cassandra APIhoz is.

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

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekből megtudhatja, hogyan lehet kiépíteni az átviteli sebességet Azure Cosmos DBban:

* [Kiosztott átviteli sebesség globális skálázása](./request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [A standard (manuális) átviteli sebesség kiépítése egy tárolóhoz](how-to-provision-container-throughput.md)
* [Az autoscale átviteli sebességének kiépítése egy tárolóra](how-to-provision-autoscale-throughput.md)
* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)