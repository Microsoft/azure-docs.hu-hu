---
title: Konzisztencia kezelése a Azure Cosmos DB
description: Megtudhatja, hogyan konfigurálhatja és kezelheti a konzisztenciaszinteket a Azure Cosmos DB a Azure Portal, a .NET SDK, a Java SDK és különböző más SDK-k használatával
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b7cab67b49196a3d50ce5483282971bbb7b9ece1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483265"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB konzisztenciaszintjeinek kezelése
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk bemutatja, hogyan kezelheti a konzisztenciaszinteket a Azure Cosmos DB. Megtudhatja, hogyan konfigurálhatja az alapértelmezett konzisztenciaszintet, bírálhatja felül az alapértelmezett konzisztenciát, hogyan kezelheti manuálisan a munkamenet-jogkivonatokat, és hogyan értheti meg a Valószínűség szerint kötött elavultság (PBS) metrikát.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az [alapértelmezett konzisztenciaszint](consistency-levels.md) az ügyfelek által alapértelmezés szerint használt konzisztenciaszint.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az alapértelmezett konzisztenciaszint megtekintéséhez vagy módosításához jelentkezzen be a Azure Portal. Keresse meg Azure Cosmos-fiókját, és nyissa meg az **Alapértelmezett konzisztencia panelt.** Válassza ki a kívánt konzisztenciaszintet az új alapértelmezett értékként, majd válassza a **Mentés lehetőséget.** A Azure Portal különböző konzisztenciaszinteket is vizualizációt biztosít a zenejegyekkel. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Konzisztencia menü a Azure Portal":::

# <a name="cli"></a>[Parancssori felület](#tab/cli)

Hozzon létre egy Cosmos-fiókot munkamenet-konzisztenciával, majd frissítse az alapértelmezett konzisztenciát.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Hozzon létre egy Cosmos-fiókot munkamenet-konzisztenciával, majd frissítse az alapértelmezett konzisztenciát.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint felülírása

Az ügyfelek felülírhatják a szolgáltatás által beállított alapértelmezett konzisztenciaszintet. A konzisztenciaszint kérésenként beállítható, ami felülírja a fiók szintjén beállított alapértelmezett konzisztenciaszintet.

> [!TIP]
> A konzisztencia csak **a kérés** szintjén lazán ható. A gyengébbről erősebb konzisztenciára való váltás érdekében frissítse a Cosmos-fiók alapértelmezett konzisztenciáját.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Aszinkron](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Java V2-alapú SZOFTVERDK-k

# <a name="async"></a>[Aszinkron](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Munkamenet-jogkivonatok használata

A munkamenetek egyik konzisztenciaszintje Azure Cosmos DB *munkamenet-konzisztencia.* Alapértelmezés szerint ez a Cosmos-fiókokra alkalmazott alapértelmezett szint. A *munkamenet-konzisztencia* használata esetén az ügyfél belsőleg használ egy munkamenet-jogkivonatot minden olvasási/lekérdezési kéréssel, hogy biztosítsa a beállított konzisztenciaszint fenntartását.

A munkamenet-jogkivonatok manuális kezeléséhez kérje le a munkamenet-jogkivonatot a válaszból, és kérésenként állítsa be őket. Ha nem kell manuálisan kezelnie a munkamenet-jogkivonatokat, nem kell használnia ezeket a mintákat. Az SDK automatikusan követi a munkamenet-jogkivonatokat. Ha nem manuálisan adja meg a munkamenet-jogkivonatot, az SDK alapértelmezés szerint a legújabb munkamenet-jogkivonatot használja.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Aszinkron](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Java V2-alapú SZOFTVERDK-k

# <a name="async"></a>[Aszinkron](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>A valószínűség alapján korlátozott frissesség (PBS) metrika monitorozása

Hogyan lesz a 2012-es konzisztencia? Az átlagos esetben a verzióelőzmények és az idő elavultságára vonatkozó korlátokat is tudunk nyújtani? A Valószínűség szerint kötött elavultság [**(PBS)**](https://pbs.cs.berkeley.edu/) metrika megpróbálja számszerűsíteni az elavultság valószínűségét, és metrikaként jeleníti meg. A PBS-metrika megtekintéséhez az Azure Cosmos-fiókját a Azure Portal. Nyissa meg **a Metrikák panelt,** és válassza a **Konzisztencia** lapot. Tekintse meg az Erősen konzisztens olvasások valószínűsége nevű grafikont a számítási feladat alapján **(lásd: PBS).**

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="PBS-gráf a Azure Portal":::

## <a name="next-steps"></a>Következő lépések

További információ az adatütközések kezelésről, vagy a következő fő fogalomra való továbbléphet a Azure Cosmos DB. Lásd az alábbi cikkeket:

* [Konzisztenciaszintek a Azure Cosmos DB](consistency-levels.md)
* [Particionálás és adatelosztás](./partitioning-overview.md)
* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)
* [Particionálás és adatelosztás](partitioning-overview.md)
* [Konzisztencia-aknák modern elosztott adatbázisrendszerek tervezése során](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Magas rendelkezésre állás](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
