---
title: Azure Cosmos DB API-hoz tartozó MongoDB-műveletekre vonatkozó kérési egységre vonatkozó díj keresése
description: Megtudhatja, hogyan lehet megkeresni az Azure Cosmos-tárolón végrehajtott MongoDB-lekérdezésekre vonatkozó kérési egység (RU) díját. Használhatja a Azure Portal, a MongoDB .NET, a Java és a Node.js illesztőprogramokat.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: e488d1acfe116409caf571e7878e454628a9dea9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201326"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>A MongoDB Azure Cosmos DB API-ban végrehajtott műveletek igénylési egységének megkeresése
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB számos olyan API-t támogat, mint például az SQL, a MongoDB, a Cassandra, a Gremlin és a table. Mindegyik API saját adatbázis-műveletekkel rendelkezik. Ezek a műveletek az egyszerű pont olvasási és írási műveleteiből származnak összetett lekérdezésekre. Minden adatbázis-művelet a művelet bonyolultsága alapján használja a rendszererőforrásokat.

Az összes adatbázis-művelet költségét az Azure Cosmos DB normalizálja, és ezek kérelemegységben (röviden RU) vannak kifejezve. A kérelem díja az összes adatbázis-művelet által felhasznált kérelmek egysége. Azt is megteheti, hogy az RUs olyan teljesítmény pénznemként van kiértékelve, amely a Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához szükséges rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát is elvégzi. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. Azt határozza meg, hogy az adatbázis-művelet írási, olvasási pont vagy lekérdezés-e, a költségeket mindig az RUs méri. További tudnivalókat a [kérelmek egységei](request-units.md) című cikkben talál.

Ez a cikk bemutatja, hogyan lehet megkeresni a MongoDB Azure Cosmos DB API-ban található tárolón végrehajtott bármely műveletre vonatkozó [kérési egység](request-units.md) (ru) felhasználását. Ha más API-t használ, tekintse meg a következő témakört: [SQL API](find-request-unit-charge.md), [CASSANDRA API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)és [Table API](find-request-unit-charge-table.md) cikkek az ru/s díj megkereséséhez.

Az RU díját egy nevű egyéni adatbázis- [parancs](https://docs.mongodb.com/manual/reference/command/) teszi elérhetővé `getLastRequestStatistics` . A parancs egy olyan dokumentumot ad vissza, amely tartalmazza az utolsó végrehajtott művelet nevét, a kérések díját és annak időtartamát. Ha a MongoDB Azure Cosmos DB API-t használja, több lehetőség is van az RU-díj lekérésére.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account) , és adja meg az adatgyűjtést, vagy válasszon ki egy olyan meglévő fiókot, amely már tartalmaz információt.

1. Lépjen a **adatkezelő** ablaktáblára, majd válassza ki a használni kívánt tárolót.

1. Válassza az **Új lekérdezés** lehetőséget.

1. Adjon meg egy érvényes lekérdezést, majd válassza a **lekérdezés végrehajtása** lehetőséget.

1. A **lekérdezési statisztikák** lehetőség kiválasztásával jelenítheti meg a tényleges kérelmek díját a végrehajtott kérelemért.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Képernyőkép a MongoDB-lekérdezési kérelmekért a Azure Portal":::

## <a name="use-the-mongodb-net-driver"></a>A MongoDB .NET-illesztőprogram használata

Ha a [hivatalos MongoDB .net-illesztőprogramot](https://docs.mongodb.com/ecosystem/drivers/csharp/)használja, végrehajthat parancsokat úgy, hogy meghívja a `RunCommand` metódust egy `IMongoDatabase` objektumon. Ehhez a metódushoz az absztrakt osztály megvalósítására van szükség `Command<>` :

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

További információ: gyors útmutató [: .net-Webalkalmazás létrehozása Azure Cosmos db API-val a MongoDB-hez](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>A MongoDB Java-illesztőprogram használata


Ha a [hivatalos MongoDB Java-illesztőprogramot](https://mongodb.github.io/mongo-java-driver/)használja, parancsokat futtathat a metódus meghívásával `runCommand` egy `MongoDatabase` objektumon:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

További információ: gyors útmutató [: Webalkalmazás létrehozása a MongoDB-hez készült Azure Cosmos db API-val és a Java SDK-](create-mongodb-java.md)val.

## <a name="use-the-mongodb-nodejs-driver"></a>A MongoDB Node.js illesztőprogramjának használata

Ha a [hivatalos MongoDB Node.js illesztőprogramját](https://mongodb.github.io/node-mongodb-native/)használja, parancsokat futtathat a metódus meghívásával `command` egy `db` objektumon:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

További információ: gyors üzembe helyezés [meglévő MongoDB Node.js webalkalmazás migrálása Azure Cosmos db](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Következő lépések

Az RU-felhasználás optimalizálásával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](./optimize-cost-reads-writes.md)