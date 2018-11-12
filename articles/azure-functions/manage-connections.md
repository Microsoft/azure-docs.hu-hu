---
title: Az Azure Functions kapcsolatok kezelése
description: Megtanulhatja, hogyan teljesítményproblémákat okozhat az Azure Functions ügyfelek statikus kapcsolat használatával.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: glenga
ms.openlocfilehash: eb5c302c807f85f24f53fa1ba32ef4cd7b52274a
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036461"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Az Azure Functions kapcsolatok kezelése

A függvényalkalmazás függvénye erőforrások megosztása, és a megosztott erőforrások közé tartoznak a kapcsolatok &mdash; HTTP kapcsolat, adatbázis-kapcsolatok és az Azure-szolgáltatások például a tárolási kapcsolatok. Számos függvényt egy időben futnak, esetén lehetséges, hogy elfogy a rendelkezésre álló kapcsolatok. Ez a cikk bemutatja, hogyan lehet, kerülje a további kapcsolatok ténylegesen szükségük van, mint a functions-kód.

## <a name="connections-limit"></a>Kapcsolatok számának korlátozása

A rendelkezésre álló kapcsolatok száma korlátozva, részben mivel a függvényalkalmazás a [Azure App Service tesztkörnyezetben](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). A védőfal ró a kód korlátozások egyike egy [cap-kapcsolatok esetén jelenleg 300 száma](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Ha eléri a korlátot, a functions futtatókörnyezete létrehoz egy naplófájlt a következő üzenettel: `Host thresholds exceeded: Connections`.

A korlát túllépése valószínűségét megnő, ha a [méretezési vezérlő hozzáad függvény alkalmazáspéldány](functions-scale.md#how-the-consumption-plan-works) további kérések kezelésére. Minden függvény alkalmazáspéldány futhat számos függvénye, egyszer mindegyike használ, amely a 300 korlát beleszámítanak kapcsolatok.

## <a name="use-static-clients"></a>Statikus ügyfelek használata

Rendelkezés a szükségesnél több kapcsolatot elkerüléséhez ügyfélpéldányok helyett az egyes függvény meghívási újakat hozna létre újból. .NET-ügyfelek, például a [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), és az Azure Storage-ügyfelek kapcsolatok kezelése egyetlen, statikus ügyfél használatakor.

Az alábbiakban néhány irányelv szükséges a következő szolgáltatásspecifikus-ügyfél használatával egy Azure Functions-alkalmazásban:

- **NE** hozzon létre egy új ügyfél minden függvény meghívási együtt.
- **Tegye** hozzon létre egy statikus ügyfél, amely minden függvény meghívási által használható.
- **Érdemes lehet** statikus ügyfél létrehozása a megosztott segítőosztály, ha a különböző függvényeket használja ugyanazt a szolgáltatást.

## <a name="client-code-examples"></a>Ügyfél hitelesítésikód-példák

Ez a szakasz azt ismerteti, létrehozása és használata az ügyfelek a függvénykódot egy ajánlott eljárásai.

### <a name="httpclient-example-c"></a>Példa HttpClient (C#)

Íme egy példa C# kódot, amely létrehozza egy statikus függvényt [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx):

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

A .NET kapcsolatos gyakori kérdések [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) "Kell I kell értékesítésére fejlesztek?" Általánosságban véve tud megszabadulni megvalósító objektumok `IDisposable` befejezése használja őket. A statikus ügyfél nem dobható el, mert nem kész, de amikor befejeződik a függvényt használja. Azt szeretné, hogy a statikus ügyfél élő az alkalmazás időtartamára.

### <a name="http-agent-examples-nodejs"></a>HTTP-ügynök példák (Node.js)

Jobb kapcsolat felügyeleti lehetőségeket biztosít, mivel a natív használata javasolt [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) osztály helyett nem natív módszerek, például a `node-fetch` modul. Kapcsolódási paraméterek vannak konfigurálva, a beállítások használatával a `http.agent` osztály. Lásd: [új ügynök (\[beállítások\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options) HTTP-ügynökkel rendelkező rendelkezésre álló részletes lehetőségeket.

A globális `http.globalAgent` által használt `http.request()` összes ezeket az értékeket saját értékeit. Az ajánlott módszer a kapcsolat korlátai funkciók konfigurálására, hogy globálisan állítson be maximális számot. Az alábbi példa állítja be a függvényalkalmazás sockets maximális száma:

```js
http.globalAgent.maxSockets = 200;
```

 Az alábbi példa egy új HTTP-kérést hoz létre egy egyéni HTTP-ügynök csak az adott kérelmet.

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient példakód (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) egy Azure Cosmos DB-példányhoz csatlakozik. Az Azure Cosmos DB-dokumentáció azt javasolja, hogy Ön [egyedülálló Azure Cosmos DB-ügyfél használata az alkalmazás teljes élettartama](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Az alábbi példa bemutatja egy függvény adatelemzésre, amely egy minta:

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

## <a name="sqlclient-connections"></a>SqlClient kapcsolatok

A függvény kódját a használjuk, hogy a .NET-keretrendszer adatszolgáltatója az SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) relációs SQL-adatbázishoz való csatlakozást. Ez a data-keretrendszerek ADO.NET, például az Entity Framework támaszkodó az alapul szolgáló szolgáltató is. Ellentétben [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) és [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) kapcsolatkészletezést alapértelmezés szerint valósítja meg az ADO.NET-kapcsolatok esetén. Kívül kapcsolatok továbbra is futtathatja, mert az adatbázishoz való csatlakozás kell optimalizálni. További információkért lásd: [SQL Server készletezési (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Bizonyos adatok keretrendszerek, például [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), általában az a kapcsolati karakterláncok beolvasása a **kapcsolati Sztringjei** szakasz egy konfigurációs fájl. Ebben az esetben explicit módon kell hozzáadnia az SQL adatbázis-kapcsolati karakterláncok a **kapcsolati karakterláncok** a függvényalkalmazás-beállításokat, majd a gyűjteményt a [local.settings.json fájljában](functions-run-local.md#local-settings-file) a helyi projektben. Létrehozásakor egy [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) a függvénykódban, tárolja a kapcsolati karakterlánc értékét a **Alkalmazásbeállítások** a kapcsolatokkal.

## <a name="next-steps"></a>További lépések

További információ arról, hogy miért statikus ügyfelek számára javasoltak, lásd: [nem megfelelő példányosítás kizárási minta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

További Azure Functions teljesítményének tippek: [teljesítményének és megbízhatóságának az Azure Functions optimalizálása](functions-best-practices.md).
