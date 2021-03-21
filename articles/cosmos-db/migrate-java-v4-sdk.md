---
title: Az alkalmazás migrálása a Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos) használatára
description: Ismerje meg, hogyan frissítheti meglévő Java-alkalmazásait a régebbi Azure Cosmos DB Java SDK-k használatával az újabb Java SDK 4,0 (com. Azure. Cosmos csomag) for Core (SQL) API-hoz.
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 92a9abec36bd75c594c67843286bf8fa067d7dba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658537"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Az alkalmazás migrálása a Azure Cosmos DB Java SDK v4 használatára
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Az SDK-val kapcsolatos további információkért tekintse meg a Azure Cosmos DB Java SDK v4 [kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos), a Azure Cosmos db Java SDK v4 [teljesítménnyel kapcsolatos TIPPEKET](performance-tips-java-sdk-v4-sql.md), valamint Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatót](troubleshoot-java-sdk-v4-sql.md).
>

Ez a cikk azt ismerteti, hogyan frissítheti a régebbi Azure Cosmos DB Java SDK-t használó meglévő Java-alkalmazást az újabb Azure Cosmos DB Java SDK 4,0 for Core (SQL) API-hoz. Azure Cosmos DB Java SDK v4 megfelel a `com.azure.cosmos` csomagnak. Ha az alkalmazást a következő Azure Cosmos DB Java SDK-k bármelyikéről telepíti át, akkor használhatja az ebben a dokumentumban található utasításokat: 

* Java SDK szinkronizálása 2. x. x
* Aszinkron Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>A Java SDK és a csomag-hozzárendelések Azure Cosmos DB

A következő táblázat felsorolja a különböző Azure Cosmos DB Java SDK-kat, a csomag nevét és a kiadási információkat:

