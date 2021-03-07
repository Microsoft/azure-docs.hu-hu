---
title: A 2. x és újabb függvények bemeneti kötésének Azure Cosmos DB
description: Megtudhatja, hogyan használhatja a Azure Functions Azure Cosmos DB bemeneti kötését.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 21ca30b24c4824a2d303d02f3df712328885e199
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435205"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB bemeneti kötés Azure Functions 2. x és újabb verziókhoz

Az Azure Cosmos DB bemeneti adatkötése az SQL API-t használja egy vagy több Azure Cosmos DB-dokumentum lekérésére, majd átadja ezeket a függvény bemeneti paraméterének. A dokumentumazonosítót vagy lekérdezési paramétereket a függvényt meghívó eseményindító alapján lehet meghatározni.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Ha a gyűjtemény [particionálva](../cosmos-db/partitioning-overview.md#logical-partitions)van, a keresési műveleteknek meg kell adniuk a partíciós kulcs értékét is.
>

<a id="example" name="example"></a>

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
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből 

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan üzenetsor-üzenet indítja el, amely egy JSON-objektumot tartalmaz. A várólista-trigger elemzi a JSON-t egy típusú objektumba `ToDoItemLookup` , amely tartalmazza az azonosító és a partíció kulcs értékét a kereséshez. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

>[!NOTE]
>A HTTP-lekérdezési karakterlánc paraméter megkülönbözteti a kis-és nagybetűket.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál.

A példa bemutatja, hogyan használható egy kötési kifejezés a `SqlQuery` paraméterben. Átadhatja az útvonal adatait a `SqlQuery` paraméternek, ahogy az ábrán látható, de jelenleg [nem adhat meg lekérdezési karakterlánc-értékeket](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Ha csak az azonosító alapján kell lekérdezni, ajánlott megkeresni az [előző példákhoz](#http-trigger-look-up-id-from-query-string-c)hasonlóan, mivel kevesebb [kérési egységet](../cosmos-db/request-units.md)fog használni. A pont olvasási műveletei (GET) [hatékonyabbak, mint a](../cosmos-db/optimize-cost-reads-writes.md) lekérdezések azonosító alapján.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A lekérdezés az `SqlQuery` attribútum tulajdonságban van megadva.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A kód a `DocumentClient` Azure Cosmos db kötés által megadott példányt használja a dokumentumok listájának olvasásához. A `DocumentClient` példány írási műveletekhez is használható.

> [!NOTE]
> A [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient) felületet is használhatja a tesztelés megkönnyítésére.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
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
namespace CosmosDBSamplesV2
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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

Az alábbi példa egy [C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

A C# szkript kódja:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy [C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

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
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

A C# szkript kódja:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
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
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
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
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
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
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
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
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – karakterlánc paraméter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – POJO paraméter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-trigger, több dokumentum beolvasása az útvonal adataiból, SqlQuery használatával](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – karakterlánc paraméter

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értéke a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére szolgál karakterlánc formájában.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@CosmosDBInput` Megjegyzések a függvény azon paramétereit, amelyek értéke Cosmos DBból származik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – POJO paraméter

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. A megadott adatbázisból és gyűjteményből származó dokumentum lekéréséhez használt azonosító és partíciós kulcs értéke. A dokumentumot ezután a rendszer a korábban létrehozott POJO egy példányára konvertálja ```ToDoItem``` , és argumentumként adta át a függvénynek.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy Route paramétert használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értékét a rendszer a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja ```Optional<String>``` .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy Route paramétert használó HTTP-kérelem indítja el, amely megadja a megkeresni kívánt azonosítót. Ez az azonosító a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére, az eredményhalmaz a-re való átalakítására szolgál, ```ToDoItem[]``` mivel a lekérdezési feltételektől függően sok dokumentum visszaadásra kerül.

> [!NOTE]
> Ha csak az azonosító alapján kell lekérdezni, ajánlott megkeresni az [előző példákhoz](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)hasonlóan, mivel kevesebb [kérési egységet](../cosmos-db/request-units.md)fog használni. A pont olvasási műveletei (GET) [hatékonyabbak, mint a](../cosmos-db/optimize-cost-reads-writes.md) lekérdezések azonosító alapján.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása az útvonal adataiból, SqlQuery használatával

Az alábbi példa egy Java-függvényt mutat be, amely több dokumentumot kér le. A függvényt egy Route paramétert használó HTTP-kérelem indítja ```desc``` el, amely megadja a mezőben keresendő karakterláncot ```description``` . A keresési kifejezés a megadott adatbázisból és gyűjteményből származó dokumentumok gyűjteményének lekérésére, valamint az eredményhalmaz ```ToDoItem[]``` és a függvény argumentumként való átadására szolgál.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
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

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

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
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
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
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-ps)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-id-query-string-ps)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-id-route-data-ps)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-multiple-docs-sqlquery-ps)

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből

Az alábbi példa bemutatja, hogyan olvashat és frissíthet egyetlen Cosmos DB dokumentumot. A dokumentum egyedi azonosítója egy üzenetsor-üzenetben JSON-értékkel van megadva.

A Cosmos DB bemeneti kötés szerepel a függvény konfigurációs fájljában (_function.js_) található kötések listájában.

<a name="queue-trigger-look-up-id-from-json-ps"></a>

```json
{
  "name": "InputDocumentIn",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id": "{queueTrigger_payload_property}",
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "in"
},
{
  "name": "InputDocumentOut",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": false,
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "out"
}
```

A _run.ps1_ fájl a PowerShell-kóddal rendelkezik, amely beolvassa a bejövő dokumentumot, és megváltoztatja a kimeneteket.

```powershell
param($QueueItem, $InputDocumentIn, $TriggerMetadata) 

$Document = $InputDocumentIn 
$Document.text = 'This was updated!' 

Push-OutputBinding -Name InputDocumentOut -Value $Document  
```

<a name="http-trigger-id-query-string-ps"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa bemutatja, hogyan olvashatja és frissítheti az egyetlen Cosmos DB dokumentumot egy webes API-ból. A dokumentum egyedi azonosítója a HTTP-kérelemben megadott querystring-paraméteren keresztül érhető el a kötés `"Id": "{Query.Id}"` tulajdonságában megadott módon.

A Cosmos DB bemeneti kötés szerepel a függvény konfigurációs fájljában (_function.js_) található kötések listájában.

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{Query.id}", 
      "PartitionKey": "{Query.partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ] 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    },
  ], 
  "disabled": false 
} 
```
  
A _run.ps1_ fájl a PowerShell-kóddal rendelkezik, amely beolvassa a bejövő dokumentumot, és megváltoztatja a kimeneteket.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 

    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
 
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
}
```

<a name="http-trigger-id-route-data-ps"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa bemutatja, hogyan olvashatja és frissítheti az egyetlen Cosmos DB dokumentumot egy webes API-ból. A dokumentum egyedi azonosítója egy Route paraméterrel van megadva. Az útvonal paraméter a HTTP-kérelem kötésének tulajdonságában van definiálva `route` , és a Cosmos db `"Id": "{Id}"` kötési tulajdonságban hivatkozik rá.

A Cosmos DB bemeneti kötés szerepel a függvény konfigurációs fájljában (_function.js_) található kötések listájában.

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{id}", 
      "PartitionKey": "{partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ], 
      "route": "todoitems/{partitionKeyValue}/{id}" 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    }
  ], 
  "disabled": false 
} 
```

A _run.ps1_ fájl a PowerShell-kóddal rendelkezik, amely beolvassa a bejövő dokumentumot, és megváltoztatja a kimeneteket.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 
    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
  
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
} 
```

<a name="queue-trigger-multiple-docs-sqlquery-ps"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa azt mutatja be, hogyan lehet több Cosmos DB dokumentumot beolvasni. A függvény konfigurációs fájlja (_function.json_) meghatározza a kötési tulajdonságokat, beleértve a következőt: `sqlQuery` . A tulajdonsághoz megadott SQL-utasítás `sqlQuery` kiválasztja a függvénynek megadott dokumentumok készletét.

```json
{ 
  "name": "Documents", 
  "type": "cosmosDB", 
  "direction": "in", 
  "databaseName": "MyDb", 
  "collectionName": "MyCollection", 
  "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}", 
  "connectionStringSetting": "CosmosDBConnection" 
} 
```

A _run1.PS_ fájlhoz a PowerShell-kód tartozik, amely beolvassa a bejövő dokumentumokat.

```powershell
param($QueueItem, $Documents, $TriggerMetadata) 

foreach ($Document in $Documents) { 
    # operate on each document 
} 
```

# <a name="python"></a>[Python](#tab/python)

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-python)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-python)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be a fájlban lévő *function.js* , és egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy olyan [Python-függvényt](functions-reference-python.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

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
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy olyan [Python-függvényt](functions-reference-python.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értékét a rendszer `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja.

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
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be a fájlban lévő *function.js* , és egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény több, egy SQL-lekérdezés által megadott dokumentumot kér le, a várólista-trigger használatával testreszabva a lekérdezési paramétereket.

A várólista-trigger paramétert biztosít `departmentId` . Egy üzenetsor-üzenet, amely `{ "departmentId" : "Finance" }` a pénzügyi részleg összes rekordját visszaküldi.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

 ---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. Ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#configuration)tájékozódhat.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](/java/api/overview/azure/functions/runtime)használja a `@CosmosDBOutput` jegyzetet a Cosmos DBba írni kívánt paraméterekhez. A Megjegyzés paraméterének a következőnek kell lennie: `OutputBinding<T>` , ahol `T` egy natív Java-típus vagy egy POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `CosmosDB` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**típusa**     | n.a. | Értékre kell állítani `cosmosDB` .        |
|**irányba**     | n.a. | Értékre kell állítani `in` .         |
|**név**     | n.a. | A függvényben szereplő dokumentumot jelképező kötési paraméter neve.  |
|**databaseName** |**DatabaseName** |A dokumentumot tartalmazó adatbázis.        |
|**collectionName** |**CollectionName** | A dokumentumot tartalmazó gyűjtemény neve. |
|**id**    | **ID** | A lekérdezni kívánt dokumentum azonosítója. Ez a tulajdonság támogatja a [kötési kifejezéseket](./functions-bindings-expressions-patterns.md). Ne állítsa be a `id` és a **sqlQuery** tulajdonságot is. Ha nem állítja be az egyiket, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Több dokumentum beolvasására szolgáló Azure Cosmos DB SQL-lekérdezés. A tulajdonság támogatja a futásidejű kötéseket, az alábbi példában látható módon: `SELECT * FROM c where c.departmentId = {departmentId}` . Ne állítsa be a `id` és a `sqlQuery` tulajdonságokat is. Ha nem állítja be az egyiket, a rendszer lekéri a teljes gyűjteményt.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Az Azure Cosmos DB-kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. |
|**partitionKey**|**PartitionKey**|Meghatározza a keresés partíciós kulcsának értékét. Tartalmazhat kötési paramétereket. A [particionált](../cosmos-db/partitioning-overview.md#logical-partitions) gyűjtemények esetében szükséges a keresés.|
|**preferredLocations**| **PreferredLocations**| Választható Meghatározza a földrajzilag replikált adatbázis-fiókok előnyben részesített helyét (régióit) a Azure Cosmos DB szolgáltatásban. Az értékeket vesszővel kell elválasztani. Például: "az USA keleti régiója, az USA déli középső régiója, Észak-Európa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="java"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](/java/api/overview/azure/functions/runtime)a [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) jegyzet kiteszi Cosmos db adatait a függvénynek. Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A rendszer nem végez automatikusan frissítéseket a függvény kilépése után. Ehelyett használja `context.bindings.<documentName>In` a és `context.bindings.<documentName>Out` a parancsot a frissítéshez. További részletekért tekintse meg a [JavaScript-példát](#example) .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A rendszer nem frissíti automatikusan a dokumentumokat a függvények kilépése után. Egy függvény dokumentumainak frissítése [kimeneti kötést](./functions-bindings-cosmosdb-v2-input.md)használ. További részletekért tekintse meg a [PowerShell-példát](#example) .

# <a name="python"></a>[Python](#tab/python)

Az adat a függvény számára elérhetővé válik egy `DocumentList` paraméter használatával. A dokumentumban végrehajtott módosítások nem maradnak meg automatikusan.

---

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB dokumentum módosításainak mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)