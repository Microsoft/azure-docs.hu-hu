---
title: Azure Cosmos DB az 1. x függvények kötéseit
description: Megtudhatja, hogyan használhatók Azure Cosmos DB eseményindítók és kötések az Azure Functions 1. x-ben.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 524df7805207ce517c7ae805fb17de1b041a2248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002110"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Functions 1. x Azure Cosmos DB kötései

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db](../cosmos-db/serverless-computing-database.md) kötések a Azure Functionsban. Azure Functions támogatja a Azure Cosmos DB trigger-, bemeneti és kimeneti kötéseit.

> [!NOTE]
> Ez a cikk az 1. x Azure Functions. További információ a kötések használatáról a 2. x és újabb függvények esetében: [Azure Cosmos db kötések Azure functions 2. x verzióhoz](functions-bindings-cosmosdb-v2.md).
>
>Ez a kötés eredetileg a DocumentDB nevet kapta. A functions 1. x verziójában csak az trigger lett átnevezve Cosmos DB; a bemeneti kötés, a kimeneti kötés és a NuGet csomag megőrzi a DocumentDB nevét.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Az Azure Cosmos DB-kötések csak az SQL API-val használhatók. Az összes többi Azure Cosmos DB API esetében az API-hoz készült statikus ügyfél használatával kell elérnie az adatbázist a függvényből, beleértve a [Azure Cosmos db API](../cosmos-db/mongodb-introduction.md)-ját a MongoDB, a [Cassandra API](../cosmos-db/cassandra-introduction.md), a [Gremlin API](../cosmos-db/graph-introduction.md)és a [Table API](../cosmos-db/table-introduction.md)számára.

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