| Java SDK| Kiadás dátuma | Köteg API-k   | Maven jar  | Java-csomag neve  |API-referencia   | Kibocsátási megjegyzések  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Aszinkron 2. x. x  | 2018. június    | Aszinkron (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Kibocsátási megjegyzések](sql-api-sdk-async-java.md) |
| 2. x. x szinkronizálása     | Szeptember 2018    | Sync   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Kibocsátási megjegyzések](sql-api-sdk-java.md)  |
| 3. x. x    | 2019. július    | Aszinkron (reaktor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | 2020. június   | Aszinkron (reaktor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Az SDK-szint implementációjának módosításai

A különböző SDK-k között a legfontosabb implementációs különbségek a következők:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>A RxJava helyére a reaktor szerepel a Azure Cosmos DB Java SDK 3. x. x és 4,0 verziójában.

Ha nem ismeri az aszinkron programozási vagy reaktív programozást, tekintse meg a következőt: a [reaktor mintájának útmutatója](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) az aszinkron programozás és a projekt-visszavezető bevezetéséhez. Ez az útmutató akkor lehet hasznos, ha Azure Cosmos DB Sync Java SDK 2. x. x vagy Azure Cosmos DB Java SDK 3. x. x szinkronizációs API-t korábban használta.

Ha Azure Cosmos DB aszinkron Java SDK 2. x. x verzióját használja, és az 4,0 SDK-ra való áttérést tervezi, tekintse meg a [reaktor vs RxJava útmutatót](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) , amely útmutatást nyújt a RxJava-kód a reaktor használatára való átalakításához.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 közvetlen kapcsolati móddal rendelkezik az aszinkron és a Sync API-kkal

Ha a Azure Cosmos DB Sync Java SDK 2. x. x verzióját használja, vegye figyelembe, hogy a TCP protokollon alapuló közvetlen kapcsolódási mód (a HTTP-vel ellentétben) az aszinkron és a Sync API-k esetében is Azure Cosmos DB Java SDK 4,0-as verzióban van implementálva.

## <a name="api-level-changes"></a>API-szintű változások

A következő API-szint módosul Azure Cosmos DB Java SDK 4. x. x verzióban az előző SDK-k (Java SDK 3. x. x, aszinkron Java SDK 2. x. x és a Sync Java SDK 2. x. x) esetében:

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="A Java SDK elnevezési konvencióinak Azure Cosmos DB":::

* A Azure Cosmos DB Java SDK 3. x. x és 4,0 az ügyfél erőforrásait a következőképpen tekinti át: `Cosmos<resourceName>` . Például:, `CosmosClient` `CosmosDatabase` , `CosmosContainer` . Míg a 2. x. x verzióban a Azure Cosmos DB Java SDK-k nem rendelkeznek egységes elnevezési sémával.

* Azure Cosmos DB Java SDK 3. x. x és 4,0 egyaránt kínál szinkronizálási és aszinkron API-kat.

  * **Java SDK 4,0** : az összes osztály a szinkronizálási API-hoz tartozik, kivéve, ha az osztály nevét a következővel fűzi hozzá `Async` `Cosmos` .

  * **Java SDK 3. x. x**: az összes osztály az aszinkron API-hoz tartozik, kivéve, ha az osztály nevét a következővel fűzi hozzá `Async` `Cosmos` .

  * **Aszinkron Java SDK 2. x. x**: az osztályok neve hasonló a Java SDK 2. x. x verziójának szinkronizálásához, a név azonban *aszinkron* módon kezdődik.

### <a name="hierarchical-api-structure"></a>Hierarchikus API-struktúra

Azure Cosmos DB a Java SDK 4,0-es és 3. x. x verziója egy hierarchikus API-struktúrát mutat be, amely egy beágyazott módon rendezi az ügyfeleket, az adatbázisokat és a tárolókat, ahogyan az a következő 4,0 SDK-kódrészletben látható:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

A Azure Cosmos DB Java SDK 2. x. x verziójában az összes erőforrásra és dokumentumra vonatkozó műveletet az ügyfél-példányon keresztül hajtja végre a rendszer.

### <a name="representing-documents"></a>Dokumentumok képviselete

Azure Cosmos DB Java SDK 4,0-as verzióban az egyéni POJO, és `JsonNodes` a dokumentumok Azure Cosmos DBból való olvasására és írására szolgáló két lehetőség.

A Azure Cosmos DB Java SDK 3. x. x verziójában az `CosmosItemProperties` objektumot a nyilvános API teszi elérhetővé, és dokumentum-ábrázolásként szolgál. Ez az osztály az 4,0-es verzióban már nem nyilvánosan elérhető.

### <a name="imports"></a>Importálja

* A Azure Cosmos DB Java SDK 4,0 csomagok kezdete `com.azure.cosmos`
* Azure Cosmos DB Java SDK 3. x. x csomag kezdete `com.azure.data.cosmos`
* A Azure Cosmos DB Java SDK 2. x. x szinkronizációs API-csomagjai a következővel kezdődnek: `com.microsoft.azure.documentdb`

* Azure Cosmos DB Java SDK 4,0 több osztályt helyez el egy beágyazott csomagban `com.azure.cosmos.models` . A csomagok némelyike az alábbiakat tartalmazza:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Az aszinkron API az összes fenti csomaghoz hasonlít
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ... stb.

### <a name="accessors"></a>Elérőnek

Azure Cosmos DB Java SDK 4,0 elérhetővé `get` teszi `set` a példány tagjainak elérését és metódusait. Például a `CosmosContainer` példány `container.getId()` és a `container.setId()` metódusok.

Ez különbözik a Azure Cosmos DB Java SDK 3. x. x verziótól, amely egy Fluent felületet tesz elérhetővé. Például egy példány, `CosmosSyncContainer` `container.id()` amely túlterhelt az érték beolvasásához vagy beállításához `id` .

## <a name="code-snippet-comparisons"></a>Kódrészletek összehasonlítása

### <a name="create-resources"></a>Az erőforrások létrehozása

A következő kódrészlet bemutatja, hogy az erőforrások hogyan jönnek létre a 4,0, 3. x. x aszinkron API-k és 2. x. x sync API-k között:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x szinkronizációs API](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>Elemek műveletei

A következő kódrészlet a 4,0, 3. x. x aszinkron API és 2. x. x sync API-k közötti műveletek különbségeit mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x szinkronizációs API](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>Indexelés

A következő kódrészlet a 4,0, 3. x. x aszinkron API-k és 2. x. x szinkronizálási API-k közötti különbségeket mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x szinkronizációs API](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>Tárolt eljárások

A következő kódrészlet azt mutatja, hogy a tárolt eljárások hogyan jönnek létre a 4,0, 3. x. x aszinkron API és 2. x. x sync API-k között:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x szinkronizációs API](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Csatorna módosítása

A következő kódrészlet a 4,0 és 3. x. x aszinkron API-k közötti adatváltozások közötti különbségeket mutatja be:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x szinkronizációs API](#tab/java-v2-sync)

* Ez a funkció Java SDK v2-szinkronizálás esetén nem támogatott. 
---

### <a name="container-level-time-to-livettl"></a>Tárolói szint élettartama (TTL)

A következő kódrészlet azt mutatja be, hogyan lehet időt létrehozni a tárolóban lévő adatai számára a 4,0, 3. x. x aszinkron API-k és 2. x. x sync API-k használatával:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x szinkronizációs API](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>Elemek szintjének élettartama (TTL)

A következő kódrészlet azt mutatja be, hogyan lehet időt létrehozni egy elemhez a 4,0, 3. x. x aszinkron API-k és 2. x. x szinkronizálási API-k használatával:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 aszinkron API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x aszinkron API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x szinkronizációs API](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>Következő lépések

* [Java-alkalmazás létrehozása](create-sql-api-java.md) Azure Cosmos db SQL API-alapú adatkezeléshez a v4 SDK használatával
* Tudnivalók a [reaktor-alapú Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) -k használatáról
* Ismerje meg, hogyan konvertálhatja a RxJava aszinkron kódot a reaktorra és az aszinkron kódra a reaktor [vs RxJava útmutató](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) segítségével
