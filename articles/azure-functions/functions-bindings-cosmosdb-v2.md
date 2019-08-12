---
title: Azure Cosmos DB a 2. x függvények kötéseit
description: Megtudhatja, hogyan használhatja az Azure Cosmos DB-eseményindítók és kötések az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 67b70a67065bfc66639b0f6911f66111829c9a0f
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774930"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Functions 2. x Azure Cosmos DB kötései

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db](../cosmos-db/serverless-computing-database.md) kötések Azure functions 2. x verzióban. Az Azure Functions támogatja a-trigger, bemeneti és kimeneti kötések az Azure Cosmos DB.

> [!NOTE]
> Ez a cikk a [Azure functions 2. x verziójának](functions-versions.md).  További információ a kötések használatáról az 1. x függvényeknél: [Azure Cosmos db kötések Azure functions 1. x verzióhoz](functions-bindings-cosmosdb.md).
>
> Ennek a kötésnek a DocumentDB eredetileg neve. A functions 2. x verziójában a trigger, a kötések és a csomag neve Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Támogatott API-k

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A függvények Azure Cosmos DB kötései a 2. x verzióhoz tartoznak a [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet csomag 3. x verziójában. A kötések forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

Használja az Azure Cosmos DB-eseményindító a [Azure Cosmos DB módosítási hírcsatorna](../cosmos-db/change-feed.md) figyeljen a beszúrások és frissítések partíciók között. A módosítási hírcsatorna beszúrások és frissítéseket, törléseket nem tesz közzé.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Trigger-példák kihagyása

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amelyek akkor aktiválódnak, ha nincsenek be vagy frissít a megadott adatbázis és gyűjtemény.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

Trigger-példák kihagyása

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa bemutatja egy kötést a Cosmos DB-eseményindító egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény naplóüzenetek ír, Cosmos DB-rekordok módosításakor.

Itt van a kötési adatait a *function.json* fájlt:

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

Íme a C#-szkriptkódot:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

Trigger-példák kihagyása

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa bemutatja egy kötést a Cosmos DB-eseményindító egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény naplóüzenetek ír, Cosmos DB-rekordok módosításakor.

Itt van a kötési adatait a *function.json* fájlt:

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

A következő JavaScript-kódot:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa egy Cosmos DB trigger-kötést mutat be a *function. JSON* fájlban, valamint egy olyan [Java](functions-reference-java.md) -függvényt, amely a kötést használja. A függvény akkor vesz részt, ha beszúrások vagy frissítések szerepelnek a megadott adatbázisban és gyűjteményben.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

A Java-kód itt látható:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@CosmosDBTrigger` a Megjegyzés azon paramétereket, amelyek értéke Cosmos DBból származik.  Ezt a jegyzetet natív Java-típusokkal, szerializálói vagy NULL értékű értékekkel lehet használni opcionális\<T > használatával.


Trigger-példák kihagyása

### <a name="trigger---python-example"></a>Trigger – Python-példa

Az alábbi példa egy Cosmos DB trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python](functions-reference-python.md) -függvényt, amely a kötést használja. A függvény naplóüzenetek ír, Cosmos DB-rekordok módosításakor.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Itt látható a Python-kód:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

## <a name="trigger---c-attributes"></a>Trigger – C# attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútum.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. Ezek a beállítások és más tulajdonságokat is beállíthat kapcsolatos információkért lásd: [eseményindító - konfiguráció](#trigger---configuration). Íme egy `CosmosDBTrigger` attribútum példa egy podpis metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).


## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `CosmosDBTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** || Meg kell `cosmosDBTrigger`. |
|**direction** || Meg kell `in`. Ez a paraméter beállítása automatikusan történik, amikor az eseményindító hoz létre az Azure Portalon. |
|**name** || A függvénykód módosítása dokumentumok listájának jelölő a használt változó neve. |
|**connectionStringSetting**|**ConnectionStringSetting** | A figyelt Azure Cosmos DB-fiókhoz való csatlakozáshoz használt kapcsolati karakterlánc tartalmazó alkalmazásbeállítás neve. |
|**databaseName**|**databaseName**  | A figyelt gyűjteményhez az Azure Cosmos DB-adatbázis neve. |
|**collectionName** |**collectionName** | A figyelt gyűjtemény neve. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Nem kötelező) A kapcsolati karakterláncot a szolgáltatásnak, amely tartalmazza a bérletek gyűjteményének tartalmazó alkalmazásbeállítás neve. Ha nincs megadva, a `connectionStringSetting` értéket használja. Ez a paraméter értéke a portálon a kötés létrehozásakor automatikusan. A bérletek gyűjteménye kapcsolati karakterláncára írási engedéllyel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Nem kötelező) A database csatlakoztatásához használt bérletek tárolásához használni kívánt gyűjtemény neve. Ha nincs megadva, az értékét a `databaseName` beállítást használja. Ez a paraméter értéke a portálon a kötés létrehozásakor automatikusan. |
|**leaseCollectionName** | **leaseCollectionName** | (Nem kötelező) A használt bérletek tárolásához használni kívánt gyűjtemény neve. Ha nincs megadva, az érték `leases` szolgál. |
|**createLeaseCollectionIfNotExists** | **createLeaseCollectionIfNotExists** | (Nem kötelező) Ha a beállítása `true`, a bérletek gyűjteménye automatikusan létrejön, amikor még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (Nem kötelező) A Kérelemegységek rendel a bérletek gyűjteménye létrehozásakor mennyisége határozza meg. Ez a beállítás csak használhatók, amikor nem `createLeaseCollectionIfNotExists` értékre van állítva `true`. Ez a paraméter értéke automatikusan, a kötés létrehozásakor a portál használatával.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| (Nem kötelező) Érték beállítása esetén azt ad hozzá egy előtagot a bérletek a bérletek gyűjteményének ennél a függvénynél, hatékonyan lehetővé teszi két különálló Azure Functions, az ugyanazon a bérletek gyűjteményének megosztása más előtagok használatával létrehozott.
|**feedPollDelay**| **feedPollDelay**| (Nem kötelező) Ha a készlet, azt határozza meg, ezredmásodpercben, a késleltetés között egy partíciót a hírcsatorna az új módosításokat a lekérdezések összes aktuális változtatások vannak ürítve. Alapértelmezés szerint 5000 (5 másodperces).
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz elindít egy feladatot a számítási, ha a partíciók lesznek elosztva a gazdagép ismert példányok között. Alapértelmezés szerint 13000 (13 másodperc).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz, amelynek a bérlet egy bérletet, egy partíciót jelölő készül. A bérlet ezen az időtartamon belül nem újítja meg, ha azt eredményezi, hamarosan lejár, és a partíció tulajdonjogának áthelyezi egy másik példánya. Alapértelmezés szerint 60000 (60 másodperc).
|**leaseRenewInterval**| **leaseRenewInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, minden bérletek példány által jelenleg birtokolt partíciók megújítási időköz. Alapértelmezés szerint 17000 (17 másodperc).
|**checkpointFrequency**| **checkpointFrequency**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, a bérlet ellenőrzőpontok közötti időtartam. Az alapértelmezett érték mindig az egyes függvények hívása után történik.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| (Nem kötelező) Ha a beállítás, azt testreszabja egy függvény hívásához szükséges fogadott elemek maximális számát.
|**startFromBeginning**| **StartFromBeginning**| Választható Ha be van állítva, a elindítja a triggert, hogy a jelenlegi időpont helyett a gyűjtemény előzményeinek elejéről olvassa be a módosításokat. Ez csak az indítás első indításakor működik, ahogy a későbbi futtatásokban is, az ellenőrzőpontok már tárolva vannak. Ha úgy állítja `true` be ezt a beállítást, hogy a már létrehozott bérletek ne legyenek hatással.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

