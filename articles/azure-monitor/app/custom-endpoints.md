---
title: Azure Application Insights alapértelmezett SDK-végpontok felülbírálása
description: Az alapértelmezett Azure Monitor Application Insights SDK-végpontok módosítása olyan régiók esetében, mint például a Azure Government.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-js
ms.openlocfilehash: 13470017281ecfa616715777e78e446e71a08b0e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489803"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights felülbírálja az alapértelmezett végpontokat

Ha Application Insightsről szeretne adatokat küldeni bizonyos régiókba, felül kell bírálnia az alapértelmezett végponti címeket. Az SDK-nak némileg eltérő módosításokra van szüksége, amelyek mindegyike a jelen cikkben szerepel. Ezek a változások megkövetelik a mintakód módosítását, valamint a `QuickPulse_Endpoint_Address` ,, és az `TelemetryChannel_Endpoint_Address` `Profile_Query_Endpoint_address` adott régió tényleges végponti címeinek cseréjét. A cikk végén a végpontok címeire mutató hivatkozások találhatók, ahol ez a konfiguráció szükséges.

> [!NOTE]
> A [csatlakozási karakterláncok](./sdk-connection-string.md?tabs=net) az új előnyben részesített módszerek, amelyekkel egyéni végpontokat állíthat be Application Insightson belül.

---

## <a name="sdk-code-changes"></a>Az SDK-kód módosításai

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> A applicationinsights.config fájl automatikusan felül lesz írva, amikor egy SDK-frissítést hajt végre. Az SDK-frissítés végrehajtása után mindenképpen adja meg újra a régió-specifikus végpontok értékeit.

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

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Módosítsa a projekt appsettings.jsfájlját a következő módon a fő végpont beállításához:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Az élő metrikák és a profil lekérdezési végpontjának értékei csak kód használatával állíthatók be. Ha az összes végpont értékének alapértelmezett értékét szeretné felülbírálni a kódban, hajtsa végre a következő módosításokat a `ConfigureServices` fájl metódusában `Startup.cs` :

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

Azure Functions azt javasoljuk, hogy a függvény alkalmazási beállításaiban a [kapcsolódási karakterláncok](./sdk-connection-string.md?tabs=net) legyenek beállítva. Ha az alkalmazás beállításait a függvények ablaktáblán belül szeretné elérni, válassza a **Beállítások**  >  **konfigurációs**  >  **alkalmazás beállításai** lehetőséget. 

Név: `APPLICATIONINSIGHTS_CONNECTION_STRING` érték: `Connection String Value`

# <a name="java"></a>[Java](#tab/java)

Módosítsa az applicationinsights.xml fájlt az alapértelmezett végponti címnek a módosításához.

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

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

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

# <a name="javascript"></a>[JavaScript](#tab/js)

Az aktuális kódrészlet (lent) az "5", a verzió a kódrészletben (SV: "#") van kódolva, és az [aktuális verzió is elérhető a githubon](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey:"INSTRUMENTATION_KEY",
    endpointUrl: "TelemetryChannel_Endpoint_Address"
}});
</script>
```

> [!NOTE]
> Az olvashatóság és a lehetséges JavaScript-hibák csökkentése érdekében az összes lehetséges konfigurációs beállítás megjelenik a fenti kódrészlet kódjának egy új sorában, ha nem szeretné módosítani egy megjegyzéses sor értékét, akkor az eltávolítható.

# <a name="python"></a>[Python](#tab/python)

A opencensus-Python SDK betöltési végpontjának módosításával kapcsolatos útmutatásért forduljon a [opencensus-Python-](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py) tárházhoz.

---

## <a name="regions-that-require-endpoint-modification"></a>Végpontok módosítását igénylő régiók

Jelenleg csak a végpontok módosítását igénylő régiók [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) és az [Azure China](/azure/china/resources-developer-guide).

|Region |  Végpont neve | Érték |
|-----------------|:------------|:-------------|
| Azure China | Telemetria-csatorna | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (élő metrikák) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Profil lekérdezése |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Telemetria-csatorna |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (élő metrikák) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profil lekérdezése |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Ha jelenleg a "api.applicationinsights.io"-n keresztül hozzáférő [Application Insights Rest APIt](https://dev.applicationinsights.io/
) használja, akkor a régió helyi végpontját kell használnia:

|Region |  Végpont neve | Érték |
|-----------------|:------------|:-------------|
| Azure China | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Ezekben a régiókban **jelenleg nem támogatottak** a kód nélküli ügynök/bővítmény alapú figyelés az Azure app Services-ban. Amint ez a funkció elérhetővé válik, a cikk frissülni fog.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a Azure Government egyéni módosításaival kapcsolatban, tekintse meg az [Azure monitorozásával és](../../azure-government/compare-azure-government-global-azure.md#application-insights)felügyeletével kapcsolatos részletes útmutatást.
- Ha többet szeretne megtudni az Azure China-ról, tekintse meg az [Azure China](/azure/china/)forgatókönyvét.
