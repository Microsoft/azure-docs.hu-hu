---
title: RESTful-végpont létrehozása
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy REST-végpontot az egyéni szolgáltatók számára. Részletesen ismerteti, hogyan kezelheti a kérelmeket és a válaszokat a támogatott REST-alapú HTTP-módszerekhez.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98200209"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>REST-végpont létrehozása egyéni szolgáltatók számára

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatók használatával testreszabhatja az Azure-beli munkafolyamatokat. Ez az oktatóanyag bemutatja, hogyan hozhat létre egyéni szolgáltatói REST-végpontot. Ha nem ismeri az egyéni Azure-szolgáltatókat, tekintse [meg az egyéni erőforrás-szolgáltatók áttekintését](overview.md)ismertető cikket.

> [!NOTE]
> Ez az oktatóanyag az [Egyéni Azure-szolgáltatók Azure functions beállítására szolgáló](./tutorial-custom-providers-function-setup.md)oktatóanyagra épül. Az oktatóanyag néhány lépése csak akkor működik, ha egy Azure Function-alkalmazás be van állítva az egyéni szolgáltatókkal való együttműködésre.

## <a name="work-with-custom-actions-and-custom-resources"></a>Egyéni műveletek és egyéni erőforrások használata

Ebben az oktatóanyagban frissíti a Function alkalmazást, hogy REST-végpontként működjön az egyéni szolgáltatónál. Az Azure-beli erőforrások és műveletek a következő alapvető REST-specifikációk szerint vannak modellezve:

- **Put**: új erőforrás létrehozása
- **Get (példány)**: meglévő erőforrás beolvasása
- **Törlés**: meglévő erőforrás eltávolítása
- **Post**: művelet elindítása
- **Get (gyűjtemény)**: az összes meglévő erőforrás listázása

 Ebben az oktatóanyagban az Azure Table Storage-t használjuk. Azonban bármilyen adatbázis vagy tárolási szolgáltatás működhet.

## <a name="partition-custom-resources-in-storage"></a>Egyéni erőforrások particionálása a Storage-ban

Mivel REST-szolgáltatást hoz létre, a létrehozott erőforrásokat kell tárolnia. Az Azure Table Storage esetében partíciót és sorokat kell létrehoznia az adataihoz. Az egyéni szolgáltatók esetében az adattárolást az egyéni szolgáltatóra kell particionálni. Amikor egy bejövő kérelmet küld az egyéni szolgáltatónak, az egyéni szolgáltató hozzáadja a `x-ms-customproviders-requestpath` fejlécet a kimenő kérelmekhez a végponthoz.

Az alábbi példa egy `x-ms-customproviders-requestpath` egyéni erőforrás fejlécét jeleníti meg:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

A példa `x-ms-customproviders-requestpath` fejléce alapján az alábbi táblázatban látható módon hozhatja létre a *partitionKey* és a *rowKey* paramétereket a tárolóhoz:

Paraméter | Sablon | Leírás
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | A *partitionKey* paraméter határozza meg az adatparticionálás módját. Általában az egyéni szolgáltatói példány particionálja az adathalmazt.
*rowKey* | `{myResourceType}:{myResourceName}` | A *rowKey* paraméter az adat egyedi azonosítóját adja meg. Általában az azonosító az erőforrás neve.

Új osztályt is létre kell hoznia az egyéni erőforrás modellezéséhez. Ebben az oktatóanyagban a következő **CustomResource** osztályt adja hozzá a Function alkalmazáshoz:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
A **CustomResource** egy egyszerű, általános osztály, amely a bemeneti adatokat is elfogadja. Ez a **TableEntity** alapul, amely az adattárolásra szolgál. A **CustomResource** osztály a következő két tulajdonságot örökli: **TableEntity**: **partitionKey** és **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Egyéni szolgáltatói REST-módszerek támogatása

> [!NOTE]
> Ha nem másolja a kódot közvetlenül ebből az oktatóanyagból, a válasz tartalmának érvényes JSON-nek kell lennie, amely beállítja a fejlécet a következőre: `Content-Type` `application/json` .

Most, hogy beállította az adatparticionálást, hozzon létre egy alapszintű szifilisz-és trigger-metódust az egyéni erőforrásokhoz és az egyéni műveletekhez. Mivel az egyéni szolgáltatók proxyként működnek, a REST-végpontnak a kérést és a választ kell modellezni és kezelni. A következő kódrészletek bemutatják, hogyan kezelheti az alapvető REST-műveleteket.

### <a name="trigger-a-custom-action"></a>Egyéni művelet elindítása

Egyéni szolgáltatók esetében az egyéni művelet a POST kéréseken keresztül aktiválódik. Egy egyéni művelet opcionálisan elfogadhat egy olyan kérés törzsét, amely bemeneti paramétereket tartalmaz. A művelet ezután egy választ ad vissza, amely jelzi a művelet eredményét, és azt, hogy sikeres vagy sikertelen volt-e.

Adja hozzá a következő **TriggerCustomAction** metódust a Function alkalmazáshoz:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

A **TriggerCustomAction** metódus fogad egy bejövő kérelmet, és egyszerűen visszaismétli a választ egy állapotkód alapján.

### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

Egyéni szolgáltatók esetében egyéni erőforrás jön létre PUT kérések használatával. Az egyéni szolgáltató egy JSON-kérés törzsét fogadja el, amely az egyéni erőforráshoz tartozó tulajdonságokat tartalmazza. Az Azure-beli erőforrások REST-modellt követnek. Ugyanezt a kérési URL-címet használhatja erőforrás létrehozására, lekérésére vagy törlésére.

