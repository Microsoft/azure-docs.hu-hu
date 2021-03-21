---
title: A .NET-alkalmazások Snapshot Debugger engedélyezése az Azure Service Fabric, a Cloud Service és a Virtual Machines szolgáltatásban | Microsoft Docs
description: A .NET-alkalmazások Snapshot Debugger engedélyezése az Azure Service Fabric, a Cloud Service és a Virtual Machines
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 4bccc2922cf20262149ef54fbe2a1a821d9551ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673501"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>A .NET-alkalmazások Snapshot Debugger engedélyezése az Azure Service Fabric, a Cloud Service és a Virtual Machines

Ha a ASP.NET vagy a ASP.NET Core-alkalmazás a Azure App Serviceban fut, erősen ajánlott [a Snapshot Debugger engedélyezése a Application Insights-portál lapon](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Ha azonban az alkalmazáshoz testre szabott Snapshot Debugger konfigurációra vagy a .NET Core előzetes verziójára van szükség, akkor ezt az utasítást az [Application Insights-portálon keresztüli engedélyezésre](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)vonatkozó utasításokon ***felül*** kell követni.

Ha az alkalmazás az Azure Service Fabric, a Cloud Service, a Virtual Machines vagy a helyszíni gépeken fut, a következő utasításokat kell használni. 

## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Pillanatkép-gyűjtemény konfigurálása ASP.NET-alkalmazásokhoz

1. Ha még nem tette meg, [engedélyezze Application Insights a webalkalmazásban](./asp-net.md).

2. Adja meg a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. Ha szükséges, testreszabhatja a [ApplicationInsights.confighoz ](./configuration-with-applicationinsights-config.md)hozzáadott Snapshot Debugger konfigurációt. Az alapértelmezett Snapshot Debugger konfiguráció többnyire üres, és az összes beállítás megadása nem kötelező. Az alábbi példa az alapértelmezett konfigurációval egyenértékű konfigurációt mutatja be:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. A pillanatképek csak a Application Insights jelentett kivételeken lesznek összegyűjtve. Bizonyos esetekben (például a .NET platform régebbi verzióiban) előfordulhat, hogy a kivételek [gyűjteményét be kell állítania](./asp-net-exceptions.md#exceptions) a portálon található pillanatképekkel kapcsolatos kivételek megtekintéséhez.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Pillanatkép-gyűjtemény konfigurálása a ASP.NET Core 2,0-es vagy újabb verzióját használó alkalmazásokhoz

1. Ha még nem tette meg, [engedélyezze Application Insights a ASP.net Core webalkalmazásban](./asp-net-core.md).

    > [!NOTE]
    > Győződjön meg arról, hogy az alkalmazás a Microsoft. ApplicationInsights. AspNetCore csomag 2.1.1-es vagy újabb verziójára hivatkozik.

2. Adja meg a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. Módosítsa az alkalmazás `Startup` osztályát a Snapshot Collector telemetria-processzorának hozzáadásához és konfigurálásához.
    1. Ha a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomag 1.3.5 vagy újabb verziója használatban van, akkor adja hozzá a következő using utasítást a következőhöz: `Startup.cs` .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Adja hozzá a következőt a ConfigureServices metódus végén a osztályban a `Startup` ben `Startup.cs` .

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Ha a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomag 1.3.4 vagy újabb verziója használatban van, akkor adja hozzá a következő using utasítást a következőhöz: `Startup.cs` .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```

       Adja hozzá a következő `SnapshotCollectorTelemetryProcessorFactory` osztályt az `Startup` osztályhoz.

       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;

               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;

               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Adja hozzá a `SnapshotCollectorConfiguration` és a `SnapshotCollectorTelemetryProcessorFactory` szolgáltatásokat az indítási folyamathoz:

        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Ha szükséges, testreszabhatja a Snapshot Debugger konfigurációt úgy, hogy hozzáad egy SnapshotCollectorConfiguration szakaszt a appsettings.jshoz. A Snapshot Debugger konfigurációjának összes beállítása nem kötelező. Az alábbi példa az alapértelmezett konfigurációval egyenértékű konfigurációt mutatja be:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Pillanatkép-gyűjtemény konfigurálása más .NET-alkalmazásokhoz

1. Ha az alkalmazás még nincs felépítve Application Insightsval, [a Application Insights engedélyezésével és a kialakítási kulcs beállításával](./windows-desktop.md)kezdheti meg az első lépéseket.

2. Adja hozzá a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazásban.

3. A pillanatképek csak a Application Insights jelentett kivételeken lesznek összegyűjtve. Előfordulhat, hogy módosítania kell a kódot a jelentéséhez. A kivételek kezelésére szolgáló kód az alkalmazás struktúrájától függ, de az alábbi példa:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Következő lépések

- Adatforgalom létrehozása az alkalmazás számára, amely kivételt indíthat. Ezután várjon 10 – 15 percet a pillanatképek Application Insights példányba való elküldésekor.
- A Azure Portal található [Pillanatképek](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) .
- Snapshot Debugger problémák elhárításával kapcsolatos segítségért lásd: [Snapshot Debugger hibaelhárítás](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

