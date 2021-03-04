---
title: C# függvények fejlesztése Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre és tehet közzé olyan kódot a C# használatával, amely a Azure Functions futtatókörnyezettel fut folyamatban.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: e29b250b25bdafb2b3af26f5669f2ae5ed485457
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041195"
---
# <a name="develop-c-functions-using-azure-functions"></a>C# függvények fejlesztése Azure Functions használatával

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ez a cikk bemutatja, hogyan fejlesztheti Azure Functions a C# használatával a .NET-osztály könyvtáraiban.

>[!IMPORTANT]
>Ez a cikk olyan .NET Class Library-függvényeket támogat, amelyek folyamaton belüli futtatásával futnak a futtatókörnyezetben. A functions a .NET 5. x verzióját is támogatja a C#-függvények folyamaton kívüli futtatásával, valamint a futtatókörnyezettől való elkülönítéssel. További információ: [.net elkülönített folyamat függvények](dotnet-isolated-process-guide.md).

C#-fejlesztőként a következő cikkek egyike is érdekli:

| Első lépések | Alapelvek| Interaktív tanulás/minták |
| -- | -- | -- | 
| <ul><li>[A Visual Studio használata](functions-create-your-first-function-visual-studio.md)</li><li>[A Visual Studio Code használata](create-first-function-vs-code-csharp.md)</li><li>[Parancssori eszközök használata](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[Üzemeltetési lehetőségek](functions-scale.md)</li><li>[Teljesítménnyel &nbsp; kapcsolatos megfontolások](functions-best-practices.md)</li><li>[A Visual Studio fejlesztése](functions-develop-vs.md)</li><li>[Függőséginjektálás](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[Kiszolgáló nélküli alkalmazás létrehozása](/learn/paths/create-serverless-applications/)</li><li>[C#-minták](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Functions támogatja a C# és C# programozási nyelveket. Ha a C# használatával kapcsolatos útmutatást keres [a Azure Portalban](functions-create-function-app-portal.md), tekintse meg a [c# parancsfájl (. CSX) fejlesztői referenciáját](functions-reference-csharp.md).

## <a name="supported-versions"></a>Támogatott verziók

A functions futtatókörnyezet verziói a .NET adott verziójával működnek. Az alábbi táblázat a .NET Core és a .NET-keretrendszer és a .NET Core legmagasabb szintjét mutatja be, amelyek a projektben a függvények adott verziójával használhatók. 

| Functions futtatókörnyezet verziója | .NET-es verzió max. verziója |
| ---- | ---- |
| Függvények 3. x | .NET Core 3.1<br/>.NET 5,0<sup>*</sup> |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET-keretrendszer 4,7 |

<sup>*</sup>[Folyamaton kívül](dotnet-isolated-process-guide.md)kell futnia.

További információ: [Azure functions Runtime-verziók áttekintése](functions-versions.md)

## <a name="functions-class-library-project"></a>Functions Class Library-projekt

A Visual Studióban a **Azure functions** Project sablon egy C# szintű függvénytár-projektet hoz létre, amely a következő fájlokat tartalmazza:

* [ A](functions-host-json.md) helyi vagy az Azure-ban futtatott, a projekt összes funkcióját érintő konfigurációs beállításokathost.js.
* [ Alocal.settings.jsa rendszer a](functions-run-local.md#local-settings-file) helyileg futtatott alkalmazások beállításait és a kapcsolódási karakterláncokat tárolja. Ez a fájl titkokat tartalmaz, és nincs közzétéve az Azure-beli Function alkalmazásban. Ehelyett [adja hozzá az alkalmazás beállításait a Function alkalmazáshoz](functions-develop-vs.md#function-app-settings).

A projekt létrehozásakor a következő példához hasonló mappastruktúrát jön létre a Build kimeneti könyvtárában:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Ez a könyvtár az Azure-beli Function alkalmazás üzembe helyezése. A functions futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési bővítmények a [projekthez NuGet csomagokként lesznek hozzáadva](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> A létrehozási folyamat létrehoz egy *function.js* az egyes függvények fájljában. Ezt *afunction.jsa* fájlon nem közvetlenül kell szerkeszteni. Nem módosíthatja a kötési konfigurációt, vagy letilthatja a függvényt a fájl szerkesztésével. A függvények letiltásával kapcsolatos további információkért lásd a [függvények letiltását](disable-function.md)ismertető témakört.


## <a name="methods-recognized-as-functions"></a>Függvényekként felismert metódusok

Egy osztály-függvénytárban a függvény egy statikus metódus a `FunctionName` és egy trigger attribútummal, az alábbi példában látható módon:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Az `FunctionName` attribútum a metódust függvény belépési pontként jelöli. A névnek egyedinek kell lennie a projekten belül, betűvel kell kezdődnie, és csak betűket, számokat, `_` és, `-` legfeljebb 127 karaktert tartalmazhat. A Project templates gyakran létrehoz egy nevű metódust `Run` , de a metódus neve lehet bármely érvényes C#-metódus neve.

Az trigger attribútum meghatározza az trigger típusát, és a bemeneti adatokat egy metódus paraméteréhez köti. A példa függvényt egy üzenetsor-üzenet indítja el, és a várólista-üzenet a paraméterben megadott metódusnak lesz átadva `myQueueItem` .

## <a name="method-signature-parameters"></a>Metódus-aláírás paraméterei

A metódus aláírása nem az trigger attribútummal használt paramétereket tartalmazhat. Íme néhány további paraméter, amelyet felvehet:

* A [bemeneti és kimeneti kötések](functions-triggers-bindings.md) úgy vannak megjelölve, hogy az attribútumokkal díszítsék őket.  
* A `ILogger` `TraceWriter` (z) vagy ([1. x verziójú](functions-versions.md#creating-1x-apps)) paraméter a [naplózáshoz](#logging).
* Egy `CancellationToken` paraméter a [kecses leállításhoz](#cancellation-tokens).
* A [kötési kifejezések](./functions-bindings-expressions-patterns.md) paraméterei trigger-metaadatok beszerzéséhez.

A függvények aláírásában szereplő paraméterek sorrendje nem számít. Például az aktiválási paramétereket más kötések előtt vagy után is elhelyezheti, és a naplózó paramétert trigger vagy kötési paraméterek előtt vagy után is elhelyezheti.

### <a name="output-bindings"></a>Kimeneti kötések

A függvények a kimeneti paraméterek használatával nulla vagy egy kimeneti kötést is meghatározhatnak. 

A következő példa egy nevű kimeneti várólista-kötés hozzáadásával módosítja az előzőt `myQueueItemCopy` . A függvény beírja az üzenet tartalmát, amely egy másik várólistában lévő új üzenetre indítja a függvényt.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

A kimeneti kötésekhez rendelt értékek a függvény bezárásakor íródnak. A függvényekben egynél több kimeneti kötést is használhat, ha egyszerűen több kimeneti paraméterhez rendeli az értékeket. 

A kötési útmutató cikkei (például a[tárolási várólisták](functions-bindings-storage-queue.md)) megmutatják, hogy milyen típusú paramétereket használhat trigger-, bemeneti vagy kimeneti kötési attribútumokkal.

### <a name="binding-expressions-example"></a>Példa kötési kifejezésekre

A következő kód beolvassa az Alkalmazásbeállítások által figyelt várólista nevét, és az üzenetsor-létrehozási időt a paraméterben kapja meg `insertionTime` .

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatikusan generált function.jsbekapcsolva

A létrehozási folyamat létrehoz egy *function.js* fájlt a Build mappában található Function mappában. Amint azt korábban említettük, a fájlt nem közvetlenül kell szerkeszteni. Nem módosíthatja a kötési konfigurációt, vagy letilthatja a függvényt a fájl szerkesztésével. 

Ennek a fájlnak a célja, hogy információt szolgáltasson a méretezési vezérlő számára [a használati tervre vonatkozó döntések skálázásához](event-driven-scaling.md). Emiatt a fájl csak trigger-információkkal, nem bemeneti vagy kimeneti kötésekkel rendelkezik.

A fájlhoz generált *function.js* tartalmaz egy `configurationSource` tulajdonságot, amely azt jelzi, hogy a futtatókörnyezet .net-attribútumokat használ a kötésekhez ahelyett, hogy *function.jsa* konfiguráción. Bemutatunk egy példát:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft. NET. SDK. functions

A NuGet-csomag [Microsoft \. net \. SDK \. függvények](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)a fájl-létrehozási *function.js* . 

Ugyanez a csomag a functions futtatókörnyezet 1. x és 2. x verziójára is használható. A cél-keretrendszer az 1. x projekt megkülönböztetése egy 2. x projektből. Az alábbiakban láthatók a *. csproj* -fájlok azon részei, amelyek különböző cél-keretrendszereket és azonos csomagot mutatnak be `Sdk` :

# <a name="v2x"></a>[v2. x +](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


A `Sdk` csomagok függőségei közé az eseményindítók és a kötések tartoznak. Egy 1. x projekt egy 1. x eseményindítóra és kötésre hivatkozik, mivel ezek az eseményindítók és kötések a .NET-keretrendszert célozzák meg, a 2. x eseményindítók és kötések pedig a .NET Core-t célozzák meg.

A `Sdk` csomag [Newtonsoft.Jstól](https://www.nuget.org/packages/Newtonsoft.Json)függ, és közvetetten a [WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage)szolgáltatásban is. Ezek a függőségek gondoskodnak arról, hogy a projekt azokat a csomagokat használja, amelyek a projekt által megcélzott functions futtatókörnyezet-verzióval működnek. Például `Newtonsoft.Json` a .NET-keretrendszer 4.6.1-es verziójának 11-es verziója, de a .NET-keretrendszer 4.6.1-re irányuló functions futtatókörnyezet csak a `Newtonsoft.Json` 9.0.1 kompatibilis. Így a projekt kódjának is a 9.0.1 használatát kell használnia `Newtonsoft.Json` .

A forráskódja `Microsoft.NET.Sdk.Functions` elérhető a GitHub-adattár [Azure \- functions \- vs \- Build \- SDK](https://github.com/Azure/azure-functions-vs-build-sdk)-ban.

## <a name="runtime-version"></a>Futtatókörnyezet verziója

A Visual Studio a [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) használatával futtatja a functions-projekteket. A Core Tools a functions futtatókörnyezet parancssori felülete.

Ha a NPM használatával telepíti az alapvető eszközöket, amelyek nem érintik a Visual Studio által használt alapvető eszközök verzióját. A functions Runtime 1. x verziójában a Visual Studio a *%USERPROFILE%\AppData\Local\Azure.functions.CLI* alapeszközök verzióját tárolja, és az ott tárolt legújabb verziót használja. A 2. x függvények esetében az alapvető eszközök a **Azure functions és a web Jobs Tools** bővítmény részét képezik. Az 1. x és a 2. x esetében egyaránt megtekintheti, hogy a konzol kimenetében milyen verzió van használatban a functions projekt futtatásakor:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

A Function alkalmazást [ReadyToRun bináris](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images)fájlként is lefordíthatja. A ReadyToRun az előre megadott fordítási forma, amely javíthatja az indítási teljesítményt, így csökkentheti a [fogyasztási tervben](consumption-plan.md)való futáskor felhasználható [rendszerindítási](event-driven-scaling.md#cold-start) hatásokat.

A ReadyToRun a .NET 3,0-es verzióban érhető el, és [a Azure functions futtatókörnyezet 3,0](functions-versions.md)-es verzióját igényli.

A projekt ReadyToRun való fordításához frissítse a projektfájlt a `<PublishReadyToRun>` és elemek hozzáadásával `<RuntimeIdentifier>` . A következő konfiguráció a Windows 32 bites Function alkalmazásban való közzétételre szolgál.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> A ReadyToRun jelenleg nem támogatja a fordítást. Az alkalmazást az üzembe helyezési célként megadott platformon kell létrehoznia. Ügyeljen arra is, hogy a Function alkalmazásban konfigurált "bitszáma" is figyeljen. Ha például az Azure-beli Function-alkalmazás Windows 64 bites, akkor az alkalmazást Windows rendszeren kell lefordítani futtatókörnyezet- `win-x64` [azonosítóként](/dotnet/core/rid-catalog).

Az alkalmazást a parancssorból is létrehozhatja a ReadyToRun. További információ: `-p:PublishReadyToRun=true` [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>A kötések támogatott típusai

Minden kötés saját támogatott típusokkal rendelkezik; például egy blob trigger attribútumot lehet alkalmazni egy karakterlánc-paraméterre, egy POCO paraméterre, egy `CloudBlockBlob` paraméterre vagy számos más támogatott típusra. A [blob-kötések kötési útmutatója](functions-bindings-storage-blob-trigger.md#usage) felsorolja az összes támogatott paraméter-típust. További információ: [triggerek és kötések](functions-triggers-bindings.md) , valamint az [egyes kötési típusok kötési dokumentációja](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Kötés a metódus visszatérési értékéhez

A metódus visszatérési értékét egy kimeneti kötéshez is használhatja, ha az attribútumot a metódus visszatérési értékére alkalmazza. Példákat az [Eseményindítók és kötések](./functions-bindings-return-value.md)című témakörben talál. 

Csak akkor használja a visszaadott értéket, ha egy sikeres függvény végrehajtása mindig visszatérési értéket ad eredményül a kimeneti kötésnek. Ellenkező esetben használja `ICollector` `IAsyncCollector` a vagy a beállítást, ahogy az a következő szakaszban látható.

## <a name="writing-multiple-output-values"></a>Több kimeneti érték írása

Ha több értéket szeretne írni egy kimeneti kötésbe, vagy ha egy sikeres függvény meghívása nem eredményez semmit a kimeneti kötéshez, használja a vagy a [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) típust. Ezek a típusok olyan írásvédett gyűjtemények, amelyek a metódus befejeződése után a kimeneti kötésbe íródnak.

Ez a példa több üzenetsor-üzenetet ír ugyanabba a várólistába a következő használatával `ICollector` :

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="async"></a>Aszinkron

A függvény [aszinkron](/dotnet/csharp/programming-guide/concepts/async/)működéséhez használja a `async` kulcsszót, és egy `Task` objektumot ad vissza.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

`out`Aszinkron függvényekben nem használhatók paraméterek. Kimeneti kötések esetén használja helyette a [függvény visszatérési értékét](#binding-to-method-return-value) vagy egy [gyűjtő objektumot](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Visszavonási tokenek

A függvények elfogadják a [CancellationToken](/dotnet/api/system.threading.cancellationtoken) paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a kódot, ha a függvény hamarosan leáll. Ezzel az értesítéssel meggyőződhet arról, hogy a függvény váratlanul leáll olyan módon, amely inkonzisztens állapotban hagyja az adatvesztést.

Az alábbi példa bemutatja, hogyan ellenőrizhető a közelgő függvények leállítása.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="logging"></a>Naplózás

A függvény kódjában a kimenetet a Application Insights nyomkövetésként megjelenő naplókba írhatja. A naplókba való írás ajánlott módja, ha egy [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)típusú paramétert tartalmaz, amely általában a neve `log` . A függvények futtatásához használt futtatókörnyezet 1. x verziója `TraceWriter` , amely Application Insights is ír, de nem támogatja a strukturált naplózást. Ne használja a `Console.Write` naplók írásához, mivel ezeket az adatApplication Insights nem rögzíti. 

### <a name="ilogger"></a>ILogger

A függvény definíciójában adjon meg egy [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) paramétert, amely támogatja a [strukturált naplózást](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Ha egy `ILogger` objektummal rendelkezik, a `Log<level>` [ILogger bővítményi metódusokat hívhat meg a](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) naplók létrehozásához. A következő kód a `Information` naplókat írja be a kategóriába `Function.<YOUR_FUNCTION_NAME>.User.` :

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

További információ a függvények megvalósításáról `ILogger` : [telemetria-adatok gyűjtése](functions-monitoring.md#collecting-telemetry-data). Az előre meghatározott kategóriák, amelyek `Function` feltételezik, hogy `ILogger` példányt használ. Ha úgy dönt, hogy ehelyett `ILogger<T>` a-t használja, a kategória neve Ehelyett a következő alapján lehet: `T` .  

### <a name="structured-logging"></a>Strukturált naplózás

A helyőrzők sorrendje, a nevük nem, meghatározza, hogy a rendszer mely paramétereket használja a naplófájlban. Tegyük fel, hogy a következő kóddal rendelkezik:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Ha megtartja ugyanazt az üzenetet, és megfordítja a paraméterek sorrendjét, az eredményül kapott üzenet szövege nem megfelelő helyen lenne.

A helyőrzők kezelése így történik, így strukturált naplózást végezhet. Application Insights a paraméter név-érték párokat és az üzenet karakterláncát tárolja. Ennek az az oka, hogy az üzenet argumentumai olyan mezők lesznek, amelyeken lekérdezéseket végezhet.

Ha a naplózó metódus hívása az előző példához hasonlóan néz ki, akkor lekérdezheti a mezőt `customDimensions.prop__rowKey` . A `prop__` rendszer hozzáadja az előtagot annak biztosításához, hogy ne legyenek ütközések a futtatókörnyezet által hozzáadott és mezőket tartalmazó mezők között.

A mezőre hivatkozva az eredeti üzenet sztringjét is lekérdezheti `customDimensions.prop__{OriginalFormat}` .  

Az alábbi példa az adat JSON-ábrázolását mutatja be `customDimensions` :

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="log-custom-telemetry"></a><a name="log-custom-telemetry-in-c-functions"></a>Egyéni telemetria naplózása

A Application Insights SDK egy függvény-specifikus verziója, amellyel egyéni telemetria-adatok küldhetők a függvényekből Application Insights: [Microsoft. Azure. webjobs. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). A következő paranccsal telepítheti a csomagot a parancssorból:

# <a name="command"></a>[Parancs](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Ebben a parancsban cserélje le a parancsot a `<VERSION>` csomag egy olyan verziójára, amely támogatja a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)telepített verzióját. 

A következő C#-példák az [Egyéni TELEMETRIA API](../azure-monitor/app/api-custom-events-metrics.md)-t használják. A példa egy .NET-osztályhoz tartozó könyvtárra mutat, de a Application Insights kód a C#-parancsfájl esetében azonos.

# <a name="v2x"></a>[v2. x +](#tab/v2)

A futtatókörnyezet 2. x vagy újabb verziói a Application Insights újabb funkcióit használják a telemetria automatikus összekapcsolásához a jelenlegi művelettel. Nem szükséges manuálisan beállítani a műveletet `Id` , `ParentId` vagy a `Name` mezőket.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

Ebben a példában az egyéni metrikai adatokat a gazdagép összesíti, mielőtt elküldi őket a customMetrics táblába. További információkért tekintse meg a [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) dokumentációját Application Insights. 

Helyileg történő futtatáskor hozzá kell adnia a `APPINSIGHTS_INSTRUMENTATIONKEY` Application Insights kulccsal rendelkező beállítást a fájl [local.settings.js](functions-run-local.md#local-settings-file) .


# <a name="v1x"></a>[v1. x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

Ne telefonáljon, `TrackRequest` vagy `StartOperation<RequestTelemetry>` mert a függvény meghívásakor duplikált kérelmeket fog látni.  A függvények futtatókörnyezete automatikusan nyomon követi a kérelmeket.

Nincs beállítva `telemetryClient.Context.Operation.Id` . Ez a globális beállítás helytelen korrelációt okoz, ha sok függvény egyidejűleg fut. Ehelyett hozzon létre egy új telemetria-példányt ( `DependencyTelemetry` , `EventTelemetry` ), és módosítsa a `Context` tulajdonságát. Ezután továbbítsa a telemetria-példányt a megfelelő `Track` metódusba `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` , `TrackMetric()` ). Ez a módszer biztosítja, hogy a telemetria megfelelő korrelációs adatokat biztosítson az aktuális függvény meghívásához.


## <a name="environment-variables"></a>Környezeti változók

Környezeti változó vagy Alkalmazásbeállítások értékének beszerzéséhez használja a (z) értéket `System.Environment.GetEnvironmentVariable` a következő példában látható módon:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Az Alkalmazásbeállítások a helyi fejlesztésekor és az Azure-ban való futtatáskor is beolvashatók környezeti változókból. Helyi fejlesztés esetén az Alkalmazásbeállítások a `Values` fájlban lévő *local.settings.js* gyűjteményből származnak. Mindkét környezetben, a helyi és az Azure-ban a `GetEnvironmentVariable("<app setting name>")` megnevezett alkalmazás értékét kérdezi le. Ha például helyileg fut, akkor a "saját hely neve" értéket adja vissza, ha a *local.settings.js* fájl tartalmazza `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` .

A [System.Configuration.ConfigurationManager. appSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) tulajdonság egy alternatív API az Alkalmazásbeállítások értékének beolvasásához, de azt javasoljuk, hogy használja az `GetEnvironmentVariable` itt látható módon.

## <a name="binding-at-runtime"></a>Kötés futásidőben

C#-és egyéb .NET-nyelveken a [kötelező kötési mintát](https://en.wikipedia.org/wiki/Imperative_programming) használhatja, az attribútumok [*deklaratív*](https://en.wikipedia.org/wiki/Declarative_programming) kötéseivel szemben. A kényszerített kötés akkor hasznos, ha a kötési paramétereket nem a tervezési idő, hanem futásidőben kell kiszámítani. Ezzel a mintával a függvény kódjában a támogatott bemeneti és kimeneti kötésekhez köthető.

A következő módon adjon meg egy kötelező kötést:

- **Ne tartalmazzon** attribútumot a függvény aláírásában a kívánt kényszerített kötésekhez.
- Adjon meg egy bemeneti paramétert [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) vagy [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Az adatkötés végrehajtásához használja a következő C#-mintát.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` az a .NET-attribútum, amely meghatározza a kötést, és `T` egy bemeneti vagy kimeneti típus, amelyet az adott kötési típus támogat. `T` nem lehet `out` paraméter típusa (például `out JObject` ). A Mobile Apps tábla kimeneti kötése például [hat kimeneti típust](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)támogat, de a [ \<T> ICollector](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) vagy a [ \<T> IAsyncCollector](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) csak kényszerített kötéssel használható.

### <a name="single-attribute-example"></a>Példa egyetlen attribútumra

A következő mintakód létrehoz egy [tárolási blob kimeneti kötést](functions-bindings-storage-blob-output.md) a futási időben definiált blob elérési úttal, majd karakterláncot ír a blobba.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

A [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) meghatározza a [Storage blob](functions-bindings-storage-blob.md) bemeneti vagy kimeneti kötését, és a [TextWriter](/dotnet/api/system.io.textwriter) egy támogatott kimeneti kötési típus.

### <a name="multiple-attribute-example"></a>Több attribútum – példa

Az előző példában beolvassa a Function alkalmazás fő Storage-fiókjának (azaz) az alkalmazás beállítását `AzureWebJobsStorage` . A Storage-fiókhoz a [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) hozzáadásával és az attribútum tömbbe való átadásával adhat meg egyéni alkalmazás-beállítást `BindAsync<T>()` . Használjon `Binder` paramétert, nem `IBinder` .  Például:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Eseményindítók és kötések 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az eseményindítók és kötésekről](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információ a Azure Functions ajánlott eljárásairól](functions-best-practices.md)
