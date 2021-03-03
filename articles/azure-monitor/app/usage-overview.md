---
title: Használat elemzése az Azure Application Insights használatával | Microsoft docs
description: Ismerje meg a felhasználókat, és hogy mit csinálnak az alkalmazással.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 32c817fea00cfd28a3e0187cc7c581d828412c69
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726155"
---
# <a name="usage-analysis-with-application-insights"></a>Használatelemzés az Application Insights szolgáltatással

A webes vagy mobil alkalmazások mely funkciói a legnépszerűbbek? A felhasználók a céljaikat az alkalmazással érik el? Kiesnek bizonyos pontokon, és később visszatérnek?  Az [Azure Application Insights](./app-insights-overview.md) segítségével hatékony információkhoz juthat az alkalmazás használatáról. Minden alkalommal, amikor frissíti az alkalmazást, megvizsgálhatja, hogy milyen jól működik a felhasználók számára. Ezzel az ismerettel a következő fejlesztési ciklusokra vonatkozó adatvezérelt döntéseket hozhat.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Telemetria küldése az alkalmazásból

A legjobb megoldás a Application Insights telepítésével érhető el az App Server kódjában és a weblapjain. Az alkalmazás ügyfél-és kiszolgáló-összetevői telemetria küldenek a Azure Portalnak elemzés céljából.

1. **Kiszolgáló kódja:** Telepítse a megfelelő modult a [ASP.net](./asp-net.md), az [Azure](./app-insights-overview.md)-ra, a [Java](./java-get-started.md)-ra, a [Node.jsra](./nodejs.md)vagy [más](./platforms.md) alkalmazásra.

    * *Nem szeretné telepíteni a kiszolgálói kódot? Egyszerűen [hozzon létre egy Azure Application Insights-erőforrást](./create-new-resource.md).*

