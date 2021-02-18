---
title: A Node.js szolgáltatások figyelése az Azure Application Insights segítségével | Microsoft Docs
description: Teljesítmény figyelése és problémák diagnosztizálása a Node.js szolgáltatásokban az Application Insights segítségével.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9eba74bcf2b4b047b3ed881342db4f7b1011f928
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585765"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>A Node.js szolgáltatások és appok figyelése az Application Insights segítségével

Az üzembe helyezést követően [Application Insights](./app-insights-overview.md) figyeli a háttérbeli szolgáltatásokat és összetevőket, hogy segítsen felderíteni és gyorsan diagnosztizálni a teljesítményt és egyéb problémákat. Az adatközpontban, az Azure-beli virtuális gépeken és a webalkalmazásokban, sőt más nyilvános felhőkben üzemeltetett Node.js-szolgáltatásokhoz Application Insights is használhatja.

A megfigyelési adatok fogadásához, tárolásához és vizsgálatához építse be az SDK-t a programkódba, majd állítson be egy megfelelő Application Insights-erőforrást az Azure-ban. Az SDK ennek az erőforrásnak küldi az adatokat további elemzés és vizsgálat céljából.

A Node.js SDK automatikusan képes figyelni a bejövő és kimenő HTTP-kéréseket, kivételeket és bizonyos rendszermérőszámokat. Az SDK a 0,20-es verziótól kezdődően bizonyos gyakori [külső csomagokat](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)is képes figyelni, például a MongoDB-t, a MySQL-t és a Redis-t. Az egyes bejövő HTTP-kérésekhez kapcsolódó összes eseményt összekapcsolja a gyorsabb hibaelhárítás érdekében.

A TelemetryClient API használatával manuálisan beállíthatók és monitorozhatók az alkalmazás és a rendszer további részletei. A TelemetryClient API-t a jelen cikk egy későbbi részében részletesebben ismertetjük.

## <a name="get-started"></a>Bevezetés

Egy alkalmazás vagy szolgáltatás monitorozásának beállításához a következő feladatokat kell elvégezni.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg róla, hogy rendelkezik Azure-előfizetéssel, vagy [igényeljen ingyenesen egy újat][azure-free-offer]. Ha szervezete már rendelkezik Azure-előfizetéssel, egy rendszergazda [az alábbi utasítások követésével][add-aad-user] Önt is hozzá tudja adni az előfizetéshez.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Application Insights-erőforrás beállítása

1. Jelentkezzen be az [Azure Portalra][portal].
2. [Application Insights-erőforrás létrehozása](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> A Node.js SDK beállítása

Építse be az SDK-t az alkalmazásba, hogy az adatokat tudjon gyűjteni.

> [!IMPORTANT]
> Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=nodejs) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.

1. Másolja az erőforrás rendszerállapot-kulcsát (más néven *rendszerállapotkulcsot*) az újonnan létrehozott erőforrásból. Az Application Insights a rendszerállapotkulcs segítségével rendeli hozzá az adatokat az Azure-erőforráshoz. Ahhoz, hogy az SDK használni tudja a rendszerállapotkulcsot, meg kell azt adni a programkód egy környezeti változójában.  

   ![Rendszerállapotkulcs másolása](./media/nodejs/instrumentation-key-001.png)

2. Adja hozzá a Node.js SDK-kódtárat az alkalmazás függőségeihez a package.json lapon. Futtassa az app gyökérkönyvtárából az alábbi parancsot:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Ha írógéppel használ, ne telepítsen külön "gépelési" csomagokat. Ez az NPM-csomag beépített tipizálásokat tartalmaz.

3. Explicit módon töltse be a kódtárat a programkódba. Mivel az SDK a rendszerállapotot több más kódtárba is beépíti, a kódtárat a lehető leghamarabb be kell betölteni, akár más `require`-utasítások előtt.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  A rendszerállapotkulcsot a környezeti változón keresztül is megadhatja `APPINSIGHTS_INSTRUMENTATIONKEY` ahelyett, hogy manuálisan átadná azt a vagy a rendszernek  `setup()` `new appInsights.TelemetryClient()` . Ez az eljárás lehetővé teszi, hogy ne írja be az erőforráskulcsot a jóváhagyott forráskódba, és eltérő erőforráskulcsot adjon meg az eltérő környezeteknél. A manuális hívás konfigurálásához `appInsights.setup('[your ikey]');` .

    További konfigurációs részletekért lásd a következő szakaszokat.

    Az SDK-t telemetria küldése nélkül is kipróbálhatja az `appInsights.defaultClient.config.disableAppInsights = true` beállításával.

