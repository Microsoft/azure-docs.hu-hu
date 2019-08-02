---
title: Azure Monitor – az Azure Application Insights felülbírálja az alapértelmezett SDK-végpontokat | Microsoft Docs
description: Az alapértelmezett Azure Application Insights SDK-végpontok módosítása olyan régiók esetében, mint a Azure Government.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: mbullwin
ms.openlocfilehash: 662594f3ef0e4d5598b52cb07006b513e7f3c35a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564971"
---
 # <a name="application-insights-overriding-default-endpoints"></a>Application Insights felülbírálja az alapértelmezett végpontokat

Ha Application Insightsről szeretne adatokat küldeni bizonyos régiókba, felül kell bírálnia az alapértelmezett végponti címeket. Az SDK-nak némileg eltérő módosításokra van szüksége, amelyek mindegyike a jelen cikkben szerepel. Ezek a változások megkövetelik a mintakód módosítását, valamint a `QuickPulse_Endpoint_Address`, `TelemetryChannel_Endpoint_Address`, és `Profile_Query_Endpoint_address` az adott régió tényleges végponti címeinek cseréjét. A cikk végén a végpontok címeire mutató hivatkozások találhatók, ahol ez a konfiguráció szükséges.

## <a name="sdk-code-changes"></a>Az SDK-kód módosításai

### <a name="net-with-applicationinsightsconfig"></a>.NET és applicationinsights. config

> [!NOTE]
> Az applicationinsights. config fájlt automatikusan felülírja a rendszer, amikor egy SDK-frissítést hajt végre. Az SDK-frissítés végrehajtása után mindenképpen adja meg újra a régió-specifikus végpontok értékeit.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="net-core"></a>.NET Core

Módosítsa a appSettings. JSON fájlt a projektben az alábbiak szerint a fő végpont beállításához:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Az élő metrikák és a profil lekérdezési végpontjának értékei csak kód használatával állíthatók be. Ha az összes végpont értékének alapértelmezett értékét szeretné felülbírálni a kódban, hajtsa végre a `ConfigureServices` következő módosításokat a `Startup.cs` fájl metódusában:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton(new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="java"></a>Java

Módosítsa a applicationinsights. xml fájlt az alapértelmezett végponti címek módosításához.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Módosítsa a `application.properties` fájlt, és adja hozzá a következőket:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

A végpontokat környezeti változók használatával is konfigurálhatja:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"INSTRUMENTATION_KEY"
      endpointUrl: "TelemetryChannel_Endpoint_Address"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Végpontok módosítását igénylő régiók

Jelenleg csak a végpontok módosítását igénylő régiók [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) és az [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Régió |  Végpont neve | Value |
|-----------------|:------------|:-------------|
| Azure China | Telemetria-csatorna | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (élő metrikák) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Profil lekérdezése |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Telemetria-csatorna |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (élő metrikák) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profil lekérdezése |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Ha jelenleg a ["API.applicationinsights.IO"-](https://dev.applicationinsights.io/
) n keresztül hozzáférő Application Insights Rest APIt használja, akkor a régió helyi végpontját kell használnia:

|Régió |  Végpont neve | Érték |
|-----------------|:------------|:-------------|
| Azure China | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Ezekben a régiókban **jelenleg nem támogatottak** a kód nélküli ügynök/bővítmény alapú figyelés az Azure app Services-ban. Amint ez a funkció elérhetővé válik, a cikk frissülni fog.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a Azure Government egyéni módosításaival kapcsolatban, tekintse meg az [Azure monitorozásával és](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)felügyeletével kapcsolatos részletes útmutatást.
- Ha többet szeretne megtudni az Azure China-ról, tekintse meg az [Azure China](https://docs.microsoft.com/azure/china/)forgatókönyvét.