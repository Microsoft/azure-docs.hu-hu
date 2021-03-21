---
title: Telemetria a keresési forgalom elemzéséhez
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search Search Traffic Analytics szolgáltatásának engedélyezése, a telemetria és a felhasználó által kezdeményezett események összegyűjtése Application Insights használatával, majd az eredmények elemzése egy Power BI jelentésben.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485469"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Telemetria-adatok gyűjtése a keresési forgalom elemzéséhez

A Search Traffic Analytics olyan minta, amellyel telemetria gyűjthet a felhasználói interakciókkal kapcsolatban az Azure Cognitive Search-alkalmazással, például a felhasználó által kezdeményezett Click Events és Keyboard Inputs szolgáltatással. Ezen információk alapján meghatározhatja a keresési megoldás hatékonyságát, beleértve a népszerű keresési kifejezéseket, az átkattintási arányt, valamint azt, hogy mely lekérdezési bemenetek nulla eredményt adnak.

Ez a minta a felhasználói adatok gyűjtéséhez [Application Insights](../azure-monitor/app/app-insights-overview.md) ( [Azure monitor](../azure-monitor/index.yml)) függőségét veszi igénybe. Ehhez a jelen cikkben leírtak szerint hozzá kell adnia a rendszerállapotot az ügyfél kódjához. Végezetül szüksége lesz egy jelentéskészítési mechanizmusra az adatelemzéshez. Javasoljuk, hogy Power BI, de használhatja az alkalmazás irányítópultját vagy bármely olyan eszközt, amely a Application Insightshoz csatlakozik.

