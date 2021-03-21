---
title: Függőséginjektálás használata a .NET Azure Functionsben
description: Ismerje meg, hogyan használhatja a függőségi befecskendezést a szolgáltatások .NET-függvényekbe való regisztrálásához és használatához
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 01/27/2021
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 66e2cd22f4bcb95be65d6d04345dcac622436a04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955088"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Függőséginjektálás használata a .NET Azure Functionsben

A Azure Functions támogatja a függőségi injektálás (DI) szoftver kialakítási mintáját, amely az osztályok és a függőségek közötti [vezérlés (NOB)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elérését szolgáló módszer.

- A függőség injekciója Azure Functions a .NET Core függőségi injekciós funkciókra épül. A [.net Core-függőségek injektálásának](/aspnet/core/fundamentals/dependency-injection) ismerete ajánlott. A függőségek felülbírálása és a konfigurációs értékek beolvasása a Azure Functions a használati tervben eltéréseket mutat.

- A függőségi befecskendezés támogatása Azure Functions 2. x-vel kezdődik.

## <a name="prerequisites"></a>Előfeltételek

A függőségi befecskendezés használata előtt telepítenie kell a következő NuGet-csomagokat:

- [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- A [Microsoft. net. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) csomag 1.0.28 vagy újabb verziója

- [Microsoft. Extensions. DependencyInjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (jelenleg csak 3. x és korábbi verziók támogatottak)

## <a name="register-services"></a>Szolgáltatások regisztrálása

A szolgáltatások regisztrálásához hozzon létre egy metódust a példányok konfigurálásához és az összetevők hozzáadásához `IFunctionsHostBuilder` .  A Azure Functions gazdagép létrehoz egy példányt, `IFunctionsHostBuilder` és közvetlenül a metódusba továbbítja azokat.

A metódus regisztrálásához adja hozzá azt a `FunctionsStartup` Assembly attribútumot, amely megadja az indításkor használt típus nevét.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

Ez a példa a [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) csomagot használja az indításhoz való regisztráláshoz `HttpClient` .

### <a name="caveats"></a>Figyelmeztetések

A Futtatás előtt és után futtatott regisztrációs lépések sorozata az indítási osztályt dolgozza fel. Ezért ne feledje a következő elemeket:

- *Az indítási osztály csak a beállítás és a regisztráció céljára szolgál.* Ne használja az indításkor regisztrált szolgáltatásokat az indítási folyamat során. Például ne próbáljon naplózni egy olyan üzenetet, amely az indítás során regisztrálva van. A regisztrációs folyamat ezen pontja túl korai ahhoz, hogy a szolgáltatások elérhetők legyenek. A `Configure` metódus futtatása után a functions Runtime továbbra is regisztrálja a további függőségeket, ami befolyásolhatja a szolgáltatások működését.

- *A függőségi injektálási tároló csak explicit módon regisztrált típusokat* tartalmaz. A metódusban csak az injekciós típusként elérhető szolgáltatások vannak beállítva `Configure` . Ennek eredményeképpen a functions-specifikus típusok, `BindingContext` például `ExecutionContext` nem érhetők el a telepítés során, vagy injektálható típusokként.

## <a name="use-injected-dependencies"></a>Beinjektált függőségek használata

A konstruktor-injektálás a függőségek elérhetővé tételéhez használható a függvényben. A konstruktorok befecskendezésének használata megköveteli, hogy ne használjon statikus osztályokat a Beinjektált szolgáltatásokhoz vagy a függvény osztályaihoz.

Az alábbi minta azt mutatja `IMyService` be, hogyan történik a és a függőségek beadása `HttpClient` egy http-triggerrel elindított függvénybe.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

Ez a példa a [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) csomagot használja az indításhoz való regisztráláshoz `HttpClient` .

## <a name="service-lifetimes"></a>Szolgáltatás élettartama

Azure Functions alkalmazások ugyanazt a szolgáltatási élettartamot biztosítják, mint a [ASP.net függőségi injekció](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). A functions alkalmazás esetében a különböző szolgáltatási élettartamok a következőképpen viselkednek:

- **Átmeneti**: az átmeneti szolgáltatások a szolgáltatás minden egyes felbontása alapján jönnek létre.
- **Hatókörön** belüli: a hatókörön belüli szolgáltatás élettartama megfelel a függvény végrehajtási élettartamának. A hatókörrel rendelkező szolgáltatások a függvények végrehajtása után jönnek létre. A szolgáltatás későbbi kérelmei a végrehajtás során újra felhasználják a meglévő szolgáltatást.
- **Egyszeres**: az egyszeres szolgáltatás élettartama megegyezik a gazdagép élettartamával, és az adott példányon végrehajtott függvények végrehajtása során újra felhasználja őket. Az egyedi élettartamú szolgáltatások a kapcsolatok és az ügyfelek számára ajánlottak, például `DocumentClient` vagy `HttpClient` példányok esetén.

A GitHubon megtekintheti és letöltheti a [különböző szolgáltatási élettartamokat tartalmazó mintát](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) .

## <a name="logging-services"></a>Naplózási szolgáltatások

Ha saját naplózási szolgáltatóra van szüksége, regisztráljon egy egyéni típust a-példányként [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , amely a [Microsoft. Extensions. Logging. absztrakciós](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) NuGet csomagon keresztül érhető el.

A Application Insights Azure Functions automatikusan hozzáadja.

> [!WARNING]
> - Ne adja hozzá `AddApplicationInsightsTelemetry()` a szolgáltatások gyűjteményhez, amely regisztrálja azokat a szolgáltatásokat, amelyek ütköznek a környezet által nyújtott szolgáltatásokkal.
> - Ne regisztrálja saját magát `TelemetryConfiguration` , vagy `TelemetryClient` Ha a beépített Application Insights funkciót használja. Ha konfigurálnia kell a saját `TelemetryClient` példányát, hozzon létre egyet a befecskendezés használatával, ahogyan az a `TelemetryConfiguration` [C# függvények napló egyéni telemetria](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions)is látható.

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> és ILoggerFactory

A gazdagép beinjektálja `ILogger<T>` és a `ILoggerFactory` szolgáltatásokat konstruktorokban tárolja.  Alapértelmezés szerint azonban ezeket az új naplózási szűrőket a rendszer kiszűri a függvények naplóiból.  A fájl módosításával `host.json` további szűrőket és kategóriákat kell megadnia.

Az alábbi példa bemutatja, hogyan adhat hozzá olyan `ILogger<HttpTrigger>` naplókat, amelyek elérhetők a gazdagép számára.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

A következő példában szereplő `host.json` fájl hozzáadja a naplózási szűrőt.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

A naplózási szintekkel kapcsolatos további információkért lásd: a [naplózási szintek konfigurálása](configure-monitoring.md#configure-log-levels).

## <a name="function-app-provided-services"></a>A függvény által biztosított szolgáltatások

A Function Host számos szolgáltatást regisztrál. A következő szolgáltatások az alkalmazástól való függőségként is biztonságosak:

|Szolgáltatástípus|Élettartama|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Futásidejű konfiguráció|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|A gazdagép-példány AZONOSÍTÓjának biztosításáért felelős|

Ha vannak olyan szolgáltatások, amelyeknek függőségi viszonyra van szüksége, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Gazdagép-szolgáltatások felülbírálása

A gazdagép által nyújtott szolgáltatások felülbírálása jelenleg nem támogatott.  Ha vannak olyan szolgáltatások, amelyeket felül szeretne bírálni, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Beállítások és beállítások használata

Az [Alkalmazásbeállítások](./functions-how-to-use-azure-function-app-settings.md#settings) által meghatározott értékek egy `IConfiguration` példányban érhetők el, amely lehetővé teszi az alkalmazás-beállítási értékek olvasását az indítási osztályban.

A példány értékeit egyéni típusba is kinyerheti `IConfiguration` . Ha az Alkalmazásbeállítások értékeit egyéni típusra másolja, a szolgáltatás egyszerűen tesztelhető, így ezek az értékek injektálható. A konfigurációs példányba beolvasott beállításoknak egyszerű kulcs/érték pároknak kell lenniük.

Vegye figyelembe a következő osztályt, amely egy konzisztens nevű tulajdonságot tartalmaz egy alkalmazás-beállítással:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Valamint egy olyan `local.settings.json` fájl, amely az egyéni beállítást a következőképpen strukturálhatja:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

A `Startup.Configure` metódusból kinyerheti a példány értékeit az `IConfiguration` Egyéni típusba a következő kód használatával:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Meghívja `Bind` azokat az értékeket, amelyek a konfigurációból az egyéni példányba egyező tulajdonságokat tartalmaznak. A beállítások példány mostantól elérhető a NOB-tárolóban egy függvénybe való behelyezéshez.

A beállítások objektumot az általános felület egy példánya fecskendezi a függvénybe `IOptions` . A `Value` tulajdonság használatával érheti el a konfigurációban található értékeket.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

A beállításokkal kapcsolatos további részletekért tekintse meg a [ASP.net Core beállítások mintáját](/aspnet/core/fundamentals/configuration/options) .

## <a name="using-aspnet-core-user-secrets"></a>ASP.NET Core felhasználói titkok használata

Helyi fejlesztés esetén ASP.NET Core biztosít egy [titkos kezelői eszközt](/aspnet/core/security/app-secrets#secret-manager) , amely lehetővé teszi a titkos adatok tárolását a projekt gyökerén kívül. Ez kevésbé valószínű, hogy a titkokat véletlenül véglegesítik a verziókövetés. A Azure Functions Core Tools (3.0.3233 vagy újabb verzió) automatikusan beolvassa a ASP.NET Core Secret Manager által létrehozott titkokat.

Ha .NET Azure Functions projektet szeretne konfigurálni a felhasználói titkok használatára, futtassa a következő parancsot a projekt gyökerében.

```bash
dotnet user-secrets init
```

Ezután használja a `dotnet user-secrets set` parancsot a titkok létrehozásához vagy frissítéséhez.

```bash
dotnet user-secrets set MySecret "my secret value"
```

A felhasználói titkok értékének a Function alkalmazás kódjában való eléréséhez használja a következőt: `IConfiguration` vagy `IOptions` .

## <a name="customizing-configuration-sources"></a>Konfigurációs források testreszabása

> [!NOTE]
> A konfiguráció forrásának testreszabása Azure Functions gazdagép 2.0.14192.0 és 3.0.14191.0-verziójának elején érhető el.

További konfigurációs források megadásához bírálja felül a `ConfigureAppConfiguration` metódust a Function app `StartUp` osztályában.

Az alábbi minta egy alapértékből és egy opcionális, a környezetre jellemző alkalmazás-beállítási fájlokból származó konfigurációs értékeket is felvesz.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Adja hozzá a konfigurációs szolgáltatókat a `ConfigurationBuilder` tulajdonságához `IFunctionsConfigurationBuilder` . További információ a konfigurációs szolgáltatók használatáról: [konfiguráció a ASP.net Coreban](/aspnet/core/fundamentals/configuration/#configuration-providers).

A a `FunctionsHostBuilderContext` következőből származik: `IFunctionsConfigurationBuilder.GetContext()` . Ezzel a kontextussal kérheti le az aktuális környezeti nevet, és feloldja a konfigurációs fájlok helyét a Function app mappában.

Alapértelmezés szerint a konfigurációs fájlok (például *aappsettings.js* ) nem másolódnak automatikusan a Function alkalmazás kimeneti mappájába. Frissítse a *. csproj* fájlt, hogy az megfeleljen a következő mintának a fájlok másolásának biztosításához.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> A felhasználási vagy prémium csomagokban futó Function apps esetében az eseményindítókban használt konfigurációs értékek módosítása a skálázási hibákhoz vezethet. Ha az osztály ezen tulajdonságok bármelyikét megváltoztatja, a `FunctionsStartup` Function app indítási hibát eredményez.

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

- [A Function-alkalmazás figyelése](functions-monitoring.md)
- [Ajánlott eljárások a functions szolgáltatáshoz](functions-best-practices.md)