5. Az adatok automatikus gyűjtésének és küldésének megkezdése hívással `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Az alkalmazás figyelése

Az SDK automatikusan gyűjt telemetria az Node.js futtatókörnyezetről és néhány gyakori külső modulról. Az alkalmazás használatával gyűjtsön össze néhányat ezekből az adatokból.

Ezután az [Azure Portalon][portal] lépjen a korábban létrehozott Application Insights-erőforráshoz. Az **Áttekintő idővonalon** tekintse meg az első néhány adatpontot. Részletesebb adatokért válasszon a diagramok különböző összetevői közül.

Az alkalmazáshoz felderített topológia megtekintéséhez használhatja az [alkalmazás-hozzárendelést](app-map.md).

#### <a name="no-data"></a>Nincsenek adatkészletek

Mivel az SDK kötegeli az adatokat az elküldéshez, az elemek késve jelenhetnek meg a portálon. Ha nem lát adatokat az erőforrásában, próbálja elvégezni valamelyik javítást az alábbiak közül:

* Folytassa az alkalmazás használatát. Végezzen el több műveletet, hogy több telemetria jöjjön létre.
* Kattintson a **Frissítés** gombra a portál erőforrásnézetében. A diagramok adott időközönként maguktól frissülnek, de manuálisan azonnal is frissíthetők.
* Ellenőrizze, hogy a [szükséges kimenő portok](./ip-addresses.md) nyitva vannak-e.
* [Keressen rá](./diagnostic-search.md) bizonyos eseményekre.
* Tekintse meg a [gyakori kérdésekkel foglalkozó részt][FAQ].

## <a name="basic-usage"></a>Alapszintű használat

A HTTP-kérések, a népszerű harmadik féltől származó függvénytár-események, a nem kezelt kivételek és a rendszermetrikák beépített gyűjteménye:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Ha a kialakítási kulcs be van állítva a környezeti változóban `APPINSIGHTS_INSTRUMENTATIONKEY` , argumentum nélkül hívható meg `.setup()` . Ez megkönnyíti a különböző erőforráskulcsot használatát a különböző környezetekben.

A többi csomag betöltése előtt töltse be a Application Insights könyvtárat, a lehető leghamarabb a `require("applicationinsights")` parancsfájlokba. Erre azért van szükség, hogy a Application Insights-függvénytár később is előkészítse a követéshez szükséges csomagokat. Ha hasonló előkészítéssel ütközik más könyvtárakkal, próbálja meg ezeket a Application Insights könyvtárat.

Mivel a JavaScript kezeli a visszahívásokat, további munkára van szükség a külső függőségek és a későbbi visszahívások közötti kérések nyomon követéséhez. Alapértelmezés szerint ez a további követés engedélyezve van; tiltsa le az `setAutoDependencyCorrelation(false)` alábbi, a [konfiguráció](#sdk-configuration) részben leírtak szerint.

## <a name="migrating-from-versions-prior-to-022"></a>Áttelepítés a 0,22 előtti verziókról

Az 0,22-es és későbbi verziókban megjelenő kiadások között megszakad a változás. Ezek a változások úgy lettek kialakítva, hogy konzisztensek legyenek más Application Insights SDK-k és a jövőbeli bővíthetőség lehetővé tétele

Általánosságban elvégezhető a Migrálás a következővel:

- Cserélje le a hivatkozásokat a következőre: `appInsights.client` `appInsights.defaultClient` .
- Hivatkozások `appInsights.getClient()` cseréje a következőre `new appInsights.TelemetryClient()`
- Az összes argumentumot cserélje le az Client. Track * metódusokra egyetlen objektummal, amely argumentumként nevesített tulajdonságokat tartalmaz. Az IDE tartozó beépített típusú célzási vagy [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) megtekintheti az egyes telemetria a kivételt képező objektumot.

Ha az SDK konfigurációs funkcióit anélkül éri el, hogy elvégezte őket a szolgáltatáshoz `appInsights.setup()` , mostantól megkeresheti ezeket a függvényeket `appInsights.Configurations` (például: `appInsights.Configuration.setAutoCollectDependencies(true)` ). Tekintse át az alapértelmezett konfiguráció módosításait a következő szakaszban.

## <a name="sdk-configuration"></a>SDK konfigurálása

Az `appInsights` objektum számos konfigurációs módszert biztosít. A következő kódrészletben vannak felsorolva az alapértelmezett értékekkel.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Ahhoz, hogy teljes körűen megállapíthassa egy szolgáltatás eseményeinek korrelációját, mindenképpen be kell állítania a `.setAutoDependencyCorrelation(true)` értéket. E beállítás lehetővé teszi, hogy a Node.js-ben az SDK kövesse a kontextust az aszinkron visszahívások során.

Tekintse át a leírásokat az IDE beépített Type Hinting vagy [applicationinsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) fájljában, ahol részletes információkat talál a vezérlőről és választható másodlagos argumentumokról.

> [!NOTE]
>  Alapértelmezés szerint úgy `setAutoCollectConsole` van konfigurálva, hogy *kizárja* a hívásokat `console.log` (és más konzolos metódusokat). A rendszer csak a támogatott harmadik féltől származó (például Winston és Bunyan) adatgyűjtési hívásokat gyűjti. Ezt a viselkedést úgy módosíthatja, hogy a használatával a metódusok hívásait is tartalmazza `console` `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>Mintavételezés

