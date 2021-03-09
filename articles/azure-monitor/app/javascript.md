---
title: Azure-Application Insights JavaScript-alapú webalkalmazásokhoz
description: Megtekintheti az oldal nézetét és a munkamenetek számát, a webes ügyfél adatait, az egyoldalas alkalmazásokat (SPA) és a használati mintákat. Kivételeket és teljesítményproblémákat észlelhet a JavaScript weblapokon.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9b8824a0f73f3a79ab70810c529cb0ed9331a797
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485486"
---
# <a name="application-insights-for-web-pages"></a>Application Insights weblapokhoz

Megismerheti a weblap vagy alkalmazás teljesítményét és használatát. Ha [Application Insightst](app-insights-overview.md) ad hozzá az oldal-parancsfájlhoz, az oldal betöltésének és az Ajax-hívásoknak, a számoknak, valamint a böngészőbeli kivételek és az Ajax-hibák részleteinek, valamint a felhasználók és a munkamenetek számát is megtalálhatja. Ezek mindegyike szegmentálható lap, ügyfél operációs rendszere és böngészőverziója, földrajzi hely és más dimenziók alapján. Beállíthat riasztásokat a hibaszámokról és a lassú lapbetöltésekről. Ha nyomkövetési hívásokat szúr be a JavaScript-kódba, nyomon követheti a webalkalmazás különböző szolgáltatásainak használati módját is.

Az Application Insights bármely weblappal használható – csak egy rövid JavaScript-kódot kell hozzáadnia. Ha a webszolgáltatása [Java](java-get-started.md) vagy [ASP.net](asp-net.md), az ügyféloldali JavaScript SDK-val együtt használhatja a kiszolgálóoldali SDK-kat az alkalmazás teljesítményének teljes körű megismeréséhez.

## <a name="adding-the-javascript-sdk"></a>A JavaScript SDK hozzáadása

> [!IMPORTANT]
> Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=js) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.