2. **Weblap kódja:** A bezárás előtt adja hozzá a következő szkriptet a weboldalához ``</head>`` . Cserélje le a rendszerállapot-kulcsot a Application Insights erőforrás megfelelő értékére:
    
    Az aktuális kódrészlet (lent) az "5", a verzió a kódrészletben (SV: "#") van kódolva, és az [aktuális verzió is elérhető a githubon](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    A webhelyek figyeléséhez szükséges speciális konfigurációk megismeréséhez tekintse meg a [JavaScript SDK-referenciát ismertető cikket](./javascript.md).

3. **Mobile App code:** Az App Center SDK használatával gyűjthet eseményeket az alkalmazásból, majd az események másolatait elküldheti az elemzéshez az [útmutató](../app/mobile-center-quickstart.md)alapján Application Insights.

4. **Telemetria beolvasása:** Futtassa a projektet hibakeresési módban néhány percre, majd keresse meg az eredményeket a Application Insights áttekintés paneljén.

    Tegye közzé alkalmazását az alkalmazás teljesítményének figyeléséhez, és Ismerje meg, hogy a felhasználók hogyan használják az alkalmazást.

## <a name="explore-usage-demographics-and-statistics"></a>A használati demográfia és a statisztika megismerése
Megtudhatja, hogy mikor használják a felhasználók az alkalmazást, milyen lapokat érdeklik leginkább, hol találhatók a felhasználók, milyen böngészőket és operációs rendszereket használnak. 

A felhasználók és a munkamenetek jelentés az adatait lapok vagy egyéni események alapján szűri, és azokat a tulajdonságok, például a hely, a környezet és a lap alapján szegmentálja. Saját szűrőket is hozzáadhat.

![Képernyőfelvétel: a felhasználók áttekintő lapja egy fiktív vállalat számára.](./media/usage-overview/users.png)  

A jobb oldali elemzések érdekes mintákat mutatnak az adathalmazban.  

* A **felhasználók** jelentés a kiválasztott időszakokban lévő lapokhoz hozzáférő egyedi felhasználók számát számolja. A Web Apps esetében a felhasználók a cookie-k használatával számítanak. Ha valaki különböző böngészőkkel vagy ügyfélszámítógépekkel fér hozzá a webhelyhez, vagy törli a cookie-kat, akkor a rendszer többször is megszámolja őket.
* A **munkamenetek** jelentés megszámolja a webhelyhez hozzáférő felhasználói munkamenetek számát. A munkamenet egy adott felhasználó által végzett tevékenység, amely több mint fél óra inaktivitási időtartammal leállt.

[További információ a felhasználókról, a munkamenetekről és az események eszközeiről](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Megőrzés – hány felhasználó érkezik vissza?

Az adatmegőrzés segítségével megismerheti, hogy a felhasználók milyen gyakran térnek vissza az alkalmazás használatára, azon felhasználók kohorszai alapján, akik bizonyos üzleti műveleteket hajtottak végre egy adott időszakban. 

- Annak megismerése, hogy a felhasználók miként térhetnek vissza másokhoz 
- A valós felhasználói adathalmazok alapján alkotott hipotézisek 
- Annak megállapítása, hogy probléma van-e az adatmegőrzéssel a termékben 

![Képernyőfelvétel: a megőrzés áttekintő lapja, amely azt mutatja, hogy a felhasználók milyen gyakran térnek vissza az alkalmazás használatára.](./media/usage-overview/retention.png) 

A felső megőrzési vezérlők lehetővé teszik meghatározott események és időtartományok meghatározását a megőrzés kiszámításához. A középső gráf a megadott időtartomány alapján vizuálisan ábrázolja a teljes megőrzési arányt. Az alsó diagram az egyes adatmegőrzési időszakot jelöli. Ez a részletességi szint lehetővé teszi, hogy megtudja, mit csinálnak a felhasználók, és mi befolyásolhatja a visszatérő felhasználók részletesebb részletességét.  

[További információ az adatmegőrzési eszközről](usage-retention.md)

## <a name="custom-business-events"></a>Egyéni üzleti események

Ha szeretné megismerni, hogy a felhasználók mit tesznek az alkalmazással, hasznos lehet a kód sorait beszúrni az egyéni események naplózására. Ezek az események a részletes felhasználói műveletekkel, például az adott gombokra kattintva követhetik nyomon a jelentős üzleti eseményeket, például a vásárlást vagy a játék megnyerését.

Az egyéni események összegyűjtéséhez a [Click Analytics automatikus gyűjtemény beépülő modulját](javascript-click-analytics-plugin.md) is használhatja.

Bár bizonyos esetekben az oldalletöltések hasznos eseményeket jelenthetnek, általában nem igaz. A felhasználó megnyithatja a termék oldalát a termék megvásárlása nélkül. 

Adott üzleti események esetében a felhasználók előrehaladását a webhelyről is elvégezheti. Megtudhatja, hogy milyen beállítások állnak rendelkezésre a különböző lehetőségekhez, és hogy hol vannak kiesésük vagy nehézségei. Ezzel az ismerettel tájékozott döntéseket hozhat a fejlesztési várakozó prioritásokkal kapcsolatban.

Az eseményeket az alkalmazás ügyféloldali oldaláról lehet naplózni:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Vagy a kiszolgáló oldalán:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Ezekhez az eseményekhez tulajdonságokat is csatolhat, így szűrheti vagy feloszthatja az eseményeket a portálon való vizsgálat során. Emellett az egyes eseményekhez, például a névtelen felhasználói AZONOSÍTÓhoz is csatolni kell a tulajdonságok standard készletét, amely lehetővé teszi egy adott felhasználó tevékenységi sorrendjének nyomon követését.

További információ az [Egyéni eseményekről](./api-custom-events-metrics.md#trackevent) és a [tulajdonságokról](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Szeletek és kockák eseményei

A felhasználók, a munkamenetek és az események eszközökön egyéni eseményeket adhat meg a felhasználó, az esemény neve és a tulajdonságok alapján.
![Képernyőfelvétel: a felhasználók áttekintő lapja egy fiktív vállalat számára.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>A telemetria megtervezése az alkalmazással

Ha az alkalmazás minden funkcióját megtervezi, gondolja át, hogyan fogja mérni a sikerességét a felhasználókkal. Döntse el, hogy milyen üzleti eseményeket kell rögzítenie, és az események követési hívásait az alkalmazásba az elejétől.

## <a name="a--b-testing"></a>A | B tesztelés
Ha nem tudja, hogy egy adott szolgáltatás melyik változata lesz sikeres, szabadítson fel mindkettőt, hogy minden elérhető legyen a különböző felhasználók számára. Mérje fel az egyes műveletek sikerességét, majd váltson át egy egységes verzióra.

Ehhez a technikához külön tulajdonságértékeket kell csatolni az alkalmazás egyes verziói által eljuttatott összes telemetria. Ezt úgy teheti meg, hogy meghatározza a tulajdonságokat az aktív TelemetryContext. Ezek az alapértelmezett tulajdonságok minden olyan telemetria-üzenethez hozzáadódnak, amelyet az alkalmazás küld – nem csak az egyéni üzeneteket, hanem a standard telemetria is.

A Application Insights portálon szűrje és ossza meg az adatait a tulajdonságértékek alapján, hogy összehasonlítsa a különböző verziókat.

Ehhez [állítson be egy telemetria-inicializálást](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**ASP.NET-alkalmazások**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

A webalkalmazás-inicializáló, például a Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core-alkalmazások**

> [!NOTE]
> Az inicializálás a `ApplicationInsights.config` vagy a használatával `TelemetryConfiguration.Active` való hozzáadása ASP.net Core alkalmazások esetében nem érvényes. 

[ASP.net Core](asp-net-core.md#adding-telemetryinitializers) alkalmazások esetében az új hozzáadását `TelemetryInitializer` a függőségi injektálási tárolóba való hozzáadásával végezheti el, az alább látható módon. Ez az `ConfigureServices` osztály metódusában történik `Startup.cs` .

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Minden új TelemetryClients automatikusan hozzáadja a megadott tulajdonságérték értékét. Az egyes telemetria-események felülbírálják az alapértelmezett értékeket.

## <a name="next-steps"></a>Következő lépések
   - [Felhasználók, munkamenetek, események](usage-segmentation.md)
   - [Tölcsérek](usage-funnels.md)
   - [Visszatartás](usage-retention.md)
   - [Felhasználókövetés](usage-flows.md)
   - [Munkafüzetek](../visualize/workbooks-overview.md)