Adja hozzá a következő **CreateCustomResource** metódust új erőforrások létrehozásához:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

A **CreateCustomResource** metódus frissíti a bejövő kérelmet, hogy tartalmazza az Azure-specifikus mezők **azonosítóját**, **nevét** és **típusát**. Ezek a mezők az Azure-szolgáltatások által használt legfelső szintű tulajdonságok. Lehetővé teszik, hogy az egyéni szolgáltató együttműködik más szolgáltatásokkal, például Azure Policyokkal, Azure Resource Manager sablonokkal és az Azure-tevékenység naplóval.

Tulajdonság | Példa | Leírás
---|---|---
**név** | {myCustomResourceName} | Az egyéni erőforrás neve
**típusa** | Microsoft. CustomProviders/resourceProviders/{resourceTypeName} | Az erőforrás típusú névtér
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>szolgáltatók/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Az erőforrás-azonosító

A tulajdonságok hozzáadása mellett a JSON-dokumentumot is mentette az Azure Table Storage-ba.

### <a name="retrieve-a-custom-resource"></a>Egyéni erőforrás lekérése

Egyéni szolgáltatók esetében az egyéni erőforrás lekérése a GET kérelmek használatával történik. Egy egyéni szolgáltató *nem* fogad el JSON-kérés törzsét. A GET kérelmek esetében a végpont a `x-ms-customproviders-requestpath` fejlécet használja a már létrehozott erőforrás visszaküldéséhez.

Adja hozzá a következő **RetrieveCustomResource** metódust a meglévő erőforrások lekéréséhez:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Az Azure-ban az erőforrások REST-modellt követnek. Az erőforrást létrehozó kérelem URL-címe szintén visszaadja az erőforrást, ha a GET-kérést elvégezték.

### <a name="remove-a-custom-resource"></a>Egyéni erőforrás eltávolítása

Egyéni szolgáltatók esetén a rendszer eltávolítja az egyéni erőforrásokat a TÖRLÉSi kérelmek használatával. Egy egyéni szolgáltató *nem* fogad el JSON-kérés törzsét. TÖRLÉSi kérelem esetén a végpont a `x-ms-customproviders-requestpath` fejléc használatával törli a már létrehozott erőforrást.

Adja hozzá a következő **RemoveCustomResource** metódust a meglévő erőforrások eltávolításához:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Az Azure-ban az erőforrások REST-modellt követnek. Az erőforrást létrehozó kérelem URL-címe is törli az erőforrást, ha TÖRLÉSi kérelmet végeznek.

### <a name="list-all-custom-resources"></a>Az összes egyéni erőforrás listázása

Az egyéni szolgáltatók esetében a gyűjtemény GET kérések használatával enumerálhatja a meglévő egyéni erőforrások listáját. Egy egyéni szolgáltató *nem* fogad el JSON-kérés törzsét. A GET-kérelmek gyűjteménye esetében a végpont a `x-ms-customproviders-requestpath` már létrehozott erőforrások enumerálásához a fejlécet használja.

Adja hozzá a következő **EnumerateAllCustomResources** metódust a meglévő erőforrások számbavételéhez:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> A RowKey QueryComparisons. GreaterThan és QueryComparisons. LessThan az Azure Table Storage szintaxisa a karakterláncok "startswith" lekérdezésének végrehajtásához.

Az összes meglévő erőforrás listázásához olyan Azure Table Storage-lekérdezést kell létrehoznia, amely biztosítja az erőforrások létezését az egyéni szolgáltatói partíció alatt. A lekérdezés ezután ellenőrzi, hogy a sor kulcsa ugyanazzal az értékkel kezdődik-e `{myResourceType}` .

## <a name="integrate-restful-operations"></a>REST-műveletek integrálása

Miután az összes REST-metódus hozzá lett adva a Function alkalmazáshoz, frissítse a fő **futtatási** módszert, amely meghívja a függvényeket a különböző REST-kérelmek kezelésére:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

A frissített **futtatási** módszer mostantól tartalmazza az Azure Table Storage-hoz hozzáadott *tableStorage* bemeneti kötést. A metódus első része beolvassa a `x-ms-customproviders-requestpath` fejlécet, és a `Microsoft.Azure.Management.ResourceManager.Fluent` könyvtár használatával elemzi az értéket erőforrás-azonosítóként. A `x-ms-customproviders-requestpath` fejlécet az egyéni szolgáltató küldi el, és megadja a bejövő kérelem elérési útját.

Az elemzett erőforrás-azonosító használatával létrehozhatja a **partitionKey** és a **rowKey** értékeket az adatok kereséséhez, illetve az egyéni erőforrások tárolásához.

A módszerek és osztályok hozzáadása után frissítenie kell a **using** metódusokat a Function alkalmazáshoz. Adja hozzá a következő kódot a C#-fájl elejéhez:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Ha az oktatóanyag bármely pontján elvész, megtalálhatja a teljes kód mintát az [egyéni szolgáltató C# Rest-végpont-referenciájában](./reference-custom-providers-csharp-endpoint.md). A Function alkalmazás befejezése után mentse a Function app URL-címét. A függvény alkalmazás a későbbi oktatóanyagokban aktiválható.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy REST-végpontot készített egy Azure-beli egyéni szolgáltatói végponttal való együttműködéshez. Ha meg szeretné tudni, hogyan hozhat létre egyéni szolgáltatót, ugorjon a cikk [oktatóanyag: egyéni szolgáltató létrehozása](./tutorial-custom-providers-create.md)című témakörre.
