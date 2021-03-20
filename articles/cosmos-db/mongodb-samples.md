---
title: Node.js-alkalmazás létrehozása Azure Cosmos DB API-MongoDB használatával
description: Egy oktatóanyag, amely létrehoz egy online adatbázist a Azure Cosmos DB API-MongoDB való használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: devx-track-js
ms.openlocfilehash: c2117c535cca679d3a7a3b75491bf60ab9567ceb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098212"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Alkalmazások készítése a MongoDB Node.js és Azure Cosmos DB API-ját használva 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](./sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Node.js MongoDB-hez](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Ebből a példából megtudhatja, hogyan hozhat létre egy Node.js és Azure Cosmos DB API-MongoDB használó konzolos alkalmazást.

A példa használatához a következőket kell tennie:

* [Hozzon létre](create-mongodb-dotnet.md#create-account) egy Azure Cosmos db API-MongoDB használatára konfigurált Cosmos-fiókot.
* Kérje le a [kapcsolatok karakterláncának](connect-mongodb-account.md) adatait.

## <a name="create-the-app"></a>Az alkalmazás létrehozása

1. Egy *app.js* fájl létrehozása és az alábbi információ bemásolása.

    ```javascript
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
    
    **Más lehetőség**: A **MongoDB Node.js 2.2 illesztőprogram** használata esetén cserélje ki a következő kódrészletet:

    Eredeti:

    ```javascript
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

    ```javascript
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
    
2. Módosítsa a következő változókat az *app.js* fájlban a fiókbeállításainak megfelelően (Hogyan található meg a [kapcsolati sztring](connect-mongodb-account.md)?):

    > [!IMPORTANT]
    > A **MongoDB Node.js 3.0 illesztőprogram** megköveteli a Cosmos DB-jelszóban lévő speciális karakterek kódolását. Az „=” karakter %3D formában kódolandó.
    >
    > Példa: A *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* jelszó kódolva *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D* lesz
    >
    > A **MongoDB Node.js 2.2 illesztőprogram** nem követeli meg a Cosmos DB-jelszóban lévő speciális karakterek kódolását.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Nyissa meg kedvenc terminálját, futtassa az **npm install mongodb --save** parancsot, majd futtassa alkalmazását a **node app.js** paranccsal.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.