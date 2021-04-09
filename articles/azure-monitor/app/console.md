---
title: Azure-Application Insights a konzol alkalmazásaihoz | Microsoft Docs
description: Webes alkalmazások figyelése a rendelkezésre állás, a teljesítmény és a használat érdekében.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: aa39a1eca04621fc4db75f755402d3679403e814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920591"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights a .NET-konzol alkalmazásaihoz

[Application Insights](./app-insights-overview.md) lehetővé teszi a webalkalmazások figyelését a rendelkezésre állás, a teljesítmény és a használat érdekében.

[Microsoft Azure](https://azure.com)-előfizetésre van szüksége. Jelentkezzen be egy Microsoft-fiókval, amely lehet Windows, Xbox Live vagy más Microsoft Cloud Services. Előfordulhat, hogy a csapata szervezeti előfizetéssel rendelkezik az Azure-hoz: kérje meg a tulajdonost, hogy vegye fel Önt a Microsoft-fiók használatával.

> [!NOTE]
> *Javasoljuk* , hogy a [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot és a hozzá tartozó utasításokat [használja az összes](./worker-service.md) konzolos alkalmazáshoz. Ez a csomag célja [`NetStandard2.0`](/dotnet/standard/net-standard) , és így a .net Core 2,1-as vagy újabb verzióban, illetve a .NET-keretrendszer 4.7.2 vagy újabb verziókban használható.

## <a name="getting-started"></a>Első lépések

> [!IMPORTANT]
> Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=net) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.

* [Hozzon létre egy Application Insights-erőforrást](./create-new-resource.md) az [Azure Portalon](https://portal.azure.com). Az alkalmazás típusa mezőben válassza az **általános** lehetőséget.
* Végezze el a kialakítási kulcs másolását. Keresse meg a kulcsot a létrehozott új erőforrás **Essentials** legördülő menüjében.
* Telepítse a legújabb [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) csomagot.
* Adja meg a kialakítási kulcsot a kódban a telemetria követése előtt (vagy állítsa be APPINSIGHTS_INSTRUMENTATIONKEY környezeti változót). Ezután manuálisan nyomon követheti a telemetria, és megtekintheti a Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> A telemetria nincs azonnal elküldve. A telemetria elemek kötegelt és a ApplicationInsights SDK által küldött kötegek. A konzolos alkalmazásokban, amelyek közvetlenül a metódusok meghívása után lépnek fel `Track()` , a telemetria nem küldhetők el, kivéve, ha `Flush()` `Sleep` / `Delay` az alkalmazás kilép, amint az a jelen cikk későbbi [részében](#full-example) látható. `Sleep` a használata esetén nem szükséges `InMemoryChannel` . Aktív probléma merült fel, amely a következő `Sleep` nyomon követéséhez szükséges: [ApplicationInsights-DotNet/Issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* A [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) Package legújabb verziójának telepítése – automatikusan nyomon követi a http, az SQL vagy más külső függőségi hívásokat.

A kód vagy a fájl használatával Application Insights inicializálható és konfigurálható `ApplicationInsights.config` . Győződjön meg arról, hogy az inicializálás a lehető leghamarabb megtörténik. 

> [!NOTE]
> A **ApplicationInsights.configra** hivatkozó utasítások csak a .NET-keretrendszert célzó alkalmazásokra vonatkoznak, és a .net Core-alkalmazásokra nem érvényesek.

### <a name="using-config-file"></a>Konfigurációs fájl használata

Alapértelmezés szerint a Application Insights SDK `ApplicationInsights.config` a munkakönyvtárban keresi a fájlt a `TelemetryConfiguration` létrehozáskor

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

A konfigurációs fájl elérési útját is megadhatja.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

További információ: [konfigurációs fájl leírása](configuration-with-applicationinsights-config.md).

A konfigurációs fájlhoz a [Microsoft. ApplicationInsights. windowsserver](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) csomag legújabb verziójának telepítésével kaphat teljes példát. Itt látható a függőségi gyűjtemény **minimális** konfigurációja, amely egyenértékű a kóddal.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Telemetria-gyűjtemény konfigurálása a kódból
> [!NOTE]
> A konfigurációs fájl olvasása nem támogatott a .NET Core-ban. Érdemes lehet Application Insights SDK-t használni [a ASP.net Core](./asp-net-core.md)

* Az alkalmazás indítási létrehozása és konfigurálása során `DependencyTrackingTelemetryModule` a példánynak egyedinek kell lennie, és meg kell őrizni az alkalmazás élettartamát.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Általános telemetria-inicializálók hozzáadása

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Ha egyszerű konstruktorral hozta létre a konfigurációt `TelemetryConfiguration()` , engedélyeznie kell a korrelációs támogatást is. **Nem szükséges** , ha a konfigurációt beolvassa a fájlból, a használt vagy a elemet `TelemetryConfiguration.CreateDefault()` `TelemetryConfiguration.Active` .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Az [itt](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/) leírtak szerint érdemes lehet a teljesítményszámláló-gyűjtő modult is telepíteni és inicializálni.


#### <a name="full-example"></a>Teljes példa

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Következő lépések
* A [függőségek figyelésével](./asp-net-dependencies.md) ellenőrizheti, hogy a REST, az SQL vagy más külső erőforrások lassulnak-e.
* [Az API használatával](./api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletesebb áttekintéséhez.

