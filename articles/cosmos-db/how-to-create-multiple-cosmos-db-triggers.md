---
title: Több független Azure Functions eseményindító létrehozása a Cosmos DB
description: Megtudhatja, hogyan konfigurálhat több független Azure Functions eseményindítót a Cosmos DBhoz eseményvezérelt architektúrák létrehozásához.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 78fff48a97965f0b80456cd3e56ed1507bc784fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336690"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Több Azure Functions eseményindító létrehozása a Cosmos DBhoz
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azt ismerteti, hogyan konfigurálhat több Azure Functions-eseményindítót a Cosmos DB-hez úgy, hogy párhuzamosan működjenek és egymástól függetlenül reagáljanak a módosításokra.

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Kiszolgáló nélküli eseményvezérelt függvények a Azure Functions eseményindítóval a Cosmos DB és a bérletek tárolójának megosztásakor" border="false":::

## <a name="event-based-architecture-requirements"></a>Eseményvezérelt architektúra-követelmények

A kiszolgáló nélküli architektúrák a [Azure functions](../azure-functions/functions-overview.md)használatával történő létrehozásakor [ajánlott](../azure-functions/functions-best-practices.md#avoid-long-running-functions) olyan kis functions-csoportokat létrehozni, amelyek együttesen működnek a nagyméretű, hosszú ideig futó függvények helyett.

Az eseményvezérelt kiszolgáló nélküli folyamatok a [Cosmos DB Azure functions eseményindítójának](./change-feed-functions.md)használatával történő létrehozásakor a forgatókönyvben fog futni, ahol több dolgot szeretne tenni, amikor egy adott [Azure Cosmos-tárolóban](./account-databases-containers-items.md#azure-cosmos-containers)új esemény van. Ha az aktiválni kívánt műveletek egymástól függetlenek, az ideális megoldás az, hogy **egy Azure functions eseményindítót hozzon létre a kívánt művelethez Cosmos db** egy adott Azure Cosmos-tárolón végzett módosításokat.

## <a name="optimizing-containers-for-multiple-triggers"></a>Tárolók optimalizálása több eseményindítóhoz

Cosmos DB esetén a Azure Functions trigger *követelményeit* figyelembe véve egy második tárolóra van szükség az állapot tárolásához, más néven a *bérletek tárolója*. Ez azt jelenti, hogy minden egyes Azure-függvényhez külön címbérleti tárolóra van szükség?

Itt két lehetőség közül választhat:

* Hozzon létre **egy-egy bérletet egy függvényben**: Ez a módszer további költségekre is fordítható, kivéve, ha [megosztott átviteli sebességű adatbázist](./set-throughput.md#set-throughput-on-a-database)használ. Ne feledje, hogy a tároló szintjének minimális átviteli [sebessége 400,](./request-units.md)a bérletek tároló esetében pedig csak a folyamat előrehaladásának és karbantartásának ellenőrzéséhez használatos.
* **Egyetlen címbérleti tárolóval kell megosztania** az összes funkcióját: Ez a második lehetőség a tárolóban lévő kiépített kérések egységének jobb használatát teszi lehetővé, mivel több Azure functions megoszthatja és használhatja ugyanazt a kiosztott átviteli sebességet.

Ennek a cikknek a célja a második lehetőség megvalósítása.

## <a name="configuring-a-shared-leases-container"></a>Megosztott bérletek tárolójának konfigurálása

A megosztott bérletek tárolójának konfigurálásához az eseményindítók esetében az egyetlen további konfigurációt kell hozzáadnia, `LeaseCollectionPrefix` [](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) ha C# vagy `leaseCollectionPrefix` [attribútumot](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) használ a JavaScript használata esetén. Az attribútum értékének logikai leírónak kell lennie az adott triggernél.

Ha például három eseményindítóval rendelkezik: egy olyan e-maileket küld, amelyek összesítést végeznek egy anyagbeli nézet létrehozásához, és az egyiket, amely egy másik tárhelyre küldi a módosításokat, akkor a későbbi elemzéshez az " `LeaseCollectionPrefix` e-mailek" kifejezést hozzárendelheti a másodikhoz, "az" anyagként ", a" Analytics "pedig a harmadikhoz.

A fontos rész az, hogy mindhárom eseményindító **használhatja ugyanazt a bérletek tároló konfigurációját** (fiók, adatbázis és tároló neve).

A `LeaseCollectionPrefix` C#-attribútumot használó, nagyon egyszerű mintakód a következőképpen fog kinézni:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

A JavaScript esetében a konfigurációt a fájlra is alkalmazhatja a következő `function.json` `leaseCollectionPrefix` attribútummal:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Mindig figyelje a megosztott bérletek tárolóján kiépített kérelmek egységeit. Minden olyan trigger, amely megosztja azt, növeli az átviteli sebesség átlagos felhasználását, ezért előfordulhat, hogy az azt használó Azure Functions számának növelésével növelheti a kiosztott átviteli sebességet.

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Cosmos DB Azure functions trigger](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) teljes konfigurációját
* Keresse meg az összes nyelvhez tartozó [minták kiterjesztett listáját](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) .
* További példákért látogasson el a kiszolgáló nélküli receptek Azure Cosmos DB és Azure Functions [GitHub-tárházba](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) .
