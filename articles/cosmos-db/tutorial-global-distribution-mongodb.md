---
title: Az Azure Cosmos DB globális terjesztési oktatóanyaga a MongoDB API-val
description: Ismerje meg, hogyan állíthatja be az Azure Cosmos DB globális terjesztését a MongoDB API használatával.
services: cosmos-db
keywords: globális terjesztés, MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 27fa9849c13de151f6922e829514cc8838f295ea
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874761"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Az Azure Cosmos DB globális terjesztésének beállítása a MongoDB API használatával

Ebben a cikkben bemutatjuk, hogyan állíthatja be az Azure Cosmos DB globális terjesztését az Azure Portallal, és hogyan csatlakozhat a MongoDB API használatával.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása a [MongoDB API](mongodb-introduction.md) használatával

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>A regionális beállítások ellenőrzése a MongoDB API használatával
A globális konfiguráció a MongoDB API-jában legegyszerűbben az *isMaster()* parancs futtatásával ellenőrizhető a Mongo parancskörnyezetben.

A Mongo parancskörnyezetben:

   ```
      db.isMaster()
   ```
   
Példa eredmények:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Csatlakozás egy kívánt régióhoz a MongoDB API használatával

A MongoDB API segítségével megadhatja a gyűjtemény olvasási beállításait egy globálisan elosztott adatbázison. Az alacsony olvasási késés és a globálisan magas rendelkezésre állás esetében egyaránt azt javasoljuk, hogy a gyűjtemény olvasási beállítását a *legközelebbi* értékre konfigurálja. A *legközelebbi* olvasási beállítás úgy van konfigurálva, hogy a legközelebb eső régióból olvasson.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Az elsődleges olvasási/írási régióval és a vészhelyreállítási (DR) forgatókönyvhöz egy másodlagos régióval is rendelkező alkalmazások esetében azt javasoljuk, hogy a gyűjtemény olvasási beállítását a *másodlagos előnyben részesítve* értékre konfigurálja. A *másodlagos előnyben részesítve* olvasási beállítás úgy van konfigurálva, hogy a másodlagos régióból olvasson, amikor az elsődleges nem érhető el.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Végül pedig ha az olvasási régiókat manuálisan szeretné megadni, a régió címkét az olvasási beállításban adhatja meg.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Ezzel el is végezte az oktatóanyagot. Ha meg szeretné ismerni, hogyan kezelheti a globálisan replikált fiók konzisztenciáját, olvassa el a [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md) című cikket. További információ a globális adatbázis-replikáció működéséről az Azure Cosmos DB szolgáltatásban: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az SQL API-k használatával

Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan fejleszthet helyileg az Azure Cosmos DB helyi emulátorával.

> [!div class="nextstepaction"]
> [Helyi fejlesztés az emulátorral](local-emulator.md)