> [!NOTE]
> A cikkben ismertetett minta speciális forgatókönyvekre és az ügyfélhez hozzáadott kód által generált kattintássorozat-adatokra vonatkozik. Ezzel szemben a szolgáltatási naplók egyszerűen állíthatók be, számos mérőszámot biztosítanak, és a portálon a kód nélkül is elvégezhető. A naplózás engedélyezése minden esetben ajánlott. További információ: a [naplófájlok adatainak összegyűjtése és elemzése](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>A releváns keresési adatokat azonosítsa

Ha hasznos mérőszámokra van szüksége a forgalmi elemzések kereséséhez, néhány jelet be kell jelentkeznie a keresési alkalmazás felhasználóival. Ezek a jelek azokat a tartalmakat jelentik, amelyeket a felhasználók érdeklik, és amelyeket fontosnak tartanak. A Search Traffic Analytics esetében ezek a következők:

+ Felhasználó által generált keresési események: csak a felhasználó által kezdeményezett keresési lekérdezések érdeklik. A többi keresési kérelem, például az aspektusok feltöltésére vagy a belső információk beolvasására használt adatok nem fontosak. Ügyeljen arra, hogy csak a felhasználó által kezdeményezett események legyenek elérhetők az eredményekben a döntés és a torzítás elkerülése érdekében.

+ Felhasználó által generált kattintási események: a keresési eredmények oldalon Egy kattintásos esemény általában azt jelenti, hogy egy dokumentum egy adott keresési lekérdezéshez tartozó eredmény.

Ha összekapcsolja a keresést, és a korrelációs AZONOSÍTÓval rendelkező események elemre kattint, mélyebben megértette, hogy milyen jól működik az alkalmazás keresési funkciója.

## <a name="add-search-traffic-analytics"></a>Keresési forgalom elemzésének hozzáadása

Az Azure Cognitive Search-szolgáltatás [portál](https://portal.azure.com) lapján nyissa meg a keresés Traffic Analytics lapot, hogy hozzáférjen egy Cheat laphoz a telemetria-minta követéséhez. Ezen a lapon kiválaszthat vagy létrehozhat egy Application Insights erőforrást, lekérheti a kialakítási kulcsot, átmásolhatja a megoldáshoz alkalmazkodó kódrészleteket, és letöltheti a sémában a séma alapján felépített Power BI jelentést is.

![Keresés a portálon Traffic Analytics oldalon](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Keresés a portálon Traffic Analytics oldalon")

## <a name="1---set-up-application-insights"></a>1 – Application Insights beállítása

Válasszon ki egy meglévő Application Insights-erőforrást, vagy [hozzon létre egyet](../azure-monitor/app/create-new-resource.md) , ha még nem rendelkezik ilyennel. Ha a keresés Traffic Analytics lapot használja, akkor másolhatja az alkalmazás által a Application Insightshoz való csatlakozáshoz szükséges kialakítási kulcsot.

Ha Application Insights erőforrással rendelkezik, az alkalmazás regisztrálásához kövesse [a támogatott nyelvekre és platformokra vonatkozó utasításokat](../azure-monitor/app/platforms.md) . A regisztráció egyszerűen felveszi a rendszerállapot-kulcsot Application Insightsról a kódra, amely beállítja a társítást. A kulcsot a portálon, illetve a keresés Traffic Analytics oldalon találhatja meg, amikor kijelöl egy meglévő erőforrást.

A Visual Studio-projektek egyes típusaihoz tartozó parancsikonok az alábbi lépésekben láthatók. Létrehoz egy erőforrást, és mindössze néhány kattintással regisztrálja az alkalmazást.

1. A Visual Studio és a ASP.net fejlesztéséhez nyissa meg a megoldást, és válassza a **projekt**  >  **Hozzáadás Application Insights telemetria** lehetőséget.

1. Kattintson az **Első lépések** lehetőségre.

1. Az alkalmazás regisztrálása Microsoft-fiók, Azure-előfizetés és egy Application Insights-erőforrás biztosításával (az új erőforrás az alapértelmezett). Kattintson a **Regisztrálás** parancsra.

Ezen a ponton az alkalmazás figyelésre van beállítva, ami azt jelenti, hogy az összes oldal terhelését az alapértelmezett metrikák követik nyomon. További információ az előző lépésekről: [Application Insights kiszolgálóoldali telemetria engedélyezése](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 – rendszerállapot-Hozzáadás

Ez a lépés a saját keresési alkalmazásának eszköze, amely a fenti lépésben létrehozott Application Insights erőforrást használja. A folyamat négy lépésből áll, a telemetria-ügyfél létrehozásával kezdve.

### <a name="step-1-create-a-telemetry-client"></a>1. lépés: telemetria-ügyfél létrehozása

Hozzon létre egy objektumot, amely eseményeket küld Application Insightsnak. A böngészőben futtatott kiszolgálóoldali alkalmazás kódjához vagy ügyféloldali kódjához is hozzáadhat rendszerállapot-kódot, amelyet C#-ként és JavaScript-változatként (más nyelveken pedig a [támogatott platformok és keretrendszerek](../azure-monitor/app/platforms.md)teljes listájában talál). Válassza ki azt a megközelítést, amely megadja a kívánt mélységű információt.

A kiszolgálóoldali telemetria az alkalmazás rétegében rögzítik a mérőszámokat, például a felhőben webszolgáltatásként futó alkalmazásokban, vagy egy vállalati hálózaton lévő helyszíni alkalmazásként. A kiszolgálóoldali telemetria rögzítik a keresést, és rákattintanak az események elemre, egy dokumentum pozíciója az eredmények között, és a lekérdezési adatok, de az adatgyűjtés hatóköre az adott rétegen elérhető információk körébe tartozik.

Előfordulhat, hogy az ügyfélen további kód szerepel, amely a lekérdezések bemeneteit, navigálást vagy kontextust tartalmaz (például a kezdőlapról vagy a termék oldaláról kezdeményezett lekérdezések). Ha ez leírja a megoldást, az ügyféloldali rendszerállapot-kialakítást is igénybe vehet, hogy a telemetria a további részleteket tükrözze. A további részletek gyűjtésének módja meghaladja a minta hatókörét, de további útmutatást a [weblapok Application Insights](../azure-monitor/app/javascript.md#explore-browserclient-side-data) áttekintése című témakörben olvashat. 

**A C# használata**

A C# esetében a **InstrumentationKey** meg kell határozni az alkalmazás konfigurációjában, például appsettings.js, ha a projekt ASP.net. Ha nem biztos benne, hogy a kulcs helyét használja, tekintse át a regisztrációs utasításokat.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**JavaScript használata**

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
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Az olvashatóság és a lehetséges JavaScript-hibák csökkentése érdekében az összes lehetséges konfigurációs beállítás megjelenik a fenti kódrészlet kódjának egy új sorában, ha nem szeretné módosítani egy megjegyzéses sor értékét, akkor az eltávolítható.


### <a name="step-2-request-a-search-id-for-correlation"></a>2. lépés: keresési azonosító kérése a korrelációhoz

Ha a keresési kérelmeket a kattintásokkal szeretné összekapcsolni, olyan korrelációs AZONOSÍTÓra van szükség, amely a két különböző eseményt érinti. Az Azure Cognitive Search keresési azonosítót biztosít, ha HTTP-fejlécet kér.

Ha a keresési azonosító lehetővé teszi, hogy az Azure Cognitive Search által kibocsátott mérőszámok korrelációban legyenek a kéréshez, a Application Insights bejelentkezett egyéni metrikákkal.

**C# használata (újabb v11 SDK)**

A legújabb SDK-nak egy http-folyamat használatával kell megadnia a fejlécet a [mintában](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy)részletezett módon.

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**C# (régebbi v10 SDK) használata**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**JavaScript használata (REST API-k hívása)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>3. lépés: keresési események naplózása

Minden alkalommal, amikor egy felhasználó egy keresési kérelmet ad ki, a következő sémával rendelkező keresési eseményként kell bejelentkeznie egy Application Insights egyéni eseményre. Ne feledje, hogy csak a felhasználó által létrehozott keresési lekérdezéseket naplózza.

+ **SearchServiceName**: (karakterlánc) keresési szolgáltatás neve
+ **SearchId**: (GUID) – a keresési lekérdezés egyedi azonosítója (a keresési válaszban érkezik)
+ **IndexName**: (karakterlánc) keresési szolgáltatási index lekérdezése
+ **QueryTerms**: (karakterlánc) a felhasználó által megadott keresési kifejezések
+ **ResultCount**: (int) a visszaadott dokumentumok száma (a keresési válaszban érkezik)
+ **ScoringProfile**: (karakterlánc) a használt pontozási profil neve, ha van ilyen

> [!NOTE]
> Adja meg a felhasználó által generált lekérdezések számát $count = True hozzáadásával a keresési lekérdezéshez. További információ: [dokumentumok keresése (REST)](/rest/api/searchservice/search-documents#query-parameters).
>

**A C# használata**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript használata**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>4. lépés: kattintson az események naplózása elemre.

Minden alkalommal, amikor egy felhasználó egy dokumentumra kattint, ez egy olyan jel, amelyet a keresési elemzési célokra kell naplózni. Ezeket az eseményeket a következő sémával naplózhatja Application Insights egyéni események használatával:

+ **Szolgáltatásnév**: (karakterlánc) keresési szolgáltatás neve
+ **SearchId**: (GUID) a kapcsolódó keresési lekérdezés egyedi azonosítója
+ **Dokumentumazonosító**: (karakterlánc) dokumentum azonosítója
+ **Pozíció**: (int) Range a dokumentumnak a keresési eredmények oldalon

> [!NOTE]
> A pozíció a kardinális sorrendre hivatkozik az alkalmazásban. Az összehasonlításhoz szabadon állíthatja be ezt a számot, amennyiben az mindig ugyanaz.
>

**A C# használata**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript használata**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 – Power BI elemzése

Miután felkészítette az alkalmazást, és ellenőrizte, hogy az alkalmazás megfelelően van csatlakoztatva a Application Insightshoz, letölt egy előre definiált jelentési sablont, hogy elemezze az adatait a Power BI Desktopban. A jelentés előre definiált diagramokat és táblázatokat tartalmaz, amelyek hasznosak a Search Traffic Analytics szolgáltatásban rögzített további adatok elemzéséhez.

1. Az Azure Cognitive Search-irányítópult bal oldali navigációs ablaktábláján, a **Beállítások** területen kattintson a **Traffic Analytics keresése** elemre.

1. A **Keresés a forgalom elemzéséhez** lapon, a 3. lépésben kattintson az **Power bi Desktop beolvasása** elemre a Power bi telepítéséhez.

   ![Power BI jelentések beolvasása](./media/search-traffic-analytics/get-use-power-bi.png "Power BI jelentések beolvasása")

1. Ugyanazon a lapon kattintson a **Power bi-jelentés letöltése** elemre.

1. A jelentés Power BI Desktopban nyílik meg, és a rendszer felszólítja, hogy kapcsolódjon Application Insightshoz, és adja meg a hitelesítő adatokat. A Application Insights-erőforrás Azure Portal lapjain a kapcsolatok adatai találhatók. A hitelesítő adatok esetében adja meg ugyanazt a felhasználónevet és jelszót, amelyet a portálon való bejelentkezéshez használ.

   ![Csatlakozás az Application Insightshoz](./media/search-traffic-analytics/connect-to-app-insights.png "Csatlakozás az Application Insightshoz")

1. Kattintson a **Betöltés** elemre.

A jelentés olyan diagramokat és táblázatokat tartalmaz, amelyek segítségével jobban tájékozott döntéseket hozhat a keresési teljesítmény és a relevancia javítása érdekében.

A metrikák a következő elemeket foglalják magukban:

+ Keresési mennyiség és a legnépszerűbb kifejezés – dokumentum párok: azok a feltételek, amelyek a dokumentumra kattintanak, a kattintások szerint rendezve jelennek meg.
+ Kattintások nélkül végzett keresések: a nem kattintást igénylő leggyakoribb lekérdezések feltételei

Az alábbi képernyőfelvételen látható, hogy a beépített jelentések hogyan nézhetnek ki, ha az összes séma elemet használta.

![Power BI irányítópult az Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI irányítópult az Azure Cognitive Search")

## <a name="next-steps"></a>Következő lépések

A keresési alkalmazás hatékony és átgondolt adatainak beszerzése a keresési szolgáltatással.

A [Application Insightsról](../azure-monitor/app/app-insights-overview.md) további információt talál, és megtekintheti a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-insights/) , ahol további információkat találhat a különböző szolgáltatási szintjeiről.

További információ a csodálatos jelentések létrehozásáról. A részletekért tekintse meg [a Power bi Desktop első lépéseit](/power-bi/fundamentals/desktop-getting-started) ismertető témakört.
