---
title: 'Gyors útmutató: webhelyek figyelése Azure Monitor Application Insights'
description: Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be az ügyfél-és böngészőalapú webhelyek figyelését Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: mvc
ms.openlocfilehash: 225ba3642a9f66b1645565552620584bff0cb1c8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726087"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Rövid útmutató: a webhely figyelésének megkezdése Azure Monitor Application Insights

Ebből a rövid útmutatóból megtudhatja, hogyan adhatja hozzá a nyílt forráskódú Application Insights JavaScript SDK-t a webhelyéhez. Azt is megtudhatja, hogyan lehet jobban megismerni a webhelye látogatói számára az ügyfél/böngésző oldali élményt.

Az Azure Monitor Application Insights segítségével egyszerűen monitorozhatja webhelye rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. Application Insights a kiszolgálóoldali figyelést, valamint az ügyfél-és böngésző-oldali figyelési képességeket is biztosítja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy webhely, amelyhez hozzá lehet adni a Application Insights JavaScript SDK-t.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

A Application Insights a helyszínen vagy a felhőben futó, internetkapcsolattal rendelkező alkalmazásokból is gyűjthet telemetria-adatokat. Az alábbi lépések végrehajtásával tekintheti meg ezeket az adatfájlokat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza **az erőforrás létrehozása**  >  **felügyeleti eszközök**  >  **Application Insights** elemet.

   > [!NOTE]
   >Ha első alkalommal hoz létre Application Insights-erőforrást, tekintse meg [Application Insights erőforrás létrehozása](./create-new-resource.md)című témakört.
1. Amikor megjelenik a konfigurációs mező, a következő táblázat segítségével hajtsa végre a beviteli mezőket:

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név. |
   | **Erőforráscsoport**     | myResourceGroup      | Az új erőforráscsoport neve Application Insights-adattároláshoz. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt is. |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz közeli helyet, vagy a közelében, ahol az alkalmazás üzemeltetve van. |
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-an-html-file"></a>HTML-fájl létrehozása

1. A helyi számítógépen hozzon létre egy ``hello_world.html`` nevű fájlt. Ebben a példában hozza létre a fájlt a C meghajtó gyökerén úgy, hogy az a következőképpen néz ki: ``C:\hello_world.html`` .
1. Másolja és illessze be az alábbi szkriptet a következőbe ``hello_world.html`` :

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Application Insights SDK konfigurálása

1. Válassza az **Áttekintés**  >  **Essentials** lehetőséget, majd másolja az alkalmazás kialakítási **kulcsát**.

   ![Új Application Insights-erőforrás űrlap](media/website-monitoring/instrumentation-key-001.png)

1. Adja hozzá a következő parancsfájlt a ``hello_world.html`` fájlhoz a záró ``</head>`` címke előtt:

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
        instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
        /* ...Other Configuration Options... */
    }});
    </script>
    ```

    > [!NOTE]
    > A jelenlegi kódrészlet (fent felsorolt) az "5", a verzió a kódrészletben a következő: "#", a [jelenlegi verzió és a konfiguráció részletei pedig elérhetők a githubon](https://go.microsoft.com/fwlink/?linkid=2156318).
   
1. Szerkessze a ``hello_world.html`` fájlt, és adja hozzá az eszközkulcsát.

1. Nyissa meg a ``hello_world.html`` fájlt egy helyi böngésző-munkamenetben. Ez a művelet létrehoz egy egyoldalas nézetet. A böngésző frissítésével több tesztoldalt is létrehozhat.

## <a name="monitor-your-website-in-the-azure-portal"></a>Webhelye figyelése a Azure Portal

1. A jelenleg futó alkalmazás részleteinek megtekintéséhez nyissa meg újra a Azure Portal Application Insights **Áttekintés** lapját. Az **Áttekintés** oldalon beolvasta a kialakítási kulcsot.

   Az áttekintő oldal négy alapértelmezett diagramjának hatóköre a kiszolgálóoldali alkalmazásadatokra van beállítva. Mivel az ügyfél/böngésző oldali interakciókat a JavaScript SDK-val alakítjuk ki, ez az adott nézet nem érvényes, kivéve, ha egy kiszolgálóoldali SDK is telepítve van.

1. Válassza az **elemzési** ![ alkalmazás Térkép ikont ](media/website-monitoring/006.png) .  Ez a művelet megnyitja az **elemzést**, amely részletes lekérdezési nyelvet biztosít a Application Insights által összegyűjtött összes adatok elemzéséhez. Az ügyféloldali böngésző kéréseivel kapcsolatos adatmegjelenítéshez futtassa a következő lekérdezést:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Az adott időtartamon belüli felhasználói kéréseket mutató elemzési diagram](./media/website-monitoring/analytics-query.png)

1. Térjen vissza az **Áttekintés** oldalra. A **vizsgálat** fejléc alatt válassza a **böngésző**, majd a **teljesítmény** lehetőséget.  A webhely teljesítményével kapcsolatos metrikák jelennek meg. A hibák és kivételek elemzéséhez egy megfelelő nézet tartozik a webhelyén. A [végpontok közötti tranzakció részleteinek](./transaction-diagnostics.md)eléréséhez választhat **mintákat** .

   ![Kiszolgálómetrikák diagram](./media/website-monitoring/browser-performance.png)

1. A fő Application Insights menüben a **használat** fejléc alatt válassza a [**felhasználók**](./usage-segmentation.md) lehetőséget a [felhasználói viselkedés elemzési eszközeinek](./usage-overview.md)megkezdéséhez. Mivel egyetlen gépről próbálunk tesztelni, csak egy felhasználó adatait fogjuk látni. Az élő webhelyek esetében a felhasználók eloszlása így néz ki:

     ![Felhasználók diagram](./media/website-monitoring/usage-users.png)

1. Több oldalt tartalmazó összetettebb webhelyhez a [**Felhasználókövetés**](./usage-flows.md) eszközzel nyomon követheti a látogatók által a webhely különböző részein áthaladó útvonalakat.

   ![Felhasználókövetés vizualizációja](./media/website-monitoring/user-flows.png)

A webhelyek figyelésére szolgáló speciális konfigurációk megismeréséhez tekintse meg a [JavaScript SDK API-referenciáját](./javascript.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy további rövid útmutatókkal vagy oktatóanyagokkal dolgozik tovább, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást a Azure Portal.

> [!NOTE]
> Ha meglévő erőforráscsoportot használt, a következő utasítások nem fognak működni. Ehelyett egyszerűen törölheti az egyes Application Insights erőforrásait. Vegye figyelembe, hogy amikor töröl egy erőforráscsoportot, az adott csoport tagjaihoz tartozó összes underyling-erőforrás is törlődik.

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza a **myResourceGroup** vagy az ideiglenes erőforráscsoport nevét.
1. Az erőforráscsoport lapon válassza a **Törlés** lehetőséget, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Teljesítményproblémák észlelése és diagnosztizálása](../logs/log-query-overview.md)

