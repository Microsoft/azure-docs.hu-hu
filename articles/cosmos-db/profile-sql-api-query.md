---
title: SQL-lekérdezés teljesítményének beolvasása & végrehajtási metrikák
description: Megtudhatja, hogyan kérhet le Azure Cosmos DB kérelmek SQL-lekérdezési végrehajtási metrikáit és profiljának SQL-lekérdezési teljesítményét.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097396"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>SQL-lekérdezés végrehajtási metrikáinak lekérése és a lekérdezési teljesítmény elemzése a .NET SDK használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk bemutatja, hogyan lehet profilt felvenni az SQL-lekérdezés teljesítményére Azure Cosmos DBon. Ez `QueryMetrics` a profilkészítés a .net SDK-ból beolvasott használatával végezhető el, és részletesen itt olvasható. A [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) egy erősen begépelt objektum, amely információkat nyújt a háttérbeli lekérdezés végrehajtásáról. Ezeket a metrikákat a [lekérdezési teljesítmény finomhangolása](./sql-api-query-metrics.md) című cikkben részletesebben dokumentáljuk.

## <a name="set-the-feedoptions-parameter"></a>A FeedOptions paraméter beállítása

A [DocumentClient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) összes túlterhelése egy opcionális [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) paramétert is igénybe veheti. Ez a beállítás lehetővé teszi, hogy a lekérdezés-végrehajtás beállítható és paraméteres legyen. 

Az SQL-lekérdezés végrehajtási metrikáinak összegyűjtéséhez be kell állítania a [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) paramétert [](/dotnet/api/microsoft.azure.documents.client.feedoptions) a FeedOptions `true` . A True értékre állításával `PopulateQueryMetrics` a rendszer a `FeedResponse` megfelelőt fogja tartalmazni `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Lekérdezési metrikák beolvasása a AsDocumentQuery ()
Az alábbi mintakód bemutatja, hogyan kérhet le mérőszámokat a [AsDocumentQuery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) metódus használatakor:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>QueryMetrics összesítése

Az előző szakaszban figyelje meg, hogy a [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) metódusnak több hívása is volt. Minden hívás egy olyan `FeedResponse` objektumot adott vissza, amely a `QueryMetrics` lekérdezés minden folytatásához tartalmaz egy-egy szótárt. Az alábbi példa bemutatja, hogyan összesítheti ezeket a `QueryMetrics` LINQ használatával:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Lekérdezési metrikák csoportosítása partíció-azonosító alapján

A `QueryMetrics` PARTÍCIÓT azonosító alapján csoportosíthatja. A Partition ID szerinti csoportosítással megtekintheti, hogy egy adott partíció okoz-e teljesítménnyel kapcsolatos problémákat, amikor másokhoz hasonlít. Az alábbi példa bemutatja, hogyan csoportosíthatók a `QueryMetrics` LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ on DocumentQuery

A `FeedResponse` következő metódussal is kérheti le a LINQ-lekérdezést `AsDocumentQuery()` :

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Költséges lekérdezések

Az egyes lekérdezések által felhasznált kérelmek mennyiségét rögzítheti a nagy átviteli sebességet használó költséges lekérdezések vagy lekérdezések vizsgálatához. A kérések díját a [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) tulajdonsággal kérheti le `FeedResponse` . Ha többet szeretne megtudni arról, hogyan kérheti le a kérelmek díját a Azure Portal és a különböző SDK-k használatával, tekintse meg [a kérelem egységének megkeresése](find-request-unit-charge.md) című cikket.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Lekérdezés végrehajtási idejének lekérése

Az ügyféloldali lekérdezés végrehajtásához szükséges idő kiszámításakor ügyeljen arra, hogy csak a metódus meghívásának idejét `ExecuteNextAsync` és a kód más részeit tartalmazza. Ezek a hívások segítenek kiszámítani, hogy mennyi ideig tartott a lekérdezés végrehajtása a következő példában látható módon:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Lekérdezések vizsgálata (általában lassú és költséges)

A vizsgálati lekérdezés olyan lekérdezésre hivatkozik, amely nem az index által szolgált, mert számos dokumentum töltődik be az eredményhalmaz visszaküldése előtt.

Az alábbi példa egy vizsgálati lekérdezést mutat be:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

A lekérdezés szűrője a System (felső) rendszerfunkciót használja, amely nem az indexből van kézbesítve. A lekérdezés egy nagy gyűjteményen való végrehajtása a következő lekérdezési metrikákat hozta létre az első folytatáshoz:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Jegyezze fel a lekérdezési metrika kimenetének következő értékeit:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

A lekérdezés 60 951 olyan dokumentumot töltött be, amely összesen 399 998 938 bájtot adott meg. A sok bájt betöltése nagy költséggel vagy a kérések egységének díjszabásával jár. A lekérdezés végrehajtása hosszú időt is igénybe vesz, amely a teljes töltött idő tulajdonsággal együtt egyértelmű:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Azt jelenti, hogy a lekérdezés végrehajtása 4,5 másodperc volt (és ez csak egy folytatás volt).

A példában szereplő lekérdezés optimalizálásához Kerülje a szűrőben lévő felső rész használatát. Ehelyett a dokumentumok létrehozásakor vagy frissítésekor az értékeket az `c.description` összes nagybetűvel kell beszúrni. A lekérdezés ekkor a következőket válik: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Ez a lekérdezés mostantól elérhető az indexből.

A lekérdezési teljesítmény finomhangolásával kapcsolatos további tudnivalókért tekintse meg a [lekérdezési teljesítmény finomhangolása](./sql-api-query-metrics.md) című cikket.

## <a name="references"></a><a id="References"></a>Hivatkozások

- [Azure Cosmos DB SQL-specifikáció](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Következő lépések

- [Lekérdezési teljesítmény hangolása](sql-api-query-metrics.md)
- [Indexelés áttekintése](index-overview.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)