Az 1. x függvények Azure Cosmos DB kötései az 1. x [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet csomagban találhatók. A kötések forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Eseményindító

A Azure Cosmos DB trigger a [Azure Cosmos db változási csatornát](../cosmos-db/change-feed.md) használja, hogy figyelje a lapkákat és a frissítéseket a partíciók között. A módosítási hírcsatorna közzéteszi a lapkákat és a frissítéseket, a törlést nem.

## <a name="trigger---example"></a>Trigger – példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely akkor jelenik meg, ha a megadott adatbázisban és gyűjteményben lapkák vagy frissítések vannak megadva.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy Cosmos DB trigger kötést mutat be a fájlban lévő *function.js* , és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény naplófájlokat ír a Cosmos DB rekordok módosításakor.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

A C# szkript kódja:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Cosmos DB trigger kötést mutat be a fájlban lévő *function.js* , és egy olyan [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény naplófájlokat ír a Cosmos DB rekordok módosításakor.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Itt látható a JavaScript-kód:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Trigger – attribútumok

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [trigger-Configuration](#trigger---configuration). `CosmosDBTrigger`Példa a metódus aláírására:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

A teljes példa: [trigger-C# példa](#trigger).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="trigger---configuration"></a>Trigger – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `CosmosDBTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `cosmosDBTrigger` . |
|**irányba** | n.a. | Értékre kell állítani `in` . Ez a paraméter automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n.a. | A módosítást tartalmazó dokumentumok listáját jelölő függvény kódjában használt változó neve. |
|**connectionStringSetting**|**ConnectionStringSetting** | A figyelt Azure Cosmos DB-fiókhoz való kapcsolódáshoz használt kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. |
|**databaseName**|**DatabaseName**  | Annak a Azure Cosmos DB-adatbázisnak a neve, amelyen a megfigyelt gyűjtemény szerepel. |
|**collectionName** |**CollectionName** | A figyelni kívánt gyűjtemény neve. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Választható Egy olyan Alkalmazásbeállítás neve, amely a címbérletet tároló szolgáltatáshoz tartozó kapcsolódási karakterláncot tartalmazza. Ha nincs beállítva, a `connectionStringSetting` rendszer az értéket használja. A rendszer automatikusan beállítja ezt a paramétert, ha a kötés a portálon jön létre. A bérletek gyűjteményéhez tartozó kapcsolatok karakterláncának írási engedélyekkel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Választható Annak az adatbázisnak a neve, amely a bérletek tárolásához használt gyűjteményt tárolja. Ha nincs beállítva, a `databaseName` rendszer a beállítás értékét használja. A rendszer automatikusan beállítja ezt a paramétert, ha a kötés a portálon jön létre. |
|**leaseCollectionName** | **LeaseCollectionName** | Választható A bérletek tárolásához használt gyűjtemény neve. Ha nincs beállítva, a rendszer az értéket `leases` használja. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Választható Ha a értékre `true` van állítva, a bérletek gyűjteménye automatikusan létrejön, ha még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Választható Meghatározza a bérletek gyűjteményének létrehozásakor hozzárendelni kívánt kérelmek mennyiségét. Ezt a beállítást csak akkor használja a rendszer, ha a `createLeaseCollectionIfNotExists` be van állítva `true` . A rendszer automatikusan beállítja ezt a paramétert, ha a kötést a portál használatával hozza létre.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Választható Ha be van állítva, egy előtagot hoz létre a függvény címbérleti gyűjteményében létrehozott bérletekhez, ami gyakorlatilag lehetővé teszi két különálló Azure Functions számára, hogy ugyanazt a címbérleti gyűjteményt használják különböző előtagok használatával.
|**feedPollDelay**| **FeedPollDelay**| Választható Ha be van állítva, a rendszer ezredmásodpercben meghatározza, hogy a rendszer hogyan kérdezi le az új változásokhoz tartozó partíciót a hírcsatornában, miután az összes aktuális változást kiüríti. Az alapértelmezett érték 5000 (5 másodperc).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Választható Ha be van állítva, a rendszer ezredmásodpercben meghatározza az intervallumot, hogy kiindítson egy feladatot a számításhoz, ha a partíciókat egyenletesen osztják el az ismert gazdagép-példányok között. Az alapértelmezett érték 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Választható A beállításakor a rendszer ezredmásodpercben meghatározza azt az intervallumot, ameddig a bérlet egy partíciót jelképező bérletre kerül. Ha a bérlet nem kerül megújításra ezen az intervallumon belül, az azt eredményezi, hogy a partíció tulajdonjoga lejár, és egy másik példányra kerül át. Az alapértelmezett érték 60000 (60 másodperc).
|**leaseRenewInterval**| **LeaseRenewInterval**| Választható A beállításakor a rendszer ezredmásodpercben meghatározza az adott példány által jelenleg tárolt partíciók címbérletének megújítási intervallumát. Az alapértelmezett érték 17000 (17 másodperc).
|**checkpointFrequency**| **CheckpointFrequency**| Választható Ha be van állítva, az meghatározza a címbérleti ellenőrzőpontok közötti intervallumot ezredmásodpercben. Az alapértelmezett érték mindig az egyes függvények hívása után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Választható Ha be van állítva, testreszabja a függvény hívásakor fogadott elemek maximális mennyiségét.
|**startFromBeginning**| **StartFromBeginning**| Választható Ha be van állítva, a elindítja a triggert, hogy a jelenlegi időpont helyett a gyűjtemény előzményeinek elejéről olvassa be a módosításokat. Ez csak az indítás első indításakor működik, ahogy a későbbi futtatásokban is, az ellenőrzőpontok már tárolva vannak. Ha úgy állítja be ezt a beállítást, hogy `true` a már létrehozott bérletek ne legyenek hatással.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger – használat

Az triggerhez egy második gyűjteményre van szükség, amelyet a a partíciók _bérletének_ tárolására használ. Mind a figyelt gyűjtemény, mind a bérleteket tartalmazó gyűjteménynek elérhetőnek kell lennie az trigger működéséhez.

>[!IMPORTANT]
> Ha több függvény úgy van konfigurálva, hogy egy Cosmos DB triggert használjon ugyanahhoz a gyűjteményhez, a függvények mindegyikének dedikált címbérleti gyűjteményt kell használnia, vagy eltérő értékre kell beállítania az `LeaseCollectionPrefix` egyes függvényeket. Ellenkező esetben csak az egyik függvény lesz aktiválva. További információ az előtagról: [konfiguráció szakasz](#trigger---configuration).

Az trigger nem jelzi, hogy a dokumentumot frissítették vagy beszúrták-e, csak magát a dokumentumot biztosítja. Ha a frissítéseket és a lapkákat különbözőképpen kell kezelnie, ezt megteheti a beszúráshoz vagy a frissítéshez tartozó időbélyegző-mezők megvalósításával.

## <a name="input"></a>Bevitel

Az Azure Cosmos DB bemeneti adatkötése az SQL API-t használja egy vagy több Azure Cosmos DB-dokumentum lekérésére, majd átadja ezeket a függvény bemeneti paraméterének. A dokumentumazonosítót vagy lekérdezési paramétereket a függvényt meghívó eseményindító alapján lehet meghatározni.

## <a name="input---example"></a>Bemenet – példa

# <a name="c"></a>[C#](#tab/csharp)

Ez a szakasz a következő példákat tartalmazza:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-c)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c)

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan üzenetsor-üzenet indítja el, amely egy JSON-objektumot tartalmaz. A várólista-trigger elemzi a JSON-t egy nevű objektumba `ToDoItemLookup` , amely tartalmazza a megkeresni kívánt azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A lekérdezés az `SqlQuery` attribútum tulajdonságban van megadva.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-trigger, több dokumentum beolvasása a DocumentClient (C#) használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A kód a `DocumentClient` Azure Cosmos db kötés által megadott példányt használja a dokumentumok listájának olvasásához. A `DocumentClient` példány írási műveletekhez is használható.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Ez a szakasz a következő példákat tartalmazza:

* [Üzenetsor-trigger, azonosító megkeresése sztringből](#queue-trigger-look-up-id-from-string-c-script)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c-script)

A HTTP-triggerek példái egy egyszerű `ToDoItem` típusra vonatkoznak:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Üzenetsor-trigger, azonosító megkeresése sztringből

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be a fájlban lévő *function.js* , és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be a fájlban lévő *function.js* , és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény több, egy SQL-lekérdezés által megadott dokumentumot kér le, a várólista-trigger használatával testreszabva a lekérdezési paramétereket.

A várólista-trigger paramétert biztosít `departmentId` . Egy üzenetsor-üzenet, amely `{ "departmentId" : "Finance" }` a pénzügyi részleg összes rekordját visszaküldi.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy [C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

A C# szkript kódja:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy [C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

A C# szkript kódja:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy [C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A lekérdezés az `SqlQuery` attribútum tulajdonságban van megadva.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

A C# szkript kódja:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával

Az alábbi példa egy [C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A kód a `DocumentClient` Azure Cosmos db kötés által megadott példányt használja a dokumentumok listájának olvasásához. A `DocumentClient` példány írási műveletekhez is használható.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ez a szakasz a következő példákat tartalmazza:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-javascript)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)


<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be egy *function.jsa* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function.jsa* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény több, egy SQL-lekérdezés által megadott dokumentumot kér le, a várólista-trigger használatával testreszabva a lekérdezési paramétereket.

A várólista-trigger paramétert biztosít `departmentId` . Egy üzenetsor-üzenet, amely `{ "departmentId" : "Finance" }` a pénzügyi részleg összes rekordját visszaküldi.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

---

## <a name="input---attributes"></a>Input-attributes

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. Ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#input---configuration)tájékozódhat.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="input---configuration"></a>Bemenet – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `DocumentDB` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa**     | n.a. | Értékre kell állítani `documentdb` .        |
|**irányba**     | n.a. | Értékre kell állítani `in` .         |
|**név**     | n.a. | A függvényben szereplő dokumentumot jelképező kötési paraméter neve.  |
|**databaseName** |**DatabaseName** |A dokumentumot tartalmazó adatbázis.        |
|**collectionName** |**CollectionName** | A dokumentumot tartalmazó gyűjtemény neve. |
|**id**    | **ID** | A lekérdezni kívánt dokumentum azonosítója. Ez a tulajdonság támogatja a [kötési kifejezéseket](./functions-bindings-expressions-patterns.md). Ne állítsa be az **ID** és a **sqlQuery** tulajdonságokat is. Ha nem állítja be az egyiket, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Több dokumentum beolvasására szolgáló Azure Cosmos DB SQL-lekérdezés. A tulajdonság támogatja a futásidejű kötéseket, az alábbi példában látható módon: `SELECT * FROM c where c.departmentId = {departmentId}` . Ne állítsa be az **ID** és a **sqlQuery** tulajdonságokat is. Ha nem állítja be az egyiket, a rendszer lekéri a teljes gyűjteményt.|
|**kapcsolat**     |**ConnectionStringSetting**|Az Azure Cosmos DB-kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve.        |
|**partitionKey**|**PartitionKey**|Meghatározza a keresés partíciós kulcsának értékét. Tartalmazhat kötési paramétereket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet – használat

# <a name="c"></a>[C#](#tab/csharp)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A rendszer nem végez automatikusan frissítéseket a függvény kilépése után. Ehelyett használja `context.bindings.<documentName>In` a és `context.bindings.<documentName>Out` a parancsot a frissítéshez. Tekintse meg a [bemeneti példát](#input).

---

## <a name="output"></a>Kimenet

A Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy új dokumentumot írjon egy Azure Cosmos DB-adatbázisba az SQL API használatával.

## <a name="output---example"></a>Kimenet – példa

# <a name="c"></a>[C#](#tab/csharp)

Ez a szakasz a következő példákat tartalmazza:

* Üzenetsor-trigger, egy doc írása
* Üzenetsor-trigger, dokumentumok írása a használatával `IAsyncCollector`

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Üzenetsor-trigger, egy doc írása

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy dokumentumot ad hozzá egy adatbázishoz a várólista-tárolóból származó adatok alapján.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely dokumentum-gyűjteményt ad hozzá egy adatbázishoz egy üzenetsor-üzenet JSON-fájljában megadott adat használatával.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Ez a szakasz a következő példákat tartalmazza:

* Üzenetsor-trigger, egy doc írása
* Üzenetsor-trigger, dokumentumok írása a használatával `IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Üzenetsor-trigger, egy doc írása

Az alábbi példa egy Azure Cosmos DB kimeneti kötést mutat be a fájlban lévő *function.js* , és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény üzenetsor-beviteli kötést használ egy olyan várólistához, amely a következő formátumban fogadja a JSON-t:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény a következő formátumban hozza létre Azure Cosmos DB dokumentumokat az egyes rekordokhoz:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával

Több dokumentum létrehozásához köthető a következőhöz, `ICollector<T>` vagy `IAsyncCollector<T>` ahol `T` a a támogatott típusok egyike.

Ez a példa egy egyszerű `ToDoItem` típusra hivatkozik:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

A fájl function.js:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C# szkript kódja:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Azure Cosmos DB kimeneti kötést mutat be egy *function.jsa* fájlban és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény üzenetsor-beviteli kötést használ egy olyan várólistához, amely a következő formátumban fogadja a JSON-t:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény a következő formátumban hozza létre Azure Cosmos DB dokumentumokat az egyes rekordokhoz:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

---

## <a name="output---attributes"></a>Kimenet – attribútumok

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [kimeneti konfiguráció](#output---configuration). `DocumentDB`Példa a metódus aláírására:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

A teljes példa: output ( [kimenet](#output)).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="output---configuration"></a>Kimenet – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `DocumentDB` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa**     | n.a. | Értékre kell állítani `documentdb` .        |
|**irányba**     | n.a. | Értékre kell állítani `out` .         |
|**név**     | n.a. | A függvényben szereplő dokumentumot jelképező kötési paraméter neve.  |
|**databaseName** | **DatabaseName**|A gyűjteményt tartalmazó adatbázis, amelyben a dokumentumot létrehozták.     |
|**collectionName** |**CollectionName**  | Annak a gyűjteménynek a neve, amelyben a dokumentumot létrehozták. |
|**Createifnotexists metódust**  |**Createifnotexists metódust**    | Logikai érték, amely azt jelzi, hogy a gyűjtemény létrejött-e, ha nem létezik. Az alapértelmezett érték a *false (hamis* ), mert az új gyűjtemények fenntartott átviteli sebességgel jönnek létre, ami a költségeket is érinti. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/documentdb/) olvasható.  |
|**partitionKey**|**PartitionKey** |Ha `CreateIfNotExists` az értéke igaz, meghatározza a létrehozott gyűjtemény partíciós kulcsának elérési útját.|
|**collectionThroughput**|**CollectionThroughput**| Ha `CreateIfNotExists` az értéke igaz, meghatározza a létrehozott gyűjtemény [átviteli sebességét](../cosmos-db/set-throughput.md) .|
|**kapcsolat**    |**ConnectionStringSetting** |Az Azure Cosmos DB-kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

Alapértelmezés szerint, amikor a függvény kimeneti paraméterére ír, létrejön egy dokumentum az adatbázisban. A dokumentum automatikusan generált GUID AZONOSÍTÓval rendelkezik. A kimeneti dokumentumhoz tartozó dokumentum AZONOSÍTÓját megadhatja a `id` kimeneti paraméternek átadott JSON-objektumban található tulajdonság megadásával.

> [!Note]
> Egy meglévő dokumentum AZONOSÍTÓjának megadásakor a rendszer felülírja az új kimeneti dokumentumot.

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Referencia |
|---|---|
| CosmosDB | [CosmosDB-hibakódok](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Következő lépések

* [További információ a kiszolgáló nélküli adatbázis-számítástechnika Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
