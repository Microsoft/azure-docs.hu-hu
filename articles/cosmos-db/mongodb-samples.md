---
title: Azure Cosmos DB API MongoDB használatával hozhat létre egy Node.js-alkalmazás
description: Oktatóanyag, amelynek során egy online adatbázist hoz létre a MongoDB-hez készült Azure Cosmos DB API-k használatával.
keywords: mongodb példák
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 994371f716a2171622c6c534329764bcf7ac585c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53534764"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Hozhat létre Azure Cosmos DB: API-t a Node.js használatával a MongoDB-alkalmazások
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js MongoDB-hez](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Ez a példa bemutatja, hogyan hozhat létre egy Azure Cosmos DB: A Node.js használatával Konzolalkalmazás MongoDB API-t.

A példa használatához a következőket kell tennie:

* [Hozzon létre](create-mongodb-dotnet.md#create-account) egy Azure Cosmos DB használatával: API a MongoDB-fiókhoz.
* A MongoDB [kapcsolati karakterlánc](connect-mongodb-account.md) információk beszerzése.

## <a name="create-the-app"></a>Az alkalmazás létrehozása

1. Egy *app.js* fájl létrehozása és az alábbi információ bemásolása.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Nem kötelező**: Ha használja a **MongoDB Node.js 2.2-es illesztőprogram**, cserélje le a következő kódrészletet:

    Eredeti:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Amire le kell cserélnie:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Módosítsa a következő változókat az *app.js* fájlban a fiókbeállításainak megfelelően (Hogyan található meg a [kapcsolati karakterlánc](connect-mongodb-account.md)?):

    > [!IMPORTANT]
    > A **MongoDB Node.js 3.0 illesztőprogram** megköveteli a Cosmos DB-jelszóban lévő speciális karakterek kódolását. Az „=” karakter %3D formában kódolandó.
    >
    > Példa: A jelszó *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv ==* kódolja a *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv 3D % 3D*
    >
    > A **MongoDB Node.js 2.2 illesztőprogram** nem követeli meg a Cosmos DB-jelszóban lévő speciális karakterek kódolását.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Nyissa meg kedvenc terminálját, futtassa az **npm install mongodb --save** parancsot, majd futtassa alkalmazását a **node app.js** paranccsal.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [MongoChef használata](mongodb-mongochef.md) az Azure Cosmos DB-vel: API a MongoDB-fiókhoz.
