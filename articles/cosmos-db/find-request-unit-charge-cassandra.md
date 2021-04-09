---
title: Cassandra API lekérdezésre vonatkozó kérési egység (RU) díja Azure Cosmos DB
description: Megtudhatja, hogyan keresheti meg az Azure Cosmos-tárolón végrehajtott Cassandra-lekérdezésekre vonatkozó kérési egység (RU) díját. A Azure Portal, a .NET és a Java illesztőprogramok segítségével megkeresheti az RU díját.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b504702ad0c74ae9728c0a8b34fa94df26184f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200511"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>A kérések egységének megkeresése Azure Cosmos DB Cassandra API végrehajtott műveletekhez
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB számos olyan API-t támogat, mint például az SQL, a MongoDB, a Cassandra, a Gremlin és a table. Mindegyik API saját adatbázis-műveletekkel rendelkezik. Ezek a műveletek az egyszerű pont olvasási és írási műveleteiből származnak összetett lekérdezésekre. Minden adatbázis-művelet a művelet bonyolultsága alapján használja a rendszererőforrásokat.

Az összes adatbázis-művelet költségét az Azure Cosmos DB normalizálja, és ezek kérelemegységben (röviden RU) vannak kifejezve. A kérelem díja az összes adatbázis-művelet által felhasznált kérelmek egysége. Azt is megteheti, hogy az RUs olyan teljesítmény pénznemként van kiértékelve, amely a Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához szükséges rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát is elvégzi. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. Azt határozza meg, hogy az adatbázis-művelet írási, olvasási pont vagy lekérdezés-e, a költségeket mindig az RUs méri. További tudnivalókat a [kérelmek egységei](request-units.md) című cikkben talál.

Ez a cikk bemutatja, hogyan lehet megkeresni a [kérési egység](request-units.md) (ru) használatát a Azure Cosmos db Cassandra API tárolóján végrehajtott bármely művelethez. Ha más API-t használ, tekintse meg az [MongoDB API](find-request-unit-charge-mongodb.md)-t, az [SQL API](find-request-unit-charge.md)-t, a [Gremlin api](find-request-unit-charge-gremlin.md)-t, valamint [Table API](find-request-unit-charge-table.md) cikkeket az ru/s díj megkereséséhez.

Ha a Azure Cosmos DB Cassandra API műveleteit hajtja végre, a rendszer az RU-díjat a bejövő adattartalomban adja vissza egy nevű mezőként `RequestCharge` . Többféleképpen is lekérheti a kérelemegység-költséget.

## <a name="use-the-net-sdk"></a>A .NET SDK használata

A [.net SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)használatával lekérheti a bejövő hasznos adatokat `Info` egy objektum tulajdonsága alatt `RowSet` :

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

További információ: gyors útmutató [: Cassandra-alkalmazás létrehozása a .net SDK és a Azure Cosmos db használatával](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>A Java SDK használata

A [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)használatával lekérheti a bejövő hasznos adatokat, ha meghívja a `getExecutionInfo()` metódust egy `ResultSet` objektumon:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

További információ: gyors útmutató [: Cassandra-alkalmazás létrehozása a Java SDK és a Azure Cosmos db használatával](create-cassandra-java.md).

## <a name="next-steps"></a>Következő lépések

Az RU-felhasználás optimalizálásával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](./optimize-cost-reads-writes.md)