Az eseményindító van szükség egy második gyűjtemény tárolásához használt _bérletek_ a külön partíciókban. A figyelt gyűjtemény és a gyűjtemény, amely tartalmazza a bérleteket is működnek az eseményindító elérhetőnek kell lennie.

>[!IMPORTANT]
> Ha több funkciók vannak konfigurálva, egy Cosmos DB-eseményindító használandó ugyanabba a gyűjteménybe, minden egyes funkciót kell egy dedikált a bérletek gyűjteményének használhatja, vagy megadhat egy másik `LeaseCollectionPrefix` minden függvényre. Ellenkező esetben a függvények csak az egyik aktiválódik. Az előtag kapcsolatos információkért tekintse meg a [konfigurációs szakasz](#trigger---configuration).

Az eseményindító egy dokumentumot frissítve lett, vagy beszúrva, azt csak biztosítja-e a saját maga a dokumentum nem jelzi. Ha frissítések, és beszúr eltérően kezelésére van szüksége, beszúrási vagy frissítési időbélyegző mezők az életbe léptetésével ezt.

## <a name="input"></a>Input (Bemenet)

Az Azure Cosmos DB bemeneti kötéssel legalább egy Azure Cosmos DB-dokumentumok lekérdezésének az SQL API-t használja, és továbbítja őket a függvény a bemeneti paraméter. A dokumentum azonosítója vagy lekérdezési paramétereket az eseményindítót, amely meghívja a függvényt alapján lehet meghatározni.

## <a name="input---examples"></a>Bemenet - példák

Példák a nyelvspecifikus, amely egyetlen dokumentum olvasása egy azonosító érték megadásával:

* [C#](#input---c-examples)
* [C# script (.csx)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Hagyja ki a bemeneti példák](#input---attributes)

### <a name="input---c-examples"></a>Bemenet - példák C#

Ez a szakasz tartalmazza az alábbi példák:

* [Üzenetsor eseményindító, keresse meg a JSON-ból azonosítója](#queue-trigger-look-up-id-from-json-c)
* [HTTP-eseményindító, ID keresse meg a lekérdezési karakterláncból.](#http-trigger-look-up-id-from-query-string-c)
* [HTTP eseményindító útvonal adatokból azonosító keresése](#http-trigger-look-up-id-from-route-data-c)
* [HTTP eseményindító útvonal-adatokból, amelyek használatával az SQL-lekérdezés azonosító keresése](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-trigger, a több docs-SQL-lekérdezés használatával](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-trigger, a több docs DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c)

Tekintse meg a példák egy egyszerű `ToDoItem` típusa:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Üzenetsor eseményindító, keresse meg a Azonosítót (C#) JSON-ból

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum beolvasása. A függvény, amely tartalmaz egy JSON-objektum egy üzenetsor által aktivált. Az üzenetsor eseményindító elemzi a JSON az nevű objektum `ToDoItemLookup`, keresse ki az Azonosítóját tartalmazza, amelyek. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

```cs
namespace CosmosDBSamplesV2
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
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-eseményindító, ID keresse meg a lekérdezési karakterláncból (C#)

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum beolvasása. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

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
                Id = "{Query.id}")] ToDoItem toDoItem,
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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP eseményindító útvonal adatokból (C#) azonosító keresése

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum beolvasása. A függvény akkor indul el, hogy a használt, keresse ki az azonosító megadása adatainak átirányítása a HTTP-kérelemmel történik. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

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
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP eseményindító útvonal adatokból, SQL-lekérdezés (C#) használatával azonosító keresése

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum beolvasása. A függvény akkor indul el, hogy a használt, keresse ki az azonosító megadása adatainak átirányítása a HTTP-kérelemmel történik. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

A példa bemutatja, hogyan használható egy kötési kifejezés a `SqlQuery` paraméterben. Átadhatja az útvonal adatait a `SqlQuery` paraméternek, ahogy az ábrán látható, de jelenleg [nem adhat meg lekérdezési karakterlánc-értékeket](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP-trigger, a több docs-SQL-lekérdezés (C#) használatával

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely dokumentumot listájának beolvasása. A függvényt egy HTTP-kérelem aktiválja. A lekérdezés van megadva a `SqlQuery` tulajdonság attribútum.

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-trigger, a több docs DocumentClient (C#) használatával

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely dokumentumot listájának beolvasása. A függvényt egy HTTP-kérelem aktiválja. A kód egy `DocumentClient` dokumentumok listája olvasható az Azure Cosmos DB-kötés által megadott-példány. A `DocumentClient` példány is használható az írási műveletek.

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

[Hagyja ki a bemeneti példák](#input---attributes)

### <a name="input---c-script-examples"></a>Bemenet – C#-példaszkriptek

Ez a szakasz tartalmazza az alábbi példák:

* [Üzenetsor eseményindító azonosító keresse meg a karakterláncból.](#queue-trigger-look-up-id-from-string-c-script)
* [Várólista-eseményindító, majd a több docs-SQL-lekérdezés használatával](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-eseményindító, ID keresse meg a lekérdezési karakterláncból.](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP eseményindító útvonal adatokból azonosító keresése](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-trigger, a több docs-SQL-lekérdezés használatával](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, a több docs DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Tekintse meg a HTTP-eseményindító példák egy egyszerű `ToDoItem` típusa:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Üzenetsor eseményindító, (C#-szkript) karakterláncból Azonosítóját keresése

Az alábbi példa bemutatja a Cosmos DB bemeneti kötéssel egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény beolvassa az egyetlen dokumentum, és frissíti a dokumentum szöveges érték.

Itt van a kötési adatait a *function.json* fájlt:

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
A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Várólista-eseményindító, majd a több docs-SQL-lekérdezés (C#-szkript) használatával

Az alábbi példa bemutatja egy Azure Cosmos DB bemeneti kötéssel a egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy SQL-lekérdezést, egy üzenetsor eseményindító segítségével testre szabhatja a lekérdezési paraméterek által megadott több dokumentumot kérdezi le.

Az üzenetsor eseményindító biztosít egy paraméter `departmentId`. Az üzenetsorbeli üzenet `{ "departmentId" : "Finance" }` adna vissza, a pénzügyi részleg összes rekordja.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>HTTP-eseményindító, ID keresse meg a lekérdezési karakterláncból (C#-szkript)

A következő példa bemutatja egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely egyetlen dokumentum beolvasása. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

Íme a *function.json* fájlt:

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

Íme a C#-szkriptkódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>HTTP eseményindító útvonal adatokból (C#-szkript) azonosító keresése

A következő példa bemutatja egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely egyetlen dokumentum beolvasása. A függvény akkor indul el, hogy a használt, keresse ki az azonosító megadása adatainak átirányítása a HTTP-kérelemmel történik. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

Íme a *function.json* fájlt:

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Íme a C#-szkriptkódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP-trigger, a több docs-SQL-lekérdezés (C#-szkript) használatával

A következő példa bemutatja egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely dokumentumot listájának beolvasása. A függvényt egy HTTP-kérelem aktiválja. A lekérdezés van megadva a `SqlQuery` tulajdonság attribútum.

Íme a *function.json* fájlt:

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

Íme a C#-szkriptkódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP-trigger, a több docs DocumentClient (C#-szkript) használatával

A következő példa bemutatja egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely dokumentumot listájának beolvasása. A függvényt egy HTTP-kérelem aktiválja. A kód egy `DocumentClient` dokumentumok listája olvasható az Azure Cosmos DB-kötés által megadott-példány. A `DocumentClient` példány is használható az írási műveletek.

Íme a *function.json* fájlt:

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

Íme a C#-szkriptkódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

### <a name="input---javascript-examples"></a>Bemenet - JavaScript-példák

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

* [Üzenetsor eseményindító, keresse meg a JSON-ból azonosítója](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-eseményindító, ID keresse meg a lekérdezési karakterláncból.](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP eseményindító útvonal adatokból azonosító keresése](#http-trigger-look-up-id-from-route-data-javascript)
* [Várólista-eseményindító, majd a több docs-SQL-lekérdezés használatával](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Üzenetsor eseményindító, keresse meg a-azonosító a JSON (JavaScript)

Az alábbi példa bemutatja a Cosmos DB bemeneti kötéssel egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény beolvassa az egyetlen dokumentum, és frissíti a dokumentum szöveges érték.

Itt van a kötési adatait a *function.json* fájlt:

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
A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>HTTP-eseményindító, ID keresse meg a lekérdezési karakterláncból (JavaScript)

A következő példa bemutatja egy [JavaScript-függvény](functions-reference-node.md) , amely egyetlen dokumentum beolvasása. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

Íme a *function.json* fájlt:

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

A következő JavaScript-kódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>HTTP eseményindító útvonal adatokból (JavaScript-) azonosító keresése

A következő példa bemutatja egy [JavaScript-függvény](functions-reference-node.md) , amely egyetlen dokumentum beolvasása. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

Íme a *function.json* fájlt:

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
      "type": "cosmosDB",
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

A következő JavaScript-kódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Várólista-eseményindító, majd a több docs-SQL-lekérdezés (JavaScript) használatával

Az alábbi példa bemutatja egy Azure Cosmos DB bemeneti kötéssel a egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy SQL-lekérdezést, egy üzenetsor eseményindító segítségével testre szabhatja a lekérdezési paraméterek által megadott több dokumentumot kérdezi le.

Az üzenetsor eseményindító biztosít egy paraméter `departmentId`. Az üzenetsorbeli üzenet `{ "departmentId" : "Finance" }` adna vissza, a pénzügyi részleg összes rekordja.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

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

[Hagyja ki a bemeneti példák](#input---attributes)

### <a name="input---python-examples"></a>Bemenet – Python-példák

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

* [Üzenetsor eseményindító, keresse meg a JSON-ból azonosítója](#queue-trigger-look-up-id-from-json-python)
* [HTTP-eseményindító, ID keresse meg a lekérdezési karakterláncból.](#http-trigger-look-up-id-from-query-string-python)
* [HTTP eseményindító útvonal adatokból azonosító keresése](#http-trigger-look-up-id-from-route-data-python)
* [Várólista-eseményindító, majd a több docs-SQL-lekérdezés használatával](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből (Python)

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python](functions-reference-python.md) -függvényt, amely a kötést használja. A függvény beolvassa az egyetlen dokumentum, és frissíti a dokumentum szöveges érték.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Itt látható a Python-kód:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncból (Python)

Az alábbi példa egy olyan [Python](functions-reference-python.md) -függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

Íme a *function.json* fájlt:

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": true,
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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>HTTP-trigger, azonosító megkeresése az útválasztási adatokból (Python)

Az alábbi példa egy olyan [Python](functions-reference-python.md) -függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

Íme a *function.json* fájlt:

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
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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

[Hagyja ki a bemeneti példák](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery (Python) használatával

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python](functions-reference-python.md) -függvényt, amely a kötést használja. A függvény egy SQL-lekérdezést, egy üzenetsor eseményindító segítségével testre szabhatja a lekérdezési paraméterek által megadott több dokumentumot kérdezi le.

Az üzenetsor eseményindító biztosít egy paraméter `departmentId`. Az üzenetsorbeli üzenet `{ "departmentId" : "Finance" }` adna vissza, a pénzügyi részleg összes rekordja.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Itt látható a Python-kód:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Hagyja ki a bemeneti példák](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Bemenet - F# példák

Az alábbi példa bemutatja a Cosmos DB bemeneti kötéssel egy *function.json* fájl és a egy [ F# függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény beolvassa az egyetlen dokumentum, és frissíti a dokumentum szöveges érték.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a F# kódot:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Ebben a példában van szükség egy `project.json` fájlt, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet-függőségek:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Hozzáadása egy `project.json` fájlt [ F# felügyeleti csomag](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Bevitel – Java-példák

Ez a szakasz tartalmazza az alábbi példák:

* [HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – karakterlánc paraméter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – POJO paraméter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP eseményindító útvonal adatokból azonosító keresése](#http-trigger-look-up-id-from-route-data-java)
* [HTTP eseményindító útvonal-adatokból, amelyek használatával az SQL-lekérdezés azonosító keresése](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-trigger, több dokumentum beolvasása az útvonal adataiból, SqlQuery használatával](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Tekintse meg a példák egy egyszerű `ToDoItem` típusa:

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

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncból – karakterlánc-paraméter (Java)

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító megadásához. Ez az azonosító a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére szolgál karakterlánc formájában.

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
              partitionKey = "{Query.id}",
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

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@CosmosDBInput` a megjegyzések a függvény azon paramétereit, amelyek értéke Cosmos DBból származik.  Ezt a jegyzetet natív Java-típusokkal, szerializálói vagy NULL értékű értékekkel lehet használni opcionális\<T > használatával.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncból – POJO paraméter (Java)

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító megadásához. Ez az azonosító a megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál. A dokumentumot ezután a rendszer a ```ToDoItem``` korábban létrehozott POJO egy példányára konvertálja, és argumentumként adta át a függvénynek.

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
              partitionKey = "{Query.id}",
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

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak (Java) alapján

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy Route paramétert használó HTTP-kérelem indítja el, amely megadja a megkeresni kívánt azonosítót. Ez az azonosító a megadott adatbázisból és gyűjteményből ```Optional<String>```származó dokumentumok beolvasására szolgál.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{id}",
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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery (Java) használatával

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy Route paramétert használó HTTP-kérelem indítja el, amely megadja a megkeresni kívánt azonosítót. Ez az azonosító a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére, az eredményhalmaz a ```ToDoItem[]```-re való átalakítására szolgál, mivel a lekérdezési feltételektől függően sok dokumentum visszaadásra kerül.

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

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>HTTP-trigger, több dokumentum beolvasása az útvonal adataiból a SqlQuery (Java) használatával

Az alábbi példa egy Java-függvényt mutat be, amely több dokumentumot is mutat. A függvényt egy Route paramétert ```desc``` használó HTTP-kérelem indítja el, amely megadja a ```description``` mezőben keresendő karakterláncot. A keresési kifejezés a megadott adatbázisból és gyűjteményből származó dokumentumok gyűjteményének lekérésére, ```ToDoItem[]``` valamint az eredményhalmaz és a függvény argumentumként való átadására szolgál.

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

## <a name="input---attributes"></a>Bemenet - attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. Ezek a beállítások és más tulajdonságokat is beállíthat kapcsolatos információkért lásd: [az alábbi konfigurációs szakaszban](#input---configuration).

## <a name="input---configuration"></a>Bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `CosmosDB` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     || Meg kell `cosmosDB`.        |
|**direction**     || Meg kell `in`.         |
|**name**     || A kötési paraméter, amely a dokumentumot, a függvény neve.  |
|**databaseName** |**databaseName** |A dokumentum tartalmazó adatbázis.        |
|**collectionName** |**collectionName** | A gyűjtemény, amely tartalmazza a dokumentum neve. |
|**id**    | **Azonosító** | A lekérdezni kívánt a dokumentum Azonosítóját. Ez a tulajdonság támogatja [kötési kifejezésekben](./functions-bindings-expressions-patterns.md). Ne állítsa mind a **azonosító** és **SQL-lekérdezés** tulajdonságait. Ha nem állít be egy, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Egy Azure Cosmos DB SQL-lekérdezés több dokumentumok használt. A tulajdonság támogatja a futtatókörnyezet kötések, mint ebben a példában: `SELECT * FROM c where c.departmentId = {departmentId}`. Ne állítsa mind a **azonosító** és **SQL-lekérdezés** tulajdonságait. Ha nem állít be egy, a rendszer lekéri a teljes gyűjteményt.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Az Azure Cosmos DB kapcsolati sztringjét tartalmazó alkalmazásbeállítás neve.        |
|**partitionKey**|**PartitionKey**|Megadja a partíciós kulcs értékét a kereséshez. Előfordulhat, hogy tartalmazza a kötési paramétereket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A C# és F# funkciók, ha a függvény kilép sikeres, a bemeneti dokumentum nevű bemeneti keresztül végzett módosítások automatikusan megmaradnak a paramétereket.

JavaScript-függvények, a frissítések nem automatikusan történik függvény kilépéskor. Ehelyett használjon `context.bindings.<documentName>In` és `context.bindings.<documentName>Out` frissítéséhez. Tekintse meg a JavaScript példáját.

## <a name="output"></a>Output

Az Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy egy Azure Cosmos DB-adatbázishoz az SQL API használatával írhat egy új dokumentumot.

## <a name="output---examples"></a>Kimenet – példa

A nyelvspecifikus példa látható:

* [C#](#output---c-examples)
* [C# script (.csx)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

Lásd még a [bemeneti példa](#input---c-examples) használó `DocumentClient`.

[Hagyja ki a kimeneti példa](#output---attributes)

### <a name="output---c-examples"></a>Kimenete – C# példák

Ez a szakasz tartalmazza az alábbi példák:

* Üzenetsor eseményindító írási egy dokumentumot
* Üzenetsor eseményindító írási docs IAsyncCollector használatával

Tekintse meg a példák egy egyszerű `ToDoItem` típusa:

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

[Hagyja ki a kimeneti példa](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Üzenetsor eseményindító írási egy dokumentumot (C#)

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) egy dokumentumot, amely hozzáad egy adatbázishoz, message Queue storage-ból a megadott adatok használatával.

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

[Hagyja ki a kimeneti példa](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Üzenetsor eseményindító írási docs IAsyncCollector (C#) használatával

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) dokumentumok gyűjteménye, amely ad egy adatbázishoz, az adatok JSON üzenetsori üzenet megadott.

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

[Hagyja ki a kimeneti példa](#output---attributes)

### <a name="output---c-script-examples"></a>Kimenet – C#-példaszkriptek

Ez a szakasz tartalmazza az alábbi példák:

* Üzenetsor eseményindító írási egy dokumentumot
* Üzenetsor eseményindító írási docs IAsyncCollector használatával

[Hagyja ki a kimeneti példa](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Üzenetsor eseményindító írási egy dokumentumot (C#-szkript)

Az alábbi példa bemutatja egy Azure Cosmos DB kimeneti kötésének az egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja egy üzenetsor, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény minden egyes rekord a következő formátumban hozza létre az Azure Cosmos DB-dokumentumot:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor eseményindító írási docs IAsyncCollector használatával

Több dokumentumot létrehozni, hogy hozhasson létre `ICollector<T>` vagy `IAsyncCollector<T>` ahol `T` a támogatott típusok egyike.

Ebben a példában egy egyszerű hivatkozik `ToDoItem` típusa:

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

Íme a function.json fájlban:

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

Íme a C#-szkriptkódot:

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

[Hagyja ki a kimeneti példa](#output---attributes)

### <a name="output---javascript-examples"></a>Kimenet – JavaScript-példák

Az alábbi példa bemutatja egy Azure Cosmos DB kimeneti kötésének az egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja egy üzenetsor, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény minden egyes rekord a következő formátumban hozza létre az Azure Cosmos DB-dokumentumot:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

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

[Hagyja ki a kimeneti példa](#output---attributes)

### <a name="output---f-examples"></a>Kimenete – F# példák

Az alábbi példa bemutatja egy Azure Cosmos DB kimeneti kötésének az egy *function.json* fájl és a egy [ F# függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja egy üzenetsor, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény minden egyes rekord a következő formátumban hozza létre az Azure Cosmos DB-dokumentumot:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt van a kötési adatait a *function.json* fájlt:

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
A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a F# kódot:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Ebben a példában van szükség egy `project.json` fájlt, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet-függőségek:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Hozzáadása egy `project.json` fájlt [ F# felügyeleti csomag](functions-reference-fsharp.md#package).

### <a name="output---java-examples"></a>Kimenet – Java-példák

* [Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték használatával](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési érték használatával](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték (Java) használatával

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési értékkel (Java)

Az alábbi példa egy olyan Java-függvényt mutat be, amelynek ```@CosmosDBOutput``` aláírását a és a érték ```String```típusa adja vissza. A függvény által visszaadott JSON-dokumentum automatikusan a megfelelő CosmosDB-gyűjteménybe lesz írva.

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

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding (Java) használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy CosmosDB egy ```OutputBinding<T>``` kimeneti paraméter használatával ír egy dokumentumot. Vegye figyelembe, hogy ebben a beállításban ez az ```outputItem``` a paraméter, amelyet a függvény aláírásával ```@CosmosDBOutput```kell kiegészíteni. A ```OutputBinding<T>``` használata lehetővé teszi a függvény számára, hogy kihasználhassa a dokumentumot a CosmosDB, miközben lehetővé teszi, hogy más értéket adjanak vissza a függvény hívójának, például egy JSON-vagy XML-dokumentumnak.

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

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding (Java) használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy ```OutputBinding<T>``` kimeneti paraméterrel több dokumentumot ír a CosmosDB. Vegye figyelembe, hogy ebben a beállításban ez az ```outputItem``` a paraméter, amelyet a függvény aláírásával ```@CosmosDBOutput```kell kiegészíteni. A kimeneti paraméter ```outputItem``` az ```ToDoItem``` objektumok listáját tartalmazza, mint a sablon paraméterének típusa. A ```OutputBinding<T>``` használata lehetővé teszi, hogy a függvény kihasználhassa a dokumentumokat a CosmosDB, miközben lehetővé teszi, hogy más értéket írjon vissza a függvény hívója számára, például egy JSON-vagy XML-dokumentumot.

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

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@CosmosDBOutput` az Cosmos DBba írandó paraméterek megjegyzéseit.  A Megjegyzés paraméterének a következőnek ```OutputBinding<T>```kell lennie:, ahol a T egy natív Java-típus vagy egy POJO.

### <a name="output---python-examples"></a>Kimenet – Python-példák

Az alábbi példa azt mutatja be, hogyan írhat dokumentumokat egy Azure CosmosDB-adatbázisba egy függvény kimenete.

A kötés definíciója a *function. JSON* fájlban van megadva, ahol a `cosmosDB`Type értéke a *következő* :.

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

Az adatbázisba való íráshoz adjon át egy dokumentum-objektumot `set` az adatbázis-paraméter metódusának.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

## <a name="output---attributes"></a>Kimenet – attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. Ezek a beállítások és más tulajdonságokat is beállíthat kapcsolatos információkért lásd: [kimenete – konfigurációs](#output---configuration). Íme egy `CosmosDB` attribútum példa egy podpis metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Teljes példa: kimenet – C# példa.

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `CosmosDB` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     || Meg kell `cosmosDB`.        |
|**direction**     || Meg kell `out`.         |
|**name**     || A kötési paraméter, amely a dokumentumot, a függvény neve.  |
|**databaseName** | **databaseName**|Az a gyűjtemény, amelyben létrehozza a dokumentumban tartalmazó adatbázis.     |
|**collectionName** |**collectionName**  | A gyűjtemény, amelyben létrehozza a dokumentumban neve. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logikai érték jelzi, hogy a gyűjtemény létrehozásakor a rendszer még nem létezik. Az alapértelmezett érték *hamis* , mert az új gyűjtemény hozható létre fenntartott átviteli sebesség, amelynek költsége van hatással. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) olvasható.  |
|**partitionKey**|**PartitionKey** |Amikor `CreateIfNotExists` értéke true, határozza meg a partíciós kulcs elérési útja a létrehozott gyűjteményre.|
|**collectionThroughput**|**collectionThroughput**| Amikor `CreateIfNotExists` értéke true, határozza meg a [átviteli](../cosmos-db/set-throughput.md) a létrehozott gyűjtemény.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Az Azure Cosmos DB kapcsolati sztringjét tartalmazó alkalmazásbeállítás neve.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

Alapértelmezés szerint ha a függvényben a kimeneti paraméter írni egy dokumentum létrehozása az adatbázisban. Ebben a dokumentumban az automatikusan előállított GUID Azonosítóhoz megegyezik a dokumentum azonosítója. Megadhatja a kimeneti dokumentum a dokumentumazonosító megadásával a `id` a kimeneti paraméter átadott tulajdonság a JSON-objektumban.

> [!Note]
> Létező dokumentum Azonosítóját adja meg, ha azt az új kimeneti dokumentum szerint felülíródik.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Leírások |
|---|---|
| CosmosDB | [CosmosDB-hibakódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz ismerteti a globális konfigurációs beállításoknak a kötéshez verziójában elérhető 2.x. További információ a globális konfigurációs beállításoknak verzióban 2.x verzióját, lásd: [verzióját az Azure Functions – host.json referencia 2.x](functions-host-json.md).

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

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|GatewayMode|Átjáró|A függvény által a Azure Cosmos DB szolgáltatáshoz való csatlakozáskor használt kapcsolati mód. A lehetőségek `Direct` a következők,`Gateway`|
|Protocol|Https|A függvény által a Azure Cosmos DB szolgáltatáshoz való kapcsolódáskor használt kapcsolati protokoll.  A [két mód magyarázata itt](../cosmos-db/performance-tips.md#networking) olvasható|
|leasePrefix|n/a|Az alkalmazás összes függvényében használandó bérlet-előtag.|

## <a name="next-steps"></a>További lépések

* [További tudnivalók a Cosmos DB-vel számítástechnika, kiszolgáló nélküli adatbázis](../cosmos-db/serverless-computing-database.md)
* [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
