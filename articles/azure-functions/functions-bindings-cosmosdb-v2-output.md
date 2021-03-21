---
title: Azure Cosmos DB a 2. x és újabb függvények kimeneti kötését
description: Megtudhatja, hogyan használhatja a Azure Functions Azure Cosmos DB kimeneti kötését.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 779b66412319ec8422977a7e56570a4d16f89aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071544"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB kimeneti kötés Azure Functions 2. x és újabb verziókhoz

A Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy új dokumentumot írjon egy Azure Cosmos DB-adatbázisba az SQL API használatával.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Ez a szakasz a következő példákat tartalmazza:

* [Üzenetsor-trigger, egy doc írása](#queue-trigger-write-one-doc-c)
* [Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával](#queue-trigger-write-docs-using-iasynccollector-c)

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Üzenetsor-trigger, egy doc írása

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy dokumentumot ad hozzá egy adatbázishoz a várólista-tárolóból származó adatok alapján.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely dokumentum-gyűjteményt ad hozzá egy adatbázishoz egy üzenetsor-üzenet JSON-fájljában megadott adat használatával.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Ez a szakasz a következő példákat tartalmazza:

* [Üzenetsor-trigger, egy doc írása](#queue-trigger-write-one-doc-c-script)
* [Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával

Több dokumentum létrehozásához köthető a következőhöz, `ICollector<T>` vagy `IAsyncCollector<T>` ahol `T` a a támogatott típusok egyike.

Ez a példa egy egyszerű `ToDoItem` típusra hivatkozik:

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

A fájl function.js:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C# szkript kódja:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

* [Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték használatával](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési érték használatával](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy dokumentumot hoz létre egy, a várólista-tárolóban lévő üzenetből származó adatokkal rendelkező adatbázishoz.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési érték használatával

Az alábbi példa egy olyan Java-függvényt mutat be, amelynek aláírását a ```@CosmosDBOutput``` és a érték típusa adja vissza ```String``` . A függvény által visszaadott JSON-dokumentum automatikusan a megfelelő CosmosDB-gyűjteménybe lesz írva.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy CosmosDB egy kimeneti paraméter használatával ír egy dokumentumot ```OutputBinding<T>``` . Ebben a példában a ```outputItem``` paramétert a függvénnyel kell megadnia ```@CosmosDBOutput``` , nem a függvény aláírását. A használata lehetővé teszi a ```OutputBinding<T>``` függvény számára, hogy kihasználhassa a dokumentumot a CosmosDB, miközben lehetővé teszi, hogy más értéket adjanak vissza a függvény hívójának, például egy JSON-vagy XML-dokumentumnak.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy kimeneti paraméterrel több dokumentumot ír a CosmosDB ```OutputBinding<T>``` . Ebben a példában a paraméter a (z ```outputItem``` ```@CosmosDBOutput``` ), nem pedig a függvény aláírása. A kimeneti paraméter az ```outputItem``` objektumok listáját tartalmazza, ```ToDoItem``` mint a sablon paraméterének típusa. A használata lehetővé teszi, ```OutputBinding<T>``` hogy a függvény kihasználhassa a dokumentumokat a CosmosDB, miközben lehetővé teszi, hogy más értéket írjon vissza a függvény hívója számára, például egy JSON-vagy XML-dokumentumot.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja az `@CosmosDBOutput` Cosmos DBba írandó paraméterek megjegyzéseit.  A Megjegyzés paraméterének a következőnek kell lennie: ```OutputBinding<T>``` , ahol a T egy natív Java-típus vagy egy POJO.

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

A tömeges beszúráshoz először az objektumokat kell megalkotni, majd futtatni a stringify függvényt. Itt látható a JavaScript-kód:

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa bemutatja, hogyan írhat adatokat Cosmos DB egy kimeneti kötés használatával. A kötés deklarálva van a függvény konfigurációs fájljában (_functions.js_), és egy üzenetsor-üzenetből veszi át az adatot, és egy Cosmos db dokumentumba ír.

```json
{ 
  "name": "EmployeeDocument",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "direction": "out" 
} 
```

A _run.ps1_ fájlban a függvény által visszaadott objektum egy objektumra van leképezve `EmployeeDocument` , amely az adatbázisban marad.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name EmployeeDocument -Value @{ 
    id = $QueueItem.name + '-' + $QueueItem.employeeId 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa azt mutatja be, hogyan írhat dokumentumokat egy Azure CosmosDB-adatbázisba egy függvény kimenete.

A kötés definíciója *function.js* van meghatározva, ahol a *típus* értékre van állítva `cosmosDB` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Az adatbázisba való íráshoz adjon át egy dokumentum-objektumot az `set` adatbázis-paraméter metódusának.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [kimeneti konfiguráció](#configuration). `CosmosDB`Példa a metódus aláírására:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `CosmosDBOutput` jegyzet elérhető az adatCosmos DBba való íráshoz. A jegyzetet alkalmazhatja a függvényre vagy egy bizonyos Function paraméterre. A Function metódus használata esetén a függvény visszatérési értéke a Cosmos DBba írt érték. Ha a jegyzetet paraméterrel használja, a paraméter típusát a `OutputBinding<T>` `T` natív Java-típusnak vagy POJO kell deklarálnia.

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
|**irányba**     | n.a. | Értékre kell állítani `out` .         |
|**név**     | n.a. | A függvényben szereplő dokumentumot jelképező kötési paraméter neve.  |
|**databaseName** | **DatabaseName**|A gyűjteményt tartalmazó adatbázis, amelyben a dokumentumot létrehozták.     |
|**collectionName** |**CollectionName**  | Annak a gyűjteménynek a neve, amelyben a dokumentumot létrehozták. |
|**Createifnotexists metódust**  |**Createifnotexists metódust**    | Logikai érték, amely azt jelzi, hogy a gyűjtemény létrejött-e, ha nem létezik. Az alapértelmezett érték a *false (hamis* ), mert az új gyűjtemények fenntartott átviteli sebességgel jönnek létre, ami a költségeket is érinti. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) olvasható.  |
|**partitionKey**|**PartitionKey** |Ha `CreateIfNotExists` igaz, a létrehozta a létrehozott gyűjtemény partíciós kulcsának elérési útját.|
|**collectionThroughput**|**CollectionThroughput**| Ha `CreateIfNotExists` igaz, meghatározza a létrehozott gyűjtemény [átviteli sebességét](../cosmos-db/set-throughput.md) .|
|**connectionStringSetting**    |**ConnectionStringSetting** |Az Azure Cosmos DB-kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve.        |
|**preferredLocations**| **PreferredLocations**| Választható Meghatározza a földrajzilag replikált adatbázis-fiókok előnyben részesített helyét (régióit) a Azure Cosmos DB szolgáltatásban. Az értékeket vesszővel kell elválasztani. Például: "az USA keleti régiója, az USA déli középső régiója, Észak-Európa". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| Választható Ha a értékre `true` van állítva `PreferredLocations` , akkor a [többrégiós írásokat](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) is kihasználhatja a Azure Cosmos db szolgáltatásban. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Alapértelmezés szerint, amikor a függvény kimeneti paraméterére ír, létrejön egy dokumentum az adatbázisban. A dokumentum automatikusan generált GUID AZONOSÍTÓval rendelkezik. A kimeneti dokumentumhoz tartozó dokumentum AZONOSÍTÓját megadhatja a `id` kimeneti paraméternek átadott JSON-objektumban található tulajdonság megadásával.

> [!Note]
> Egy meglévő dokumentum AZONOSÍTÓjának megadásakor a rendszer felülírja az új kimeneti dokumentumot.

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Referencia |
|---|---|
| CosmosDB | [CosmosDB-hibakódok](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Beállítások host.js

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti a 2. x verzióban. A 2. x verzióban található globális konfigurációs beállításokkal kapcsolatos további információkért lásd: [host.jsAzure functions 2. x verzió hivatkozása](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Description |
|---------|---------|---------|
|GatewayMode|Átjáró|A függvény által a Azure Cosmos DB szolgáltatáshoz való csatlakozáskor használt kapcsolati mód. A lehetőségek a következők `Direct` , `Gateway`|
|Protokoll|Https|A függvény által a Azure Cosmos DB szolgáltatáshoz való kapcsolódáskor használt kapcsolati protokoll.  A [két mód magyarázata itt](../cosmos-db/performance-tips.md#networking) olvasható|
|leasePrefix|n.a.|Az alkalmazás összes függvényében használandó bérlet-előtag.|

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)
