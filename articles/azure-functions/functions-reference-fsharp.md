---
title: 'Azure Functions F # fejlesztői segédlet'
description: 'Ismerje meg, hogyan fejlesztheti Azure Functions az F # szkript használatával.'
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87088019"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F # fejlesztői segédlet

Az F # for Azure Functions egy olyan megoldás, amely a felhőben egyszerűen futtat kis mennyiségű kódot vagy "függvényeket". Az adat az F # függvénybe kerül a Function argumentumok használatával. Az argumentumok nevei a ben vannak megadva `function.json` , és előre definiált nevek vannak a függvények eléréséhez, például a Function Logger és a lemondási tokenekhez. 

>[!IMPORTANT]
>Az F # szkriptet (. fsx) csak az Azure Functions futtatókörnyezet [1. x verziója](functions-versions.md#creating-1x-apps) támogatja. Ha F # verziót kíván használni a Runtime 2. x vagy újabb verziójában, akkor egy előre lefordított F # Class Library-projektet (. FS) kell használnia. F # Class Library-projektet hoz létre, kezelhet és tesz közzé a Visual Studióval, ahogy azt egy C#-beli [függvénytár-projekt](functions-dotnet-class-library.md)lenne. További információ a functions-verziókról: [Azure functions Runtime Versions – áttekintés](functions-versions.md).

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői referenciát](functions-reference.md).

## <a name="how-fsx-works"></a>A. fsx működése
Egy `.fsx` fájl F # szkript. Azt is megteheti, hogy egyetlen fájlban található F # projekt. A fájl tartalmazza a program kódját (ebben az esetben az Azure-függvényt) és a függőségek kezelésére vonatkozó irányelveket.

Ha egy Azure- `.fsx` függvényt használ, a gyakran szükséges szerelvények automatikusan beletartoznak a szolgáltatásba, így a függvényre koncentrálhat, nem pedig a "szabványos" kódot.

## <a name="folder-structure"></a>Mappa szerkezete

Az F # parancsfájl-projekthez tartozó mappastruktúrát a következőhöz hasonlóan néz ki:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Van egy megosztott [host.jsa](functions-host-json.md) fájlban, amely a Function alkalmazás konfigurálására használható. Mindegyik függvényhez tartozik egy saját kódlap (. fsx) és egy kötési konfigurációs fájl (function.js).

