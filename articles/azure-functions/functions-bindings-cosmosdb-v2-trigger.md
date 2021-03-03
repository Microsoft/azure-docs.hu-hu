---
title: Azure Cosmos DB trigger a 2. x és újabb függvényeknél
description: Ismerje meg, hogyan használhatja a Azure Cosmos DB triggert a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6f4e43efeb1882f52bd335d83a3660a94040ab8a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729215"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB trigger Azure Functions 2. x és újabb verziókhoz

A Azure Cosmos DB trigger a [Azure Cosmos db változási csatornát](../cosmos-db/change-feed.md) használja, hogy figyelje a lapkákat és a frissítéseket a partíciók között. A módosítási hírcsatorna közzéteszi a lapkákat és a frissítéseket, a törlést nem.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely akkor jelenik meg, ha a megadott adatbázisban és gyűjteményben lapkák vagy frissítések vannak megadva.

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

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy Cosmos DB trigger kötést mutat be a fájlban lévő *function.js* , és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény naplófájlokat ír, amikor Cosmos DB rekordokat adnak hozzá vagy módosítanak.

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

# <a name="java"></a>[Java](#tab/java)

Ez a függvény akkor kerül meghívásra, ha a megadott adatbázisban és gyűjteményben lapkák vagy frissítések vannak.

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


A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@CosmosDBTrigger` Megjegyzés azon paramétereket, amelyek értéke Cosmos DBból származik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Cosmos DB trigger kötést mutat be a fájlban lévő *function.js* , és egy olyan [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény naplófájlokat ír, amikor Cosmos DB rekordokat adnak hozzá vagy módosítanak.

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa bemutatja, hogyan futtathat egy függvényt adatváltozásként a Cosmos DBban.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

A _run.ps1_ fájlban hozzáférhet a függvényt a paraméter használatával indító dokumentumhoz `$Documents` .

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy Cosmos DB trigger kötést mutat be a fájlban lévő *function.js* és egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény naplófájlokat ír a Cosmos DB rekordok módosításakor.

A *function.js* fájlban található kötési adatfájlok:

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

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [trigger-Configuration](#configuration). `CosmosDBTrigger`Példa a metódus aláírására:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

A teljes példa: [trigger](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](/java/api/overview/azure/functions/runtime)használja a `@CosmosDBInput` Megjegyzések Cosmos DBból beolvasott paraméterek megjegyzéseit.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `CosmosDBTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `cosmosDBTrigger` . |
|**irányba** | n.a. | Értékre kell állítani `in` . Ez a paraméter automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n.a. | A módosítást tartalmazó dokumentumok listáját jelölő függvény kódjában használt változó neve. |
|**connectionStringSetting**|**ConnectionStringSetting** | A figyelt Azure Cosmos DB-fiókhoz való kapcsolódáshoz használt kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. |
|**databaseName**|**DatabaseName**  | Annak a Azure Cosmos DB-adatbázisnak a neve, amelyen a megfigyelt gyűjtemény szerepel. |
|**collectionName** |**CollectionName** | A figyelni kívánt gyűjtemény neve. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Választható Egy olyan Alkalmazásbeállítás neve, amely tartalmazza a kapcsolódási karakterláncot a címbérleti gyűjteményt tároló Azure Cosmos DB fiókhoz. Ha nincs beállítva, a `connectionStringSetting` rendszer az értéket használja. A rendszer automatikusan beállítja ezt a paramétert, ha a kötés a portálon jön létre. A bérletek gyűjteményéhez tartozó kapcsolatok karakterláncának írási engedélyekkel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Választható Annak az adatbázisnak a neve, amely a bérletek tárolásához használt gyűjteményt tárolja. Ha nincs beállítva, a `databaseName` rendszer a beállítás értékét használja. A rendszer automatikusan beállítja ezt a paramétert, ha a kötés a portálon jön létre. |
|**leaseCollectionName** | **LeaseCollectionName** | Választható A bérletek tárolásához használt gyűjtemény neve. Ha nincs beállítva, a rendszer az értéket `leases` használja. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Választható Ha a értékre `true` van állítva, a bérletek gyűjteménye automatikusan létrejön, ha még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Választható Meghatározza a bérletek gyűjteményének létrehozásakor hozzárendelni kívánt kérelmek számát. Ezt a beállítást csak akkor használja a rendszer, ha a `createLeaseCollectionIfNotExists` be van állítva `true` . A rendszer automatikusan beállítja ezt a paramétert, ha a kötést a portál használatával hozza létre.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Választható Ha be van állítva, a rendszer hozzáadja az értéket az ehhez a függvényhez tartozó címbérleti gyűjteményben létrehozott bérletek előtagjaként. Az előtaggal két különálló Azure Functions is megoszthatja ugyanazt a címbérletet különböző előtagok használatával.
|**feedPollDelay**| **FeedPollDelay**| Választható Az az idő (ezredmásodpercben), amely során a rendszer lekérdezi a partíciót az új változásokhoz a hírcsatornán, miután az összes aktuális változást kiüríti. Az alapértelmezett érték 5 000 ezredmásodperc vagy 5 másodperc.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Választható Ha be van állítva, a rendszer ezredmásodpercben meghatározza az intervallumot, hogy kiindítson egy feladatot a számításhoz, ha a partíciókat egyenletesen osztják el az ismert gazdagép-példányok között. Az alapértelmezett érték 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Választható A beállításakor a rendszer ezredmásodpercben meghatározza azt az intervallumot, ameddig a bérlet egy partíciót jelképező bérletre kerül. Ha a bérlet nem kerül megújításra ezen az intervallumon belül, az azt eredményezi, hogy a partíció tulajdonjoga lejár, és egy másik példányra kerül át. Az alapértelmezett érték 60000 (60 másodperc).
|**leaseRenewInterval**| **LeaseRenewInterval**| Választható A beállításakor a rendszer ezredmásodpercben meghatározza az adott példány által jelenleg tárolt partíciók címbérletének megújítási intervallumát. Az alapértelmezett érték 17000 (17 másodperc).
|**checkpointFrequency**| **CheckpointFrequency**| Választható Ha be van állítva, az meghatározza a címbérleti ellenőrzőpontok közötti intervallumot ezredmásodpercben. Az alapértelmezett érték mindig az egyes függvények hívása után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Választható Ha be van állítva, ez a tulajdonság határozza meg a függvényhívás által fogadott elemek maximális számát. Ha a figyelt gyűjteményben lévő műveleteket tárolt eljárásokkal hajtják végre, a [tranzakció hatóköre](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) megmarad a változási csatornán lévő elemek olvasása során. Ennek eredményeképpen a fogadott elemek száma magasabb lehet a megadott értéknél, így az ugyanazon tranzakció által módosított elemek egy atomi köteg részeként lesznek visszaadva.
|**startFromBeginning**| **StartFromBeginning**| Választható Ez a beállítás azt jelzi, hogy a trigger beolvassa a gyűjtemény korábbi változásait, nem pedig az aktuális időpontot. Az elejétől való olvasás csak az indítás első indításakor működik, ahogy az a későbbi futtatásokban is, az ellenőrzőpontok már tárolódnak. Ha úgy állítja be ezt a beállítást, hogy a `true` már létrehozott bérletek ne legyenek hatással. |
|**preferredLocations**| **PreferredLocations**| Választható Meghatározza a földrajzilag replikált adatbázis-fiókok előnyben részesített helyét (régióit) a Azure Cosmos DB szolgáltatásban. Az értékeket vesszővel kell elválasztani. Például: "az USA keleti régiója, az USA déli középső régiója, Észak-Európa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Az triggerhez egy második gyűjteményre van szükség, amelyet a a partíciók _bérletének_ tárolására használ. Mind a figyelt gyűjtemény, mind a bérleteket tartalmazó gyűjteménynek elérhetőnek kell lennie az trigger működéséhez.

>[!IMPORTANT]
> Ha több függvény úgy van konfigurálva, hogy egy Cosmos DB triggert használjon ugyanahhoz a gyűjteményhez, a függvények mindegyikének dedikált címbérleti gyűjteményt kell használnia, vagy eltérő értékre kell beállítania az `LeaseCollectionPrefix` egyes függvényeket. Ellenkező esetben csak az egyik függvény lesz aktiválva. További információ az előtagról: [konfiguráció szakasz](#configuration).

Az trigger nem jelzi, hogy a dokumentumot frissítették vagy beszúrták-e, csak magát a dokumentumot biztosítja. Ha a frissítéseket és a lapkákat különbözőképpen kell kezelnie, ezt megteheti a beszúráshoz vagy a frissítéshez tartozó időbélyegző-mezők megvalósításával.

## <a name="next-steps"></a>Következő lépések

- [Azure Cosmos DB dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB dokumentum módosításainak mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)
