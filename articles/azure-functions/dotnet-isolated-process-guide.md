---
title: .NET elkülönített folyamat útmutatója a .NET 5.0-s Azure Functions
description: Ismerje meg, hogyan futtathat C#-függvényeket a .NET 5.0-ban folyamaton keresztül az Azure-ban egy .NET elkülönített folyamattal.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 53f3c79886d26b20a584d747759176ea842741cf
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739277"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Útmutató a függvények .NET 5.0-ban való futtatásához az Azure-ban

Ez a cikk bevezetést tartalmaz a C# használatának a .NET elkülönített folyamatfunkciók fejlesztésében, amelyek a folyamat során elfogynak a Azure Functions. A folyamaton túli futtatás lehetővé teszi, hogy a függvénykódot külön Azure Functions futtatókörnyezettől. Emellett lehetővé teszi az aktuális .NET 5.0-kiadást célzó függvények létrehozására és futtatására is. 

| Első lépések | Alapelvek| Példák |
|--|--|--| 
| <ul><li>[A Visual Studio Code használata](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Parancssori eszközök használata](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[A Visual Studio használata](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Üzemeltetési lehetőségek](functions-scale.md)</li><li>[Figyelés](functions-monitoring.md)</li> | <ul><li>[Referenciaminták](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Ha nem kell támogatnia a .NET 5.0-t, vagy nem kell folyamaton túl futtatnia a [függvényeket,](functions-dotnet-class-library.md)érdemes lehet C#-osztálytári függvényeket fejleszteni.

## <a name="why-net-isolated-process"></a>Miért érdemes a .NET elkülönített folyamatot?

Korábban Azure Functions .NET-függvények csak szorosan integrált módot támogatnak, amelyek osztálytárként futnak [a](functions-dotnet-class-library.md) gazdagép folyamatában. Ez a mód mély integrációt biztosít a gazdafolyamat és a függvények között. A .NET-osztálytár függvények például megoszthatnak kötési API-kat és típusokat. Ehhez az integrációhoz azonban szorosabb kapcsolatra van szükség a gazdafolyamat és a .NET-függvény között. Például a folyamatban futó .NET-függvények futtatásának a .NET ugyanazon verzióján kell futnia, mint a Functions-futtatókörnyezetnek. Ha lehetővé szeretné tenni, hogy ezeken a korlátozásokon kívül fusson, dönthet úgy, hogy elkülönített folyamatban futtatja a futtatásokat. Ez a folyamatelszigetelés lehetővé teszi az aktuális .NET-kiadásokat (például .NET 5.0) támogató függvények fejlesztését is, amelyek nem támogatottak natív módon a Functions-futtatásban.

Mivel ezek a függvények külön folyamatban [](#differences-with-net-class-library-functions) futnak, van néhány funkció- és funkcióbeli különbség a .NET izolált függvényalkalmazások és a .NET-osztálytári függvényalkalmazások között.

### <a name="benefits-of-running-out-of-process"></a>A folyamatban nem futó folyamatok előnyei

Ha a folyamat fut, a .NET-függvények az alábbi előnyöket biztosítják: 

+ Kevesebb ütközés: mivel a függvények külön folyamatban futnak, az alkalmazásban használt szerelvények nem ütköznek a gazdafolyamat által használt szerelvények különböző verzióival.  
+ A folyamat teljes körű vezérlése: Ön vezérelheti az alkalmazás indítását, és szabályozhatja a használt konfigurációkat és a középső szoftver elindulásait.
+ Függőséginjektálás: mivel teljes körűen szabályozhatja a folyamatot, használhatja az aktuális .NET-viselkedéseket a függőséginjektáláshoz, valamint a middleware beépítésével a függvényalkalmazásba. 

## <a name="supported-versions"></a>Támogatott verziók

A .NET egyetlen olyan verziója, amely jelenleg nem használható folyamaton, a .NET 5.0.

## <a name="net-isolated-project"></a>.NET elkülönített projekt

A .NET izolált függvényprojekt alapvetően egy .NET-konzolalkalmazás-projekt, amely a .NET 5.0-t célozza meg. A .NET izolált projektekhez az alábbi alapszintű fájlok szükségesek:

+ [host.jsfájlban található.](functions-host-json.md)
+ [local.settings.jsfájlban található.](functions-run-local.md#local-settings-file)
+ A projektet és a függőségeket definiáló C#-projektfájl (.csproj).
+ Program.cs fájl, amely az alkalmazás belépési pontja.

## <a name="package-references"></a>Csomaghivatkozások

Ha a folyamat fut, a .NET-projekt egyedi csomagkészletet használ, amely az alapvető funkciókat és a kötésbővítményeket is megvalósítja. 

### <a name="core-packages"></a>Alapcsomagok 

A .NET-függvények izolált folyamatban való futtatásához a következő csomagok szükségesek:

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Bővítménycsomagok

Mivel az elkülönített .NET-folyamatokban futó függvények különböző kötéstípusokat használnak, a kötésbővítmény-csomagok egyedi készletét igénylik. 

Ezeket a bővítménycsomagokat a [Microsoft.Azure.Functions.Worker.Extensions alatt találja.](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)

## <a name="start-up-and-configuration"></a>Indítás és konfigurálás 

A .NET izolált függvények használata esetén hozzáférhet a függvényalkalmazás indításához, amely általában a Program.cs fájlban van. Az Ön feladata a saját gazdagéppéldány létrehozása és indítása. Így közvetlen hozzáféréssel rendelkezik az alkalmazás konfigurációs folyamathoz is. Amikor elfogy a folyamat, sokkal könnyebben adhat hozzá konfigurációkat, adhat hozzá függőségeket, és futtathatja a saját középső szoftverét. 

Az alábbi kód egy [HostBuilder-folyamatra mutat példát:]

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Ehhez a kódhoz a `using Microsoft.Extensions.DependencyInjection;` szükséges. 

A [HostBuilder] használatával egy teljesen inicializált [IHost-példányt] hoz létre és ad vissza, amelyet aszinkron módon futtatva elindítja a függvényalkalmazást. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfiguráció

A [ConfigureFunctionsWorkerDefaults] metódussal adjuk hozzá a függvényalkalmazás folyamaton túli futtatásához szükséges beállításokat, amelyek a következő funkciókat tartalmazzák:

+ Az átalakítók alapértelmezett készlete.
+ Állítsa be az alapértelmezett [JsonSerializerOptions beállítást] úgy, hogy hagyja figyelmen kívül a tulajdonságnevekben szereplő kis- és a nagy- és kis- és értékeket.
+ Integrálás Azure Functions naplózással.
+ Kimeneti kötés – középső szoftver és funkciók.
+ Függvényvégrehajtás – középső szoftver.
+ Alapértelmezett gRPC-támogatás. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

A gazdagépkészítő folyamathoz való hozzáférés azt jelenti, hogy az inicializálás során bármilyen alkalmazásspecifikus konfigurációt be lehet állítani. A [ConfigureAppConfiguration] metódust többször is meg lehet hívni a [HostBuilderen] a függvényalkalmazáshoz szükséges konfigurációk hozzáadásához. Az alkalmazáskonfigurációval kapcsolatos további információkért lásd: Configuration in ASP.NET Core (Konfigurálás a [ASP.NET Core-ban).](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true) 

Ezek a konfigurációk egy külön folyamatban futó függvényalkalmazásra vonatkoznak. A függvénygazda vagy eseményindító és kötés konfigurációjának módosításaihoz továbbra is a fájlban találhatóhost.js[kell használnia.](functions-host-json.md)   

### <a name="dependency-injection"></a>Függőséginjektálás

A .NET-osztálytárakhoz képest a függőséginjektálás egyszerűbb. Ahelyett, hogy létre kell hoznia egy indítási osztályt a szolgáltatások regisztrálása érdekében, csak meg kell hívnia a [ConfigureServices] szolgáltatást a gazdagépkészítőn, és az [IServiceCollection] bővítmény metódusával kell beinjektálnia bizonyos szolgáltatásokat. 

Az alábbi példa egyszeri szolgáltatásfüggőségeket injektál:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Ehhez a kódhoz `using Microsoft.Extensions.DependencyInjection;` a szükséges. További információ: [Függőséginjektálás a ASP.NET Core-ban.](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)

### <a name="middleware"></a>Közbenső szoftver

A .NET izolált is támogatja a middleware-regisztrációt is, szintén egy, a szoftverben találhatóhoz hasonló ASP.NET. Ez a modell lehetővé teszi, hogy logikát injektáljon a meghívási folyamatba, valamint a függvények végrehajtása előtt és után.

A [ConfigureFunctionsWorkerDefaults] bővítmény metódus túlterheléssel rendelkezik, amely lehetővé teszi a saját middleware regisztrálását, ahogyan az alábbi példában látható.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Az egyéni middleware függvényalkalmazásban való használatának teljesebb példájért tekintse meg az egyéni [middleware referenciamintát.](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware)

## <a name="execution-context"></a>Végrehajtási környezet

A .NET izolált függvény [egy FunctionContext objektumot] ad át a függvény metódusának. Ez az objektum lehetővé teszi egy [ILogger-példány] lehívását a naplókba való íráshoz a [GetLogger] metódus hívásával és egy sztring megírásával. `categoryName` További információ: [Naplózás.](#logging) 

## <a name="bindings"></a>Kötések 

A kötések metódusok, paraméterek és visszatérési típusok attribútumainak használatával határozhatóak meg. A függvény metódus egy metódus, amely egy attribútummal és egy bemeneti paraméterre alkalmazott triggerattribútummal rendelkezik, az alábbi `Function` példában látható módon:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Az eseményindító attribútum meghatározza az eseményindító típusát, és a bemeneti adatokat egy metódusparaméterhez köti. Az előző példa függvényt egy üzenetsor-üzenet aktiválja, az üzenetsor üzenete pedig a paraméterben lévő metódusnak lesz `myQueueItem` átadva.

A `Function` attribútum függvény belépési pontként jelöli meg a metódust. A névnek egyedinek kell lennie a projekten belül, betűvel kell kezdődnie, és csak betűket, számokat, és , és legfeljebb `_` `-` 127 karakter hosszú lehet. A projektsablonok gyakran hoznak létre egy nevű metódust, de a metódus neve `Run` bármilyen érvényes C#-metódusnév lehet.

Mivel a .NET elkülönített projektek külön munkavégző folyamatban futnak, a kötések nem használhatjak ki a gazdag kötési osztályokat, például a , a és a `ICollector<T>` `IAsyncCollector<T>` `CloudBlockBlob` osztályt. A mögöttes szolgáltatási SDK-kból örökölt típusok, például a [DocumentClient] és a [BrokeredMessage nem támogatottak közvetlenül.] Ehelyett a kötések sztringekra, tömbökre és szerializálható típusokra, például egyszerű régi osztályobjektumra (POCO-k) támaszkodnak. 

HTTP-eseményindítók esetén a [HttpRequestData] és a [HttpResponseData] használatával kell hozzáférnie a kérés- és válaszadatokhoz. Ennek az az oka, hogy nincs hozzáférése az eredeti HTTP-kérés- és válaszobjektumhoz, amikor a folyamat fut.

Az eseményindítók és kötések használatának teljes körű referenciamintákat a kötésbővítmények referenciamintája [tartalmaz.](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions) 

### <a name="input-bindings"></a>Bemeneti kötések

Egy függvény nulla vagy több bemeneti kötéssel is rendelkezik, amelyek adatokat tudnak átadni egy függvénynek. Az eseményindítókhoz hasonló a bemeneti kötések is egy kötési attribútum bemeneti paraméterre való alkalmazásával határozhatóak meg. A függvény végrehajtásakor a futtatás megkísérli lekért adatokat lekért kötésben. A kért adatok gyakran az eseményindító által kötési paramétereket használó információktól függenek.  

### <a name="output-bindings"></a>Kimeneti kötések

Kimeneti kötésbe való íráshoz kimeneti kötési attribútumot kell alkalmaznia a függvény metódusára, amely meghatározta a kötött szolgáltatásba való írás módját. A metódus által visszaadott érték a kimeneti kötésbe lesz írva. Az alábbi példa például egy nevű üzenetsorba ír egy sztringértéket `functiontesting2` egy kimeneti kötés használatával:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Több kimeneti kötés

A kimeneti kötésbe írt adatok mindig a függvény visszatérési értéke. Ha egynél több kimeneti kötésbe kell írnia, létre kell hoznia egy egyéni visszatérési típust. Ennek a visszatérési típusnak a kimeneti kötési attribútumot kell alkalmaznia a osztály egy vagy több tulajdonságán. Az alábbi példa egy HTTP-eseményindítóból a HTTP-válaszba és az üzenetsor kimeneti kötésére is ír:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

A HTTP-eseményindítók válasza mindig kimenetnek minősül, ezért nincs szükség visszaadott érték attribútumra.

### <a name="http-trigger"></a>HTTP-eseményindító

A HTTP-eseményindítók a bejövő HTTP-kérési üzenetet a függvénynek átadott [HttpRequestData] objektumra fordítják le. Ez az objektum adatokat biztosít a kérésből, például `Headers` , , , és `Cookies` `Identities` `URL` opcionálisan egy üzenetet `Body` is. Ez az objektum a HTTP-kérés objektumát ábrázolja, nem magát a kérést. 

Hasonlóképpen, a függvény egy [HttpResponseData] objektumot ad vissza, amely a HTTP-válasz létrehozásához használt adatokat, például a , és opcionálisan `StatusCode` a üzenetet is `Headers` `Body` tartalmazza.  

Az alábbi kód egy HTTP-eseményindító 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Naplózás

A .NET izolált használatával a függvénynek átadott [FunctionContext] objektumból származó [ILogger-példány] használatával írhat a naplókba. Hívja meg a [GetLogger] metódust egy sztringérték átadásával, amely annak a kategóriának a neve, amelyben a naplók meg vannak írva. A kategória általában annak a függvénynek a neve, amelyből a naplók meg vannak írva. A kategóriákkal kapcsolatos további információkért tekintse meg a [figyelési cikket.](functions-monitoring.md#log-levels-and-categories) 

Az alábbi példa bemutatja, hogyan lehet [lekért ILogger-naplót és] naplókat írni egy függvényben:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Különböző [ILogger-metódusok használatával] különböző naplózási szinteket írhat, például vagy `LogWarning` `LogError` . A naplózási szintekkel kapcsolatos további információkért tekintse meg a [figyelési cikket.](functions-monitoring.md#log-levels-and-categories)

A [függőséginjektálás] használata esetén egy ILogger [is rendelkezésre áll.](#dependency-injection)

## <a name="differences-with-net-class-library-functions"></a>A .NET-osztálytár függvényei közötti különbségek

Ez a szakasz a .NET 5.0 folyamaton nem futó működési és viselkedési különbségeit ismerteti a folyamatban futó .NET-osztálytári függvényekkel összehasonlítva:

| Funkció/viselkedés |  Folyamatban (.NET Core 3.1) | Folyamaton nem része (.NET 5.0) |
| ---- | ---- | ---- |
| .NET-verziók | LTS (.NET Core 3.1) | Aktuális (.NET 5.0) |
| Alapcsomagok | [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Bővítménycsomagok kötése | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | A [Microsoft.Azure.Functions.Worker.Extensions alatt.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Naplózás | [A függvénynek átadott ILogger] | [A FunctionContext függvényből] [lekért ILogger] |
| Megszakítási jogkivonatok | [Támogatott](functions-dotnet-class-library.md#cancellation-tokens) | Nem támogatott |
| Kimeneti kötések | Out paraméterek | Visszatérési értékek |
| Kimeneti kötések fajtái |  `IAsyncCollector`, [DocumentClient,] [BrokeredMessage], és egyéb ügyfélspecifikus típusok | Egyszerű típusok, JSON-szerializálható típusok és tömbök. |
| Több kimeneti kötés | Támogatott | [Támogatott](#multiple-output-bindings) |
| HTTP-eseményindító | [HttpRequest (HttpRequest)] / [ObjectResult (Objektum eredményének létrehozása)] | [HttpRequestData] / [HttpResponseData] |
| Tartós függvények | [Támogatott](durable/durable-functions-overview.md) | Nem támogatott | 
| Imperatív kötések | [Támogatott](functions-dotnet-class-library.md#binding-at-runtime) | Nem támogatott |
| function.jsaz összetevőre | Generált | Nincs létrehozva |
| Konfiguráció | [host.jsbe](functions-host-json.md) | [host.jsés](functions-host-json.md) egyéni inicializálás |
| Függőséginjektálás | [Támogatott](functions-dotnet-dependency-injection.md)  | [Támogatott](#dependency-injection) |
| Közbenső szoftver | Nem támogatott | Támogatott |
| Hideg kezdési idők | Tipikus | Ez tovább tart az időponthoz időben való indítás miatt. A lehetséges késések csökkentése érdekében Windows helyett Linuxon fut. |
| ReadyToRun (Használatra kész) | [Támogatott](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Ismert problémák

A .NET izolált folyamatfunkciók futtatásával kapcsolatos problémákat áthidaló megoldásokkal kapcsolatos információkért tekintse meg ezt az ismert [problémák oldalát.](https://aka.ms/AAbh18e) A problémák jelentéséhez [hozzon létre egy problémát ebben a GitHub-adattárban.](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)  

## <a name="next-steps"></a>Következő lépések

+ [További információ az eseményindítókról és kötésekről](functions-triggers-bindings.md)
+ [További információ az ajánlott eljárásokról a Azure Functions](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration (Alkalmazáskonfiguráció konfigurálása)]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection (IServiceCollection)]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices (Szolgáltatások konfigurálása)]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult (Objektum eredményének létrehozása)]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