A függvények futtatókörnyezetének [2. x vagy újabb](functions-versions.md) verziójában szükséges kötési kiterjesztések a fájlban vannak definiálva `extensions.csproj` , a mappában lévő tényleges függvénytár-fájlokkal `bin` . Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](./functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

## <a name="binding-to-arguments"></a>Argumentumok kötése
Minden kötés támogatja az argumentumok bizonyos készletét, ahogy az a [Azure functions eseményindítók és a kötések fejlesztői referenciájában](functions-triggers-bindings.md)szerepel. Például a blob triggert kötő argumentumok egyike egy POCO, amely F # rekord használatával fejezhető ki. Például:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Az F # Azure-függvény egy vagy több argumentumot fog elkészíteni. Ha Azure Functions argumentumokról beszélünk, a *bemeneti* argumentumok és a *kimeneti* argumentumok szerepelnek. A bemeneti argumentum pontosan az, ahogy hangzik: bemenet az F # Azure-függvénynek. A *kimeneti* argumentum változó adat, vagy olyan `byref<>` argumentum, amely az adatokat a függvényből visszaadja  .

A fenti példában `blob` egy bemeneti argumentum, és `output` egy kimeneti argumentum. Figyelje meg, hogy a következőhöz használtuk `byref<>` : `output` (nincs szükség a `[<Out>]` jegyzet hozzáadására). A `byref<>` típus használata lehetővé teszi, hogy a függvény megváltoztassa az argumentumra hivatkozó rekordot vagy objektumot.

Ha az F # rekord bemeneti típusként van használatban, a rekord definícióját meg kell jelölni, `[<CLIMutable>]` hogy az Azure functions-keretrendszer megfelelően állítsa be a mezőket a rekordnak a függvénybe való továbbítása előtt. A motorháztető alatt `[<CLIMutable>]` létrehozza a rögzítési tulajdonságokat a rekord tulajdonságaihoz. Például:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Az F # osztály is használható a és a kimenő argumentumokhoz is. Egy osztály esetében a tulajdonságok általában a képolvasók és a beállítók számára szükségesek. Például:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Naplózás
Ha a kimenetet F # formátumban szeretné naplózni a [folyamatos átviteli naplókba](../app-service/troubleshoot-diagnostic-logs.md) , a függvénynek [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)típusú argumentumot kell használnia. A konzisztencia érdekében javasoljuk, hogy az argumentum neve legyen `log` . Például:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Aszinkron
A `async` munkafolyamat használható, de az eredménynek egy értéket kell visszaadnia `Task` . Ezt például a következővel végezheti el `Async.StartAsTask` :

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Lemondási token
Ha a függvénynek szabályosan kell kezelnie a leállítást, [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argumentumot adhat meg. Ezt kombinálhatja például a `async` következővel:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Névterek importálása
A névtereket a szokásos módon lehet megnyitni:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

A rendszer automatikusan megnyitja a következő névtereket:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Külső szerelvények hivatkozása
Ehhez hasonlóan a keretrendszer szerelvény-referenciái is felvehetők az `#r "AssemblyName"` irányelvbe.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

A Azure Functions üzemeltetési környezet automatikusan hozzáadja a következő szerelvényeket:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Emellett a következő szerelvények speciális betokozással is rendelkezhetnek, és a simplename hivatkozhatnak (például `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Ha privát szerelvényre kell hivatkoznia, feltöltheti az összeállítási fájlt egy mappába a `bin` függvényhez képest, és hivatkozhat rá a fájlnév használatával (például  `#r "MyAssembly.dll"`). A fájlok a függvény mappájába való feltöltésével kapcsolatos információkért tekintse meg a következő szakaszt a csomagkezelő című részben.

## <a name="editor-prelude"></a>Szerkesztői bevezetés
Az F # Compiler szolgáltatást támogató szerkesztő nem fogja tudni, hogy az Azure Functions automatikusan tartalmazza-e a névtereket és szerelvényeket. Ezért hasznos lehet olyan bevezetés, amely segít a szerkesztőnek megkeresni a használt szerelvényeket, és explicit módon megnyitni a névtereket. Például:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Amikor a Azure Functions végrehajtja a kódot, az feldolgozza a forrást a `COMPILED` definiált módon, így a szerkesztő Prelude figyelmen kívül lesz hagyva.

<a name="package"></a>

## <a name="package-management"></a>Csomagkezelés
Ha F # függvényben szeretne NuGet-csomagokat használni, adjon hozzá egy fájlt a függvény `project.json` mappájához a Function alkalmazás fájlrendszerében. Az alábbi példa olyan `project.json` fájlt tartalmaz, amely egy NuGet-csomagot hoz létre a `Microsoft.ProjectOxford.Face` 1.1.0 verzióra:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Csak a .NET-keretrendszer 4,6 támogatott, ezért győződjön meg arról, hogy a `project.json` fájl az `net46` itt látható módon van meghatározva.

Amikor feltölt egy `project.json` fájlt, a futtatókörnyezet megkapja a csomagokat, és automatikusan hozzáadja a csomag szerelvényekre mutató hivatkozásokat. Nincs szükség irányelvek hozzáadására `#r "AssemblyName"` . Csak adja hozzá a szükséges `open` utasításokat a `.fsx` fájlhoz.

Érdemes lehet automatikusan hivatkozni a szerkesztőben lévő szerelvényekre, hogy javítsa a szerkesztő interakcióját az F # fordítási szolgáltatásokkal.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>`project.json`Fájl hozzáadása az Azure-függvényhez
1. Először is győződjön meg arról, hogy a Function alkalmazás fut, amelyet a függvény megnyitásával tehet meg a Azure Portalban. Emellett hozzáférést biztosít a folyamatos átviteli naplókhoz, ahol megjelenik a csomag telepítési kimenete.
2. Egy fájl feltöltéséhez `project.json` használja a [Function apps-fájlok frissítése](functions-reference.md#fileupdate)című témakörben leírt módszerek egyikét. Ha a [Azure functions folyamatos üzembe helyezését](functions-continuous-deployment.md)használja, hozzáadhat egy fájlt az `project.json` előkészítési ágra, mielőtt felvenné azt a telepítési ágra.
3. A `project.json` fájl hozzáadása után az alábbi példához hasonló kimenet jelenik meg a függvény folyamatos átviteli naplójában:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Környezeti változók
Környezeti változó vagy Alkalmazásbeállítások értékének beszerzéséhez használja `System.Environment.GetEnvironmentVariable` például a következőt:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>A. fsx kód újrafelhasználása
A más fájlokból származó kódokat `.fsx` egy direktíva használatával is használhatja `#load` . Például:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Az elérési utak az `#load` irányelvhez képest a fájl helyéhez viszonyítva jelennek meg `.fsx` .

* `#load "logger.fsx"` betölti a Function mappában található fájlt.
* `#load "package\logger.fsx"` betölti a mappában található fájlt a `package` függvény mappájába.
* `#load "..\shared\mylogger.fsx"` a mappában lévő fájlt `shared` a (z) függvény mappájával megegyező szinten tölti be, amely közvetlenül a alatt található `wwwroot` .

Az `#load` irányelv csak az `.fsx` (F # script) fájlokkal működik, és nem `.fs` fájlokkal.

## <a name="next-steps"></a>Következő lépések
További információkat találhat az alábbi forrásokban:

* [F#-útmutató](/dotnet/articles/fsharp/index)
* [Ajánlott eljárások Azure Functions](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* [Tesztelés Azure Functions](functions-test-a-function.md)
* [Azure Functions skálázás](functions-scale.md)
