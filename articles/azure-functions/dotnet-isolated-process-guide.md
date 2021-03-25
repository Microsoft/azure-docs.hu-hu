---
title: .NET elkülönített folyamat útmutató a .NET 5,0-hez a Azure Functions
description: Megtudhatja, hogyan futtathat a C#-függvényeket a .net 5,0-es folyamaton kívüli Azure-ban az Azure-ban.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 4da685c247427e78297df1753779ee9b5c7866b8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023197"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Útmutató a függvények futtatásához a .NET 5,0-ben az Azure-ban

Ez a cikk bevezetést biztosít a C# használatával a .NET elkülönített folyamat-függvények fejlesztéséhez, amelyek folyamaton kívül futnak Azure Functions. A folyamaton kívüli futtatással a Azure Functions futtatókörnyezetből választhatja ki a függvény kódját. Emellett lehetővé teszi olyan függvények létrehozását és futtatását, amelyek az aktuális .NET 5,0 kiadást célozzák meg. 

| Első lépések | Alapelvek| Példák |
|--|--|--| 
| <ul><li>[A Visual Studio Code használata](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Parancssori eszközök használata](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[A Visual Studio használata](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Üzemeltetési lehetőségek](functions-scale.md)</li><li>[Figyelés](functions-monitoring.md)</li> | <ul><li>[Hivatkozási minták](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Ha nem kell támogatnia a .NET 5,0-et vagy a függvények folyamaton kívüli futtatását, érdemes lehet [C# szintű függvénytár-függvényeket létrehoznia](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Miért van a .NET elkülönített folyamat?

A korábban Azure Functions csak a .NET függvények szorosan integrált módját támogatja, amelyek a gazdagéptel azonos folyamatban lévő [osztály-függvénytárként](functions-dotnet-class-library.md) futnak. Ez a mód mély integrációt biztosít a gazdagép és a függvények között. A .NET Class Library függvények például megoszthatják a kötési API-kat és típusokat. Ez az integráció azonban szigorúbb összekapcsolást is igényel a gazdagép és a .NET-függvény között. Például a folyamaton belüli .NET-függvények futtatásához a .NET-es verziónak kell futnia a functions futtatókörnyezettel. Ha engedélyezni szeretné, hogy a megkötéseken kívül fusson, mostantól egy elkülönített folyamaton is futtathat. A folyamat elkülönítése lehetővé teszi olyan függvények fejlesztését is, amelyek az aktuális .NET-kiadásokat használják (például .NET 5,0), a functions futtatókörnyezet által nem támogatott natív módon.

Mivel ezek a függvények külön folyamatban futnak, néhány [funkció és funkció](#differences-with-net-class-library-functions) a .net elkülönített Function apps és a .net Class Library függvények alkalmazásai között van.

### <a name="benefits-of-running-out-of-process"></a>A folyamaton kívüli Futtatás előnyei

A folyamaton kívüli futtatásakor a .NET-függvények a következő előnyöket vehetik igénybe: 

+ Kevesebb ütközés: mivel a függvények külön folyamatban futnak, az alkalmazásban használt szerelvények nem ütköznek a gazdagépi folyamat által használt azonos szerelvények eltérő verziójával.  
+ A folyamat teljes körű ellenőrzése: az alkalmazás elindítását szabályozhatja, és szabályozhatja a felhasznált konfigurációkat és a köztes közbenső lépéseket.
+ Függőségi befecskendezés: mivel teljes mértékben felügyeli a folyamatot, az aktuális .NET-viselkedést használhatja a függőségek befecskendezéséhez, és a middleware-t is beépítheti a Function alkalmazásba. 

## <a name="supported-versions"></a>Támogatott verziók

A .net 5,0-es verziójának jelenleg csak a .NET-et futtató verziója támogatott.

## <a name="net-isolated-project"></a>.NET elkülönített projekt

A .net elkülönített függvények projektje alapvetően egy .NET-konzolos alkalmazás-projekt, amely a .NET 5,0-et célozza meg. A következő alapvető fájlok szükségesek a .NET elkülönített projektekben:

+ [host.js](functions-host-json.md) fájlon.
+ [local.settings.js](functions-run-local.md#local-settings-file) fájlon.
+ C# projektfájl (. csproj), amely meghatározza a projektet és a függőségeket.
+ A program. cs fájl, amely az alkalmazás belépési pontja.

## <a name="package-references"></a>Csomagok hivatkozásai

A folyamaton kívüli futtatásakor a .NET-projekt a csomagok egyedi készletét használja, amely az alapvető funkciókat és a kötési bővítményeket is megvalósítja. 

### <a name="core-packages"></a>Alapcsomagok 

A .NET-függvények elkülönített folyamatban való futtatásához a következő csomagok szükségesek:

+ [Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft. Azure. functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Kiterjesztési csomagok

Mivel a .NET elkülönített folyamatokban futó függvények különböző kötési típusokat használnak, a kötési bővítmények egyedi készletét igénylik. 

Ezeket a kiterjesztési csomagokat a [Microsoft. Azure. functions. Worker. Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)alatt találja.

## <a name="start-up-and-configuration"></a>Indítás és konfigurálás 

A .NET elkülönített függvények használatakor Ön hozzáférhet a Function alkalmazás elindításához, amely általában a program. cs. Ön felelős a saját gazdagép-példányának létrehozásához és elindításához. Ennek megfelelően közvetlen hozzáférést is biztosít az alkalmazás konfigurációs folyamatához. A folyamaton kívüli folyamatok esetében sokkal könnyebben hozzáadhat konfigurációkat, befecskendezheti a függőségeket, és saját middleware-t is futtathat. 

Az alábbi kód egy [HostBuilder] -folyamatra mutat példát:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

A [HostBuilder] egy teljesen inicializált [IHost] -példány létrehozására és visszaküldésére szolgál, amelyet aszinkron módon futtat a Function alkalmazás indításához. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfiguráció

A [ConfigureFunctionsWorkerDefaults] metódussal adhatók hozzá azok a beállítások, amelyek szükségesek a Function alkalmazásnak a folyamaton kívüli futtatásához, amely a következő funkciókat tartalmazza:

+ Az átalakítók alapértelmezett készlete.
+ Állítsa be úgy az alapértelmezett [JsonSerializerOptions] , hogy figyelmen kívül hagyja a tulajdonságok neveit.
+ Integrálás Azure Functions naplózással.
+ A kimeneti kötések és szolgáltatások.
+ Függvény-végrehajtási middleware.
+ Alapértelmezett gRPC-támogatás. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Ha a gazdagép-építő folyamathoz fér hozzá, akkor az inicializálás során bármely alkalmazásspecifikus konfigurációt is beállíthat. A [ConfigureAppConfiguration] metódust a [HostBuilder] egy vagy több alkalommal is meghívhatja a Function alkalmazás által igényelt konfigurációk hozzáadásához. Az alkalmazás konfigurálásával kapcsolatos további tudnivalókért tekintse meg [a ASP.net Core konfiguráció](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)című témakört. 

Ezek a konfigurációk egy külön folyamatban futó Function alkalmazásra vonatkoznak. A functions Host vagy az trigger és a kötési konfiguráció módosításához továbbra is ahost.jskell használnia a [ fájlon](functions-host-json.md).   

### <a name="dependency-injection"></a>Függőséginjektálás

A függőségi befecskendezés egyszerűsített, a .NET-osztály könyvtáraihoz képest. Ahelyett, hogy a szolgáltatások regisztrálásához indítási osztályt kellene létrehoznia, csak meg kell hívnia a [ConfigureServices] -t a gazdagép-építőben, és a [IServiceCollection] bővítményi metódusait kell használnia adott szolgáltatások beadásához. 

Az alábbi példa egy különálló szolgáltatás-függőséget szúr be:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

További információ: [függőségi befecskendezés ASP.net Coreban](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Köztes szoftverek

A .NET izolált is támogatja a middleware-regisztrációt, a ASP.NET-ben találhatóhoz hasonló modell használatával. Ez a modell lehetővé teszi a logika beírását a Meghívási folyamatba, a függvények végrehajtása előtt és után.

A [ConfigureFunctionsWorkerDefaults] -bővítmény metódusának túlterhelése lehetővé teszi a saját middleware regisztrálását, ahogy az a következő példában látható.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Ha szeretné jobban megtekinteni az egyéni middleware használatát a Function alkalmazásban, tekintse meg az [Egyéni middleware-referenciát ismertető mintát](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Végrehajtási környezet

A .NET elkülönített [FunctionContext] -objektumot továbbít a Function metódusoknak. Ez az objektum lehetővé teszi, hogy egy [ILogger] -példányt írjon a naplókba úgy, hogy meghívja a [GetLogger] metódust, és megad egy `categoryName` karakterláncot. További információ: [naplózás](#logging). 

## <a name="bindings"></a>Kötések 

A kötések meghatározása a metódusok, paraméterek és visszatérési típusok attribútumainak használatával történik. A Function metódus egy olyan metódus, amely egy `Function` attribútummal és egy bemeneti paraméterre alkalmazott trigger attribútummal rendelkezik, az alábbi példában látható módon:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Az trigger attribútum meghatározza az trigger típusát, és a bemeneti adatokat egy metódus paraméteréhez köti. Az előző példa függvényt egy üzenetsor-üzenet indítja el, és a várólista-üzenet a paraméterben megadott metódusnak lesz átadva `myQueueItem` .

Az `Function` attribútum a metódust függvény belépési pontként jelöli. A névnek egyedinek kell lennie a projekten belül, betűvel kell kezdődnie, és csak betűket, számokat, `_` és, `-` legfeljebb 127 karaktert tartalmazhat. A Project templates gyakran létrehoz egy nevű metódust `Run` , de a metódus neve lehet bármely érvényes C#-metódus neve.

Mivel a .NET elkülönített projektek külön munkavégző folyamatban futnak, a kötések nem tudják kihasználni a részletes kötési osztályokat, például, `ICollector<T>` `IAsyncCollector<T>` és `CloudBlockBlob` . Az alapul szolgáló Service SDK-k (például a [DocumentClient] és a [BrokeredMessage]) által örökölt típusok esetében nem támogatott a közvetlen támogatás is. Ehelyett a kötések karakterláncokat, tömböket és szerializálható típusokat, például egyszerű, régi osztályú objektumokat (POCOs) használnak. 

HTTP-eseményindítók esetén a [HttpRequestData] és a [HttpResponseData] használatával kell elérnie a kérelmeket és a válaszokat. Ennek az az oka, hogy nincs hozzáférése az eredeti HTTP-kérelemhez és a válasz-objektumokhoz a folyamaton kívüli futtatásakor.

Az eseményindítók és kötések folyamaton kívüli futtatásához használható hivatkozási minták teljes készletét a [kötési bővítmények hivatkozási mintája](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions)tartalmazza. 

### <a name="input-bindings"></a>Bemeneti kötések

A függvények nulla vagy több bemeneti kötést tartalmazhatnak, amelyek adatokat adhatnak át egy függvénynek. A triggerekhez hasonlóan a bemeneti kötések is definiálva vannak, ha egy kötési attribútumot alkalmaz egy bemeneti paraméterre. A függvény végrehajtásakor a futtatókörnyezet megkísérli a kötésben megadott adat lekérését. A kért adatok gyakran az trigger által a kötési paraméterek használatával biztosított információtól függenek.  

### <a name="output-bindings"></a>Kimeneti kötések

Ha kimeneti kötést szeretne írni, egy kimeneti kötési attribútumot kell alkalmaznia a Function metódusra, amely azt határozza meg, hogyan írhat a kötött szolgáltatásba. A metódus által visszaadott érték a kimeneti kötésbe íródik. Például a következő példa egy karakterlánc-értéket ír egy nevű üzenet-várólistába egy `functiontesting2` kimeneti kötés használatával:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Több kimeneti kötés

A kimeneti kötésbe írt adat mindig a függvény visszatérési értéke. Ha egynél több kimeneti kötést kell írnia, egyéni visszatérési típust kell létrehoznia. Ennek a visszatérési típusnak az osztály egy vagy több tulajdonságára alkalmazott kimeneti kötési attribútummal kell rendelkeznie. Az alábbi példa egy HTTP-triggert ír a HTTP-válaszra és egy üzenetsor-kimeneti kötésre:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

A HTTP-triggerekre adott válasz mindig kimenetnek minősül, ezért nincs szükség visszatérési érték attribútumra.

### <a name="http-trigger"></a>HTTP-eseményindító

A HTTP-eseményindítók lefordítják a bejövő HTTP-kérési üzenetet egy [HttpRequestData] objektumba, amely át lett adva a függvénynek. Ez az objektum a kérelemből származó adatokkal szolgál, beleértve a következőket:,,, `Headers` `Cookies` `Identities` `URL` és opcionálisan egy üzenetet `Body` . Ez az objektum a HTTP-kérelem objektumának ábrázolása, és nem maga a kérelem. 

Hasonlóképpen, a függvény egy [HttpResponseData] objektumot ad vissza, amely a http-válasz létrehozásához használt adatokkal, például az üzenettel `StatusCode` `Headers` és opcionálisan egy üzenettel rendelkezik `Body` .  

A következő kód egy HTTP-trigger 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Naplózás

A .NET-alapú elkülönítésben a függvénynek átadott [FunctionContext] objektumból beszerzett [ILogger] -példány használatával írhat naplókat. Hívja meg a [GetLogger] metódust, és adjon meg egy olyan karakterlánc-értéket, amely a naplók írásának kategóriája. A kategória általában annak az adott függvénynek a neve, amelyből a naplók íródnak. A kategóriákkal kapcsolatos további tudnivalókért tekintse meg a [figyelési cikket](functions-monitoring.md#log-levels-and-categories). 

Az alábbi példa bemutatja, hogyan szerezhet be egy [ILogger] , és hogyan írhat naplókat egy függvényen belül:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

A különböző naplózási szintek, például a vagy a [ILogger] különböző módszerekkel írhatók `LogWarning` `LogError` . A naplózási szintekkel kapcsolatos további tudnivalókért tekintse meg a [figyelési cikket](functions-monitoring.md#log-levels-and-categories).

A [függőségi injektálás](#dependency-injection)használatakor a rendszer [ILogger] is biztosít.

## <a name="differences-with-net-class-library-functions"></a>A .NET-osztály függvénytár-funkcióival kapcsolatos különbségek

Ez a szakasz ismerteti a .NET 5,0-as folyamaton futó működési és viselkedési különbségek aktuális állapotát a folyamaton belül futó .NET-es függvénytár-funkciókhoz képest:

| Szolgáltatás/viselkedés |  Folyamaton belüli (.NET Core 3,1) | Folyamaton kívüli (.NET 5,0) |
| ---- | ---- | ---- |
| .NET-verziók | LTS (.NET Core 3,1) | Aktuális (.NET 5,0) |
| Alapcsomagok | [Microsoft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Kötési bővítmények csomagjai | [Microsoft. Azure. webjobs. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | A [Microsoft. Azure. functions. Worker. Extensions alatt. *](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Naplózás | A függvénynek átadott [ILogger] | A [FunctionContext] -ből beszerzett [ILogger] |
| Visszavonási tokenek | [Támogatott](functions-dotnet-class-library.md#cancellation-tokens) | Nem támogatott |
| Kimeneti kötések | Kimenő paraméterek | Visszatérési értékek |
| Kimeneti kötések fajtái |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage]és más ügyfél-specifikus típusok | Egyszerű típusok, JSON-szerializálható típusok és tömbök. |
| Több kimeneti kötés | Támogatott | [Támogatott](#multiple-output-bindings) |
| HTTP-eseményindító | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Tartós függvények | [Támogatott](durable/durable-functions-overview.md) | Nem támogatott | 
| Kényszerített kötések | [Támogatott](functions-dotnet-class-library.md#binding-at-runtime) | Nem támogatott |
| function.jsaz összetevőn | Létrehozott | Nem generált |
| Konfiguráció | [host.jsbekapcsolva](functions-host-json.md) | [host.js](functions-host-json.md) és egyéni inicializálás |
| Függőséginjektálás | [Támogatott](functions-dotnet-dependency-injection.md)  | [Támogatott](#dependency-injection) |
| Köztes szoftverek | Nem támogatott | Támogatott |
| Hideg indítási idő | Tipikus | Az igény szerinti indítás miatt már nem. A lehetséges késések csökkentése érdekében a Windows helyett Linuxon futtassa a parancsot. |
| ReadyToRun | [Támogatott](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Ismert problémák

A .NET elkülönített folyamat-függvények futtatásával kapcsolatos megoldásokkal kapcsolatos további információkért tekintse meg [az ismert problémák oldalát](https://aka.ms/AAbh18e). A problémák jelentéséhez [hozzon létre egy problémát a GitHub-tárházban](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose).  

## <a name="next-steps"></a>Következő lépések

+ [További információ az eseményindítók és kötésekről](functions-triggers-bindings.md)
+ [További információ a Azure Functions ajánlott eljárásairól](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
