---
title: .NET elkülönített folyamat útmutató a .NET 5,0-hez a Azure Functions
description: Megtudhatja, hogyan futtathat a C#-függvényeket a .net 5,0-es folyamaton kívüli Azure-ban az Azure-ban.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: d6db6c366ae51dbdc5bf062e79358f752e4a05f5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425906"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Útmutató a függvények futtatásához a .NET 5,0-ben az Azure-ban

_A .NET 5,0-támogatás jelenleg előzetes verzióban érhető el._

Ez a cikk bevezetést biztosít a C# használatával a .NET elkülönített folyamat-függvények fejlesztéséhez, amelyek folyamaton kívül futnak Azure Functions. A folyamaton kívüli futtatással a Azure Functions futtatókörnyezetből választhatja ki a függvény kódját. Emellett lehetővé teszi olyan függvények létrehozását és futtatását, amelyek az aktuális .NET 5,0 kiadást célozzák meg. 

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
+ Program.cs-fájl, amely az alkalmazás belépési pontja.

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

A .NET elkülönített függvények használatakor a Function alkalmazás elindítását, amely általában a Program.cs-ben érhető el. Ön felelős a saját gazdagép-példányának létrehozásához és elindításához. Ennek megfelelően közvetlen hozzáférést is biztosít az alkalmazás konfigurációs folyamatához. A folyamaton kívül sokkal könnyebben lehet függőségeket beszúrni és middleware-t futtatni. 

A következő kód egy folyamat példáját mutatja be `HostBuilder` :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="20-33":::

A egy `HostBuilder` teljesen inicializált példány létrehozására és visszaküldésére szolgál `IHost` , amelyet aszinkron módon futtat a Function alkalmazás elindításához. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="35":::

### <a name="configuration"></a>Konfiguráció

Ha a gazdagép-építő folyamathoz fér hozzá, az inicializálás során bármely alkalmazásspecifikus konfigurációt beállíthat. Ezek a konfigurációk egy külön folyamatban futó Function alkalmazásra vonatkoznak. A functions Host vagy az trigger és a kötési konfiguráció módosításához továbbra is ahost.jskell használnia a [ fájlon](functions-host-json.md).      

Az alábbi példa bemutatja, hogyan adhat hozzá olyan konfigurációt `args` , amely parancssori argumentumként van beolvasva: 
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="21-24" :::

