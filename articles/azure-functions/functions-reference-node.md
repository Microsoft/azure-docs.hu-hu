---
title: JavaScript fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a JavaScript használatával.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 03/07/2021
ms.custom: devx-track-js
ms.openlocfilehash: 971fb2a3239614a708e14c109e567081f1ec9ff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614904"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript fejlesztői útmutató

Ez az útmutató részletes információkat tartalmaz, amelyek segítenek a Azure Functions JavaScript használatával való sikeres fejlesztésében.

Express.js, Node.js vagy JavaScript-fejlesztőként, ha még nem ismeri a Azure Functionst, először olvassa el a következő cikkek egyikét:

| Első lépések | Alapelvek| Interaktív tanulás |
| -- | -- | -- | 
| <ul><li>[Node.js függvény a Visual Studio Code használatával](./create-first-function-vs-code-node.md)</li><li>[Node.js függvény a Terminal/Command parancssorral](./create-first-function-cli-node.md)</li><li>[Node.js függvény a Azure Portal használatával](functions-create-function-app-portal.md)</li></ul> | <ul><li>[Fejlesztői útmutató](functions-reference.md)</li><li>[Üzemeltetési lehetőségek](functions-scale.md)</li><li>[Írógéppel függvények](#typescript)</li><li>[Teljesítménnyel &nbsp; kapcsolatos megfontolások](functions-best-practices.md)</li></ul> | <ul><li>[Kiszolgáló nélküli alkalmazás létrehozása](/learn/paths/create-serverless-applications/)</li><li>[Refrakció Node.js és expressz API-k kiszolgáló nélküli API-khoz](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>JavaScript-függvény alapjai

A JavaScript (Node.js) függvény egy exportált művelet, `function` amely az aktiváláskor fut ([a triggerek function.jsbe van állítva](functions-triggers-bindings.md)). Az összes függvénynek átadott első argumentum egy `context` objektum, amely a kötési adatok fogadására és küldésére, a naplózásra és a futtatókörnyezettel folytatott kommunikációra szolgál.

## <a name="folder-structure"></a>Mappa szerkezete

A JavaScript-projekthez szükséges mappastruktúrát a következőhöz hasonlóan néz ki. Ez az alapértelmezett érték módosítható. További információért lásd az alábbi [scriptFile](#using-scriptfile) szakaszt.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

A projekt gyökerében van egy megosztott [host.jsa](functions-host-json.md) fájlban, amely a Function alkalmazás konfigurálására használható. Minden függvényhez tartozik egy, a saját programkódját (. js) és a kötési konfigurációs fájlt (function.json) tartalmazó mappa. A `function.json` szülő könyvtár neve mindig a függvény neve.

A függvények futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési kiterjesztések a fájlban vannak meghatározva `extensions.csproj` , és a mappában lévő tényleges függvénytárak fájljai vannak megadva `bin` . Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](./functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

## <a name="exporting-a-function"></a>Függvény exportálása

A JavaScript-függvényeket a [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (vagy) használatával kell exportálni [`exports`](https://nodejs.org/api/modules.html#modules_exports) . Az exportált függvénynek JavaScript-függvénynek kell lennie, amely az aktiváláskor végrehajtódik.

Alapértelmezés szerint a functions Runtime a függvényét keresi a alkalmazásban `index.js` , ahol `index.js` ugyanazokat a szülő könyvtárat osztja meg a megfelelővel `function.json` . Az alapértelmezett esetben az exportált függvény csak a fájlból, vagy a (z) vagy a (z) nevű exportálásból exportálható `run` `index` . A fájl helyének és a függvény exportálási nevének konfigurálásához olvassa el a [függvény belépési pontjának konfigurálását](functions-reference-node.md#configure-function-entry-point) alább.

Az exportált függvény számos argumentumot adott meg a végrehajtáskor. Az első szükséges argumentum mindig egy `context` objektum. Ha a függvény szinkronban van (nem ad vissza ígéretet), akkor meg kell adnia az `context` objektumot, mivel a `context.done` helyes használathoz hívás szükséges.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Aszinkron függvény exportálása
Ha egy [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarációt vagy egyszerű JavaScriptet [ígér](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a functions futtatókörnyezet 2. x verziójában, akkor nem kell explicit módon meghívnia a [`context.done`](#contextdone-method) visszahívást, hogy jelezze a függvény befejezését. A függvény akkor fejeződik be, amikor az exportált aszinkron függvény/ígéret befejeződik. Az 1. x verziójú futtatókörnyezetet megcélzó függvények esetén a kód végrehajtásának befejezése után is meg kell hívni a hívást [`context.done`](#contextdone-method) .

A következő példa egy egyszerű függvény, amely naplózza, hogy elindították, és azonnal befejezi a végrehajtást.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Aszinkron függvény exportálásakor a kimeneti kötést is konfigurálhatja az érték megadásához `return` . Ez akkor ajánlott, ha csak egy kimeneti kötése van.

Ha a használatával szeretne kimenetet rendelni `return` , módosítsa a `name` tulajdonságot a `$return` következőre: `function.json` .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Ebben az esetben a függvénynek a következő példához hasonlóan kell kinéznie:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Kötések 
A JavaScriptben a [kötések](functions-triggers-bindings.md) konfigurálva és definiálva vannak a függvény function.jsján. A függvények számos módon működnek együtt a kötésekkel.

### <a name="inputs"></a>Bevitelek
A bemenet két kategóriára oszlik Azure Functionsban: az egyik az trigger bemenete, a másik pedig a további bemenet. Az trigger és más bemeneti kötések (-kötések `direction === "in"` ) háromféle módon olvashatók be a függvényekben:
 - **_[Ajánlott]_ A függvénynek átadott paraméterek.** A függvénynek ugyanolyan sorrendben kell átadni őket, mint *function.js*. A `name` *function.json* elemben definiált tulajdonságnak nem kell megegyeznie a paraméter nevével, bár ennek a következőnek kell lennie:.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Az objektum tagjaként [`context.bindings`](#contextbindings-property) .** Minden egyes tagot afunction.jsáltal `name` meghatározott tulajdonság nevez el.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **A JavaScript-objektumot használó bemenetként [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) .** Ez lényegében ugyanaz, mint a bemenetek paraméterként való továbbítása, de lehetővé teszi a bemenetek dinamikus kezelését.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Kimenetek
A () kimeneteit a `direction === "out"` függvény számos módon képes írni. Minden esetben a `name` kötés *function.jsban* definiált tulajdonsága megegyezik a függvényben írt Object tag nevével. 

Az alábbi módszerek egyikével rendelhet adatokat a kimeneti kötésekhez (ne kombinálja ezeket a metódusokat):

- **_[Több kimenet esetén ajánlott)_ Objektum visszaadása.** Ha aszinkron/Promise returning függvényt használ, a hozzárendelt kimeneti adatokat tartalmazó objektumot adhat vissza. Az alábbi példában a kimeneti kötések neve "httpResponse" és "queueOutput", *function.jsa* következőben:.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Ha szinkron függvényt használ, ezt az objektumot a [`context.done`](#contextdone-method) (lásd: példa) használatával adhatja vissza.
- **_[Egyetlen kimenethez ajánlott_ Közvetlenül egy értéket ad vissza, és a $return kötési nevet használja.** Ez csak az aszinkron/Promise Returns függvények esetében működik. Lásd: példa az [aszinkron függvények exportálására](#exporting-an-async-function). 
- **Értékek kiosztása `context.bindings` a** következőhöz Az értékeket közvetlenül is hozzárendelheti a Context. kötésekhez.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Kötések adattípusa

Egy bemeneti kötés adattípusának megadásához használja a `dataType` tulajdonságot a kötés definíciójában. Ha például bináris formátumban szeretné beolvasni egy HTTP-kérelem tartalmát, használja a következőt `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

A következő lehetőségei `dataType` : `binary` , `stream` és `string` .

## <a name="context-object"></a>környezeti objektum

A futásidejű objektum használatával `context` továbbítja a függvény és a futtatókörnyezet adatait. A kötések adatainak olvasására és beállítására, valamint a naplókba való írásra használatos, az `context` objektum mindig a függvénynek átadott első paraméter.

A szinkron kódot tartalmazó függvények esetében a környezeti objektum tartalmazza `done` azt a visszahívást, amelyet a függvény feldolgozásakor hív meg. `done`Ha az aszinkron kód írásakor explicit módon meghívja a hívást, a `done` visszahívás implicit módon lesz meghívva.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

A függvénynek átadott környezet egy tulajdonságot tesz elérhetővé `executionContext` , amely egy olyan objektum, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság neve  | Típus  | Leírás |
|---------|---------|---------|
| `invocationId` | Sztring | Egyedi azonosítót biztosít az adott függvény meghívásához. |
| `functionName` | Sztring | A futó függvény nevét adja meg. |
| `functionDirectory` | Sztring | Megadja a functions app könyvtárat. |

Az alábbi példa bemutatja, hogyan lehet visszaadni a következőt: `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>Context. binds tulajdonság

```js
context.bindings
```

Egy elnevezett objektumot ad vissza, amely a kötési adatok olvasására vagy hozzárendelésére szolgál. A bemeneti és trigger kötési adatok a tulajdonságok beolvasásával érhetők el `context.bindings` . A kimeneti kötési adatokat hozzá lehet rendelni az adatokat a következőhöz: `context.bindings`

Például a function.jskövetkező kötési definíciói lehetővé teszik a várólista tartalmának elérését, `context.bindings.myInput` és a kimenetek hozzárendelését egy várólistához a használatával `context.bindings.myOutput` .

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Kiválaszthatja, hogy a kimeneti kötési adatokat az `context.done` objektum helyett a metódussal definiálja `context.binding` (lásd alább).

### <a name="contextbindingdata-property"></a>Context. bindingData tulajdonság

```js
context.bindingData
```

Egy elnevezett objektumot ad vissza, amely trigger-metaadatokat és függvény Meghívási adatokat tartalmaz ( `invocationId` , `sys.methodName` , `sys.utcNow` , `sys.randGuid` ). Az eseményindító metaadatainak példáját itt tekintheti meg: [példa az Event hub](functions-bindings-event-hubs-trigger.md)-ra.

### <a name="contextdone-method"></a>Context. Done metódus

```js
context.done([err],[propertyBag])
```

Lehetővé teszi, hogy a futtatókörnyezet tudja, hogy a kód befejeződött. Ha a függvény a [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarációt használja, nem kell használnia `context.done()` . A `context.done` visszahívás implicit módon van meghívva. Az aszinkron függvények a 8. csomópontban vagy egy újabb verzióban érhetők el, amelyhez a functions futtatókörnyezet 2. x verziója szükséges.

Ha a függvény nem aszinkron függvény, **meg kell hívnia** , `context.done` hogy tájékoztassa a futtatást a függvény befejezéséről. Ha hiányzik a végrehajtás, időtúllépés történik.

A `context.done` metódus lehetővé teszi, hogy a felhasználó által definiált hibát visszaadja a futtatókörnyezetnek, valamint egy olyan JSON-objektumot, amely kimeneti kötési adatokat tartalmaz. `context.done`A tulajdonság az objektumon beállított bármilyen érték felülírásával lett átadva `context.bindings` .

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context. log metódus  

```js
context.log(message)
```

Lehetővé teszi, hogy az alapértelmezett nyomkövetési szinten írja a folyamatos átviteli függvény naplóit, és más naplózási szintek is elérhetők legyenek. A nyomkövetési naplózást a következő szakaszban részletesen ismertetjük. 

## <a name="write-trace-output-to-logs"></a>Nyomkövetési kimenet írása a naplókba

A függvények területen a `context.log` metódusokkal lehet nyomkövetési kimenetet írni a naplókba és a konzolba. A híváskor az `context.log()` üzenet a naplókba kerül az alapértelmezett nyomkövetési szinten, amely az _adatok_ nyomkövetési szintje. A függvények integrálva vannak az Azure Application Insights a Function app-naplók jobb rögzítése érdekében. A Application Insights a Azure Monitor részeként lehetővé teszi az alkalmazások telemetria és a nyomkövetési kimenetek gyűjtését, vizuális megjelenítését és elemzését. További információ: [monitoring Azure functions](functions-monitoring.md).

Az alábbi példa egy naplót ír az információs nyomkövetési szinten, beleértve a Meghívási azonosítót is:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Minden `context.log` metódus támogatja ugyanazt a paraméter-formátumot, amelyet a Node.js [util. Format metódus](https://nodejs.org/api/util.html#util_util_format_format)támogat. Vegye figyelembe a következő kódot, amely az alapértelmezett nyomkövetési szinttel írja be a függvény naplóit:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Ugyanezt a kódot a következő formátumban is megírhatja:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> Ne használja `console.log` nyomkövetési kimenetek írásához. Mivel `console.log` a rendszer a függvény alkalmazási szintjén rögzíti a kimenetet, nem kötődik egy adott függvény hívásához, és nem jelenik meg egy adott függvény naplóiban. Emellett a függvények futtatókörnyezetének 1. x verziója nem támogatja a használatát a `console.log` -konzolra való íráshoz.

### <a name="trace-levels"></a>Nyomkövetési szintek

Az alapértelmezett szinten kívül a következő naplózási módszerek is elérhetők, amelyek lehetővé teszik a függvények naplóinak adott nyomkövetési szinten való írását.

| Metódus                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **hiba (_üzenet_)**   | Hiba szintű eseményt ír a naplókba.   |
| **Figyelmeztetés (_üzenet_)**    | Figyelmeztetési szintű eseményt ír a naplókba. |
| **információ (_üzenet_)**    | Az adatszintű naplózás vagy az alacsonyabb értékre írja az adatokat.    |
| **részletes (_üzenet_)** | Írás a részletes szintű naplózásba.           |

Az alábbi példa a figyelmeztetési nyomkövetési szinten ugyanazt a naplót írja be az információs szint helyett:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Mivel a _hiba_ a legmagasabb nyomkövetési szint, ez a nyomkövetés minden nyomkövetési szinten a kimenetbe kerül, amíg a naplózás engedélyezve van.

### <a name="configure-the-trace-level-for-logging"></a>Nyomkövetési szint konfigurálása a naplózáshoz

A functions lehetővé teszi a küszöbértékek nyomkövetési szintjének meghatározását a naplókba vagy a konzolba való íráshoz. Az adott küszöbérték-beállítások a függvények futtatókörnyezetének verziójától függenek.

# <a name="v2x"></a>[v2. x +](#tab/v2)

A naplókba írt Nyomkövetések küszöbértékének megadásához használja a `logging.logLevel` tulajdonságot a host.jsfájljában. Ezzel a JSON-objektummal meghatározhatja a Function alkalmazás összes függvényének alapértelmezett küszöbértékét, és meghatározhatja az egyes függvények adott küszöbértékeit is. További információ: [a Azure functions figyelésének konfigurálása](configure-monitoring.md).

# <a name="v1x"></a>[v1. x](#tab/v1)

A naplókba és a konzolba írt összes nyomkövetés küszöbértékének megadásához használja a `tracing.consoleLevel` host.jsfájljában található tulajdonságot. Ez a beállítás a Function alkalmazás összes függvényére érvényes. A következő példa a nyomkövetési küszöbértéket állítja be a részletes naplózás engedélyezéséhez:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

A **consoleLevel** értékei a metódusok neveinek felelnek meg `context.log` . Ha le szeretné tiltani az összes nyomkövetési naplózást a konzolon, állítsa a **consoleLevel** beállítást _ki_ értékre. További információ: [host.jsa v1. x dokumentációjában](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Egyéni telemetria naplózása

Alapértelmezés szerint a függvények a kimenetet nyomkövetésként Application Insightsba írja. A további szabályozáshoz Ehelyett használhatja a [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) -t, hogy egyéni telemetria-adatait küldjön a Application Insights-példányra. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

A `tagOverrides` paraméter beállítja a `operation_Id` függvény Meghívási azonosítóját. Ez a beállítás lehetővé teszi egy adott függvény összes automatikusan generált és egyéni telemetria korrelációját.

## <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és-kötések

A HTTP-és webhook-eseményindítók és a HTTP-kimeneti kötések a HTTP-üzenetküldést a kérelmek és válaszok objektumok használatával jelölik.  

### <a name="request-object"></a>Kérelem objektum

A `context.req` (kérelem) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                    |
| ------------- | -------------------------------------------------------------- |
| _törzse_        | Egy objektum, amely tartalmazza a kérelem törzsét.               |
| _fejlécek_     | Egy objektum, amely a kérések fejléceit tartalmazza.                   |
| _metódus_      | A kérelem HTTP-metódusa.                                |
| _originalUrl_ | A kérelem URL-címe.                                        |
| _paraméterei_      | Egy objektum, amely a kérelem útválasztási paramétereit tartalmazza. |
| _lekérdezés_       | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  |
| _rawBody_     | Az üzenet törzse sztringként.                           |


### <a name="response-object"></a>Válaszobjektum

A `context.res` (válasz) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                               |
| --------- | --------------------------------------------------------- |
| _törzse_    | Egy objektum, amely tartalmazza a válasz törzsét.         |
| _fejlécek_ | Egy objektum, amely tartalmazza a válasz fejléceit.             |
| _isRaw_   | Azt jelzi, hogy a válasz formátuma kimarad.    |
| _állapota_  | A válasz HTTP-állapotkódot.                     |
| _sütik_ | A válaszban beállított HTTP cookie-objektumok tömbje. A HTTP cookie-objektumok egy `name` , `value` , és más cookie-tulajdonságokkal rendelkeznek, például `maxAge` vagy `sameSite` . |

### <a name="accessing-the-request-and-response"></a>A kérelem és válasz elérése 

HTTP-eseményindítók használata esetén a HTTP-kérelem és a válasz-objektumok többféleképpen is elérhetők:

+ **`req` `res` Az objektumról és tulajdonságairól `context` .** Így a hagyományos minta használatával érheti el a HTTP-adatok elérését a környezeti objektumból ahelyett, hogy a teljes mintát kellene használnia `context.bindings.name` . Az alábbi példa bemutatja, hogyan érheti el a és az objektumokat a következőn `req` `res` `context` :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Az elnevezett bemeneti és kimeneti kötések közül.** Így a HTTP-trigger és a kötések ugyanúgy működnek, mint bármely más kötés. A következő példa egy nevesített kötés használatával állítja be a válasz objektumot `response` : 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Válasz csak]_ A hívásával `context.res.send(body?: any)` .** A rendszer egy HTTP-választ hoz létre bemenetként `body` a válasz törzsében. `context.done()` implicit módon van meghívva.

+ **_[Válasz csak]_ A hívásával `context.done()` .** A speciális HTTP-kötés a metódusnak átadott választ adja vissza `context.done()` . A következő HTTP kimeneti kötés meghatározza a `$return` kimeneti paramétert:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Skálázás és Egyidejűség

Alapértelmezés szerint a Azure Functions automatikusan figyeli az alkalmazás terhelését, és szükség esetén további gazdagép-példányokat hoz létre a Node.jshoz. A functions beépített (nem a felhasználó által konfigurálható) küszöbértékeket használ a különböző típusú triggerekhez, hogy eldöntse, mikor kell hozzáadnia a példányokat, például az üzenetek korát és a várólista méretét a QueueTrigger. További információ: [How the fogyasztás and Premium Plans Work](event-driven-scaling.md).

Ez a skálázási viselkedés elegendő sok Node.js alkalmazáshoz. A CPU-kötésű alkalmazásokhoz több nyelvi feldolgozói folyamat használatával növelheti a teljesítményt.

Alapértelmezés szerint minden functions Host-példány egyetlen nyelvi munkavégző folyamattal rendelkezik. A [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) alkalmazás beállításával növelheti a munkavégző folyamatok számát a gazdagépen (legfeljebb 10). Azure Functions ezt követően megpróbál egyenletesen terjeszteni egyidejű függvényeket a feldolgozók között. 

A FUNCTIONS_WORKER_PROCESS_COUNT minden olyan gazdagépre vonatkozik, amelyet a functions hoz létre, amikor az alkalmazás az igények kielégítése érdekében felskálázást végez. 

## <a name="node-version"></a>Csomópont verziója

A következő táblázat az operációs rendszer által támogatott Node.js-verziókat mutatja be a functions futtatókörnyezet egyes főbb verzióihoz:

| Függvények verziója | Csomópont verziója (Windows) | Csomópont verziója (Linux) |
|---|---| --- |
| 3. x (ajánlott) | `~14` ajánlott<br/>`~12`<br/>`~10` | `node|14` ajánlott<br/>`node|12`<br/>`node|10` |
| 2. x  | `~12`<br/>`~10`<br/>`~8` | `node|10`<br/>`node|8`  |
| 1. x | 6.11.2 (a futtatókörnyezet zárolta) | n.a. |

A futtatókörnyezet által használt aktuális verziót `process.version` bármely függvényből megtekintheti.

### <a name="setting-the-node-version"></a>A csomópont verziójának beállítása

A Windows Function apps esetében az `WEBSITE_NODE_DEFAULT_VERSION` [alkalmazás beállítását](functions-how-to-use-azure-function-app-settings.md#settings) egy támogatott LTS-verzióra kell beállítania az Azure-ban `~14` .

Linux Function-alkalmazások esetén futtassa a következő Azure CLI-parancsot a csomópont verziójának frissítéséhez.

```bash
az functionapp config set --linux-fx-version "node|14" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

## <a name="dependency-management"></a>Függőségkezelés
Ha a JavaScript-kódban a közösségi kódtárakat szeretné használni, ahogy az az alábbi példában is látható, gondoskodnia kell arról, hogy az Azure-ban minden függőség telepítve legyen az függvényalkalmazás.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Meg kell adnia egy `package.json` fájlt a függvényalkalmazás gyökerében. A fájl definiálása lehetővé teszi, hogy az alkalmazás összes funkciója ugyanazt a gyorsítótárazott csomagot használja, amely a legjobb teljesítményt biztosítja. Ha ütközés lép fel, azt megoldhatja egy `package.json` adott függvény mappájában található fájl hozzáadásával.  

Ha a forrásoldali vezérlőből helyez üzembe alkalmazásokat, a tárházban található összes `package.json` fájl az `npm install` üzembe helyezés során a mappájában fog megjelenni. Ha azonban a portálon vagy a CLI-n keresztül végzi a telepítést, manuálisan kell telepítenie a csomagokat.

A függvényalkalmazás kétféleképpen telepíthet csomagokat: 

### <a name="deploying-with-dependencies"></a>Üzembe helyezés függőségekkel
1. Telepítse az összes szükséges csomagot helyileg a futtatásával `npm install` .

2. Telepítse a kódot, és ellenőrizze, hogy a `node_modules` mappa szerepel-e a központi telepítésben. 


### <a name="using-kudu"></a>A kudu használata
1. Nyissa meg a következőt: `https://<function_app_name>.scm.azurewebsites.net`.

2. Kattintson a **hibakeresési konzol**  >  **cmd** elemre.

3. Nyissa meg a ( `D:\home\site\wwwroot` z) parancsot, majd húzza a package.jsfájlt a **wwwroot** mappába a lap felső részén.  
    Más módokon is feltölthet fájlokat a Function alkalmazásba. További információ: [Function app Files frissítése](functions-reference.md#fileupdate). 

4. A package.jsfájl feltöltése után futtassa a `npm install` parancsot a **kudu távoli végrehajtási konzolon**.  
    Ez a művelet letölti a package.jsfájlban jelzett csomagokat, és újraindítja a Function alkalmazást.

## <a name="environment-variables"></a>Környezeti változók

Saját környezeti változókat adhat hozzá egy Function-alkalmazáshoz mind a helyi, mind a Felhőbeli környezetekben, mint például az operatív titkok (kapcsolati karakterláncok, kulcsok és végpontok) vagy a környezeti beállítások (például profilkészítési változók). Ezeket a beállításokat a `process.env` függvény kódjában érheti el.

### <a name="in-local-development-environment"></a>Helyi fejlesztési környezetben

Helyileg futtatva a functions projekt tartalmaz egy [ `local.settings.json` fájlt](./functions-run-local.md), ahol a környezeti változókat az `Values` objektumban tárolja. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>Az Azure felhőalapú környezetében

Az Azure-ban futtatott Function app lehetővé teszi, hogy az [alkalmazás beállításait](functions-app-settings.md), például a szolgáltatás kapcsolódási karakterláncait használja, és környezeti változókként tegye elérhetővé ezeket a beállításokat a végrehajtás során. 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>Környezeti változók elérése a kódban

Az alkalmazás beállításait környezeti változókként használva érheti el `process.env` , ahogy az itt látható a második és a harmadik hívásban, `context.log()` ahol naplózzák a `AzureWebJobsStorage` és a `WEBSITE_SITE_NAME` környezeti változókat:

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="ecmascript-modules-preview"></a><a name="ecmascript-modules"></a>ECMAScript modulok (előzetes verzió)

> [!NOTE]
> Mivel a ECMAScript-modulok jelenleg Node.js 14-ben *kísérleti* módon vannak megjelölve, Node.js 14 Azure functions előzetes verzióként érhetők el. Amíg Node.js 14 ECMAScript-modul támogatása *stabilra* vált, az API-nak vagy működésének lehetséges változásai várhatók.

A [ECMAScript-modulok](https://nodejs.org/docs/latest-v14.x/api/esm.html#esm_modules_ecmascript_modules) (es-modulok) a Node.js új hivatalos standard moduljának rendszere. Az ebben a cikkben szereplő kódrészletek a CommonJS szintaxist használják. Node.js 14 Azure Functions futtatásakor választhat, hogy az ES modulok szintaxisát használja-e a függvények írásához.

Ha az ES-modulokat egy függvényben szeretné használni, módosítsa a fájlnevét egy `.mjs` bővítmény használatára. A következő *index. mjs* fájl példa egy http-triggert használó függvény, amely az es modulok szintaxisát használja a könyvtár importálásához `uuid` és egy érték visszaadásához.

```js
import { v4 as uuidv4 } from 'uuid';

export default async function (context, req) {
    context.res.body = uuidv4();
};
```

## <a name="configure-function-entry-point"></a>Függvény belépési pontjának konfigurálása

A `function.json` Tulajdonságok `scriptFile` és az `entryPoint` exportált függvény helyének és nevének konfigurálására használhatók. Ezek a tulajdonságok akkor lehetnek fontosak, ha a JavaScript transpiled.

### <a name="using-scriptfile"></a>Az `scriptFile` használata

Alapértelmezés szerint a JavaScript-függvényt `index.js` egy olyan fájl hajtja végre, amely ugyanazokat a szülő könyvtárat osztja meg, mint a megfelelő `function.json` .

`scriptFile` a következő példához hasonló mappa-struktúra beszerzésére használható:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

A `function.json` for `myNodeFunction` műveletnek tartalmaznia kell egy olyan `scriptFile` tulajdonságot, amely a fájlra mutat, és az exportált függvény fut.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Az `entryPoint` használata

A `scriptFile` (vagy) alkalmazásban a `index.js` függvényt exportálni kell, `module.exports` hogy a rendszer megtalálja és futtassa a parancsot. Alapértelmezés szerint az aktiváláskor végrehajtandó függvény az egyetlen Exportálás az adott fájlból, a nevű exportálásból, `run` vagy a nevű exportálásból `index` .

Ez a alkalmazásban konfigurálható a `entryPoint` `function.json` következő példában látható módon:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

A functions v2. x-ben, amely támogatja a `this` paramétert a felhasználói függvényekben, a függvény kódja a következő példához hasonlóan lehet:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Ebben a példában fontos megjegyezni, hogy bár egy objektum exportálása folyamatban van, nincs garancia a végrehajtások állapotának megőrzésére.

## <a name="local-debugging"></a>Helyi hibakeresés

A `--inspect` paraméterrel megkezdett Node.js folyamat egy hibakeresési ügyfelet figyel a megadott porton. Azure Functions 2. x verzióban megadhat argumentumokat a kódot futtató Node.js folyamathoz a környezeti változó vagy az alkalmazás beállításainak hozzáadásával `languageWorkers:node:arguments = <args>` . 

A helyi hibakereséshez adja hozzá a `"languageWorkers:node:arguments": "--inspect=5858"` `Values` fájlt a [local.settings.js](./functions-run-local.md#local-settings-file) fájlhoz, és csatoljon egy hibakeresőt a 5858-es porthoz.

A VS Code használatakor a `--inspect` rendszer automatikusan hozzáadja a paramétert a `port` projekt launch.jsfájljában lévő érték használatával.

Az 1. x verzióban a beállítás `languageWorkers:node:arguments` nem fog működni. A hibakeresési portot a [`--nodeDebugPort`](./functions-run-local.md#start) Azure functions Core Tools paraméterrel lehet kiválasztani.

## <a name="typescript"></a>TypeScript

Ha a functions futtatókörnyezet 2. x verzióját célozza meg, a [Visual Studio Code](./create-first-function-cli-typescript.md) -hoz és a [Azure functions Core Tools](functions-run-local.md) -hoz egyaránt Azure functions is létrehozhat Function apps-alkalmazásokat olyan sablon használatával, amely támogatja az írógéppel Function app-projekteket. A sablon `package.json` olyan fájlokat hoz létre és `tsconfig.json` projektfájlok, amelyek megkönnyítik a JavaScript-függvények transpile, futtatását és közzétételét ezekkel az eszközökkel.

A létrehozott `.funcignore` fájl azt jelzi, hogy mely fájlok ki vannak zárva, amikor egy projekt közzé lett téve az Azure-ban.  

Az transpiled (. TS) a kimeneti könyvtár JavaScript-fájljaiba (. js) kerülnek `dist` . Az írógéppel a (z) [ `scriptFile` paraméterének](#using-scriptfile) használatával `function.json` jelezheti a megfelelő. js fájl helyét a `dist` mappában. A kimeneti helyet a sablon a `outDir` fájl paraméterének használatával állítja be `tsconfig.json` . Ha módosítja ezt a beállítást vagy a mappa nevét, a futtatókörnyezet nem találja a futtatandó kódot.

A helyi fejlesztés és üzembe helyezés módja a fejlesztői eszköztől függ.

### <a name="visual-studio-code"></a>Visual Studio Code

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) bővítményhez készült Azure functions lehetővé teszi a függvények fejlesztését a géppel. Az alapvető eszközök a Azure Functions bővítmény követelménye.

Ha a Visual Studio Code-ban szeretné létrehozni az írógéppel Function alkalmazást, válassza `TypeScript` a nyelvét a Function-alkalmazás létrehozásakor.

Amikor lenyomja az **F5** billentyűt az alkalmazás helyi futtatásához, a transpilation a gazdagép (func.exe) inicializálása előtt történik. 

Ha a Function alkalmazást az Azure-ban az **üzembe helyezés az alkalmazásban...** gomb használatával helyezi üzembe, a Azure functions-bővítmény először a JavaScript-fájlok éles környezetben való létrehozására kész buildjét hozza létre a géppel készített forrásfájlok közül.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A központi eszközök használata során többféleképpen is eltérhetnek a JavaScript-projekttől.

#### <a name="create-project"></a>Projekt létrehozása

Ha a Core Tools használatával szeretne létrehozni egy írógéppel Function app-projektet, a Function alkalmazás létrehozásakor meg kell adnia az írógéppel nyelvi beállítást. Ezt a következő módszerek egyikével teheti meg:

- Futtassa a `func init` parancsot, válassza a `node` nyelvi verem lehetőséget, majd válassza a lehetőséget `typescript` .

- Futtassa a következő parancsot: `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Helyi Futtatás

Ha a Function app-kódot helyileg szeretné futtatni a Core Tools használatával, használja a következő parancsokat a helyett `func host start` : 

```command
npm install
npm start
```

A `npm start` parancs egyenértékű a következő parancsokkal:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Közzététel az Azure-ban

Mielőtt az parancsot az [`func azure functionapp publish`] Azure-ba történő üzembe helyezéshez használja, a JavaScript-fájlok éles környezetben történő létrehozását a géppel készített forrásfájlokből hozza létre. 

A következő parancsok a géppel készített projekt előkészítését és közzétételét teszik közzé a Core Tools használatával: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Ebben a parancsban cserélje le a helyére a `<APP_NAME>` Function alkalmazás nevét.

## <a name="considerations-for-javascript-functions"></a>A JavaScript-függvények szempontjai

Ha JavaScript-függvényekkel dolgozik, vegye figyelembe a következő részekben ismertetett szempontokat.

### <a name="choose-single-vcpu-app-service-plans"></a>VCPU App Service csomagok kiválasztása

Ha a App Service csomagot használó Function-alkalmazást hoz létre, azt javasoljuk, hogy egyetlen vCPU csomagot válasszon, nem pedig több vCPU rendelkező csomagot. Napjainkban a függvények a JavaScript-funkciókat hatékonyabban futtatják az vCPU virtuális gépeken, a nagyobb méretű virtuális gépek használata azonban nem eredményezi a várt teljesítményt. Ha szükséges, manuálisan is kibővítheti a további vCPU virtuálisgép-példányok hozzáadásával, vagy engedélyezheti az autoskálázást is. További információ: a [Példányszám manuális vagy automatikus skálázása](../azure-monitor/autoscale/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Hidegindító

Azure Functions a kiszolgáló nélküli üzemeltetési modellben való fejlesztésekor a hideg indítás a valóság. A *hidegindító* kifejezés arra utal, hogy amikor a Function alkalmazás első alkalommal indul el egy adott időszak után, a folyamat hosszabb időt vesz igénybe. Ha a JavaScript-függvények nagy függőségi fákkal rendelkeznek, akkor a hideg indítás jelentős lehet. A hidegindító folyamat felgyorsításához [futtassa a függvényeket csomagfájlként](run-functions-from-deployment-package.md) , ha lehetséges. Számos üzembe helyezési módszer alapértelmezés szerint a Futtatás a csomag modelljével történik, de ha nagy mennyiségű hideg indul el, és nem fut így, ez a változás jelentős javulást nyújthat.

### <a name="connection-limits"></a>Kapcsolatok korlátai

Ha egy Azure Functions-alkalmazásban szolgáltatás-specifikus ügyfelet használ, ne hozzon létre új ügyfelet minden függvény meghívásával. Ehelyett hozzon létre egyetlen, statikus ügyfelet a globális hatókörben. További információ: [kapcsolatok kezelése Azure Functionsban](manage-connections.md).

### <a name="use-async-and-await"></a>Használat `async` és `await`

A JavaScript-Azure Functions írásakor a és a kulcsszavak használatával kell írnia a kódot `async` `await` . A kód és a visszahívások helyett a (z) és az `async` `await` `.then` `.catch` ígéretekkel való írás során két gyakori probléma elkerülhető:
 - Olyan nem kezelt kivételeket dobott le, amelyek [összeomlanak a Node.js folyamaton](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), ami hatással van más függvények végrehajtására.
 - Váratlan viselkedés, például hiányzó naplók a Context. log naplófájlból, a nem megfelelően várt aszinkron hívások miatt.

Az alábbi példában az aszinkron metódust `fs.readFile` egy hiba-első visszahívási függvény hívja meg második paramétereként. Ez a kód a fent említett problémákat is okozhatja. A megfelelő hatókörben nem kifejezetten bekövetkező kivétel összeomlott a teljes folyamaton (a probléma #1). A `context.done()` visszahívás funkció hatókörén kívüli hívás azt jelenti, hogy a függvény meghívása a fájl olvasása előtt megszűnik (probléma #2). Ebben a példában a- `context.done()` től kezdődően a hiányzó naplóbejegyzések közül a túl korán való hívása `Data from file:` .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

A `async` és a `await` kulcsszavak használata segít elkerülni mindkét hibát. A [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) hiba-első visszahívás-stílusú függvények várakozási függvényekben való bekapcsolásához használja a Node.js Utility függvényt.

Az alábbi példában a függvény végrehajtása során felmerülő kezeletlen kivételek csak a kivételt okozó egyéni hívás meghiúsulnak. A `await` kulcsszó azt jelenti, hogy a `readFileAsync` csak a befejezést követő végrehajtás után következik `readFile` be. `async`A és a `await` esetében nem kell meghívnia a `context.done()` visszahívást is.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

+ [Azure Functions – ajánlott eljárások](functions-best-practices.md)
+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
+ [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

["az Azure functionapp Publishing"]: functions-run-local.md#project-file-deployment
