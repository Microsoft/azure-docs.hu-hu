---
title: Az alkalmazás migrálása a Azure Cosmos DB .NET SDK 3,0 (com. Azure. Cosmos) használatára
description: Megtudhatja, hogyan frissítheti meglévő .NET-alkalmazását a v2 SDK-ból az újabb .NET SDK v3 (com. Azure. Cosmos csomag) for Core (SQL) API-hoz.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 514a6c6daccfe63865ae0b2b9f5bf29c5cbedc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334021"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Az alkalmazás migrálása a Azure Cosmos DB .NET SDK v3 használatára
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Ha szeretne többet megtudni a Azure Cosmos DB .NET SDK v3-ról, tekintse meg a [kibocsátási megjegyzéseket](sql-api-sdk-dotnet-standard.md), a [.net GitHub-tárházat](https://github.com/Azure/azure-cosmos-dotnet-v3), a .net SDK v3 [teljesítménnyel kapcsolatos tippeket](performance-tips-dotnet-sdk-v3-sql.md), valamint a [hibaelhárítási útmutatót](troubleshoot-dot-net-sdk.md).
>

Ebből a cikkből megtudhatja, hogyan frissítheti meglévő .NET-alkalmazásait az újabb Azure Cosmos DB .NET SDK v3 for Core (SQL) API-ra. Azure Cosmos DB .NET SDK v3 megfelel a Microsoft. Azure. Cosmos névtérnek. Ha az alkalmazást a következő Azure Cosmos DB .NET SDK-k bármelyikéről telepíti át, akkor használhatja az ebben a doc-ban megadott információkat:

* Azure Cosmos DB .NET-keretrendszer SDK v2 az SQL API-hoz
* Azure Cosmos DB .NET Core SDK v2 az SQL API-hoz

A cikkben szereplő utasítások a Azure Cosmos DB .NET SDK v3 for Core (SQL) API-hoz tartozó következő külső könyvtárak áttelepítését is segítik:

* .NET-módosítási hírcsatorna-feldolgozó függvénytár 2,0
* .NET tömeges végrehajtó függvénytár 1,1 vagy újabb

## <a name="whats-new-in-the-net-v3-sdk"></a>A .NET v3 SDK újdonságai

A v3 SDK számos használhatósági és teljesítménybeli javítást tartalmaz, többek között a következőket:

* Intuitív programozási modell elnevezése
* .NET Standard 2,0 * *
* Nagyobb teljesítmény a stream API támogatásán keresztül
* Az URI Factory szükségességét lecserélő Fluent-hierarchia
* Beépített támogatás a hírcsatorna-feldolgozó függvénytárának módosításához
* Beépített támogatás a tömeges műveletekhez
* Mockabale API-k egyszerűbb egység-teszteléshez
* Tranzakciós köteg-és blézer-támogatás
* Csatlakoztatható szerializálók
* Nem particionált és autoscale tárolók méretezése

* * Az SDK a .NET Standard 2,0 szabványt célozza meg, amely egyesíti a meglévő Azure Cosmos DB .NET-keretrendszer és a .NET Core SDK-kat egyetlen .NET SDK-ban. A .net SDK-t bármely olyan platformon használhatja, amely megvalósítja a .NET Standard 2,0-et, beleértve a .NET-keretrendszer 4.6.1 + és a .NET Core 2.0 + alkalmazásokat is.

A hálózatkezelés, az újrapróbálkozási logika és az SDK alacsonyabb szintjei nagyrészt változatlanok maradnak.

**A Azure Cosmos DB .NET SDK v3 mostantól nyílt forráskódú.** Üdvözöljük a lekéréses kérelmeket, és a rendszer naplózza a [githubon](https://github.com/Azure/azure-cosmos-dotnet-v3/) a hibákat, és nyomon követi a visszajelzéseket. Minden olyan funkciót felhasználunk, amely javítani fogja a felhasználói élményt.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Miért érdemes áttérni a .NET v3 SDK-ra

A számos használhatósági és teljesítménybeli fejlesztésen túl a legújabb SDK-ban végrehajtott új szolgáltatás-beruházások nem lesznek a régebbi verzióra portolva.

Noha az [2,0 SDK-k támogatásának](sql-api-sdk-dotnet.md)kivonására nincs azonnali terv, az SDK-kat később újabb verziók váltja fel, és az SDK karbantartási módba kerül. A legjobb fejlesztési élmény érdekében javasoljuk, hogy mindig az SDK legújabb támogatott verziójával kezdjen.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>A fő név változása v2 SDK-ról v3 SDK-ra

A következő név módosult a .NET 3,0 SDK-ban, hogy az a Core (SQL) API API-elnevezési konvencióinak megfelelően illeszkedjen:

* `DocumentClient` átnevezve `CosmosClient`
* `Collection` átnevezve `Container`
* `Document` átnevezve `Item`

A rendszer az összes erőforrás-objektumot átnevezi további tulajdonságokkal, amelyek az erőforrás nevét is tartalmazzák az érthetőség kedvéért.

A fő osztály neve a következő:

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Osztályok lecserélve a .NET v3 SDK-ra

A következő osztályok lettek lecserélve az 3,0 SDK-ra:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

A Microsoft.Azure.Documents. A UriFactory osztályt a Fluent terv váltotta fel. A Fluent kialakítás az URL-címeket belsőleg hozza létre, és lehetővé teszi `Container` , hogy a, a és a helyett egyetlen objektumot lehessen átadni `DocumentClient` `DatabaseName` `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Az elem-azonosító generálásának módosításai

Az elem azonosítója már nem töltődik fel automatikusan a .NET v3 SDK-ban. Ezért az elem AZONOSÍTÓjának tartalmaznia kell egy generált azonosítót. Tekintse meg a következő példát:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Az alapértelmezett viselkedés módosult a kapcsolási mód esetében

Az SDK v3 mostantól alapértelmezés szerint a Direct + TCP kapcsolati módokat az előző v2 SDK-val hasonlítja össze, amely Alapesetben az átjáró + HTTPS kapcsolati módokhoz képest. Ez a változás nagyobb teljesítményt és méretezhetőséget biztosít.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>A FeedOptions módosításai (QueryRequestOptions a v 3.0 SDK-ban)

Az `FeedOptions` SDK v2 osztálya már átnevezve lett az `QueryRequestOptions` SDK v3-es verziójában, és a osztályon belül számos tulajdonság módosult a név és/vagy az alapértelmezett érték, vagy teljesen el lett távolítva.  

`FeedOptions.MaxDegreeOfParallelism` a rendszer átnevezte, `QueryRequestOptions.MaxConcurrency` és az alapértelmezett érték, és a társított viselkedés változatlan marad, a párhuzamos lekérdezés-végrehajtás során a műveletek futtatása a párhuzamosságok nélkül történik.

`FeedOptions.EnableCrossPartitionQuery` el lett távolítva, és az SDK 3,0 alapértelmezett viselkedése az, hogy a rendszer a több partíciós lekérdezést is végrehajtja anélkül, hogy a tulajdonságot kifejezetten engedélyezni kellene.

`FeedOptions.PopulateQueryMetrics` Alapértelmezés szerint engedélyezve van a válasz diagnosztika tulajdonságában lévő eredményekkel.

`FeedOptions.RequestContinuation` Mostantól a lekérdezési módszerekre lett előléptetve.

A következő tulajdonságok lettek eltávolítva:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Ügyfél létrehozása

A .NET SDK v3 egy Fluent `CosmosClientBuilder` osztályt biztosít, amely lecseréli az SDK v2 URI Factory szükségességét.

A következő példa egy új `CosmosClientBuilder` erős ConsistencyLevel és az előnyben részesített helyszínek listáját hozza létre:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>A feed Processor API-k módosítása közvetlenül a v3 SDK-ból

A v3 SDK beépített támogatást nyújt az adatváltozások módosítását lehetővé tevő API-k módosításához, így ugyanazt az SDK-t használhatja az alkalmazás létrehozásához és a processzorok megvalósításának módosításához. Korábban egy külön módosítási hírcsatorna-feldolgozó függvénytárat kellett használnia.

További információ: [How to migrál The Change feed Processor Library to the Azure Cosmos db .net v3 SDK](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>A tömeges végrehajtó függvénytár használata közvetlenül a v3 SDK-ból

A v3 SDK beépített támogatást nyújt a tömeges végrehajtó függvénytárhoz, és lehetővé teszi, hogy ugyanazt az SDK-t használja az alkalmazás létrehozásához és a tömeges műveletek végrehajtásához. Korábban egy külön tömeges végrehajtó függvénytárat kellett használnia.

További információ: Hogyan lehet [áttérni a tömeges végrehajtó könyvtárból a Azure Cosmos db .net v3 SDK-ba való tömeges támogatásra](how-to-migrate-from-bulk-executor-library.md)

## <a name="code-snippet-comparisons"></a>Kódrészletek összehasonlítása

A következő kódrészlet az erőforrások .NET v2 és v3 SDK-k közötti létrehozásának különbségeit mutatja be:

## <a name="database-operations"></a>Adatbázis-műveletek

### <a name="create-a-database"></a>Adatbázis létrehozása

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Adatbázis beolvasása azonosító alapján

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Adatbázis törlése

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Tárolók műveletei

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Tároló létrehozása (az autoskálázás és a lejárati idő élettartama

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Tároló tulajdonságainak olvasása

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Tároló törlése

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Elemek és lekérdezési műveletek

### <a name="create-an-item"></a>Elem létrehozása

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Egy tároló összes elemének olvasása

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Lekérdezési elemek

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Elem törlése

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Következő lépések

* [Konzolos alkalmazás létrehozása](sql-api-get-started.md) Azure Cosmos db SQL API-alapú adatkezeléshez a v3 SDK használatával
* További információ [a v3 SDK-val kapcsolatos lehetőségekről](sql-api-dotnet-v3sdk-samples.md)
