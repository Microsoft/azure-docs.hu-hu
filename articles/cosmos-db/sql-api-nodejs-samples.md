---
title: Node.js példák az Azure Cosmos-adatbázisban tárolt adatkezelésre
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Node.js-példákat.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: e2d8316e0b44593e220a0c9a9358b465ad39c9eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216836"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Node.js-példák az adatok Azure Cosmos DB-ben történő kezeléséhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK-példák](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-példák](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-példák](sql-api-java-sdk-samples.md)
> * [Spring-adatforrások v3 SDK-példák](sql-api-spring-data-sdk-samples.md)
> * [Node.js példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

A rendszer az [Azure-Cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub-tárház részét képező olyan megoldásokat tartalmaz, amelyek a szifiliszi műveleteket és a Azure Cosmos db erőforrásokkal kapcsolatos egyéb gyakori műveleteket végzik. Ez a cikk a következő információkat tartalmazza:

* Az egyes Node.js-példák projektfájljaiban a feladatokra mutató hivatkozások.
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

A [JavaScript SDK](sql-api-sdk-node.md)-ra is szüksége lesz.
   
   > [!NOTE]
   > Minden minta önálló, magát állítja be, és végül kitakarít maga után. Így a minták egyszerre több hívást intéznek a [Containers.create](/javascript/api/%40azure/cosmos/containers) felé. Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott tároló teljesítményszintjének megfelelően.
   > 
   > 

## <a name="database-examples"></a>Adatbázis-példák

A [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) -fájl bemutatja, hogyan hajthatja végre a szifilisz-műveleteket az adatbázison. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata. 

| Feladat | API-referencia |
| --- | --- |
| [Adatbázis létrehozása, ha még nem létezik](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases#createifnotexists-databaserequest--requestoptions-) |
| [Fiók adatbázisainak felsorolása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases#readall-feedoptions-) |
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database#read-requestoptions-) |
| [Adatbázis törlése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database#delete-requestoptions-) |

## <a name="container-examples"></a>Példák tárolókra

A [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) -fájl bemutatja, hogyan hajthatja végre a szifilisz-műveleteket a tárolón. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata. 

| Feladat | API-referencia |
| --- | --- |
| [Tároló létrehozása, ha még nem létezik](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers#createifnotexists-containerrequest--requestoptions-) |
| [Fiók tárolóinak felsorolása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers#readall-feedoptions-) |
| [Tároló definíciójának beolvasása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container#read-requestoptions-) |
| [Tároló törlése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container#delete-requestoptions-) |

## <a name="item-examples"></a>Példák elemekre

A [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) -fájl bemutatja, hogyan hajthatja végre a szifilisz-műveleteket az elemen. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata. 

| Feladat | API-referencia |
| --- | --- |
| [Elemek létrehozása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items#create-t--requestoptions-) |
| [A tároló összes elemének beolvasása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items#readall-feedoptions-) |
| [Elem beolvasása azonosító alapján](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item#read-requestoptions-) |
| [Az elem olvasása csak akkor, ha az elem megváltozott](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Dokumentumok lekérdezése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items) |
| [Elem cseréje](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item) |
| [Elem cseréje feltételes ETag-ellenőrzéssel](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Elem törlése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item) |

## <a name="indexing-examples"></a>Indexelési példák

A [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) -fájl az indexelés kezelését mutatja be. A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez lásd: a [házirendek indexelése](index-policy.md), az [indexelési típusok](index-overview.md#index-types)és az [indexelési útvonalak](index-policy.md#include-exclude-paths) fogalmi cikkei. 

| Feladat | API-referencia |
| --- | --- |
| [Egy adott tétel manuális indexelése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Egy adott elem manuális kizárása az indexből](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Elérési út kizárása az indexből](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy#excludedpaths) |
| [Tartományindex létrehozása sztringútvonalon](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind), [IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy), [Items.query](/javascript/api/%40azure/cosmos/items) |
| [Tároló létrehozása alapértelmezett indexPolicyvel, majd ennek online frissítése](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers)

## <a name="server-side-programming-examples"></a>Kiszolgálóoldali programozási példák

A [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) projekt [index. TS](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) fájlja bemutatja, hogyan hajthatja végre a következő feladatokat. Ha a következő minták futtatása előtt szeretné megtudni Azure Cosmos DB kiszolgálóoldali programozását, tekintse meg a [tárolt eljárások, eseményindítók és a felhasználó által definiált függvények](stored-procedures-triggers-udfs.md) fogalmi cikkét. 

| Feladat | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures) |
| [Tárolt eljárás végrehajtása](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure) |

A kiszolgálóoldali programozásról további információt az [Azure Cosmos DB kiszolgálóoldali programozás ismertetőjének tárolt eljárásokat, adatbázis-triggereket és UDF-eket leíró részében talál](stored-procedures-triggers-udfs.md).