Alapértelmezés szerint az SDK az összes összegyűjtött adatokat elküldi a Application Insights szolgáltatásnak. Ha nagy mennyiségű adatot gyűjt, érdemes lehet a mintavételezést engedélyezni a továbbított adatok mennyiségének csökkentése érdekében. Állítsa be az `samplingPercentage` `config` ügyfél objektumának mezőjét ennek elvégzéséhez. `samplingPercentage`A 100 (alapértelmezett) érték esetén a rendszer az összes adatküldés után a 0 értéket jelenti.

Ha automatikus korrelációt használ, az egyetlen kérelemhez társított összes adattal egységként fog szerepelni vagy kizárva.

A mintavétel engedélyezéséhez adja hozzá a következő kódot:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Több szerepkör a több összetevőt használó alkalmazásokhoz

Ha az alkalmazás több olyan összetevőből áll, amelyek mindegyikét ugyanazzal a kialakítási kulccsal szeretné kialakítani, és továbbra is ezeket az összetevőket külön egységként látja a portálon, mintha különálló rendszerállapot-kulcsokat (például az alkalmazás térképén külön csomópontokat) használ, akkor előfordulhat, hogy manuálisan kell konfigurálnia a RoleName mezőt, hogy megkülönböztesse az adott összetevő telemetria az Application Insights adatokat küldő más összetevőktől

A következő paranccsal állíthatja be a RoleName mezőt:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Harmadik féltől származó automatikus rendszerállapot-kialakítás

Az aszinkron hívások kontextusának nyomon követéséhez néhány módosításra van szükség harmadik féltől származó könyvtárakban (például MongoDB és Redis). Alapértelmezés szerint a Application Insights [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) a Monkey-patch néhány ilyen függvénytárat fog használni. Ezt a környezeti változó beállításával lehet letiltani `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` .

> [!NOTE]
> A környezeti változó beállításával előfordulhat, hogy az események már nem lesznek megfelelően társítva a megfelelő művelethez.

 Egyéni majom – a javítások letiltható úgy, hogy a `APPLICATION_INSIGHTS_NO_PATCH_MODULES` környezeti változót a letiltani kívánt csomagok vesszővel elválasztott listájára állítja (például: `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` ) a és a csomagok javításának elkerülése érdekében `console` `redis` .

Jelenleg kilenc csomag van kialakítva:,,,,,,, `bunyan` `console` `mongodb` `mongodb-core` `mysql` `redis` `winston` `pg` és `pg-pool` . Tekintse meg a [diagnosztikai csatorna-közzétevők információs fájlját](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) , amelyből megtudhatja, hogy a csomagok pontosan melyik verzióját kell megjavítani.

A `bunyan` , a `winston` és a `console` javítások Application Insights nyomkövetési eseményeket eredményeznek attól függően, hogy `setAutoCollectConsole` engedélyezve van-e. A REST Application Insights függőségi eseményeket fog előállítani attól függően `setAutoCollectDependencies` , hogy engedélyezve van-e.

### <a name="live-metrics"></a>Élő metrikák

