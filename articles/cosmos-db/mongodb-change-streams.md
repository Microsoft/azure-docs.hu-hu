---
title: Adatfolyamok módosítása Azure Cosmos DB API-MongoDB
description: Megtudhatja, hogyan használhatja a MongoDB n Azure Cosmos DB API-ját az adatain végrehajtott módosítások beszerzéséhez.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rosouz
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 941fe8929b75fdebf187186ca7078b0ae1dd261c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658520"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Adatfolyamok módosítása Azure Cosmos DB API-MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A Azure Cosmos DB API-MongoDB való [adatcsatorna](change-feed.md) -támogatás módosítása a Streams API-t használva érhető el. Az adatfolyamok módosítása API használatával az alkalmazások beszerezhetik a gyűjteményen vagy az egyetlen szegmensben lévő elemeken végrehajtott módosításokat. Később további műveleteket is végrehajthat az eredmények alapján. A gyűjtemény elemeinek módosításait a rendszer a módosítási idő sorrendjében rögzíti, és a rendezési sorrendet a rendszer a szegmens kulcsa szerint biztosítja.

> [!NOTE]
> Az adatfolyamok módosításának használatához hozza létre a Azure Cosmos DB API-ját a MongoDB-fiókhoz a 3,6-es vagy újabb verziójú kiszolgálóval. Ha az adatfolyam-változási példákat egy korábbi verzióra futtatja, akkor előfordulhat, hogy a *folyamat neve ismeretlen: $changeStream* hiba.

## <a name="examples"></a>Példák

Az alábbi példa azt szemlélteti, hogyan lehet módosítani a gyűjtemény összes elemének változásait. Ez a példa létrehoz egy kurzort, amellyel megtekintheti a beszúrt, frissített vagy lecserélt elemeket. A `$match` `$project` `fullDocument` változtatási adatfolyamok beszerzéséhez a szakasz, a szakasz és a beállítás szükséges. A módosítási streameket használó törlési műveletek figyelése jelenleg nem támogatott. Megkerülő megoldásként hozzáadhat egy lágy jelölőt a törölt elemekhez. Hozzáadhat például egy attribútumot a "törölt" nevű elemhez. Ha törölni szeretné az elemeket, beállíthatja a "törölt" `true` értéket, és beállíthatja az élettartamot az elemnél. Mivel a "törölt" érték frissítése frissül `true` , ez a változás látható lesz a változási adatfolyamban.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa bemutatja, hogyan használható a Java-ban a Change Stream funkció, a teljes példa a [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java)-tárházban található. Ez a példa azt is bemutatja, hogyan használható a `resumeAfter` metódus a legutóbbi olvasás összes módosításának kereséséhez. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>Egyetlen szegmensen belüli változások

Az alábbi példa azt szemlélteti, hogyan lehet módosítani az elemeket egyetlen szegmensen belül. Ez a példa a "a" és a "1" értékkel egyenlő, a szilánkok kulcsának értékét tartalmazó elemek változásait olvassa be. Lehetséges, hogy a különböző ügyfelek különböző szegmensekről származó módosításokat olvasnak párhuzamosan.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Aktuális korlátozások

A Change streamek használatakor a következő korlátozások érvényesek:

* A `operationType` és a `updateDescription` Tulajdonságok még nem támogatottak a kimeneti dokumentumban.
* A `insert` , a `update` és az `replace` Operations típusok jelenleg támogatottak. A törlési művelet vagy más események azonban még nem támogatottak.

A korlátozások miatt a $match szakasz, a $project szakasz és a fullDocument lehetőségek szükségesek, ahogy az előző példákban is látható.

A Azure Cosmos DB SQL API-ban megjelenő változási csatornától eltérően a változási adatfolyamok nem használhatók fel külön [módosítási adatcsatornára](change-feed-processor.md) , vagy a bérletek tárolóra van szükségük. Jelenleg nem támogatott a [Azure functions eseményindítók](change-feed-functions.md) feldolgozása a változási adatfolyamok feldolgozásához.

## <a name="error-handling"></a>Hibakezelés

A Change streamek használatakor a következő hibakódok és üzenetek támogatottak:

* **Http-hibakód 16500** – ha a Change stream szabályozása megtörtént, üres lapot ad vissza.

* **NamespaceNotFound (OperationType-érvénytelenítés)** – ha nem létező gyűjteményen futtatja a Change streamet, vagy ha a gyűjtemény el van dobva, a `NamespaceNotFound` rendszer hibát ad vissza. Mivel a `operationType` tulajdonság nem adható vissza a kimeneti dokumentumban a hiba helyett, `operationType Invalidate` a `NamespaceNotFound` rendszer a hibát adja vissza.

## <a name="next-steps"></a>Következő lépések

* [A MongoDB Azure Cosmos DB API-ban való használatának ideje az élettartam lejáratára](mongodb-time-to-live.md)
* [Indexelés Azure Cosmos DB API-MongoDB](mongodb-indexing.md)
