---
title: A Graph tömeges végrehajtó .NET-kódtár használata Azure Cosmos DB Gremlin API-val
description: Megtudhatja, hogyan használhatja a tömeges végrehajtó függvénytárat a Graph-beli adatAzure Cosmos DB Gremlin API-tárolóba való tömeges importálásához.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: chrande
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 15e94dac02770bf28aae4cbfc4e337cb68b8be40
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425323"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>A Graph tömeges végrehajtó .NET-kódtár használata tömeges műveletek végrehajtásához Azure Cosmos DB Gremlin API-ban
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Ez az oktatóanyag a Graph-objektumok Azure Cosmos DB Gremlin API-tárolóba történő importálásához és frissítéséhez nyújt útmutatást az Azure CosmosDB tömeges végrehajtó .NET-függvénytárának használatáról. Ez a folyamat a [tömeges végrehajtó függvénytárában](./bulk-executor-overview.md) lévő Graph osztályt használja a csúcspont-és Edge-objektumok programozott módon történő létrehozásához, majd a hálózatra irányuló kérelmek több példányának beszúrásához. Ez a viselkedés a tömeges végrehajtó könyvtárán keresztül állítható be, hogy az adatbázis és a helyi memória erőforrásai optimálisan használhatók legyenek.

Ahelyett, hogy a Gremlin-lekérdezéseket egy adatbázisba küldi, ahol a rendszer kiértékeli a parancsot, majd egyszerre hajt végre egyet, a tömeges végrehajtó függvénytár használatával Ehelyett az objektumok helyi létrehozásához és ellenőrzéséhez szükséges. Az objektumok létrehozását követően a kódtár használatával a gráfobjektumokat szekvenciálisan küldheti az adatbázis-szolgáltatásba. Ezzel a módszerrel az adatfeldolgozási sebesség akár a százszorosára növelhető, ami ideális módszerré teszi a kezdeti adatmigrálásokhoz és az időszakos adatátviteli műveletekhez. További információért látogasson el a [Azure Cosmos db Graph tömeges végrehajtó minta alkalmazás](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)GitHub oldalára.

## <a name="bulk-operations-with-graph-data"></a>Tömeges gráfadatműveletek

A [tömeges végrehajtó függvénytár](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph) tartalmaz egy `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` névteret, amely lehetővé teszi a Graph-objektumok létrehozásához és importálásához szükséges funkciókat. 

Az alábbi folyamat bemutatja, hogyan használható az adatmigrálás a Gremlin API-tárolók esetében:
1. Rekordok lekérése az adatforrásból.
2. Hozzon létre `GremlinVertex`- és `GremlinEdge`-objektumokat a beszerzett rekordokból, és adja hozzá őket egy `IEnumerable`-adatstruktúrához. Az alkalmazásnak ebben a részében kell megvalósítani a kapcsolatok észlelését és hozzáadását végző logikát, arra az esetre, ha az adatforrás nem egy gráfadatbázis.
3. A [Graph BulkImportAsync metódus](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync) használatával szúrhatja be a gráfobjektumokat a gyűjteménybe.

Ezzel a mechanizmussal jobb adatmigrálási hatékonyság érhető el, mint a Gremlin-ügyfél használata esetében. Azért tapasztalható ez a javulás, mert a Gremlin használata esetén az alkalmazás egyszerre egy lekérdezést küld le, amelyet ellenőrizni kell, ki kell értékelni, majd végre kell hajtani az adatok létrehozásához. A tömeges végrehajtó függvénytár kezeli az érvényesítést az alkalmazásban, és minden hálózati kérelem esetében egyszerre több gráf-objektumot fog küldeni.

### <a name="creating-vertices-and-edges"></a>Csúcsok és élek létrehozása

A `GraphBulkExecutor` a `BulkImportAsync` metódust biztosítja, amelyhez a `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element` névtérben definiált `GremlinVertex` vagy `GremlinEdge` objektumok egy `IEnumerable`-listája szükséges. A mintában az éleket és a csúcsokat különválasztottuk két BulkExecutor-importálási feladatba. Lásd az alábbi példát:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

