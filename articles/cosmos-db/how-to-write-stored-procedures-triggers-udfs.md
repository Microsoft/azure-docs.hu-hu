---
title: Tárolt eljárások, eseményindítók és UDF írása Azure Cosmos DB
description: Megtudhatja, hogyan határozhat meg tárolt eljárásokat, eseményindítókat és felhasználó által definiált függvényeket Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 7600d8aa2f78e06ea4046273635fdbba18042010
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028862"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB lehetővé teszi a JavaScript nyelvre integrált, tranzakciós végrehajtását, amely lehetővé teszi **tárolt eljárások**, **Eseményindítók** és **felhasználó által definiált függvények (UDF-EK)** írását. Ha az SQL API-t Azure Cosmos DBban használja, megadhatja a tárolt eljárásokat, eseményindítókat és UDF JavaScript nyelven. Megírhatja a logikát a JavaScriptben, és végrehajthatja azt az adatbázismotor használatával. Eseményindítókat, tárolt eljárásokat és UDF hozhat létre és futtathat [Azure Portal](https://portal.azure.com/)használatával, a [JavaScript nyelv integrált lekérdezési API-ját Azure Cosmos DB](javascript-query-api.md) és az [Cosmos db SQL API ügyféloldali SDK](sql-api-dotnet-samples.md)-kat. 

A tárolt eljárások, triggerek és felhasználó által definiált függvények meghívásához regisztrálnia kell. További információ: a [tárolt eljárások, eseményindítók, felhasználó által definiált függvények használata a Azure Cosmos DBban](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Particionált tárolók esetén a tárolt eljárás végrehajtásakor meg kell adni egy partíciós kulcs értékét a kérés beállításai között. A tárolt eljárásokat a rendszer mindig a partíciós kulcsra szűkíti. A másik partíciós kulcs értékkel rendelkező elemek nem lesznek láthatók a tárolt eljárásban. Ez is a triggerekre is vonatkozik.
> [!Tip]
> A Cosmos támogatja a tárolók üzembe helyezését tárolt eljárásokkal, eseményindítókkal és felhasználó által definiált függvényekkel. További információ: [Azure Cosmos db tároló létrehozása kiszolgálóoldali funkciókkal.](./manage-with-templates.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Tárolt eljárások írása

A tárolt eljárások JavaScript használatával íródnak, és az Azure Cosmos-tárolóban lévő elemeket hozhatnak létre, frissíthetnek, olvashatnak, lekérhetnek és törölhetnek. A tárolt eljárások gyűjtemény szerint vannak regisztrálva, és a gyűjteményben található bármilyen dokumentumon vagy mellékleten működhetnek.

Az alábbi egyszerű tárolt eljárás egy "Hello World" választ ad vissza.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

A környezeti objektum hozzáférést biztosít a Azure Cosmos DBban végrehajtható összes művelethez, valamint a kérés és a válasz objektumokhoz való hozzáféréshez is. Ebben az esetben a válasz objektum segítségével állíthatja be a válasz törzsét, amelyet vissza szeretne adni az ügyfélnek.

Az írást követően a tárolt eljárást regisztrálni kell egy gyűjteményben. További információ: [tárolt eljárások használata Azure Cosmos db](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) cikkben.

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Elemek létrehozása tárolt eljárás használatával

Amikor tárolt eljárással hoz létre egy tételt, az elem bekerül az Azure Cosmos-tárolóba, és az újonnan létrehozott elem AZONOSÍTÓját adja vissza. Egy elem létrehozása aszinkron művelet, amely a JavaScript visszahívási függvénytől függ. A visszahívási függvény két paraméterrel rendelkezik – egyet a hiba objektumhoz abban az esetben, ha a művelet meghiúsul, és egy másik a visszatérési értékhez. Ebben az esetben a létrehozott objektum. A visszahíváson belül kezelheti a kivételt, vagy hibát okozhat. Ha nincs megadva visszahívás, és hiba történt, akkor a Azure Cosmos DB futtatókörnyezet hibát jelez.

A tárolt eljárás egy paramétert is tartalmaz a Leírás megadásához, ez egy logikai érték. Ha a paraméter értéke TRUE (igaz), és a Leírás hiányzik, akkor a tárolt eljárás kivételt jelez. Ellenkező esetben a tárolt eljárás többi része továbbra is fut.

A következő példában tárolt eljárás az új Azure Cosmos-elemek egy tömbjét veszi fel bemenetként, beszúrja az Azure Cosmos-tárolóba, és visszaadja a beszúrt elemek számát. Ebben a példában a ToDoList mintát használjuk a gyors üzembe helyezési [.net SQL API](create-sql-api-dotnet.md) -ból

```javascript
function createToDoItems(items) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();
    var count = 0;

    if (!items) throw new Error("The array is undefined or null.");

    var numItems = items.length;

    if (numItems == 0) {
        getContext().getResponse().setBody(0);
        return;
    }

    tryCreate(items[count], callback);

    function tryCreate(item, callback) {
        var options = { disableAutomaticIdGeneration: false };

        var isAccepted = collection.createDocument(collectionLink, item, options, callback);

        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    function callback(err, item, options) {
        if (err) throw err;
        count++;
        if (count >= numItems) {
            getContext().getResponse().setBody(count);
        } else {
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Tömbök bemeneti paraméterként a tárolt eljárásokhoz

Azure Portal tárolt eljárás definiálásakor a bemeneti paraméterek mindig karakterláncként lesznek elküldve a tárolt eljáráshoz. Még ha sztringek tömbjét is át kell adni bemenetként, a rendszer karakterlánccá alakítja át a tömböt, és elküldi a tárolt eljárásnak. Ennek megkerüléséhez definiálhat egy függvényt a tárolt eljáráson belül a sztring tömbként való elemzéséhez. A következő kód bemutatja, hogyan elemezheti a karakterlánc bemeneti paramétereit tömbként:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>Tárolt eljárásokon belüli tranzakciók

A tároló elemein belüli tranzakciókat tárolt eljárással is megvalósíthatja. Az alábbi példa egy fantasy Football Gaming-alkalmazáson belüli tranzakciókat használ, hogy egyetlen művelet keretében két csapat között kereskedni lehessen. A tárolt eljárás megkísérli beolvasni a két Azure Cosmos-elemet, amelyek megfelelnek az argumentumként átadott Player-azonosítóknak. Ha mindkét játékos megtalálható, a tárolt eljárás a csapatuk cseréjével frissíti az elemeket. Ha bármilyen hiba fordul elő, a tárolt eljárás olyan JavaScript-kivételt mutat be, amely implicit módon megszakítja a tranzakciót.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery =
    {
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };

    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 =
            {
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}]
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Kötött végrehajtás a tárolt eljárásokon belül

Az alábbi példa egy olyan tárolt eljárást mutat be, amely tömegesen importál elemeket egy Azure Cosmos-tárolóba. A tárolt eljárás kezeli a korlátos végrehajtást, ha ellenőrzi a logikai visszatérési értéket a ból `createDocument` , majd a tárolt eljárás minden egyes meghívásakor beszúrt elemek számát használja a kötegek közötti előrehaladás nyomon követéséhez és folytatásához.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="async-await-with-stored-procedures"></a><a id="async-promises"></a>Aszinkron várakozás tárolt eljárásokkal

A következő példa egy olyan tárolt eljárásra mutat be példát, amely aszinkron módon használja az ígéreteket egy segítő függvény használatával. A tárolt eljárás lekérdezi egy adott elemmel, és lecseréli azt.

```javascript
function async_sample() {
    const ERROR_CODE = {
        NotAccepted: 429
    };

    const asyncHelper = {
        queryDocuments(sqlQuery, options) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.queryDocuments(__.getSelfLink(), sqlQuery, options, (err, feed, options) => {
                    if (err) reject(err);
                    resolve({ feed, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        },

        replaceDocument(doc) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.replaceDocument(doc._self, doc, (err, result, options) => {
                    if (err) reject(err);
                    resolve({ result, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        }
    };

    async function main() {
        let continuation;
        do {
            let { feed, options } = await asyncHelper.queryDocuments("SELECT * from c", { continuation });

            for (let doc of feed) {
                doc.newProp = 1;
                await asyncHelper.replaceDocument(doc);
            }

            continuation = options.continuation;
        } while (continuation);
    }

    main().catch(err => getContext().abort(err));
}
```

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Eseményindítók írása

Azure Cosmos DB támogatja az előtriggereket és az eseményindítókat. Az adatbázis-elemek módosítása és az eseményindítók végrehajtása előtt az eseményindítók végrehajtása az adatbázis-elemek módosítása után történik. Az eseményindítók nem lesznek automatikusan végrehajtva, azokat minden olyan adatbázis-művelethez meg kell adni, amelyben végre szeretné hajtani a műveletet. Miután definiált egy triggert, [regisztrálnia kell és meg kell hívnia a pre-triggert](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) az Azure Cosmos db SDK-k használatával.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Trigger előtti

Az alábbi példa azt mutatja be, hogyan használható a pre-trigger a létrehozott Azure Cosmos-elemek tulajdonságainak ellenőrzésére. Ebben a példában kihasználjuk a ToDoList mintát a gyors üzembe helyezési [.net SQL API](create-sql-api-dotnet.md)-ból, hogy egy timestamp tulajdonságot adjon hozzá egy újonnan hozzáadott elemhez, ha az nem tartalmaz egyet.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Az előzetes eseményindítóknak nem lehetnek bemeneti paramétereik. A triggerben található kérelem objektum a művelethez társított kérelem üzenetének kezelésére szolgál. Az előző példában a pre-trigger fut egy Azure Cosmos-elem létrehozásakor, és a kérelem üzenet törzse tartalmazza a JSON formátumban létrehozandó tételt.

Ha a triggerek regisztrálva vannak, megadhatja azokat a műveleteket, amelyeket futtathat a használatával. Ezt a triggert egy `TriggerOperation` értékkel kell létrehozni `TriggerOperation.Create` , ami azt jelenti, hogy az triggert egy csere műveletben használja, ahogy az a következő kódban nem engedélyezett.

A pre-triggerek regisztrálásával és meghívásával kapcsolatos Példákért lásd: [Pre-triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [trigger utáni](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkek. 

### <a name="post-triggers"></a><a id="post-triggers"></a>Triggerek utáni

Az alábbi példa egy trigger utáni műveletet mutat be. Ez a trigger lekérdezi a metaadat-elemeket, és frissíti azt az újonnan létrehozott elemmel kapcsolatos részletekkel.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Fontos megjegyezni, hogy az eseményindítók tranzakciós végrehajtása Azure Cosmos DBban. Az utólagos indítás a mögöttes elemhez tartozó tranzakció részeként fut. Az indítás utáni végrehajtás során felmerülő kivétel esetén a teljes tranzakció sikertelen lesz. Minden véglegesítés visszavonásra kerül, és a rendszer kivételt ad vissza.

A pre-triggerek regisztrálásával és meghívásával kapcsolatos Példákért lásd: [Pre-triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [trigger utáni](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkek. 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Felhasználó által definiált függvények írása

Az alábbi minta létrehoz egy UDF-t a jövedelemadó kiszámításához a különböző bevételi zárójelek esetében. Ezt a felhasználó által definiált függvényt ezután egy lekérdezésen belül fogjuk használni. Ebben a példában feltételezhető, hogy egy "jövedelem" nevű tároló található a következő tulajdonságokkal:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

A következő egy függvény definíciója a jövedelemadó kiszámításához a különböző bevételi zárójelek esetében:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

A felhasználó által definiált függvények regisztrálásával és használatával kapcsolatos Példákért lásd: [felhasználói függvények használata Azure Cosmos db](how-to-use-stored-procedures-triggers-udfs.md#udfs) cikkben.

## <a name="logging"></a>Naplózás 

Tárolt eljárás, eseményindítók vagy felhasználó által definiált függvények használatakor a parancs használatával naplózhatja a lépéseket `console.log()` . Ez a parancs egy karakterláncot fog összpontosítani a hibakereséshez, ha a `EnableScriptLogging` értéke TRUE (igaz), ahogy az alábbi példában is látható:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Következő lépések

További fogalmak és útmutató: tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása vagy használata Azure Cosmos DBban:

* [Tárolt eljárások, eseményindítók és felhasználói függvények regisztrálása és használata az Azure Cosmos DB-ben](how-to-use-stored-procedures-triggers-udfs.md)

* [Tárolt eljárások és eseményindítók írása a JavaScript lekérdezési API használatával Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Azure Cosmos DB tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [A JavaScript nyelvi integrált lekérdezési API használata a Azure Cosmos DBban](javascript-query-api.md)