Ha engedélyezni szeretné az élő metrikák küldését az alkalmazásból az Azure-ba, használja a következőt: `setSendLiveMetrics(true)` . Az élő metrikák szűrése a portálon jelenleg nem támogatott.

### <a name="extended-metrics"></a>Kiterjesztett mérőszámok

> [!NOTE]
> Bővített natív metrikák küldésének lehetősége a következő verzióban lett hozzáadva: 1.4.0

Ha engedélyezni szeretné a kiterjesztett natív metrikák küldését az alkalmazásból az Azure-ba, telepítse a különálló natív metrikai csomagot. Az SDK automatikusan betöltődik, ha telepítve van, és megkezdi Node.js natív metrikák gyűjtését.

```bash
npm install applicationinsights-native-metrics
```

Jelenleg a natív metrikai csomag a Garbage Collection CPU-idő, az esemény hurok-osztásjelek és a halom használatának alapszámú gyűjtését végzi el:

- **Szemét-gyűjtemény**: az egyes típusú Garbage-gyűjteményekre fordított CPU-idő mennyisége, valamint az egyes típusok számának gyakorisága.
- **Esemény-hurok**: hány órajel történt, és mennyi CPU-időt töltöttek fel az összeg.
- **Halom vs nem kupac**: az alkalmazás memóriahasználat mennyisége a kupacban vagy a nem halomban van.

### <a name="distributed-tracing-modes"></a>Elosztott nyomkövetési módok