A tömeges végrehajtó könyvtár paramétereivel kapcsolatos további információkért tekintse meg a [BulkImportData Azure Cosmos db témakört](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

A hasznos adatokat példányosítani kell `GremlinVertex`- és `GremlinEdge`-objektumokba. Ezeket az objektumokat így lehet létrehozni:

**Csúcspontok**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Élek**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> A tömeges végrehajtó segédprogram nem keresi automatikusan a meglévő csúcspontokat a szegélyek hozzáadása előtt. Ezt az alkalmazásban kell ellenőrizni a BulkImport feladatok futtatása előtt.

## <a name="sample-application"></a>Mintaalkalmazás

### <a name="prerequisites"></a>Előfeltételek
* Visual Studio 2019 az Azure-fejlesztési számítási feladattal. A [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) ingyenes használatba veheti az első lépéseket.
* Azure-előfizetés. Itt hozhat létre [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Azt is megteheti, hogy az Azure-előfizetés nélkül is létrehozhat egy Cosmos-adatbázis-fiókot, amely [ingyenes kipróbálási Azure Cosmos db biztosít](https://azure.microsoft.com/try/cosmosdb/) .
* Azure Cosmos DB Gremlin API-adatbázis **korlátlan gyűjteménnyel**. Ez az útmutató bemutatja, hogy hogyan foghat neki [az Azure Cosmos DB Gremlin API használatának a .NET környezetben](./create-graph-dotnet.md).
* Git. További információért tekintse meg a [Git letöltési oldalát](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása
Ebben az oktatóanyagban az első lépések lépéseit követjük a GitHubon üzemeltetett [Azure Cosmos db gráf tömeges végrehajtó minta](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) használatával. Ez az alkalmazás egy .NET-megoldást tartalmaz, amely véletlenszerűen állít elő csúcspont és él objektumokat, majd tömeges beszúrásokat hajt végre a megadott gráfadatbázis-fiókba. Az alkalmazás lekéréséhez futtassa az alábbi `git clone` parancsot:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Ez az adattár tartalmazza a GraphBulkExecutor mintát a következő fájlokkal:

Fájl|Description
---|---
`App.config`|Itt vannak megadva az alkalmazás- és adatbázis-specifikus paraméterek. Ezt a fájlt előbb módosítani kell, hogy a céladatbázishoz és -gyűjteményekhez csatlakozzon.
`Program.cs`| Ez a fájl tartalmazza a `DocumentClient` gyűjtemény létrehozása, a tisztítások kezelését és a tömeges végrehajtó kérések küldésének logikáját.
`Util.cs`| Ez a fájl egy segítőosztályt tartalmaz, amely a tesztadatok létrehozásához szükséges mögöttes logikát biztosítja, valamint az adatbázis és a gyűjtemények meglétének ellenőrzését végzi.

Az `App.config` fájlban az alábbi konfigurációs értékek adhatók meg:

Beállítás|Leírás
---|---
`EndPointUrl`|Ez a **.NET SDK-végpont**, amely az Azure Cosmos DB Gremlin API-adatbázisfiók Áttekintés paneljén található. A formátuma a következő: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Ez az Azure Cosmos DB-fiók alatt listázott elsődleges vagy másodlagos kulcs. További információért tekintse meg [az Azure Cosmos DB-adatokhoz való hozzáférés biztonságossá tételével foglalkozó cikket](./secure-access-to-data.md#primary-keys).
`DatabaseName`, `CollectionName`|Ezek a **céladatbázis és -gyűjtemény nevei**. Ha a `ShouldCleanupOnStart` értéke `true`, a rendszer elveti a korábbi neveket, és ezekkel az értékekkel, valamint a `CollectionThroughput` értékkel egy új adatbázist és gyűjteményt hoz létre. Hasonlóképp, ha a `ShouldCleanupOnFinish` értéke `true`, a rendszer az értékek használatával törli az adatbázist, amint az adatok feldolgozása befejeződött. Vegye figyelembe, hogy a célgyűjteménynek **korlátlan gyűjteménynek kell lennie**.
`CollectionThroughput`|A rendszer ezt egy új gyűjtemény létrehozásához használja, ha a `ShouldCleanupOnStart` értéke `true`.
`ShouldCleanupOnStart`|Ez a program futtatása előtt törli az adatbázis-fiókot és -gyűjteményeket, és újakat hoz létre a `DatabaseName`, `CollectionName` és `CollectionThroughput` értékekkel.
`ShouldCleanupOnFinish`|Ez eltávolítja a megadott `DatabaseName` és `CollectionName` értékkel rendelkező adatbázis-fiókot és -gyűjteményeket a program futtatása után.
`NumberOfDocumentsToImport`|Ez határozza meg a mintában létrehozott tesztelési célú csúcsok és élek számát. Ez a szám vonatkozik a csúcsokra és az élekre egyaránt.
`NumberOfBatches`|Ez határozza meg a mintában létrehozott tesztelési célú csúcsok és élek számát. Ez a szám vonatkozik a csúcsokra és az élekre egyaránt.
`CollectionPartitionKey`|Ez a csúcsok és élek tesztelése során lesz majd használva, amikor is a rendszer automatikusan osztja ki ezt a tulajdonságot. A rendszer ezt használja az adatbázis és a gyűjtemények ismételt létrehozásához, ha a `ShouldCleanupOnStart` beállítás értéke `true`.

### <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

1. Adja hozzá az adott adatbázis konfigurációs paramétereit az `App.config` fájlban. A rendszer ez alapján hoz létre egy DocumentClient-példányt. Ha az adatbázis és a tároló még nem lettek létrehozva, automatikusan létrejönnek.
2. Futtassa az alkalmazást. Ez kétszer hívja meg a `BulkImportAsync` metódust, egyszer a csúcsok és egyszer az élek importálása végett. Ha bármelyik objektum beszúrása során hiba lép fel, a rendszer felveszi az objektumot a `.\BadVertices.txt` vagy a `.\BadEdges.txt` fájlba.
3. Ellenőrizze az eredményeket a gráfadatbázis lekérdezésével. Ha a `ShouldCleanupOnFinish` beállítás értéke true (igaz), az adatbázis automatikusan törölve lesz.

## <a name="next-steps"></a>Következő lépések

* További információ a NuGet csomag részleteiről és a tömeges végrehajtó .NET-függvénytár kibocsátási megjegyzéséről: [tömeges végrehajtó SDK – részletek](sql-api-sdk-bulk-executor-dot-net.md). 
* Tekintse át a [teljesítménnyel kapcsolatos tippeket](./bulk-executor-dot-net.md#performance-tips) a tömeges végrehajtó használatának további optimalizálása érdekében.
* Az ebben a névtérben definiált osztályokkal és névterekkel kapcsolatos további információért olvassa el [a BulkExecutor.Graph áttekintését tartalmazó cikket](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph).