---
title: 'Azure Cosmos DB SQL API: Java SDK v4 példák'
description: A GitHub tartalmaz az Azure Cosmos DB általános feladataihoz, többek között a CRUD-műveletekhez kötődő Java-példákat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 0b0bdd204b2aa322adeb7373911b90e742c71999
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018989"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4 példák
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

> [!IMPORTANT]  
> Ha többet szeretne megtudni a Java SDK v4-ről, tekintse meg a Azure Cosmos DB Java SDK v4 [kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md), a [Maven-tárházat](https://mvnrepository.com/artifact/com.azure/azure-cosmos), a Azure Cosmos db Java SDK v4 [teljesítménnyel kapcsolatos TIPPEKET](performance-tips-java-sdk-v4-sql.md), valamint Azure Cosmos db Java SDK v4 [hibaelhárítási útmutatót](troubleshoot-java-sdk-v4-sql.md) . Ha jelenleg a v4-nél régebbi verziót használ, tekintse meg a következőt: [migrálás Azure Cosmos db Java SDK v4](migrate-java-v4-sdk.md) -re – útmutató a v4-re való frissítéshez.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Az [Azure-Cosmos-Java-SQL-API-Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) GitHub-adattár tartalmazza a legújabb, a szifilisz-műveleteket és a Azure Cosmos db erőforrásokkal kapcsolatos gyakori műveleteket végrehajtó alkalmazásokat. Ez a cikk a következő információkat tartalmazza:

* Az egyes Java-projektfájlokban lévő feladatok hivatkozásai. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

A mintaalkalmazás futtatásához a következőkre lesz szüksége:

* Java fejlesztői készlet 8-as verziója
* Azure Cosmos DB Java SDK v4

Igény szerint a Maven használatával beszerezheti a legújabb Azure Cosmos DB Java SDK v4 bináris fájlokat a projektben való használathoz. A Maven automatikusan hozzáadja az összes szükséges függőséget. Egyéb esetben közvetlenül letöltheti a pom.xml fájlban szereplő függőségeket, és az elérési úthoz adhatja őket.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**A mintaalkalmazások futtatása**

A mintaadattár klónozása:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

A mintákat IDE (Eclipse, IntelliJ vagy VSCODE) vagy a parancssorból is futtathatja a Maven használatával.

Ezeket a környezeti változókat be kell állítani

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

a minták olvasási/írási hozzáférésének biztosítása a fiókhoz.

A minta futtatásához adja meg a fő osztályt 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

ahol a *sample. szinkron. MainClass* lehet
* crudquickstart. Sync. SampleCRUDQuickstart
* crudquickstart. aszinkron. SampleCRUDQuickstartAsync
* indexmanagement. Sync. SampleIndexManagement
* indexmanagement. aszinkron. SampleIndexManagementAsync
* StoredProcedure. Sync. SampleStoredProcedure
* StoredProcedure. aszinkron. SampleStoredProcedureAsync
* changefeed. SampleChangeFeedProcessor *(a Changefeed csak aszinkron mintát tartalmaz, nincs szinkronizálási minta.)* ... stb.

> [!NOTE]
> Minden minta önálló; magát állítja be, és törli önmagát. A minták több hívást adnak ki a létrehozásához `CosmosContainer` . Minden ilyen hívás esetén az előfizetésére 1 órányi használat terhelődik a létrehozott gyűjtemény teljesítményszintjének megfelelően. 
> 
> 

## <a name="database-examples"></a>Adatbázis-példák
Az [adatbázis szifilisz-mintáinak](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) fájlja a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-adatbázisokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata. 

| Feladat | API-referencia |
| --- | --- |
| [Adatbázis létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient. createDatabaseIfNotExists |
| [Adatbázis beolvasása azonosító alapján](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient. getDatabase |
| [Az összes adatbázis olvasása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Adatbázis törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Gyűjtemény-példák
A [gyűjteményi szifilisz mintáinak](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájlja a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata.

| Feladat | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase. Createcontainerifnotexists függvényt |
| [Gyűjtemény konfigurált teljesítményének módosítása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Gyűjtemény beolvasása azonosító alapján](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [Egy adatbázisban lévő összes gyűjtemény olvasása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Gyűjtemény törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Példák az autoscale-gyűjtésre

Ha többet szeretne megtudni a minták futtatása előtt, tekintse meg ezeket az utasításokat, amelyekkel engedélyezheti az autoskálázást a [fiókjában](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) , valamint az [adatbázisaiban és a tárolókban](./provision-throughput-autoscale.md).

Az [autoscale Database szifilisz Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) fájl az alábbi feladatok végrehajtását mutatja be.

| Feladat | API-referencia |
| --- | --- |
| [Adatbázis létrehozása a megadott autoscale Max átviteli sebességgel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient. createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

Az [autoscale Collection szifilisz-minták](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) fájlja a következő feladatok elvégzését mutatja be. 

| Feladat | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása a megadott autoscale Max átviteli sebességgel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase. Createcontainerifnotexists függvényt |
| [Egy gyűjtemény konfigurált autoskálázási maximális átviteli sebességének módosítása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [Gyűjtemény automatikus skálázási átviteli konfigurációjának olvasása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Elemzési tár-gyűjtési példák

Az [analitikai tár gyűjteményének szifilisz-mintái](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) fájlja a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-gyűjteményekről, olvassa el a Azure Cosmos DB szinapszis és az analitikai tároló című témakört.

| Feladat | API-referencia |
| --- | --- |
| [Gyűjtemény létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase. Createcontainerifnotexists függvényt |

## <a name="document-examples"></a>Dokumentumpéldák
A [Document szifilisz Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) fájl a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, olvassa el a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata.

| Feladat | API-referencia |
| --- | --- |
| [Dokumentum létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [Dokumentum olvasása ID (azonosító) alapján](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Dokumentumok lekérdezése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Dokumentum cseréje](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Dokumentum frissítése és beszúrása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Dokumentum törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Dokumentum cseréje feltételes ETag-vizsgálattal](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition. setType<br>AccessCondition.setCondition |
| [Dokumentum olvasása csak a dokumentum módosítása esetén](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition. setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Indexelési példák
A [gyűjteményi szifilisz mintáinak](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) fájlja a következő feladatok elvégzését mutatja be. A következő minták futtatása előtt a Azure Cosmos DB indexelésének megismeréséhez lásd: a [házirendek indexelése](index-policy.md), az [indexelési típusok](index-overview.md#index-types)és az [indexelési útvonalak](index-policy.md#include-exclude-paths) fogalmi cikkei. 

| Feladat | API-referencia |
| --- | --- |
| Dokumentum kizárása az indexből | ExcludedIndex<br>IndexingPolicy |
| Lusta indexelés használata | IndexingPolicy.IndexingMode |
| [Megadott dokumentumok elérési útjának belefoglalása az indexbe](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [A megadott dokumentumok elérési útjának kizárása az indexből](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Összetett index létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Tartományvizsgálati művelet kikényszerítése kivonatolt indexelt elérési útvonalon | FeedOptions.EnableScanInQuery |
| Tartomány-indexek használata a Sztringeken | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Indexátalakítás végrehajtása | - |
| [Térinformatikai index létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

További információ az indexelésről: [Azure Cosmos DB indexelési szabályzat](index-policy.md).

## <a name="query-examples"></a>Lekérdezéspéldák
A [lekérdezési minták](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) fájl a következő feladatokat mutatja be az SQL-lekérdezési nyelvtan használatával. Ha többet szeretne megtudni a Azure Cosmos DB SQL-lekérdezési referenciáról az alábbi minták futtatása előtt, tekintse meg a [Azure Cosmos db SQL-lekérdezési példákat](./sql-query-getting-started.md). 

| Feladat | API-referencia |
| --- | --- |
| [Az összes dokumentum lekérdezése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Egyenlőség lekérdezése == használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Egyenlőtlenség lekérdezése != és NOT használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Lekérdezés tartományi operátorokkal, például a >, <, >=, <= használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Lekérdezés tartományi operátorokkal sztringekben](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Lekérdezés RENDELÉSsel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Lekérdezés eltérő](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Lekérdezés összesítő függvényekkel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Aldokumentumok használata](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Lekérdezés dokumentumon belüli illesztésekkel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Lekérdezés string, Math és Array operátorral](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Lekérdezés paraméteres SQL-lel az SqlQuerySpec használatával](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Lekérdezés explicit lapozással](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Particionált gyűjtemények lekérdezése párhuzamosan](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Lekérdezés az ORDER BY particionált gyűjtemények esetén | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Változáscsatornák példái 
A következő feladatok elvégzéséhez a [hírcsatorna-feldolgozó minta fájl módosítása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) látható. Ha a következő minták futtatása előtt szeretné megismerni a Azure Cosmos DB a hírcsatornát, tekintse meg az [olvasási Azure Cosmos db módosítási hírcsatorna](read-change-feed.md) és a [hírcsatorna-feldolgozó módosítása](change-feed-processor.md)című témakört.

| Feladat | API-referencia |
| --- | --- |
| [Alapszintű módosítási hírcsatorna funkciói](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Változási hírcsatorna olvasása adott időpontból | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Változási hírcsatorna olvasása az elejétől](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Kiszolgálóoldali programozási példák

A [tárolt eljárási minta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) fájl bemutatja, hogyan végezheti el a következő feladatokat. Ha többet szeretne megtudni a Azure Cosmos DB kiszolgálóoldali programozásáról a következő minták futtatása előtt, tekintse meg a [tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md)című témakört.

| Feladat | API-referencia |
| --- | --- |
| [Tárolt eljárás létrehozása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Tárolt eljárás végrehajtása](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.exearanyos |
| [Tárolt eljárás törlése](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Felhasználókezelés példái
A felhasználói felügyeleti minta fájl a következő feladatokat mutatja be:

| Feladat | API-referencia |
| --- | --- |
| Felhasználó létrehozása | - |
| Engedélyek beállítása gyűjteményen vagy dokumentumban | - |
| Felhasználó engedélylistájának lekérése |- |