Alapértelmezés szerint az SDK egy Application Insights SDK-val ellátott más alkalmazások/szolgáltatások által értelmezett fejléceket küld. Engedélyezheti a [W3C nyomkövetési környezet](https://github.com/w3c/trace-context) fejlécek küldését és fogadását is a meglévő AI-fejléceken kívül, így nem fogja megszakítani a korrelációt a meglévő örökölt szolgáltatásaival. A W3C-fejlécek engedélyezése lehetővé teszi, hogy az alkalmazás más, a Application Insights által nem kiépített szolgáltatásokkal is korreláljon, de ezt a W3C szabványt kell alkalmaznia.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient API

A TelemetryClient API részletes leírásával kapcsolatban lásd az [egyéni eseményekhez és a mérőszámokhoz rendelkezésre álló Application Insights API-t](./api-custom-events-metrics.md).

Az Application Insights Node.js SDK használatával bármilyen kérést, eseményt, mérőszámot vagy kivételt követhet. Az alábbi példakód a használható API-k közül mutat be néhányat.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Függőségek követése

A következő kóddal követheti a függőségeket:

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Egy példa a használatával `trackMetric` , amellyel mérhető, hogy mennyi ideig tart az Event loop ütemezése:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Egyéni tulajdonság hozzáadása eseményekhez

A következő kóddal adhat hozzá egyéni tulajdonságot minden eseményhez:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET-kérések követése

A HTTP GET kérések manuális nyomon követéséhez használja a következő kódot:

> [!NOTE]
> Alapértelmezés szerint minden kérés nyomon van követve. Az automatikus gyűjtés letiltásához hívja a. setAutoCollectRequests (false) metódust a Start () hívása előtt.

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

A kérelmeket a metódussal is nyomon követheti `trackNodeHttpRequest` :

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>Kiszolgáló indítási idejének követése

A következő kóddal követheti a kiszolgáló indítási idejét:

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="flush"></a>Flush

Alapértelmezés szerint a telemetria 15 másodpercig pufferelt, mielőtt a rendszer elküldi a betöltési kiszolgálónak. Ha az alkalmazás rövid élettartammal (például CLI-eszközzel) rendelkezik, szükség lehet a pufferelt telemetria manuális kiürítésére az alkalmazás leállításakor `appInsights.defaultClient.flush()` .

Ha az SDK észleli, hogy az alkalmazás összeomlik, a rendszer a kiürítést hívja meg Önnek `appInsights.defaultClient.flush({ isAppCrashing: true })` . A flush beállítással az `isAppCrashing` alkalmazás rendellenes állapotban van, nem alkalmas a telemetria küldésére. Az SDK Ehelyett az összes pufferelt telemetria menti az [állandó tárterületre](./data-retention-privacy.md#nodejs) , és lehetővé teszi az alkalmazás befejezését. Az alkalmazás újraindításakor a rendszer megpróbálja elküldeni az állandó tárolóba mentett összes telemetria.

### <a name="preprocess-data-with-telemetry-processors"></a>Az telemetria processzorokkal rendelkező adatfeldolgozás

Az összegyűjtött adatokat feldolgozhatja és szűrheti, mielőtt elküldi az adatmegőrzést a *telemetria processzorok* használatával. A telemetria-processzorokat a rendszer a telemetria-elemek felhőbe való küldése előtt a hozzájuk rendelt sorrendben hívja meg.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Ha egy telemetria-processzor hamis értéket ad vissza, a rendszer nem küldi el a telemetria-elemeket.

Az összes telemetria-feldolgozó megkapja a telemetria-és a hozzá tartozó borítékot a vizsgálathoz és a módosításhoz. Emellett környezeti objektumokat is kapnak. Ennek az objektumnak a tartalmát a paraméter határozza meg a `contextObjects` nyomon követett telemetria követési módszerének meghívásakor. Az automatikusan összegyűjtött telemetria esetében ez az objektum az elérhető kérelmekkel kapcsolatos információkkal és az állandó kérelem tartalmával van kitöltve `appInsights.getCorrelationContext()` (ha az automatikus függőségi korreláció engedélyezve van).

A telemetria processzorhoz tartozó írógéppel-típus a következő:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Előfordulhat például, hogy egy olyan processzor, amely eltávolítja a kivételeket tartalmazó verem-nyomkövetési adatait, a következőképpen írható és adható meg:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Több kialakítási kulcs használata

Több Application Insights-erőforrást is létrehozhat, és a megfelelő kialakítási kulcsaik ("rendszerállapotkulcsot") használatával különböző adatforrásokat küldhet mindegyiknek.

 Például:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Speciális konfigurációs beállítások

Az ügyfél objektum olyan `config` tulajdonságot tartalmaz, amelyben számos opcionális beállítás található a speciális forgatókönyvek esetében. Ezeket a következőképpen lehet beállítani:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Ezek a tulajdonságok az ügyfél-specifikusak, így a `appInsights.defaultClient` szolgáltatással létrehozott ügyfelektől külön is konfigurálható `new appInsights.TelemetryClient()` .

| Tulajdonság                        | Leírás                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | A Application Insights erőforrás azonosítója.                                                      |
| endpointUrl                     | A betöltési végpont, amely a telemetria-adattartalmakat küldi el.                                                      |
| quickPulseHost                  | Az Élő metrikastream gazdagép, amely élő metrikákat küld a telemetria.                                            |
| proxyHttpUrl                    | Egy proxykiszolgáló az SDK HTTP-forgalmához (opcionális, alapértelmezetten a `http_proxy` környezeti változótól leállt).     |
| proxyHttpsUrl                   | Egy proxykiszolgáló az SDK HTTPS-forgalmához (opcionális, alapértelmezetten a `https_proxy` környezeti változótól leállt).   |
| httpAgent                       | Egy http. Az SDK HTTP-forgalmához használandó ügynök (nem kötelező, alapértelmezés szerint nincs meghatározva).                                   |
| httpsAgent                      | Egy HTTPS. Az SDK HTTPS-forgalmához használandó ügynök (nem kötelező, alapértelmezés szerint nincs meghatározva).                                 |
| maxBatchSize                    | Azon telemetria-elemek maximális száma, amelyeket a rendszer a betöltési végponthoz tartozó hasznos adatokba foglal (alapértelmezés szerint `250` ).   |
| maxBatchIntervalMs              | Az a maximális időtartam, ameddig egy hasznos adat eléri a maxBatchSize (alapértelmezett `15000` ).               |
| disableAppInsights              | Jelző, amely azt jelzi, hogy a telemetria-átvitel le van-e tiltva (alapértelmezett `false` ).                                 |
| samplingPercentage              | A nyomon követett telemetria elemek százalékos aránya (alapértelmezett `100` ).                      |
| correlationIdRetryIntervalMs    | Az a várakozási idő, amely után újra kell hívni az azonosítót az összetevők közötti korrelációhoz (alapértelmezett `30000` ).     |
| correlationHeaderExcludedDomains| Azoknak a tartományoknak a listája, amelyeket ki szeretne zárni az összetevők korrelációs fejlécének injektálásával (alapértelmezett: [config. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Következő lépések

* [A telemetria figyelése a portálon](./overview-dashboard.md)
* [Analytics-lekérdezések írása a telemetrián](../logs/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md
