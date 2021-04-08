---
title: A Azure Cosmos DB régiói közötti ütközések kezelése
description: Ismerje meg, hogyan kezelheti az ütközéseket Azure Cosmos DB a Last-Writer-WINS vagy egy egyéni ütközés-feloldási szabályzat létrehozásával
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f98c2201159350f5774f4d2b05102384f31f3af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339342"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Az ütközés-feloldási házirendek kezelése Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A többrégiós írások esetében, ha több ügyfél is ír ugyanarra az elemre, ütközések merülhetnek fel. Ütközés esetén az ütközést a különböző ütközés-feloldási házirendek használatával oldhatja fel. Ez a cikk az ütközés-feloldási szabályzatok kezelését ismerteti.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Utolsó-író-WINS ütközés-feloldási szabályzat létrehozása

Ezek a minták bemutatják, hogyan állíthat be egy tárolót egy utolsó író-WINS ütközés-feloldási házirenddel. Az utolsó-író-WINS alapértelmezett útvonala az időbélyeg mező vagy a `_ts` tulajdonság. Az SQL API esetében ez egy numerikus típusú felhasználó által megadott elérési útra is beállítható. Ütközés esetén a legmagasabb érték nyer. Ha az elérési út nincs beállítva vagy érvénytelen, a rendszer az alapértelmezett értéket adja meg `_ts` . A szabályzattal megoldott ütközések nem jelennek meg az ütközési hírcsatornában. Ezt a szabályzatot minden API használhatja.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javav4"></a> Java v4 SDK

# <a name="async"></a>[Aszinkron](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionLWWAsync)]

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionLWWSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-lww-javav2"></a>Java v2 SDK-k

# <a name="async-java-v2-sdk"></a>[Aszinkron Java v2 SDK](#tab/async)

[Aszinkron Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[A Java v2 SDK szinkronizálása](#tab/sync)

A [Java v2 SDK szinkronizálása](sql-api-sdk-java.md) (Maven [com. microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Egyéni ütközés-feloldási szabályzat létrehozása tárolt eljárás használatával

Ezek a minták bemutatják, hogy az ütközések feloldásához hogyan állíthat be egyéni ütközésfeloldási szabályzatot egy tárolóhoz tárolt eljárás használatával. Ezek az ütközések nem jelennek meg az ütközési hírcsatornában, kivéve, ha hiba van a tárolt eljárásban. Miután létrehozta a szabályzatot a tárolóval, létre kell hoznia a tárolt eljárást. Az alábbi .NET SDK-minta egy példát mutat be. Ezt a házirendet csak a Core (SQL) API támogatja.

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Minta egyéni ütközés-feloldás tárolt eljárása

Az egyéni ütközések feloldására szolgáló tárolt eljárásokat az alább látható függvény aláírása alapján kell megvalósítani. A függvény nevének nem kell megegyeznie a tárolt eljárás tárolóval való regisztrálása során használt névvel, de egyszerűsíti a névadást. Itt látható a tárolt eljáráshoz szükséges paraméterek leírása.

- **incomingItem**: az ütközéseket generáló véglegesítő vagy frissített tétel. NULL értékű a törlési műveletekhez.
- **existingItem**: a jelenleg véglegesített tétel. Ez az érték nem null értékű a frissítésben, és null értékű INSERT vagy DELETE esetén.
- **isTombstone**: logikai érték, amely azt jelzi, hogy a incomingItem ütközik-e egy előzőleg törölt elemmel. Igaz értéke esetén a existingItem is null értékű.
- **conflictingItems**: a tárolóban lévő összes elem véglegesített verziójának tömbje, amely ütközik a incomingItem azonosítóval vagy bármely más egyedi index-tulajdonsággal.

> [!IMPORTANT]
> Akárcsak a tárolt eljárásokhoz hasonlóan, egy egyéni ütközés-feloldási eljárás ugyanazzal a partíciós kulccsal fér hozzá az adatokhoz, és bármilyen beszúrási, frissítési vagy törlési műveletet végrehajthat az ütközések feloldásához.

Ez a minta tárolt eljárás az ütközéseket az elérési út legalacsonyabb értékének kiválasztásával oldja fel `/myCustomId` .

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav4"></a> Java v4 SDK

# <a name="async"></a>[Aszinkron](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionSprocAsync)]

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionSprocSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav2"></a>Java v2 SDK-k

# <a name="async-java-v2-sdk"></a>[Aszinkron Java v2 SDK](#tab/async)

[Aszinkron Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[A Java v2 SDK szinkronizálása](#tab/sync)

A [Java v2 SDK szinkronizálása](sql-api-sdk-java.md) (Maven [com. microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```
---

A tároló létrehozása után létre kell hoznia a `resolver` tárolt eljárást.

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

A tároló létrehozása után létre kell hoznia a `resolver` tárolt eljárást.

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

A tároló létrehozása után létre kell hoznia a `resolver` tárolt eljárást.

## <a name="create-a-custom-conflict-resolution-policy"></a>Egyéni ütközésfeloldási szabályzat létrehozása

Ezek a minták bemutatják, hogyan állíthat be egyéni ütközésfeloldási szabályzatot egy tárolóhoz. Ezek az ütközések az ütközési hírcsatornában jelennek meg.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-javav4"></a> Java v4 SDK

# <a name="async"></a>[Aszinkron](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) aszinkron API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionCustomAsync)]

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionCustomSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-javav2"></a>Java v2 SDK-k

# <a name="async-java-v2-sdk"></a>[Aszinkron Java v2 SDK](#tab/async)

[Aszinkron Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[A Java v2 SDK szinkronizálása](#tab/sync)

A [Java v2 SDK szinkronizálása](sql-api-sdk-java.md) (Maven [com. microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Olvasás az ütközéscsatornából

Ezek a minták bemutatják, hogyan lehet olvasni egy tároló ütközéscsatornájából. Az ütközések csak akkor jelennek meg az ütközési hírcsatornában, ha nem lettek automatikusan feloldva, vagy egyéni ütközési házirendet használnak.

### <a name="net-sdk"></a><a id="read-from-conflict-feed-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```
---

### <a name="java-v2-sdks"></a><a id="read-from-conflict-feed-javav2"></a>Java v2 SDK-k

# <a name="async-java-v2-sdk"></a>[Aszinkron Java v2 SDK](#tab/async)

[Aszinkron Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```
# <a name="sync-java-v2-sdk"></a>[A Java v2 SDK szinkronizálása](#tab/sync)

A [Java v2 SDK szinkronizálása](sql-api-sdk-java.md) (Maven [com. microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az alábbi Azure Cosmos DB fogalmakkal:

- [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
- [Többrégiós írások konfigurálása az alkalmazásokban](how-to-multi-master.md)
- [Ügyfelek konfigurálása a többhelyű-hez](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Régiók hozzáadása vagy eltávolítása a Azure Cosmos DB-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- A [régióbeli írások configuremulti az alkalmazásokban](how-to-multi-master.md).
- [Particionálás és adatelosztás](partitioning-overview.md)
- [Indexelés az Azure Cosmos DB-ben](index-policy.md)
