---
title: Azure-Application Insights ASP.NET Core alkalmazásokhoz | Microsoft Docs
description: ASP.NET Core webalkalmazások figyelése a rendelkezésre állás, a teljesítmény és a használat érdekében.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: be4cbe5f95fa6901ae4299662750c232d4700a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711501"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights ASP.NET Core alkalmazásokhoz

Ez a cikk azt ismerteti, hogyan engedélyezhető a Application Insights [ASP.net Core](/aspnet/core) alkalmazáshoz. A cikkben szereplő utasítások elvégzése után a Application Insights összegyűjti a kérelmeket, a függőségeket, a kivételeket, a teljesítményszámlálókat, a szívveréseket és a naplókat a ASP.NET Core alkalmazásból.

Az itt használt példa egy [MVC-alkalmazás](/aspnet/core/tutorials/first-mvc-app) , amely a célokat célozza `netcoreapp3.0` . Ezek az utasítások az összes ASP.NET Core alkalmazásra alkalmazhatók. Ha a [Worker szolgáltatást](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)használja, használja az [itt](./worker-service.md)található utasításokat.

## <a name="supported-scenarios"></a>Támogatott esetek

A [ASP.NET Core Application INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -val a futtatásuk helyétől függetlenül nyomon követheti az alkalmazásait. Ha az alkalmazás fut, és hálózati kapcsolattal rendelkezik az Azure-hoz, a telemetria gyűjthet. A Application Insights figyelése mindenhol támogatott a .NET Core-ban. Támogatás kiterjed a következőkre:
* **Operációs rendszer**: Windows, Linux vagy Mac.
* **Üzemeltetési módszer**: folyamatban vagy folyamatban.
* **Üzembe helyezési módszer**: keretrendszer függő vagy önálló.
* **Webkiszolgáló**: IIS (Internet Information Server) vagy vércse.
* **Üzemeltetési platform**: az Azure app Service, az Azure VM, a Docker, az Azure Kubernetes Service (ak) Web Apps funkciója stb.
* **.Net Core-verzió**: az összes hivatalosan [támogatott](https://dotnet.microsoft.com/download/dotnet-core) .net Core-verzió.
* **Ide**: Visual Studio, vs Code vagy Command line.

> [!NOTE]
> ASP.NET Core 3,1 [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) vagy újabb verzió szükséges.

## <a name="prerequisites"></a>Előfeltételek

- Működő ASP.NET Core alkalmazás. Ha ASP.NET Core alkalmazást kell létrehoznia, kövesse ezt az [ASP.net Core oktatóanyagot](/aspnet/core/getting-started/).
- Érvényes Application Insights kialakítási kulcs. Ez a kulcs szükséges ahhoz, hogy bármilyen telemetria küldjön a Application Insightsnak. Ha létre kell hoznia egy új Application Insights-erőforrást a kialakítási kulcs beszerzéséhez, tekintse meg a [Application Insights erőforrás létrehozása](./create-new-resource.md)című témakört.

> [!IMPORTANT]
> Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=net) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.


## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights kiszolgálóoldali telemetria engedélyezése (Visual Studio)

Visual Studio for Mac használja a [manuális útmutatót](#enable-application-insights-server-side-telemetry-no-visual-studio). Ezt az eljárást csak a Visual Studio Windows-verziója támogatja.

1. Nyissa meg a projektjét a Visual Studióban.

    > [!TIP]
    > Ha szeretné, beállíthatja a projekthez tartozó verziókövetés beállítását, hogy nyomon követhesse a Application Insights által végrehajtott összes módosítást. A verziókövetés engedélyezéséhez válassza a **fájl**  >  **Hozzáadás a forrás vezérlőelemhez** lehetőséget.

2. Válassza a **projekt**  >  **Hozzáadás Application Insights telemetria** elemet.

3. Válassza az első **lépések** lehetőséget. A kiválasztott szöveg változhat a Visual Studio verziójától függően. Egyes korábbi verziók esetén az **ingyenes indítás** gomb használható.

4. Válassza ki előfizetését. Ezután válassza az **erőforrás**-  >  **regisztráció** lehetőséget.

5. Miután hozzáadta Application Insights a projekthez, ellenőrizze, hogy az SDK legújabb stabil kiadását használja-e. Ugrás a **Project**  >  **NuGet-csomagok kezelése**  >  **Microsoft. ApplicationInsights. AspNetCore**. Ha szükséges, válassza a **frissítés** lehetőséget.

     ![Képernyőfelvétel: a Application Insights csomag kiválasztása a frissítéshez](./media/asp-net-core/update-nuget-package.png)

6. Ha követte a választható tippet, és hozzáadta a projektet a forrás vezérlőelemhez, lépjen a   >  **Team Explorer**  >  **változások** megtekintése elemre. Ezután válassza ki az egyes fájlokat, és tekintse meg a Application Insights telemetria által végzett módosítások különbségeit.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Application Insights kiszolgálóoldali telemetria engedélyezése (nincs Visual Studio)

1. Telepítse a [ASP.NET Core Application INSIGHTS SDK NuGet-csomagot](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Javasoljuk, hogy mindig a legújabb stabil verziót használja. Az SDK teljes kibocsátási megjegyzéseit a [nyílt forráskódú GitHub-](https://github.com/Microsoft/ApplicationInsights-dotnet/releases)tárházban találja.

    A következő mintakód a projekt fájljában felvenni kívánt módosításokat mutatja be `.csproj` .

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.16.0" />
        </ItemGroup>
    ```

2. Adja hozzá `services.AddApplicationInsightsTelemetry();` a `ConfigureServices()` metódust az `Startup` osztályban, ahogy az alábbi példában is látható:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Állítsa be a kialakítási kulcsot.

    Bár a kialakítási kulcsot argumentumként is megadhatja `AddApplicationInsightsTelemetry` , javasoljuk, hogy adja meg a kialakítási kulcsot a konfigurációban. Az alábbi mintakód bemutatja, hogyan adható meg a kialakítási kulcs a alkalmazásban `appsettings.json` . Győződjön meg arról, hogy `appsettings.json` a közzététel során a rendszer átmásolja az alkalmazás gyökerének mappájába.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Azt is megteheti, hogy a rendszerállapot-kulcsot a következő környezeti változók egyikében is megadja:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Például:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` általában az [Azure web Appsban](./azure-web-apps.md?tabs=net)használatos, de használható minden olyan helyen is, ahol ez az SDK támogatott. (Ha kód nélküli webalkalmazás-figyelést végez, akkor ezt a formátumot kell megadni, ha nem használ kapcsolati karakterláncokat.)

    A rendszerállapot-kulcsok beállítása helyett már használhatja a [kapcsolatok karakterláncait](./sdk-connection-string.md?tabs=net)is.

    > [!NOTE]
    > A kód WINS-ben megadott rendszerállapot-kulcs a környezeti változón keresztül `APPINSIGHTS_INSTRUMENTATIONKEY` , amely más beállításokon keresztül nyerhető.

### <a name="user-secrets-and-other-configuration-providers"></a>Felhasználói titkok és egyéb konfigurációs szolgáltatók

Ha a kialakítási kulcsot ASP.NET Core felhasználói titokban szeretné tárolni, vagy egy másik konfigurációs szolgáltatótól kéri le, akkor a túlterhelés paraméterrel is felhasználható `Microsoft.Extensions.Configuration.IConfiguration` . Például: `services.AddApplicationInsightsTelemetry(Configuration);`.
A Microsoft. ApplicationInsights. AspNetCore [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)-verziótól kezdődően a hívás `services.AddApplicationInsightsTelemetry()` automatikusan beolvassa a kialakítási kulcsot `Microsoft.Extensions.Configuration.IConfiguration` az alkalmazásból. Nem kell explicit módon megadnia a következőt: `IConfiguration` .

## <a name="run-your-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és tegye a kérelmeket. A telemetria-nek most Application Insightsre kell irányulnia. A Application Insights SDK automatikusan begyűjti a bejövő webes kérelmeket az alkalmazásba, valamint a következő telemetria is.

### <a name="live-metrics"></a>Élő metrikák

Az [élő metrikák](./live-stream.md) segítségével gyorsan ellenőrizheti, hogy a Application Insights figyelése megfelelően van-e konfigurálva. Habár néhány percet is igénybe vehet, amíg a telemetria elindul a Portálon és az elemzésekben, az élő metrikák közel valós időben mutatják be a futó folyamat CPU-használatát. Más telemetria is megjeleníthet, például a kérelmeket, a függőségeket, a Nyomkövetéseket stb.

### <a name="ilogger-logs"></a>ILogger-naplók

Az alapértelmezett beállítás a `ILogger` súlyossági és a feletti naplókat gyűjti `Warning` . Ezt a konfigurációt [testre lehet szabni](#how-do-i-customize-ilogger-logs-collection).

### <a name="dependencies"></a>Függőségek

A függőségi gyűjtemény alapértelmezés szerint engedélyezve van. [Ez](asp-net-dependencies.md#automatically-tracked-dependencies) a cikk az automatikusan összegyűjtött függőségeket ismerteti, valamint a manuális nyomon követésre szolgáló lépéseket is tartalmaz.

### <a name="performance-counters"></a>Teljesítményszámlálók

A [teljesítményszámlálók](./performance-counters.md) támogatása ASP.net Core korlátozott:

* Az SDK 2.4.1-es és újabb verziói a teljesítményszámlálókat gyűjtik, ha az alkalmazás az Azure Web Apps (Windows) rendszeren fut.
* A 2.7.1-es és újabb verziójú SDK-verziók akkor gyűjtik a teljesítményszámlálókat, ha az alkalmazás Windows-és célhelyeken `NETSTANDARD2.0` vagy később fut.
* A .NET-keretrendszert célzó alkalmazások esetében az SDK összes verziója támogatja a teljesítményszámlálókat.
* A 2.8.0 és újabb verziójú SDK-verziók támogatják a CPU-/memória-számlálót a Linux rendszerben. A Linux nem támogatja a többi számlálót. A Linux-(és más nem Windows-környezetekben található) rendszerszámlálók használatának ajánlott módja a [EventCounters](#eventcounter) használata.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` Alapértelmezés szerint engedélyezve van. A [EventCounter](eventcounters.md) -oktatóanyag útmutatást tartalmaz a gyűjteni kívánt számlálók listájának konfigurálásához.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Ügyféloldali telemetria engedélyezése webalkalmazásokhoz

Az előző lépések elegendő segítséget nyújtanak a kiszolgálóoldali telemetria gyűjtésének megkezdéséhez. Ha az alkalmazás ügyféloldali összetevőket tartalmaz, kövesse a következő lépéseket a [használati telemetria](./usage-overview.md)gyűjtésének megkezdéséhez.

1. A alkalmazásban `_ViewImports.cshtml` adja hozzá a befecskendezést:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. A alkalmazásban `_Layout.cshtml` szúrja be a `HtmlHelper` szakaszt a szakasz végén, `<head>` de minden más parancsfájl előtt. Ha az oldalról bármilyen egyéni JavaScript-telemetria szeretne jelenteni, szúrja be azt a kódrészlet után:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Másik lehetőségként használhatja `FullScript` a `ScriptBody` -t az SDK v 2.14-as verziójában. Akkor használja ezt a beállítást, ha a `<script>` címkét a tartalom biztonsági házirendjének beállításához kell vezérelni:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

A `.cshtml` korábban hivatkozott fájlnevek egy alapértelmezett MVC-alkalmazás sablonból származnak. Végül, ha megfelelően szeretné engedélyezni az ügyféloldali figyelést az alkalmazáshoz, a JavaScript-kódrészletnek szerepelnie kell a `<head>` figyelni kívánt alkalmazás minden oldalának szakaszában. Ezt a célt ehhez az alkalmazás-sablonhoz a JavaScript-kódrészlet hozzáadásával érheti el `_Layout.cshtml` . 

Ha a projekt nem tartalmazza `_Layout.cshtml` , az [ügyféloldali figyelést](./website-monitoring.md)továbbra is hozzáadhatja. Ezt úgy teheti meg, hogy hozzáadja a JavaScript-kódrészletet egy ezzel egyenértékű fájlhoz, amely az `<head>` alkalmazáson belüli összes oldalt vezérli. Emellett a kódrészletet több oldalra is hozzáadhatja, de a megoldás nehezen karbantartható, és általában nem ajánlott.

## <a name="configure-the-application-insights-sdk"></a>A Application Insights SDK konfigurálása

Az alapértelmezett konfiguráció módosításához testreszabhatja a ASP.NET Core Application Insights SDK-t. Előfordulhat, hogy a Application Insights ASP.NET SDK felhasználói megismerik a konfiguráció módosítással történő módosítását `ApplicationInsights.config` `TelemetryConfiguration.Active` . ASP.NET Core esetében szinte az összes konfigurációs módosítást az `ConfigureServices()` osztály metódusa végzi el `Startup.cs` , hacsak nem irányította másképpen. A következő fejezetei további információkat nyújtanak.

> [!NOTE]
> ASP.NET Core alkalmazásokban a konfiguráció módosításának módosítása `TelemetryConfiguration.Active` nem támogatott.

### <a name="using-applicationinsightsserviceoptions"></a>A ApplicationInsightsServiceOptions használata

A következő példához hasonlóan módosíthat néhány gyakori beállítást `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry` , ahogy az alábbi példában is látható:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

A beállítások teljes listája `ApplicationInsightsServiceOptions`

|Beállítás | Leírás | Alapértelmezett
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Engedélyezés/letiltás `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Engedélyezés/letiltás `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Engedélyezés/letiltás `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Engedélyezés/letiltás `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Engedélyezés/letiltás `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Engedélyezés/letiltás `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | LiveMetrics funkció engedélyezése/letiltása | true
|EnableAdaptiveSampling | Adaptív mintavételezés engedélyezése/letiltása | true
|EnableHeartbeat | A szívverések funkció engedélyezése/letiltása, amely rendszeres időközönként (15 perces alapértelmezett) a "HeartbeatState" nevű egyéni metrikát küldi el a (z), például a .NET-es verzióval, az Azure-környezettel kapcsolatos információkkal, ha vannak ilyenek, stb. | true
|AddAutoCollectedMetricExtractor | Az AutoCollectedMetrics Extractor engedélyezése/letiltása, amely egy olyan TelemetryProcessor, amely előre összevont metrikákat küld a kérelmek/függőségek számára a mintavétel megkezdése előtt. | true
|RequestCollectionOptions.TrackExceptions | Engedélyezheti vagy letilthatja a nem kezelt kivételek nyomon követését a kérelmek gyűjtési modulja által. | hamis a NETSTANDARD 2.0-ban (mivel a kivételeket a ApplicationInsightsLoggerProvider követte nyomon), ellenkező esetben igaz.
|EnableDiagnosticsTelemetryModule | Engedélyezés/letiltás `DiagnosticsTelemetryModule` . Ha letiltja ezt a beállítást, a rendszer figyelmen kívül hagyja a következő beállításokat: `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

A legnaprakészebb listához tekintse [meg `ApplicationInsightsServiceOptions` a konfigurálható beállításokat](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) .

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150--above"></a>A Microsoft. ApplicationInsights. AspNetCore SDK-beli 2.15.0-& konfigurációs javaslata

A Microsoft. ApplicationInsights. AspNetCore SDK [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0)verziójától kezdődően a következő javaslat szerint kell konfigurálni az összes rendelkezésre álló beállítást `ApplicationInsightsServiceOptions` , beleértve az alkalmazások példányát használó instrumentationkey is `IConfiguration` . A beállításoknak a "ApplicationInsights" szakasz alá kell esnie, ahogy az alábbi példában is látható. A következő szakasz a rendszerállapot-kulcs konfigurálásának appsettings.js, valamint az adaptív mintavételi és teljesítményszámláló-gyűjtemény letiltására is lehetőséget nyújt.

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

Ha a `services.AddApplicationInsightsTelemetry(aiOptions)` használatban van, a felülbírálja a beállításait `Microsoft.Extensions.Configuration.IConfiguration` .

### <a name="sampling"></a>Mintavételezés

A ASP.NET Core Application Insights SDK a rögzített sebességű és az adaptív mintavételezést is támogatja. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van.

További információ: [adaptív mintavételezés konfigurálása ASP.net Core alkalmazásokhoz](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

Használjon [telemetria-inicializálást](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) , ha további információkkal szeretné bővíteni a telemetria.

Vegyen fel minden újat a `TelemetryInitializer` `DependencyInjection` tárolóba, ahogy az a következő kódban is látható. Az SDK automatikusan felveszi `TelemetryInitializer` a tárolóba felvett bármelyiket `DependencyInjection` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` egyszerű inicializálók esetén működik. Mások számára a következők szükségesek: `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
### <a name="removing-telemetryinitializers"></a>TelemetryInitializers eltávolítása

A telemetria inicializálók alapértelmezés szerint jelennek meg. Az összes vagy adott telemetria-inicializáló eltávolításához használja a következő mintát a hívása *után* `AddApplicationInsightsTelemetry()` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // Remove a specific built-in telemetry initializer
    var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                        (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
    if (tiToRemove != null)
    {
        services.Remove(tiToRemove);
    }

    // Remove all initializers
    // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
    services.RemoveAll(typeof(ITelemetryInitializer));
}
```

### <a name="adding-telemetry-processors"></a>Telemetria-processzorok hozzáadása

Egyéni telemetria-processzorokat a `TelemetryConfiguration` bővítmény metódusának használatával adhat hozzá `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . A [speciális szűrési helyzetekben](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)telemetria processzorokat használ. Használja az alábbi példát.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Alapértelmezett TelemetryModules konfigurálása vagy eltávolítása

A Application Insights telemetria-modulok használatával automatikusan gyűjti az adott számítási feladatokhoz tartozó hasznos telemetria, anélkül, hogy a felhasználó manuális követését kellene használnia.

A következő automatikus gyűjtési modulok alapértelmezés szerint engedélyezve vannak. Ezeknek a moduloknak a feladata a telemetria automatikus gyűjtése. Az alapértelmezett viselkedés megváltoztatásához letilthatja vagy konfigurálhatja őket.

* `RequestTrackingTelemetryModule` – RequestTelemetry gyűjt a bejövő webes kérelmekből.
* `DependencyTrackingTelemetryModule` – A kimenő HTTP-hívások és az SQL-hívások [DependencyTelemetry](./asp-net-dependencies.md) gyűjti.
* `PerformanceCollectorModule` – A Windows PerformanceCounters gyűjti.
* `QuickPulseTelemetryModule` – Az élő metrika portálon megjelenített telemetria gyűjti.
* `AppServicesHeartbeatTelemetryModule` -Gyűjti a szívveréseket (amelyek egyéni mérőszámként lesznek elküldve), Azure App Service környezettel kapcsolatban, ahol az alkalmazás fut.
* `AzureInstanceMetadataTelemetryModule` -Gyűjti a szívveréseket (amelyek egyéni mérőszámként lesznek elküldve) az Azure-beli virtuálisgép-környezetről, ahol az alkalmazás üzemeltetve van.
* `EventCounterCollectionModule` – Gyűjti a [EventCounters.](eventcounters.md) Ez a modul egy új szolgáltatás, amely az SDK 2.8.0-es és újabb verzióiban érhető el.

Az alapértelmezett beállítások konfigurálásához `TelemetryModule` használja a bővítmény metódusát a `ConfigureTelemetryModule<T>` `IServiceCollection` következő példában látható módon:.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

A 2.12.2-verziótól kezdődően [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) egyszerűen letiltható az alapértelmezett modulok letiltására szolgáló lehetőség.

### <a name="configuring-a-telemetry-channel"></a>Telemetria-csatorna konfigurálása

Az alapértelmezett [telemetria csatorna](./telemetry-channels.md) `ServerTelemetryChannel` . A következő példában látható módon felülbírálhatja azt.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetria dinamikus letiltása

Ha a telemetria feltételesen és dinamikusan szeretné letiltani, akkor előfordulhat, hogy `TelemetryConfiguration` a kódban bárhol feloldja a példányt ASP.net Core függőségi injektálási tárolóval, és beállítja `DisableTelemetry` a jelölőt.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

A fenti nem akadályozza meg, hogy az automatikus gyűjtési modulok telemetria gyűjtsenek. A fenti megközelítéssel csak a telemetria küldése Application Insights lesz letiltva. Ha egy adott automatikus gyűjtési modul nem szükséges, érdemes [eltávolítani a telemetria modult](#configuring-or-removing-default-telemetrymodules) .

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights támogatja a 3. X ASP.NET Core?

Igen. Frissítsen [Application INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -ra ASP.net Core 2.8.0 vagy újabb verzióra. Az SDK régebbi verziói nem támogatják a 3. X ASP.NET Core.

Emellett, ha [itt](#enable-application-insights-server-side-telemetry-visual-studio)Visual Studio-alapú utasításokat használ, frissítsen a visual Studio 2019 (16.3.0) legújabb verziójára a bevezetéshez. A Visual Studio korábbi verziói nem támogatják a ASP.NET Core 3. X alkalmazások automatikus előkészítését.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követhetem nyomon az automatikusan összegyűjtött telemetria?

Szerezzen be egy példányt a `TelemetryClient` konstruktor injekció használatával, és hívja meg a szükséges `TrackXXX()` metódust. Nem javasoljuk, hogy új `TelemetryClient` vagy `TelemetryConfiguration` példányokat hozzon létre egy ASP.net Core alkalmazásban. Az egy példánya `TelemetryClient` már regisztrálva van a `DependencyInjection` tárolóban, amely a `TelemetryConfiguration` többi telemetria együtt osztozik. Az új `TelemetryClient` példányok létrehozása csak akkor javasolt, ha olyan konfigurációra van szükség, amely eltér a többi telemetria.

Az alábbi példa azt szemlélteti, hogyan követhet nyomon a vezérlő további telemetria.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

További információ a Application Insightsban található egyéni adatjelentésekről: [Application Insights egyéni metrikák API-referencia](./api-custom-events-metrics.md). Hasonló módszer használható egyéni metrikák küldésére Application Insights a [GETMETRIC API](./get-metric.md)használatával.

### <a name="how-do-i-customize-ilogger-logs-collection"></a>Hogyan testreszabja a ILogger-naplók gyűjteményét?

Alapértelmezés szerint a rendszer csak a súlyossági és a feletti naplókat rögzíti `Warning` automatikusan. Ennek a viselkedésnek a megváltoztatásához explicit módon bírálja felül a szolgáltató naplózási konfigurációját az `ApplicationInsights` alább látható módon.
A következő konfiguráció lehetővé teszi, hogy a ApplicationInsights az összes súlyossági naplót rögzítse `Information` .

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

Fontos megjegyezni, hogy a következő nem eredményezi a ApplicationInsights-szolgáltató számára a `Information` naplók rögzítését. Ennek az az oka, hogy az SDK egy alapértelmezett naplózási szűrőt ad hozzá, `ApplicationInsights` amely arra utasítja a rögzítést, hogy csak a `Warning` és annál újabb Emiatt explicit felülbírálásra van szükség a ApplicationInsights.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

További információ a [ILogger-konfigurációról](ilogger.md#control-logging-level).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Egyes Visual Studio-sablonok a IWebHostBuilder UseApplicationInsights () bővítményi metódusát használták a Application Insights engedélyezéséhez. Még érvényes ez a használat?

Habár a bővítmény módszer `UseApplicationInsights()` továbbra is támogatott, a Application INSIGHTS SDK-beli 2.8.0-verzióban elavultként van megjelölve. Az SDK a következő főverziójában el lesz távolítva. A Application Insights-telemetria engedélyezésének ajánlott módja a használata, `AddApplicationInsightsTelemetry()` mert túlterheléseket biztosít a konfiguráció szabályozásához. Emellett ASP.NET Core 3. X alkalmazásban `services.AddApplicationInsightsTelemetry()` az alkalmazás-felismerések engedélyezése egyetlen módszer.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core alkalmazást telepítem Web Apps. Továbbra is engedélyezni kell a Application Insights bővítményt a Web Appsból?

Ha az SDK-t a jelen cikkben látható módon telepíti a Build időpontban, nem kell engedélyeznie a [Application Insights bővítményt](./azure-web-apps.md) a app Service portálról. Ha a bővítmény telepítve van, akkor a rendszer akkor is vissza fog térni, ha észleli, hogy az SDK már hozzá van adva az alkalmazáshoz. Ha engedélyezi a Application Insights a bővítményből, nem kell telepítenie és frissítenie az SDK-t. Ha azonban a jelen cikkben szereplő utasítások alapján engedélyez Application Insights, nagyobb rugalmasságot biztosít, mivel:

   * Application Insights telemetria továbbra is a következővel fog működni:
       * Minden operációs rendszer, beleértve a Windows, a Linux és a Mac rendszereket is.
       * Az összes közzétételi mód, beleértve az önálló vagy a keretrendszertől függőket is.
       * Minden cél keretrendszer, beleértve a teljes .NET-keretrendszert is.
       * Minden üzemeltetési lehetőség, beleértve a Web Apps, a virtuális gépeket, a Linuxot, a tárolókat, az Azure Kubernetes szolgáltatást és a nem Azure-beli üzemeltetést.
       * Az összes .NET Core verzió, beleértve az előzetes verziókat is.
   * A Visual studióból történő hibakeresés során a telemetria helyileg is megtekintheti.
   * Az API használatával nyomon követheti a további egyéni telemetria `TrackXXX()` .
   * Teljes hozzáférése van a konfigurációhoz.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Engedélyezhető Application Insights-figyelés az olyan eszközökkel, mint a Állapotmonitor?

Nem. A [Állapotmonitor](./monitor-performance-live-website-now.md) és a [Állapotmonitor v2](./status-monitor-v2-overview.md) jelenleg csak a 4. x ASP.net támogatja.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Ha Linuxon futtatom az alkalmazást, az összes funkció támogatott?

Igen. Az SDK funkcióinak támogatása minden platformon azonos, a következő kivételekkel:

* Az SDK az [eseménynaplókat](./eventcounters.md) a Linux rendszeren gyűjti [, mivel a teljesítményszámlálók csak](./performance-counters.md) a Windows rendszerben támogatottak. A legtöbb metrika ugyanaz.
* Annak ellenére `ServerTelemetryChannel` , hogy alapértelmezés szerint engedélyezve van, ha az alkalmazás Linux vagy MacOS rendszeren fut, a csatorna nem hoz létre automatikusan helyi tárolási mappát, hogy a telemetria átmenetileg megmaradjanak, ha hálózati problémák vannak. Ennek a korlátozásnak a miatt a telemetria elvész, ha ideiglenes hálózati vagy kiszolgálói problémák merülnek fel. A probléma megkerüléséhez konfigurálja a csatorna helyi mappáját:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

Ez a korlátozás nem alkalmazható a [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) és az újabb verziókra.

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Támogatott ez az SDK az új .NET Core 3. X feldolgozó szolgáltatás-sablon alkalmazásaihoz?

Ehhez az SDK `HttpContext` -hoz szükséges, ezért nem működik semmilyen nem http-alkalmazásban, beleértve a .net Core 3. X Worker Service-alkalmazásokat. Tekintse meg [ezt](worker-service.md) a dokumentumot, amely lehetővé teszi az Application bepillantást az alkalmazásokban az újonnan kiadott Microsoft. ApplicationInsights. WorkerService SDK használatával.

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

* [Olvassa el és járuljon hozzá a kódhoz](https://github.com/microsoft/ApplicationInsights-dotnet).

A legújabb frissítések és hibajavítások [olvassa el a kibocsátási megjegyzéseket](./release-notes.md).

## <a name="next-steps"></a>Következő lépések

* [Fedezze fel a felhasználói folyamatokat](./usage-flows.md) , hogy megtudja, hogyan navigálnak a felhasználók az alkalmazáson keresztül.
* [Egy pillanatkép-gyűjtemény konfigurálásával](./snapshot-debugger.md) megtekintheti a forráskód és a változók állapotát a kivétel pillanatában.
* [Az API használatával](./api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletes áttekintéséhez.
* A [rendelkezésre állási tesztek](./monitor-web-app-availability.md) segítségével folyamatosan, a világ minden pontján ellenőrizhető az alkalmazás.
* [Függőséginjektálás az ASP.NET Core-ban](/aspnet/core/fundamentals/dependency-injection)
