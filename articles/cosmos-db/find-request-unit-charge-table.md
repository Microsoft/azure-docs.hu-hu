---
title: Table API lekérdezésekre vonatkozó kérési egység (RU) díja Azure Cosmos DB
description: Megtudhatja, hogyan kell megkeresni az Azure Cosmos-tárolón végrehajtott Table API lekérdezésekre vonatkozó kérési egység (RU) díját. A Azure Portal, a .NET, a Java, a Python és a Node.js nyelv használatával megkeresheti az RU díját.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201313"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>A kérések egységének megkeresése Azure Cosmos DB Table API végrehajtott műveletekhez
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB számos olyan API-t támogat, mint például az SQL, a MongoDB, a Cassandra, a Gremlin és a table. Mindegyik API saját adatbázis-műveletekkel rendelkezik. Ezek a műveletek az egyszerű pont olvasási és írási műveleteiből származnak összetett lekérdezésekre. Minden adatbázis-művelet a művelet bonyolultsága alapján használja a rendszererőforrásokat.

Az összes adatbázis-művelet költségét az Azure Cosmos DB normalizálja, és ezek kérelemegységben (röviden RU) vannak kifejezve. A kérelem díja az összes adatbázis-művelet által felhasznált kérelmek egysége. Azt is megteheti, hogy az RUs olyan teljesítmény pénznemként van kiértékelve, amely a Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához szükséges rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát is elvégzi. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. Azt határozza meg, hogy az adatbázis-művelet írási, olvasási pont vagy lekérdezés-e, a költségeket mindig az RUs méri. További tudnivalókat a [kérelmek egységei](request-units.md) című cikkben talál.

Ez a cikk bemutatja, hogyan lehet megkeresni a [kérési egység](request-units.md) (ru) használatát a Azure Cosmos db Table API tárolóján végrehajtott bármely művelethez. Ha más API-t használ, tekintse meg a MongoDB, [Cassandra API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)és [SQL API](find-request-unit-charge.md) -cikkek [API](find-request-unit-charge-mongodb.md)-ját az ru/s díj megkereséséhez.

## <a name="use-the-net-sdk"></a>A .NET SDK használata

Jelenleg az egyetlen SDK, amely a Table Operations RU díját adja vissza, a [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Az `TableResult` objektum egy olyan tulajdonságot tesz elérhetővé `RequestCharge` , amelyet az SDK tölt fel, amikor a Azure Cosmos db Table API használja:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

További információ: gyors útmutató [: table API-alkalmazás létrehozása a .net SDK és a Azure Cosmos db használatával](create-table-dotnet.md).

## <a name="next-steps"></a>Következő lépések

Az RU-felhasználás optimalizálásával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](./optimize-cost-reads-writes.md)