A `ConfigureAppConfiguration` metódus a fordítási folyamat és az alkalmazás további részének konfigurálására szolgál. Ez a példa egy [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true)is használ, amely megkönnyíti több konfigurációs elem hozzáadását. Mivel `ConfigureAppConfiguration` ugyanezt a példányt adja vissza, a több [`IConfiguration `](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true) konfigurációs elem hozzáadásához többször is meghívhatja azt. A konfigurációk teljes készletét a és a rendszerből is elérheti [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

A konfigurálással kapcsolatos további tudnivalókért tekintse meg [a ASP.net Core konfiguráció](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)című témakört. 

### <a name="dependency-injection"></a>Függőséginjektálás

A függőségi befecskendezés egyszerűsített, a .NET-osztály könyvtáraihoz képest. Ahelyett, hogy a szolgáltatások regisztrálásához indítási osztályt kellene létrehoznia, csak meg kell hívnia a `ConfigureServices` gazdagép-építőt, és a bővítmény metódusait kell használnia az adott szolgáltatások beadásához [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) . 

Az alábbi példa egy különálló szolgáltatás-függőséget szúr be:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="29-32" :::

További információ: [függőségi befecskendezés ASP.net Coreban](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Köztes szoftverek

A .NET izolált is támogatja a middleware-regisztrációt, a ASP.NET-ben találhatóhoz hasonló modell használatával. Ez a modell lehetővé teszi a logika beírását a Meghívási folyamatba, a függvények végrehajtása előtt és után.

Bár az API-k teljes körű regisztrációs készlete még nem érhető el, a middleware-regisztráció támogatott, és egy példát is adtunk a middleware mappában található minta alkalmazáshoz.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="25-28" :::

## <a name="execution-context"></a>Végrehajtási környezet

A .NET elkülönített egy `FunctionContext` objektumot továbbít a függvények metódusai számára. Ez az objektum lehetővé teszi egy [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) példány írását a naplókba úgy, hogy meghívja a `GetLogger` metódust, és megadja a `categoryName` karakterláncot. További információ: [naplózás](#logging). 

## <a name="bindings"></a>Kötések 

A kötések meghatározása a metódusok, paraméterek és visszatérési típusok attribútumainak használatával történik. A Function metódus egy olyan metódus, amelynek a `Function` és egy trigger attribútuma egy bemeneti paraméterre van alkalmazva, az alábbi példában látható módon:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="11-14" :::

Az trigger attribútum meghatározza az trigger típusát, és a bemeneti adatokat egy metódus paraméteréhez köti. Az előző példa függvényt egy üzenetsor-üzenet indítja el, és a várólista-üzenet a paraméterben megadott metódusnak lesz átadva `myQueueItem` .

Az `Function` attribútum a metódust függvény belépési pontként jelöli. A névnek egyedinek kell lennie a projekten belül, betűvel kell kezdődnie, és csak betűket, számokat, `_` és, `-` legfeljebb 127 karaktert tartalmazhat. A Project templates gyakran létrehoz egy nevű metódust `Run` , de a metódus neve lehet bármely érvényes C#-metódus neve.

Mivel a .NET elkülönített projektek külön munkavégző folyamatban futnak, a kötések nem tudják kihasználni a részletes kötési osztályokat, például, `ICollector<T>` `IAsyncCollector<T>` és `CloudBlockBlob` . Az alapul szolgáló Service SDK-k (például a [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) és a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)) által örökölt típusok esetében nem támogatott a közvetlen támogatás is. Ehelyett a kötések karakterláncokat, tömböket és szerializálható típusokat, például egyszerű, régi osztályú objektumokat (POCOs) használnak. 

HTTP-eseményindítók esetén `HttpRequestData` a és a használatával kell `HttpResponseData` elérnie a kérelmeket és a válaszokat. Ennek az az oka, hogy nincs hozzáférése az eredeti HTTP-kérelemhez és a válasz-objektumokhoz a folyamaton kívüli futtatásakor. 

### <a name="input-bindings"></a>Bemeneti kötések

A függvények nulla vagy több bemeneti kötést tartalmazhatnak, amelyek adatokat adhatnak át egy függvénynek. A triggerekhez hasonlóan a bemeneti kötések is definiálva vannak, ha egy kötési attribútumot alkalmaz egy bemeneti paraméterre. A függvény végrehajtásakor a futtatókörnyezet megkísérli a kötésben megadott adat lekérését. A kért adatok gyakran az trigger által a kötési paraméterek használatával biztosított információtól függenek.  

### <a name="output-bindings"></a>Kimeneti kötések

Ha kimeneti kötést szeretne írni, egy kimeneti kötési attribútumot kell alkalmaznia a Function metódusra, amely azt határozza meg, hogyan írhat a kötött szolgáltatásba. A metódus által visszaadott érték a kimeneti kötésbe íródik. Például a következő példa egy karakterlánc-értéket ír egy nevű üzenet-várólistába egy `functiontesting2` kimeneti kötés használatával:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="11-21" :::

### <a name="multiple-output-bindings"></a>Több kimeneti kötés

A kimeneti kötésbe írt adat mindig a függvény visszatérési értéke. Ha egynél több kimeneti kötést kell írnia, egyéni visszatérési típust kell létrehoznia. Ennek a visszatérési típusnak az osztály egy vagy több tulajdonságára alkalmazott kimeneti kötési attribútummal kell rendelkeznie. A következő példa a HTTP-válaszra és egy üzenetsor-kimeneti kötésre ír adatokat:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" range="14-33":::

### <a name="http-trigger"></a>HTTP-eseményindító

A HTTP-eseményindítók lefordítják a bejövő HTTP-kérési üzenetet egy olyan `HttpRequestData` objektumba, amely át lett adva a függvénynek. Ez az objektum a kérelemből származó adatokkal szolgál, beleértve a következőket:,,, `Headers` `Cookies` `Identities` `URL` és opcionálisan egy üzenetet `Body` . Ez az objektum a HTTP-kérelem objektumának ábrázolása, és nem maga a kérelem. 

Hasonlóképpen, a függvény egy objektumot ad vissza `HttpReponseData` , amely a http-válasz létrehozásához használt adatokkal, például az üzenettel `StatusCode` `Headers` és opcionálisan egy üzenettel rendelkezik `Body` .  

A következő kód egy HTTP-trigger 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="13-27" :::

## <a name="logging"></a>Naplózás

A .NET elkülönített szolgáltatásban a [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) `FunctionContext` függvénynek átadott objektumból beszerzett példány használatával írhat naplókat. Hívja `GetLogger` meg a metódust, és adjon meg egy olyan karakterlánc-értéket, amely a naplók írásának kategóriája. A kategória általában annak az adott függvénynek a neve, amelyből a naplók íródnak. A kategóriákkal kapcsolatos további tudnivalókért tekintse meg a [figyelési cikket](functions-monitoring.md#log-levels-and-categories). 

Az alábbi példa bemutatja, hogyan kérhet le `ILogger` és írhat naplókat egy függvényen belül:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="17-18" ::: 

Különféle `ILogger` naplózási szintek írására különböző módszerekkel, például `LogWarning` vagy `LogError` . A naplózási szintekkel kapcsolatos további tudnivalókért tekintse meg a [figyelési cikket](functions-monitoring.md#log-levels-and-categories).

A [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) a [függőségi befecskendezés](#dependency-injection)használatakor is meg van biztosítva.

## <a name="differences-with-net-class-library-functions"></a>A .NET-osztály függvénytár-funkcióival kapcsolatos különbségek

Ez a szakasz ismerteti a .NET 5,0-as folyamaton futó működési és viselkedési különbségek aktuális állapotát a folyamaton belül futó .NET-es függvénytár-funkciókhoz képest:

| Szolgáltatás/viselkedés |  Folyamaton belüli (.NET Core 3,1) | Folyamaton kívüli (.NET 5,0) |
| ---- | ---- | ---- |
| .NET-verziók | LTS (.NET Core 3,1) | Aktuális (.NET 5,0) |
| Alapcsomagok | [Microsoft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Kötési bővítmények csomagjai | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Alatt [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Naplózás | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) átadva a függvénynek | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) beszerzés forrása `FunctionContext` |
| Visszavonási tokenek | [Támogatott](functions-dotnet-class-library.md#cancellation-tokens) | Nem támogatott |
| Kimeneti kötések | Kimenő paraméterek | Visszatérési értékek |
| Kimeneti kötések fajtái |  `IAsyncCollector`, [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient), [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)és más ügyfél-specifikus típusok | Egyszerű típusok, JSON-szerializálható típusok és tömbök. |
| Több kimeneti kötés | Támogatott | [Támogatott](#multiple-output-bindings) |
| HTTP-eseményindító | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Tartós függvények | [Támogatott](durable/durable-functions-overview.md) | Nem támogatott | 
| Kényszerített kötések | [Támogatott](functions-dotnet-class-library.md#binding-at-runtime) | Nem támogatott |
| function.jsaz összetevőn | Létrehozott | Nem generált |
| Konfiguráció | [host.jsbekapcsolva](functions-host-json.md) | [host.js](functions-host-json.md) és [Egyéni inicializálás](#configuration) |
| Függőséginjektálás | [Támogatott](functions-dotnet-dependency-injection.md)  | [Támogatott](#dependency-injection) |
| Köztes szoftverek | Nem támogatott | [Támogatott](#middleware) |
| Hideg indítási idő | Tipikus | Az igény szerinti indítás miatt már nem. A lehetséges késések csökkentése érdekében a Windows helyett Linuxon futtassa a parancsot. |
| ReadyToRun | [Támogatott](functions-dotnet-class-library.md#readytorun) | _TBD_ |


## <a name="next-steps"></a>Következő lépések

+ [További információ az eseményindítók és kötésekről](functions-triggers-bindings.md)
+ [További információ a Azure Functions ajánlott eljárásairól](functions-best-practices.md)
