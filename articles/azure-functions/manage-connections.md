---
title: Kapcsolatok kezelése az Azure Functionsben
description: Megtudhatja, hogyan kerülheti el a teljesítménnyel kapcsolatos problémákat a Azure Functions a statikus kapcsolódási ügyfelek használatával.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: ec16ce3e7f9793be2a012a029bcca31c9a7ea4cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936702"
---
# <a name="manage-connections-in-azure-functions"></a>Kapcsolatok kezelése az Azure Functionsben

Functions-alkalmazásokban lévő függvények erőforrásainak megosztása. A megosztott erőforrások közé tartoznak a kapcsolatok: HTTP-kapcsolatok, adatbázis-kapcsolatok, valamint olyan szolgáltatásokhoz való kapcsolódások, mint például az Azure Storage. Ha egyszerre több függvény fut, lehetséges, hogy elfogynak az elérhető kapcsolatok. Ez a cikk ismerteti a függvények kódolását, hogy elkerülje a szükségesnél több kapcsolat használatát.

## <a name="connection-limit"></a>Kapcsolati korlát

A rendelkezésre álló kapcsolatok száma részben korlátozott, mert egy Function alkalmazás egy sandbox- [környezetben](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)fut. Az egyik korlátozás, amelyet a homokozó a kódban kiszab, korlátozza a kimenő kapcsolatok számát, amely jelenleg 600 aktív (1 200 összesen) kapcsolatra vonatkozik. Ha eléri ezt a korlátot, a functions futtatókörnyezet a következő üzenetet írja a naplókba: `Host thresholds exceeded: Connections` . További információt a [functions szolgáltatás korlátai](functions-scale.md#service-limits)című témakörben talál.

Ez a korlát/példány. Ha a [méretezési vezérlő](event-driven-scaling.md) felvette a több kérés kezelésére szolgáló Function app-példányokat, minden példányhoz tartozik egy független kapcsolódási korlát. Ez azt jelenti, hogy nincs globális kapcsolati korlát, és az összes aktív példányon több mint 600 aktív kapcsolat lehet.

Hibaelhárítás esetén győződjön meg arról, hogy engedélyezte a Application Insights használatát a Function alkalmazáshoz. Application Insights segítségével megtekintheti a függvények alkalmazásainak (például a végrehajtások) mérőszámait. További információ: [telemetria megtekintése Application Insightsban](analyze-telemetry-data.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statikus ügyfelek

Ha el szeretné kerülni, hogy a szükségesnél több kapcsolat ne legyen használatban, ne hozzon létre újakat az egyes függvények meghívásával. Javasoljuk, hogy az ügyfélkapcsolatokat minden olyan nyelven újra használja, amelyet a függvényének megírásával használhat. Például a .NET-ügyfelek, például a [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true), a [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)és az Azure Storage-ügyfelek kezelhetik a kapcsolatokat, ha egyetlen, statikus ügyfelet használ.

Az alábbiakban néhány útmutatást talál, ha a szolgáltatásra jellemző ügyfelet használ egy Azure Functions alkalmazásban:

- *Ne hozzon* létre új ügyfelet minden függvény meghívásával.
- *Hozzon* létre egyetlen, statikus ügyfelet, amelyet minden funkció használhat.
- Ha a különböző függvények ugyanazt a szolgáltatást használják, *érdemes lehet* egyetlen, statikus ügyfelet létrehozni egy megosztott segítő osztályban.

## <a name="client-code-examples"></a>Példák az ügyfél kódjára

Ez a szakasz az ügyfelek a függvény kódjából történő létrehozásához és használatához ajánlott eljárásokat mutatja be.

### <a name="httpclient-example-c"></a>HttpClient-példa (C#)

Íme egy példa a C#-függvény kódjára, amely létrehoz egy statikus [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) -példányt:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

A .NET-beli [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) kapcsolatos gyakori kérdés, hogy "érdemes megsemmisíteni az ügyfelem?" Általánosságban elmondható, hogy olyan objektumokat távolít el, amelyek a használat közben lépnek érvénybe `IDisposable` . Azonban nem távolítja el a statikus ügyfelet, mert a függvény befejeződése után nem használja. Azt szeretné, hogy a statikus ügyfél az alkalmazás időtartamára éljünk.

### <a name="http-agent-examples-javascript"></a>HTTP-ügynökre vonatkozó példák (JavaScript)

Mivel jobb ügyfélkapcsolat-kezelési lehetőségeket biztosít, a natív osztályt kell használnia a [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) nem natív metódusok helyett, például a `node-fetch` modult. A kapcsolatok paramétereinek beállítása az osztály beállításain keresztül történik `http.agent` . A HTTP-ügynökkel elérhető részletes beállításokért lásd: [új ügynök ( \[ Beállítások \] )](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

A `http.globalAgent` által használt globális osztály `http.request()` mindegyik értéke a megfelelő alapértelmezett értékre van állítva. A függvényekben a kapcsolatok korlátainak konfigurálásához ajánlott módszer a maximális szám megadása globálisan. A következő példa a függvény alkalmazáshoz tartozó szoftvercsatornák maximális számát állítja be:

```js
http.globalAgent.maxSockets = 200;
```

 Az alábbi példa egy új HTTP-kérést hoz létre egyéni HTTP-ügynökkel a kérelemhez:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient-kód – példa (C#)

A [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) egy Azure Cosmos db-példányhoz csatlakozik. Az Azure Cosmos DB dokumentációja azt javasolja, hogy [az alkalmazás élettartama során egy egyedi Azure Cosmos db ügyfelet használjon](../cosmos-db/performance-tips.md#sdk-usage). Az alábbi példa egy függvényt ábrázol egy mintát:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
Ha a v3. x függvényekkel dolgozik, Microsoft.Azure.DocumentDB. Core fájlra mutató hivatkozást kell használnia. Adjon hozzá egy hivatkozást a kódban:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Emellett hozzon létre egy "Function. Proj" nevű fájlt az triggerhez, és adja hozzá az alábbi tartalmat:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a>CosmosClient – példa (JavaScript)
A [CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) egy Azure Cosmos db-példányhoz csatlakozik. Az Azure Cosmos DB dokumentációja azt javasolja, hogy [az alkalmazás élettartama során egy egyedi Azure Cosmos db ügyfelet használjon](../cosmos-db/performance-tips.md#sdk-usage). Az alábbi példa egy függvényt ábrázol egy mintát:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient-kapcsolatok

A függvény kódja a .NET-keretrendszer adatszolgáltatóját használhatja SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) számára, hogy kapcsolatot hozzon egy SQL-alapú kapcsolati adatbázissal. Ez a ADO.NET alapuló adatkeretrendszerek alapjául szolgáló szolgáltató is, például [Entity Framework](/ef/ef6/). A [HttpClient](/dotnet/api/system.net.http.httpclient) és a [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) kapcsolattól eltérően a ADO.NET alapértelmezés szerint implementálja a kapcsolatok készletezését. Mivel azonban továbbra is kifogyhat a kapcsolatok, érdemes optimalizálni a kapcsolatokat az adatbázissal. További információ: [SQL Server kapcsolatok készletezése (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Egyes adatkeretrendszerek (például a Entity Framework) általában a konfigurációs fájl **ConnectionStrings** szakaszából kapják meg a kapcsolatok karakterláncait. Ebben az esetben explicit módon fel kell vennie az SQL Database-kapcsolódási karakterláncokat a Function app-beállítások és a helyi projektben lévő [local.settings.jsfájljának](functions-run-local.md#local-settings-file) a **kapcsolódási karakterláncok** gyűjteményéből. Ha a [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) egy példányát hozza létre, a kapcsolati sztring értékét a többi kapcsolattal együtt kell tárolnia az **Alkalmazásbeállítások** között.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy a statikus ügyfelek miért javasoltak: [helytelen példány-létrehozási minta](/azure/architecture/antipatterns/improper-instantiation/).

További Azure Functions teljesítménnyel kapcsolatos tippeket a [Azure functions teljesítményének és megbízhatóságának optimalizálása](functions-best-practices.md)című témakörben talál.
