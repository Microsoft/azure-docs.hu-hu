---
title: Egy SQL-lekérdezésre vonatkozó kérési egység (RU) díja Azure Cosmos DB
description: Megtudhatja, hogyan keresheti meg az Azure Cosmos-tárolón végrehajtott SQL-lekérdezésekre vonatkozó kérési egység (RU) díját. A Azure Portal, a .NET, a Java, a Python és a Node.js nyelv használatával megkeresheti az RU díját.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: f716245d93727a0447bd1c67924ce7577c70b503
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201298"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>A kérések egységének megkeresése a Azure Cosmos DB SQL API-ban végrehajtott műveletekhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB számos olyan API-t támogat, mint például az SQL, a MongoDB, a Cassandra, a Gremlin és a table. Mindegyik API saját adatbázis-műveletekkel rendelkezik. Ezek a műveletek az egyszerű pont olvasási és írási műveleteiből származnak összetett lekérdezésekre. Minden adatbázis-művelet a művelet bonyolultsága alapján használja a rendszererőforrásokat.

Az összes adatbázis-művelet költségét az Azure Cosmos DB normalizálja, és ezek kérelemegységben (röviden RU) vannak kifejezve. A kérelem díja az összes adatbázis-művelet által felhasznált kérelmek egysége. Azt is megteheti, hogy az RUs olyan teljesítmény pénznemként van kiértékelve, amely a Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához szükséges rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát is elvégzi. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. Azt határozza meg, hogy az adatbázis-művelet írási, olvasási pont vagy lekérdezés-e, a költségeket mindig az RUs méri. További információért lásd a [kérelmek egységeit és annak szempontjait](request-units.md) ismertető cikket.

Ez a cikk bemutatja, hogyan lehet megkeresni a [kérési egység](request-units.md) (ru) felhasználását az Azure Cosmos db SQL API-ban lévő tárolón végrehajtott műveletekhez. Ha más API-t használ, tekintse meg a MongoDB, a [Cassandra API](find-request-unit-charge-cassandra.md), a [Gremlin api](find-request-unit-charge-gremlin.md)és a [Table API](find-request-unit-charge-table.md) cikkekhez tartozó [API](find-request-unit-charge-mongodb.md)-t, ahol megtalálhatja az ru/s díját.

Jelenleg ezt a felhasználást csak a Azure Portal használatával, vagy az SDK-k egyikén keresztül a Azure Cosmos DBtól visszaküldött válasz vizsgálatával mérhető fel. Ha az SQL API-t használja, több lehetősége is van arra, hogy megkeresse a művelet RU-felhasználását egy Azure Cosmos-tárolón.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account) , és adja meg az adatgyűjtést, vagy válasszon olyan meglévő Azure Cosmos-fiókot, amely már tartalmaz információt.

1. Lépjen a **adatkezelő** ablaktáblára, majd válassza ki a használni kívánt tárolót.

1. Válassza az **Új SQL-lekérdezés** lehetőséget.

1. Adjon meg egy érvényes lekérdezést, majd válassza a **lekérdezés végrehajtása** lehetőséget.

1. A **lekérdezési statisztikák** lehetőség kiválasztásával jelenítheti meg a tényleges kérelmek díját a végrehajtott kérelemért.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Képernyőkép az SQL-lekérdezési kérések díjszabásáról a Azure Portal":::

## <a name="use-the-net-sdk"></a>A .NET SDK használata

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

A [.net SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) -ből visszaadott objektumok egy tulajdonságot tesznek elérhetővé `RequestCharge` :

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

A [.net SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) által visszaadott objektumok a következő tulajdonságot teszik elérhetővé `RequestCharge` :

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

További információ: gyors útmutató [: .net-Webalkalmazás létrehozása a Azure Cosmos db SQL API-fiók használatával](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>A Java SDK használata

A [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) által visszaadott objektumok egy metódust tesznek elérhetővé `getRequestCharge()` :

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

További információ: gyors útmutató [: Java-alkalmazás létrehozása Azure Cosmos db SQL API-fiók használatával](create-sql-api-java.md).

## <a name="use-the-nodejs-sdk"></a>Az Node.js SDK használata

A [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) által visszaadott objektumok olyan alobjektumot tesznek elérhetővé `headers` , amely az alapul szolgáló http API által visszaadott összes fejlécet leképezi. A kérelem díja a kulcs alatt érhető el `x-ms-request-charge` :

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

További információ: gyors útmutató [: Node.js-alkalmazás létrehozása egy Azure Cosmos db SQL API-fiók használatával](create-sql-api-nodejs.md). 

## <a name="use-the-python-sdk"></a>A Python SDK használata

A `CosmosClient` [Python SDK](https://pypi.org/project/azure-cosmos/) -ból származó objektum egy olyan `last_response_headers` szótárt tesz elérhetővé, amely leképezi a mögöttes http API által visszaadott összes fejlécet az utolsó művelet végrehajtásához. A kérelem díja a kulcs alatt érhető el `x-ms-request-charge` :

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

További információ: gyors útmutató [: Python-alkalmazás létrehozása Azure Cosmos db SQL API-fiók használatával](create-sql-api-python.md). 

## <a name="next-steps"></a>Következő lépések

Az RU-felhasználás optimalizálásával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](./optimize-cost-reads-writes.md)
* [Kiosztott átviteli sebesség globális skálázása](./request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Adatátviteli kapacitás kiépítése egy tároló számára](how-to-provision-container-throughput.md)
* [A Azure Cosmos DB metrikáinak monitorozása és hibakeresése](use-metrics.md)
