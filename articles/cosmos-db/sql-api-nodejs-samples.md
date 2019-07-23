---
title: Node. js-példák a Azure Cosmos DB
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Node.js-példákat.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: dech
ms.openlocfilehash: 640f6a12f3df1bb8ded69c66eda3b307e6bdb74c
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384875"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Azure Cosmos DB Node.js-példák
> [!div class="op_single_selector"]
> * [.NET-példák](sql-api-dotnet-samples.md)
> * [Java-példák](sql-api-java-samples.md)
> * [Aszinkron Java-példák](sql-api-async-java-samples.md)
> * [Node.js-példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure-kódminták katalógusa](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Az [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub-adattárban találhatók olyan mintamegoldások, amelyek CRUD- és egyéb általános műveleteket végeznek Azure Cosmos DB-erőforrásokon. Ez a cikk a következő információkat tartalmazza:

* Az egyes Node.js-példák projektfájljaiban a feladatokra mutató hivatkozások.
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Aktiválhatja a [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)előfizetői előnyeit: A Visual Studio-előfizetése minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A [JavaScript SDK](sql-api-sdk-node.md)-ra is szüksége lesz.
   
   > [!NOTE]
   > Minden minta önálló, magát állítja be, és végül kitakarít maga után. Így a minták egyszerre több hívást intéznek a [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) felé. Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott tároló teljesítményszintjének megfelelően.
   > 
   > 

## <a name="database-examples"></a>Adatbázispéldák
A [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) projekt [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) fájlja a következő feladatok végrehajtását mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata. 

| Tevékenység | API-referencia |
| --- | --- |
| [Adatbázis létrehozása, ha még nem létezik](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Fiók adatbázisainak felsorolása](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |
| [Adatbázis törlése](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |

## <a name="container-examples"></a>Példák tárolókra
A [ContainerManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) projekt [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) fájlja bemutatja, hogyan végezheti el az alábbi feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata. 

| Tevékenység | API-referencia |
| --- | --- |
| [Tároló létrehozása, ha még nem létezik](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Fiók tárolóinak felsorolása](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Gyűjtemény beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |
| [Tároló törlése](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |

## <a name="item-examples"></a>Példák elemekre
Az [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) projekt [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) fájlja bemutatja, hogyan végezheti el az alábbi feladatokat. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](databases-containers-items.md) fogalmi cikkének használata. 

| Tevékenység | API-referencia |
| --- | --- |
| [Elemek létrehozása](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [A tároló összes elemének beolvasása](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Elem beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Az elem olvasása csak akkor, ha az elem megváltozott](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Dokumentumok lekérdezése](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Elem cseréje](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Elem cseréje feltételes ETag-ellenőrzéssel](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Elem törlése](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Indexelési példák
Az [IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) projekt [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) fájlja a következő feladatok végrehajtását mutatja be.  A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez lásd: a [házirendek indexelése](index-policy.md), az [indexelési típusok](index-types.md)és az indexelési [útvonalak](index-paths.md) fogalmi cikkei. 

| Tevékenység | API-referencia |
| --- | --- |
| [Meghatározott dokumentum manuális indexelése](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Meghatározott dokumentum manuális kizárása az indexből](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Szakaszolt indexelés használata tömeges importáláshoz és nagy méretű tárolók beolvasásához](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [Elérési út kizárása az indexből](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Karakterlánc-útvonalon végzett keresés engedélyezése tartományi művelethez](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [Tartományindex létrehozása karakterlánc-útvonalon](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Tároló létrehozása alapértelmezett indexPolicyvel, majd ennek online frissítése](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Kiszolgálóoldali programozási példák
A [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) projekt [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) fájlja a következő feladatok végrehajtását mutatja be. Ha a következő minták futtatása előtt szeretné megtudni Azure Cosmos DB kiszolgálóoldali programozását, tekintse meg a [tárolt eljárások, eseményindítók és a felhasználó által definiált függvények](stored-procedures-triggers-udfs.md) fogalmi cikkét. 

| Tevékenység | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Tárolt eljárás végrehajtása](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

A kiszolgálóoldali programozással kapcsolatos további információkért lásd [: Azure Cosmos db kiszolgálóoldali programozás: Tárolt eljárások, adatbázis-eseményindítók és](stored-procedures-triggers-udfs.md)UDF.

