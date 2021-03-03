---
title: SharePoint-hely megfigyelése az Application Insights segítségével
description: Új alkalmazás figyelésének megkezdése új kialakítási kulccsal
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: e4cf30fbb5a0861d75fea852222f731a575ca7f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714017"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>SharePoint-hely megfigyelése az Application Insights segítségével

Az Azure Application Insights figyeli alkalmazásai rendelkezésre állását, teljesítményét és használatát. Ebből a cikkből megismerheti, hogyan állíthatja be egy SharePoint-helyhez.

> [!NOTE]
> Biztonsági okokból a jelen cikkben ismertetett parancsfájlt nem lehet közvetlenül felvenni a SharePoint modern UX weblapjára. Alternatív megoldásként használhatja a [SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) szolgáltatást, amellyel létrehozhat egy egyéni bővítményt, amellyel a SharePoint-webhelyekre Application Insights telepíthet.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása
Az [Azure Portalon](https://portal.azure.com) hozzon létre egy új Application Insights-erőforrást. Az alkalmazás típusának válassza az ASP.NET lehetőséget.

![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/sharepoint/001.png)

A megnyíló ablakban megtekintheti az alkalmazása teljesítmény- és használati adatait. Amikor legközelebb bejelentkezik az Azure-ba, a kezdőképernyőn található csempére kattintva léphet közvetlenül erre a panelre. Vagy a Tallózás gombra kattintva is megkeresheti.

## <a name="add-the-script-to-your-web-pages"></a>A szkript hozzáadása weblapokhoz

Az aktuális kódrészlet (lent) az "5", a verzió a kódrészletben (SV: "#") van kódolva, és az [aktuális verzió is elérhető a githubon](https://go.microsoft.com/fwlink/?linkid=2156318).

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.gbl.min.js", // The SDK URL Source
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

> [!NOTE]
> A SharePoint URL-címe a "...\ai.2.gbl.min.js" modul formátumát használja (jegyezze fel a további **. GBL.**) Ez a másodlagos modul formátuma szükséges ahhoz, hogy elkerülje a parancsfájlok betöltésének sorrendje által okozott problémát, ami azt eredményezi, hogy az SDK inicializálása sikertelen lesz, és a telemetria események elvesztését eredményezi.
>
> A problémát a requireJS betöltése és inicializálása okozza az SDK előtt.

Szúrja be a szkriptet közvetlenül az &lt; &gt; összes nyomon követni kívánt oldal/Head címkéjébe. Ha a webhelynek van főoldala, akkor a parancsfájlt ott helyezheti el. Egy ASP.NET MVC-projektben a következő helyre helyezné a szkriptet: View\Shared\_Layout.cshtml

A szkript tartalmazza a kialakítási kulcsot, amely a telemetriát az Application Insights-erőforrásra irányítja.

### <a name="add-the-code-to-your-site-pages"></a>Kód hozzáadása webhely lapjaihoz
#### <a name="on-the-master-page"></a>A mesterlapon
Ha szerkeszti webhelye mesterlapját, az biztosítja a webhely összes lapjának figyelését.

Tekintse át a mesterlapot, és szerkessze a SharePoint Designerrel vagy más szerkesztővel.

![Képernyőkép, amely bemutatja, hogyan szerkesztheti a mesterlapot a SharePoint Designer vagy más szerkesztő használatával.](./media/sharepoint/03-master.png)

Adja hozzá a kódot közvetlenül a </head> tag. 

![Képernyőkép, amely bemutatja, hová kell hozzáadni a kódot a webhely oldalához.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Vagy önálló lapokon
Adott számú lap figyeléséhez adja hozzá a szkriptet egyesével a lapokhoz. 

Szúrjon be egy kijelzőt, és ágyazza bele a kódrészletet.

![Képernyőfelvétel: a parancsfájl hozzáadásával figyelheti a korlátozott lapokat.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Alkalmazás adatainak megtekintése
Helyezze ismét üzembe alkalmazását.

Térjen vissza alkalmazása paneljéhez az [Azure Portalon](https://portal.azure.com).

Az első események megjelennek a keresésben. 

![Képernyőkép, amely az alkalmazásban megtekinthető új adatnézeteket jeleníti meg.](./media/sharepoint/09-search.png)

Ha több adatot vár, néhány másodperc elteltével kattintson a Frissítés elemre.

## <a name="capturing-user-id"></a>Felhasználói azonosító rögzítése
A normál weblapkódrészlet nem rögzíti a felhasználói azonosítót a SharePointból, de ez egy apró módosítással elvégezhető.

1. Az Application Insights Alapvető erőforrások legördülő menüjéből másolja ki alkalmazása kialakítási kulcsát. 

    ![Képernyőkép, amely az alkalmazás rendszerállapot-legördülő menüjéből való másolását mutatja Application Insightsban.](./media/sharepoint/02-props.png)

1. Az alábbi részletben szereplő „XXXX”-et cserélje le a kialakítási kulcsra. 
2. Ágyazza be a szkriptet a SharePoint alkalmazásába annak a kódrészletnek a helyére, amelyet a portálról töltött le.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Következő lépések
* [Webes tesztek](./monitor-web-app-availability.md) webhelye rendelkezésre állásának figyeléséhez.
* [Application Insights](./app-insights-overview.md) más típusú alkalmazásokhoz.

<!--Link references-->