1. Először Application Insights erőforrásra van szüksége. Ha még nem rendelkezik erőforrás-és kialakítási kulccsal, kövesse az [új erőforrás létrehozása című témakört](create-new-resource.md).
2. Másolja a kialakítási _kulcsot_ (más néven "rendszerállapotkulcsot") vagy a [kapcsolódási karakterláncot](#connection-string-setup) arra az erőforrásra, amelyen a JavaScript-telemetria el szeretné juttatni (az 1. lépésből) Adja hozzá a `instrumentationKey` `connectionString` Application INSIGHTS JavaScript SDK vagy a beállításához.
3. Adja hozzá a Application Insights JavaScript SDK-t a weboldalához vagy az alkalmazáshoz az alábbi két lehetőség egyikének használatával:
    * [NPM-telepítő](#npm-based-setup)
    * [JavaScript-kódrészlet](#snippet-based-setup)

> [!IMPORTANT]
> A JavaScript SDK-t csak egy módszer használatával adhatja hozzá az alkalmazáshoz. Ha a NPM telepítőjét használja, ne használja a kódrészletet, és fordítva.

> [!NOTE]
> A NPM telepítője a JavaScript SDK-t a projekttől való függőségként telepíti, az IntelliSense engedélyezésével, míg a kódrészlet futásidőben beolvassa az SDK-t. Mindkettő támogatja ugyanazt a funkciót. Azok a fejlesztők azonban, akik több egyéni eseményt és konfigurációt kívánnak használni, általában a NPM beállítását választják, míg a felhasználóknak a beépített webes elemzések gyors engedélyezését keresik.

### <a name="npm-based-setup"></a>NPM-alapú telepítés

Telepítse a NPM-on keresztül.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> A **beírások beletartoznak a csomagba**, így **nem** kell külön begépelési csomagot telepítenie.
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Kódrészlet-alapú telepítés

Ha az alkalmazás nem használja az NPM-t, akkor közvetlenül a weblapjait az egyes lapok tetején található kódrészlettel Application Insightshatja. Lehetséges, hogy a szakasz első szkriptje legyen, `<head>` hogy képes legyen figyelni az összes függőséggel kapcsolatos esetleges problémát, és opcionálisan bármilyen JavaScript-hibát. Ha a Blazer Server alkalmazást használja, adja hozzá a kódrészletet a (z) szakaszban található fájl elejéhez `_Host.cshtml` `<head>` .

Annak érdekében, hogy az alkalmazás által használt kódrészlet melyik verzióját használja, az 2.5.5-verziótól kezdődően egy új "Ai. internal. kódrészlet" címkét fog tartalmazni, amely tartalmazza az azonosított kódrészlet verzióját.

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Az olvashatóság és a lehetséges JavaScript-hibák csökkentése érdekében az összes lehetséges konfigurációs beállítás megjelenik a fenti kódrészlet kódjának egy új sorában, ha nem szeretné módosítani egy megjegyzéses sor értékét, akkor az eltávolítható.


#### <a name="reporting-script-load-failures"></a>Jelentéskészítési parancsfájlok betöltésének hibái

A kódrészlet jelen verziója észleli a hibákat, amikor az SDK-t a CDN-ből tölti be kivételként a Azure Monitor portálra (a hibák &gt; kivételei &gt; böngészőben), ez a kivétel az ilyen típusú hibák láthatóságát biztosítja, így biztos lehet benne, hogy az alkalmazás nem jelent telemetria (vagy más kivételeket) a várt módon. Ez a jel fontos szempont, hogy megértsük, hogy elvesztette a telemetria, mert az SDK nem töltődött be vagy inicializált, ami a következőket eredményezheti:
- A felhasználók által használt (vagy a használni próbált) webhely bejelentése
- Hiányzik a telemetria, hogy a végfelhasználók hogyan használják a webhelyet;
- Hiányzó JavaScript-hibák, amelyek esetleg blokkolják a végfelhasználókat a hely sikeres használatával.

A kivétel részleteiért lásd az [SDK-Betöltési hiba](javascript-sdk-load-failure.md) elhárítása oldalt.

Ennek a hibának a bejelentése kivételként a portálon nem használja az Application betekintő konfigurációjának konfigurációs beállítását, ```disableExceptionTracking``` ezért ha ez a hiba történik, mindig a kódrészlet fogja jelenteni, még akkor is, ha az ablak. onError támogatása le van tiltva.

Az SDK-betöltési hibák jelentése kifejezetten nem támogatott IE 8 (vagy kevesebb) esetén. Ez segít a kódrészletek minified méretének csökkentésében azáltal, hogy a legtöbb környezet nem kizárólag IE 8 vagy kevesebb. Ha ezt a követelményt szeretné megkapni, és ezeket a kivételeket is el kívánja fogadni, akkor be kell állítania egy beolvasott Poly-kitöltést, vagy létre kell hoznia egy saját kódrészlet-verziót, amelyet a ```XDomainRequest``` helyett használ ```XMLHttpRequest``` , ezért javasolt a [megadott kódrészletet](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) kiindulási pontként használni.

> [!NOTE]
> Ha a kódrészlet egy korábbi verzióját használja, erősen ajánlott a legújabb verzióra frissíteni, hogy megkapja ezeket a korábban nem jelentett problémákat.

#### <a name="snippet-configuration-options"></a>Kódrészlet konfigurációs beállításai

Az összes konfigurációs beállítás mostantól a szkript végére került, így elkerülhetők a JavaScript-hibák, amelyek nem csupán az SDK betöltését okozzák, hanem letiltják a hiba jelentését is.

Az egyes konfigurációs beállítások egy új sorban jelennek meg, ha nem szeretné felülbírálni a felsorolt elemek alapértelmezett értékét [nem kötelező] értékkel, eltávolíthatja ezt a sort a visszaadott oldal méretének csökkentése érdekében.

Az elérhető konfigurációs lehetőségek a következők

| Név | Típus | Leírás
|------|------|----------------
| src | karakterlánc **[kötelező]** | Az a teljes URL-cím, ahová be kell tölteni az SDK-t. Ezt az értéket egy dinamikusan hozzáadott parancsfájl vagy címke "src" attribútumához használja a rendszer &lt; &gt; . Használhatja a nyilvános CDN-helyet vagy a saját privát üzemeltetését.
| name | karakterlánc *[nem kötelező]* | A inicializált SDK globális neve, alapértelmezett érték: `appInsights` . Így ```window.appInsights``` a inicializált példányra mutató hivatkozás lesz. Megjegyzés: Ha egy név értéket ad meg, vagy egy korábbi példányt rendel hozzá (a globális név appInsightsSDK), akkor ez a name érték a globális névtérben is definiálva lesz ```window.appInsightsSDK=<name value>``` , ezt az SDK inicializálási kódja megköveteli, hogy a rendszer inicializálja és frissítse a megfelelő kódrészletet és proxy metódusokat.
| ld | szám az MS-ban *[opcionális]* | Meghatározza azt a betöltési késleltetést, ameddig a rendszer megpróbálja betölteni az SDK-t. Az alapértelmezett érték a 0ms, és minden negatív érték azonnal hozzáad egy szkript címkét &lt; a &gt; lap főrégiójához, amely letiltja az oldal betöltési eseményét, amíg a parancsfájl be nem töltődik (vagy sikertelen).
| useXhr | logikai érték *[opcionális]* | Ez a beállítás csak jelentési SDK-betöltési hibák esetén használatos. A jelentéskészítés először a beolvasást () fogja használni, ha elérhető, majd a tartalék x/óra értékre állítja be ezt az értéket, csak a beolvasás ellenőrzését. Ezt az értéket csak akkor kell használni, ha az alkalmazás olyan környezetben van használatban, amelyben a fetch nem fogja elküldeni a sikertelen események eseményeit.
| crossOrigin | karakterlánc *[nem kötelező]* | Ennek a beállításnak a megadásával az SDK letöltéséhez hozzáadott parancsfájl-címke tartalmazza a crossOrigin attribútumot a karakterlánc értékkel. Ha nincs definiálva (az alapértelmezett) nincs crossOrigin attribútum hozzáadva. Az ajánlott értékek nincsenek definiálva (az alapértelmezett érték); ""; vagy a "névtelen" (az összes érvényes értéknél lásd a [HTML-attribútumot: `crossorigin` ](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) dokumentáció)
| cfg | objektum **[kötelező]** | Az inicializálás során a Application Insights SDK-nak átadott konfiguráció.

### <a name="connection-string-setup"></a>A kapcsolatok karakterláncának beállítása

A NPM vagy a kódrészlet telepítésekor a Application Insights példányát is konfigurálhatja egy kapcsolatok karakterlánc használatával. Egyszerűen cserélje le a mezőt a `instrumentationKey` `connectionString` mezőre.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Telemetria küldése a Azure Portalba

Alapértelmezés szerint a Application Insights JavaScript SDK autogyűjt számos telemetria elemet, amelyek hasznosak lehetnek az alkalmazás állapotának meghatározásához és a mögöttes felhasználói élményhez. Ezek a következők:

- Nem **kezelt kivételek** az alkalmazásban, beleértve a következő információkat:
    - Verem nyomkövetése
    - Kivétel részletei és a hibát kísérő üzenet
    - Hiba a sorban & oszlop száma
    - A hiba előfordulásának URL-címe
- Az **alkalmazás által** használt **hálózati függőségi kérelmek** és a **beolvasás** (a beolvasási gyűjtemény alapértelmezés szerint le van tiltva) kérelmek, információk befoglalása
    - A függőség forrásának URL-címe
    - A függőség igénylésére szolgáló parancs & metódus
    - A kérelem időtartama
    - Az eredmény kódja és a kérelem sikerességi állapota
    - A kérelmet készítő felhasználó azonosítója (ha van)
    - Korrelációs környezet (ha van), ahol a kérelem készül
- **Felhasználói adatok** (például hely, hálózat, IP)
- **Eszköz adatai** (például böngésző, operációs rendszer, verzió, nyelv, modell)
- **A munkamenetadatokat**

### <a name="telemetry-initializers"></a>Telemetria inicializálók
A telemetria inicializálók segítségével módosítható a begyűjtött telemetria tartalma, mielőtt a felhasználó böngészőjéből elküldjék. Azt is felhasználhatják, hogy bizonyos telemetria ne küldjön vissza `false` . Több telemetria-inicializáló is felvehető a Application Insights-példányba, és ezeket a rendszer a hozzáadásuk sorrendjében hajtja végre.

A bemenet argumentuma `addTelemetryInitializer` egy olyan visszahívás, amely argumentumként fogadja el a [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) függvényt, és visszaadja a vagy a értéket `boolean` `void` . Ha visszatér `false` , a rendszer nem küldi el az telemetria elemet, ellenkező esetben a következő telemetria-inicializálást (ha van ilyen), vagy pedig a telemetria-gyűjtemény végpontjának küldi el.

Példa telemetria inicializálók használatára:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Konfiguráció
A legtöbb konfigurációs mező neve úgy van elnevezve, hogy a hamis értékre legyenek kiválasztva. Az összes mező megadása nem kötelező, kivéve a következőt: `instrumentationKey` .

| Name | Alapértelmezett | Leírás |
|------|---------|-------------|
| instrumentationKey | null | **Kötelező**<br>A Azure Portaltól beszerzett kialakítási kulcs. |
| accountId | null | Egy nem kötelező fiókazonosító, ha az alkalmazás a felhasználókat fiókokba csoportosítja. Nincsenek szóközök, vesszők, pontosvesszők, egyenlők vagy függőleges sávok |
| sessionRenewalMs | 1800000 | A rendszer naplózza a munkamenetet, ha a felhasználó ennél az időtartamnál ezredmásodpercben inaktív. Az alapértelmezett érték 30 perc |
| sessionExpirationMs | 86400000 | A rendszer naplózza a munkamenetet, ha ez az időtartam ezredmásodpercben továbbra is fennáll. Az alapértelmezett érték 24 óra |
| maxBatchSizeInBytes | 10000 | A telemetria-köteg maximális mérete. Ha egy köteg túllépi ezt a korlátot, a rendszer azonnal elküldi, és új köteget indít el |
| maxBatchInterval | 15 000 | Mennyi ideig kell a Batch telemetria a küldés előtt (ezredmásodperc) |
| disableExceptionTracking | hamis | Ha az értéke igaz, a rendszer nem gyűjti össze a kivételeket. Az alapértelmezett érték a false (hamis). |
| disableTelemetry | hamis | Ha az értéke igaz, a rendszer nem gyűjti és nem továbbítja a telemetria. Az alapértelmezett érték a false (hamis). |
| enableDebug | hamis | Ha az értéke igaz, a rendszer az SDK-naplózási beállításoktól függetlenül kivételt okoz a **belső** hibakeresési adatvesztés **helyett** . Az alapértelmezett érték a false (hamis). <br>**_Megjegyzés:_** Ha ez a beállítás engedélyezve van, a rendszer elveti a telemetria, amikor belső hiba történik. Ez hasznos lehet a konfigurációval vagy az SDK használatával kapcsolatos problémák gyors azonosításához. Ha nem kívánja elveszíteni a telemetria a hibakeresés során, érdemes lehet `consoleLoggingLevel` `telemetryLoggingLevel` a vagy a helyett használni `enableDebug` . |
| loggingLevelConsole | 0 | **Belső** Application Insights hibák naplózása a konzolon. <br>0: kikapcsolva, <br>1: csak kritikus hibák, <br>2: minden (hibák & figyelmeztetés) |
| loggingLevelTelemetry | 1 | **Belső** Application Insights hibákat küld a telemetria. <br>0: kikapcsolva, <br>1: csak kritikus hibák, <br>2: minden (hibák & figyelmeztetés) |
| diagnosticLogInterval | 10000 | belső A belső naplózási várólista lekérdezési időköze (MS) |
| samplingPercentage | 100 | Az elküldeni kívánt események százalékos aránya. Az alapértelmezett érték 100, ami azt jelenti, hogy az összes esemény el lesz küldve. Állítsa be ezt, ha nagy méretű alkalmazásokhoz szeretné megőrizni az adatkorlátot. |
| autoTrackPageVisitTime | hamis | Ha az értéke igaz, a rendszer nyomon követi az előző műszeres lap megtekintési idejét, és telemetria, és új időzítőt indít el az aktuális oldalmegtekintéshez. Az alapértelmezett érték a false (hamis). |
| disableAjaxTracking | hamis | Ha az értéke igaz, a rendszer nem gyűjti az Ajax-hívásokat. Az alapértelmezett érték a false (hamis). |
| disableFetchTracking | true | Ha az értéke igaz, a rendszer nem gyűjti be a kérelmeket. Az alapértelmezett érték TRUE (igaz) |
| overridePageViewDuration | hamis | Ha az értéke TRUE (igaz), a rendszer a trackPageView alapértelmezett viselkedését az oldal nézet időtartamának végére rögzíti, amikor a trackPageView hívása történik. Ha hamis, és nem ad meg egyéni időtartamot a trackPageView, a rendszer a navigációs időzítési API használatával számítja ki az oldal nézetének teljesítményét. Az alapértelmezett érték a false (hamis). |
| maxAjaxCallsPerView | 500 | Alapértelmezett 500 – meghatározza, hogy hány AJAX-hívást fog figyelni az oldal nézetében. Az-1 értékre állítva az összes (korlátlan) AJAX-hívást a lapon figyelheti. |
| disableDataLossAnalysis | true | Hamis érték esetén a rendszer a belső telemetria-küldő puffereket a még el nem küldött elemek indításakor ellenőrzi. |
| disableCorrelationHeaders | hamis | Hamis érték esetén az SDK két fejlécet ("Request-id" és "kérés-környezet") hoz létre az összes függőségi kérelemhez, hogy azok összekapcsolják őket a kiszolgálói oldalon található megfelelő kérelmekkel. Az alapértelmezett érték a false (hamis). |
| correlationHeaderExcludedDomains |  | A korrelációs fejlécek letiltása adott tartományokban |
| correlationHeaderDomains |  | Korrelációs fejlécek engedélyezése adott tartományokhoz |
| disableFlushOnBeforeUnload | hamis | Alapértelmezett hamis érték. Ha az értéke TRUE (igaz), a flush metódus nem lesz meghívva, ha a onBeforeUnload esemény-eseményindítók |
| enableSessionStorageBuffer | true | Alapértelmezett érték: true (igaz). Ha az értéke igaz, a rendszer az összes el nem juttatott telemetria rendelkező puffert tárolja a munkamenet-tárolóban. A rendszer visszaállítja a puffert az oldal betöltésekor |
| isCookieUseDisabled | hamis | Alapértelmezett hamis érték. Ha az érték TRUE (igaz), az SDK nem tárol és nem olvas be semmilyen cookie-t. Vegye figyelembe, hogy ez letiltja a felhasználói és munkamenet-cookie-kat, és használhatatlanná teszi a használati lapokat és a tartalmakat. |
| cookieDomain | null | Egyéni cookie-tartomány. Ez akkor hasznos, ha Application Insights cookie-kat szeretne megosztani altartományokon keresztül. |
| isRetryDisabled | hamis | Alapértelmezett hamis érték. Ha hamis, próbálkozzon újra 206 (részleges siker), 408 (időtúllépés), 429 (túl sok kérés), 500 (belső kiszolgálóhiba), 503 (a szolgáltatás nem érhető el) és 0 (offline, csak ha észlelhető) |
| isStorageUseDisabled | hamis | Ha az érték TRUE (igaz), az SDK nem tárolja és nem olvassa be a helyi és munkamenet-tárolóból származó összes adatforrást. Az alapértelmezett érték a false (hamis). |
| isBeaconApiDisabled | true | Ha hamis, az SDK az összes telemetria elküldi a [Beacon API](https://www.w3.org/TR/beacon) használatával |
| onunloadDisableBeacon | hamis | Alapértelmezett hamis érték. Ha a TAB be van zárva, az SDK az összes fennmaradó telemetria elküldi a [Beacon API](https://www.w3.org/TR/beacon) használatával. |
| sdkExtension | null | Beállítja az SDK-bővítmény nevét. Csak alfabetikus karakterek engedélyezettek. A bővítmény neve előtagként szerepel az "Ai. internal. sdkVersion" címkében (például "ext_javascript: 2.0.0"). Az alapértelmezett érték null. |
| isBrowserLinkTrackingEnabled | hamis | Az alapértelmezett érték a false (hamis). Ha az érték TRUE (igaz), az SDK nyomon fogja követni az összes [böngészőbeli hivatkozás](/aspnet/core/client-side/using-browserlink) kérését. |
| appId | null | A AppId az AJAX-függőségek közötti korrelációt használja a kiszolgálóoldali kérelmekkel az ügyfélen. Ha a Beacon API engedélyezve van, nem használható automatikusan, de manuálisan is beállítható a konfigurációban. Az alapértelmezett érték null |
| enableCorsCorrelation | hamis | Ha az értéke igaz, az SDK két fejlécet ("Request-id" és "Request-Context") ad hozzá az összes CORS-kérelemhez a kimenő AJAX-függőségek összekapcsolásához a kiszolgálói oldalon található megfelelő kérelmekkel. Az alapértelmezett érték false (hamis) |
| namePrefix | nem definiált | Egy nem kötelezően megadandó érték, amely a localStorage és a cookie neveként a Postfix nevet fogja használni.
| enableAutoRouteTracking | hamis | Az útvonalak változásainak automatikus követése egyoldalas alkalmazásokban (SPA). Ha az érték TRUE (igaz), akkor minden útvonal változása egy új oldalmegtekintést küld Application Insightsnak. A kivonatoló útvonalak változásai ( `example.com/foo#bar` ) új oldalletöltésekként is rögzítve lesznek.
| enableRequestHeaderTracking | hamis | Igaz értéke esetén az AJAX & a lekérési kérelmek fejlécének nyomon követése, az alapértelmezett érték a false.
| enableResponseHeaderTracking | hamis | Igaz értéke esetén a rendszer az AJAX & beolvasási kérelem válaszának fejléceit nyomon követi, az alapértelmezett érték a false.
| distributedTracingMode | `DistributedTracingModes.AI` | Beállítja az elosztott nyomkövetési módot. Ha AI_AND_W3C mód vagy W3C mód van beállítva, a rendszer a W3C nyomkövetési környezet fejléceit (traceparent/tracestate) hozza létre és tartalmazza az összes kimenő kérelemben. AI_AND_W3C biztosítva a visszamenőleges kompatibilitáshoz bármely örökölt Application Insights által biztosított szolgáltatással. Lásd [itt](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)a példát.
| enableAjaxErrorStatusText | hamis | Alapértelmezett hamis érték. Ha az értéke TRUE (igaz), a nem a sikertelen AJAX-kérelmekhez tartozó függőségi esemény szövege
| enableAjaxPerfTracking | hamis | Alapértelmezett hamis érték. A felkeresett és a további böngészőablakokat is tartalmazó jelző. a jelzett `ajax` (x/óra és Fetch) teljesítménybeli időzítések jelentettek mérőszámokat.
| maxAjaxPerfLookupAttempts | 3 | Az alapértelmezett érték 3. Az ablak keresésének maximális száma. a teljesítmény időzítése (ha elérhető), ez azért szükséges, mert nem minden böngésző tölti ki az ablakot. a teljesítmény az x/h-kérelem végének bejelentése előtt, valamint a beolvasási kérelmeknél ez a befejezés után lesz hozzáadva.
| ajaxPerfLookupDelay | 25 | Az alapértelmezett érték 25 MS. Az a várakozási idő, ameddig a rendszer újra megkísérli megkeresni a Windowst. a kérelmek teljesítményének időzítése `ajax` ezredmásodpercben történik, és közvetlenül a setTimeout () függvénynek lesz átadva.
| enableUnhandledPromiseRejectionTracking | hamis | Ha az értéke igaz, a nem kezelt ígéretek elutasítása automatikusan történik, és JavaScript-hibaként fog jelenteni. Ha a disableExceptionTracking értéke igaz (ne kövesse nyomon a kivételeket), a rendszer figyelmen kívül hagyja a konfigurációs értéket, és nem kezeli az ígéretek elutasítását.

## <a name="enable-time-on-page-tracking"></a>Időbeli nyomon követés engedélyezése

A beállítás szerint `autoTrackPageVisitTime: true` a felhasználó által az egyes lapokon töltött idő nyomon követhető. Minden egyes új Oldalmegtekintésnél az *előző* oldalon eltöltött felhasználói időtartamot [Egyéni metrikaként](../essentials/metrics-custom-overview.md) kell elküldeni `PageVisitTime` . Ez az egyéni metrika a [Metrikaböngésző](../essentials/metrics-getting-started.md) "napló alapú metrika" néven látható.

## <a name="enable-correlation"></a>Korreláció engedélyezése

A korreláció olyan adatokat hoz létre és küld, amelyek lehetővé teszik az elosztott nyomkövetést, valamint az [alkalmazás-hozzárendelést](../app/app-map.md), a [végpontok közötti tranzakciós nézetet](../app/app-map.md#go-to-details)és más diagnosztikai eszközöket.

Az alábbi példa a korreláció engedélyezéséhez szükséges összes lehetséges konfigurációt mutatja, az alábbi forgatókönyvekre vonatkozó megjegyzésekkel:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Ha az ügyfél által kommunikáló külső kiszolgálók közül bármelyik nem fogadja el a és a `Request-Id` `Request-Context` fejlécet, és nem tudja frissíteni a konfigurációját, akkor a konfigurációs tulajdonsággal kell őket egy kizárási listára helyeznie `correlationHeaderExcludeDomains` . Ez a tulajdonság támogatja a helyettesítő karaktereket.

A kiszolgálónak képesnek kell lennie a kapcsolatok fogadására a jelen fejlécekben. A kiszolgálóoldali `Access-Control-Allow-Headers` konfigurációtól függően gyakran szükséges a kiszolgálóoldali lista kiterjesztése a és a manuális hozzáadásával `Request-Id` `Request-Context` .

Hozzáférés-vezérlés engedélyezése – fejlécek: `Request-Id` , `Request-Context` , `<your header>`

> [!NOTE]
> Ha 2020-es vagy újabb kiadásban kiadott OpenTelemtry-vagy Application Insights SDK-kat használ, javasoljuk, hogy használja a [WC3 nyomkövetési környezet –](https://www.w3.org/TR/trace-context/)-t. [Itt](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)találja a konfigurációs útmutatást.

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Alapértelmezés szerint ez az SDK **nem** fogja kezelni az egyoldalas alkalmazásokban megjelenő, az állapot-alapú útvonalak módosítását. Az egyoldalas alkalmazás automatikus útvonal-módosítási nyomon követésének engedélyezéséhez hozzáadhat a `enableAutoRouteTracking: true` telepítési konfigurációhoz.

Jelenleg egy külön [reakciós beépülő modult](javascript-react-plugin.md)is kínálunk, amelyet az SDK-val inicializálhat. Emellett az útvonal-változások nyomon követését is végrehajtja az Ön számára, valamint más reagáló specifikus telemetria is gyűjthet.
> [!NOTE]
> Csak akkor használja `enableAutoRouteTracking: true` , ha **nem** az reagáló beépülő modult használja. Mindkettő képes az útvonal változásakor új oldalmegtekintések küldésére. Ha mindkettő engedélyezve van, a rendszer duplikált oldalmegtekintéseket küldhet.

## <a name="extensions"></a>Bővítmények

| Bővítmények |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Kattintson az Analytics automatikus gyűjtemény elemre.](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Böngésző-és ügyféloldali adat megismerése

A böngésző-és ügyféloldali adatokat a **metrikák** segítségével tekintheti meg, és hozzáadhatja az Önt érdeklő egyéni metrikákat:

![Képernyőkép a Application Insights metrikáinak oldaláról, amely egy webalkalmazás metrikáinak adatait jeleníti meg.](./media/javascript/page-view-load-time.png)

Az adatait a JavaScript SDK-ból is megtekintheti a portálon elérhető böngésző használatával.

Válassza a **böngésző** lehetőséget, majd válassza a **hibák** vagy a **teljesítmény** lehetőséget.

![Képernyőkép a Application Insights böngésző oldaláról, amely bemutatja, hogyan adhatók hozzá böngészőbeli hibák vagy a böngésző teljesítménye a webalkalmazáshoz megtekinthető mérőszámokhoz.](./media/javascript/browser.png)

### <a name="performance"></a>Teljesítmény

![Képernyőkép a Application Insights teljesítményéről, amely megjeleníti a webalkalmazás műveleti metrikáinak grafikus megjelenítését.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Függőségek

![Képernyőkép a teljesítmény oldaláról Application Insights a webalkalmazások függőségi metrikáinak megjelenítését bemutató ábrán.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Elemzés

A JavaScript SDK által gyűjtött telemetria lekéréséhez válassza a **megtekintés a naplókban (elemzés)** gombot. Egy utasítás hozzáadásával `where` `client_Type == "Browser"` csak a JavaScript SDK-ból és más SDK-k által gyűjtött kiszolgálóoldali telemetria fog adatokat kizárni.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Forrás-hozzárendelés támogatása

A kivétel telemetria minified hívási verem lehet unminified a Azure Portal. A kivétel részletei panelen lévő összes meglévő integráció az újonnan unminified hívási verem fog működni.

#### <a name="link-to-blob-storage-account"></a>BLOB Storage-fiókra mutató hivatkozás

A Application Insights-erőforrást saját Azure Blob Storage-tárolóhoz csatolhatja, hogy automatikusan felhasználja a hívási veremet. Az első lépésekhez tekintse meg az [automatikus forrás-hozzárendelés támogatását](./source-map-support.md)ismertető témakört.

### <a name="drag-and-drop"></a>Drag and drop

1. Válasszon ki egy kivételt telemetria elemet a Azure Portal a "végpontok közötti tranzakció részletei" megtekintéséhez.
2. Azonosítsa, hogy mely forrás-térképek felelnek meg ehhez a hívási veremnek. A forrás-hozzárendelésnek egyeznie kell egy verem-keret forrásfájljait, de utótagja `.map`
3. Húzza a forrás leképező térképeket a hívási verembe a Azure Portal ![ egy animált képet, amely bemutatja, hogyan húzhatja át a forrás-leképezési fájlokat egy Build mappából a Azure Portal hívási verem ablakába.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights webes alapszintű

A könnyű használhatóság érdekében telepítheti az Application Insights alapszintű verzióját is
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ez a verzió a szolgáltatások és funkciók minimális számát tartalmazza, és arra támaszkodik, hogy felépítse azt, ahogy jónak látja. Például nem hajt végre autocollectiont (nem kezelt kivételeket, AJAX-t stb.). Az egyes telemetria-típusok (például stb.) küldésére szolgáló API `trackTrace` `trackException` -k nem szerepelnek ebben a verzióban, ezért meg kell adnia a saját burkolóját. Az egyetlen elérhető API `track` . Itt található egy [minta](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) .

## <a name="examples"></a>Példák

A futtatható-példákat lásd: [Application Insights JavaScript SDK-minták](https://github.com/Azure-Samples?q=applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Frissítés a Application Insights korábbi verziójáról

Az SDK v2 verziójában feltört változások:
- A jobb API-aláírások engedélyezéséhez néhány API-hívás (például a trackPageView és a trackException) frissült. Az Internet Explorer 8 és a böngésző korábbi verziói nem támogatottak.
- Az telemetria-borítékban az Adatséma frissítései miatt a mező neve és szerkezete módosul.
- Áthelyezve a következő helyre: `context.operation` `context.telemetryTrace` . Egyes mezők is módosultak ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Az aktuális oldalmegtekintési azonosító (például a SPA-alkalmazások) manuális frissítéséhez használja a következőt: `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > A nyomkövetési azonosító egyediként való megtartásához, ahol korábban már használta, használja a következőt: `Util.newId()` `Util.generateW3CId()` . Mindkét esetben végül a művelet azonosítója jelenik meg.

Ha az aktuális Application bepillantást előállító SDK-t (1.0.20) használja, és szeretné megtekinteni, hogy az új SDK működik-e futásidőben, frissítse az URL-címet az aktuális SDK-betöltési forgatókönyvtől függően.

- Letöltés CDN-forgatókönyv használatával: frissítse a jelenleg használt kódrészletet a következő URL-címre való Rámutatás céljából:
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- NPM-forgatókönyv: meghívja a `downloadAndSetup` teljes ApplicationInsights-szkript letöltését a CDN-ből, és inicializálja a kialakítási kulccsal:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

Tesztelés belső környezetben a figyelés telemetria ellenőrzéséhez a várt módon működik. Ha minden működik, frissítse API-aláírásait az SDK v2 verziójára, és telepítse az üzemi környezetekben.

## <a name="sdk-performanceoverhead"></a>SDK-teljesítmény/terhelés

Ha mindössze 36 KB-os tömörített, és csak ~ 15 MS-ot szeretne inicializálni, a Application Insights elhanyagolható mennyiségű loadtime hoz létre a webhelyhez. A kódrészlet használatával a könyvtár minimális összetevői gyorsan betöltődik. Addig a teljes szkript le lesz töltve a háttérben.

A parancsfájl a CDN-ből való letöltését követően az oldal összes nyomon követését várólistára helyezi. Miután a letöltött parancsfájl aszinkron módon inicializálja az inicializálást, a rendszer a várólistára helyezett összes eseményt nyomon követte. Ennek eredményeképpen az oldal teljes életciklusa során semmilyen telemetria nem fog elveszíteni. Ez a telepítési folyamat zökkenőmentes analitikai rendszerrel rendelkező oldalt biztosít a felhasználók számára.

> Összegzés:
> - ![NPM verziója](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip tömörített méret](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 MS** teljes inicializálási idő
> - A lap életciklusa során kimaradt **nulla** követés

## <a name="browser-support"></a>Böngészőtámogatás

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Operát](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
A Chrome legújabb ✔ |  Firefox legújabb ✔ | IE 9 + & Edge ✔<br>IE 8 – kompatibilis | Az Opera legújabb ✔ | A Safari legújabb ✔ |

## <a name="es3ie8-compatibility"></a>ES3/IE8-kompatibilitás

SDK-ként számos felhasználó nem tudja szabályozni az ügyfelek által használt böngészőket. Ennek megfelelően biztosítania kell, hogy ez az SDK továbbra is "work" maradjon, és ne szakítsa meg a JS-végrehajtást egy régebbi böngészővel való betöltéskor. Habár ideális megoldás az IE8 és a régebbi generációs (ES3) böngészők támogatására, számos nagy ügyfelet/felhasználót kell megadnia, amely továbbra is a "work" lapokat igényli, és azt is, hogy a végfelhasználók milyen böngészőt használnak a felhasználók számára.

Ez nem jelenti azt, hogy csak a legkisebb közös funkciókat fogjuk támogatni, csak azt, hogy a ES3-kód kompatibilitását és a hozzájuk tartozó új szolgáltatások hozzáadását olyan módon kell hozzáadni, amely nem szakítja meg a ES3 JavaScript-elemzést, és nem vehető fel választható szolgáltatásként.

[Az IE8-támogatással kapcsolatos részletes információkért lásd: GitHub](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

A Application Insights JavaScript SDK nyílt forráskódú a forráskód megtekintéséhez vagy a projekthez való hozzájáruláshoz, látogasson el a [hivatalos GitHub-tárházba](https://github.com/Microsoft/ApplicationInsights-JS). 

A legújabb frissítések és hibajavítások [olvassa el a kibocsátási megjegyzéseket](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> További lépések
* [Használat követése](usage-overview.md)
* [Egyéni események és a mérőszámok](api-custom-events-metrics.md)
* [Összeállítás, mérés, tanulás](usage-overview.md)
* [Az SDK betöltési hibájának hibaelhárítása](javascript-sdk-load-failure.md)
