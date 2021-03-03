---
title: Tartós entitások – Azure Functions
description: Megtudhatja, milyen tartós entitásokat használ, és hogyan használhatja őket a Azure Functions Durable Functions-bővítményében.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 8b1c4077c036cbb75738115437d29ffd14b160ff
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723673"
---
# <a name="entity-functions"></a>Entitás-függvények

Az Entity functions olyan műveleteket határoz meg, amelyek olyan kis méretű állapotok olvasására és frissítésére szolgálnak, amelyek *tartós entitások*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitás-triggerrel* működik. Az Orchestrator függvényektől eltérően az Entity functions az entitások állapotát explicit módon kezeli, ahelyett, hogy az állapotot a vezérlési folyamaton keresztül implicit módon jelképezi.
Az entitások lehetővé teszik az alkalmazások méretezését azáltal, hogy számos entitáson keresztül terjesztik a munkát, amelyek mindegyike szerény méretű állapotú.

> [!NOTE]
> Az Entity functions és a kapcsolódó funkciók csak Durable Functions 2,0-es és újabb verziókban érhetők el. A .NET és a JavaScript jelenleg támogatott.

## <a name="general-concepts"></a>Általános fogalmak

Az entitások olyan kis-és nagyvállalati szolgáltatásokat is tanúsítanak, amelyek üzenetek használatával kommunikálnak. Minden entitás egyedi identitással és belső állapottal rendelkezik (ha létezik). A szolgáltatásokhoz vagy objektumokhoz hasonlóan az entitások is végrehajtják a műveleteket, amikor a rendszer erre kéri. Egy művelet végrehajtásakor előfordulhat, hogy az entitás belső állapotát frissíti. Külső szolgáltatásokat is meghívhat, és megvárhatja a választ. Az entitások a megbízható várólistákon keresztül implicit módon elküldött üzenetek használatával kommunikálnak más entitásokkal, összeszerelésekkel és ügyfelekkel. 

Az ütközések elkerülése érdekében az egyetlen entitáson végrehajtott összes művelet végrehajtása a szerializált, azaz a másik után történik.

> [!NOTE]
> Az entitások meghívásakor a rendszer feldolgozza a hasznos adatokat a befejezéshez, majd egy új végrehajtást ütemezhet a jövőbeli bemenetek megérkezése után. Ennek eredményeképpen az entitás-végrehajtási naplók további végrehajtást is megjeleníthetnek az egyes entitások meghívása után. Ez a várt érték.

### <a name="entity-id"></a>Entitás azonosítója
Az entitások egyedi azonosítóval, az *entitás azonosítójának* használatával érhetők el. Az entitás-AZONOSÍTÓk egyszerűen olyan karakterláncok, amelyek egyedileg azonosítanak egy entitás-példányt. A következőkből áll:

* Az **entitás neve**, amely az entitás típusát azonosító név. Ilyen például a "Counter". A névnek meg kell egyeznie az entitást megvalósító entitás-függvény nevével. Nem érzékeny az esetre.
* Az **entitás kulcsa**, amely egy olyan karakterlánc, amely egyedileg azonosítja az entitást az azonos nevű entitások között. Ilyen például egy GUID.

Előfordulhat például, hogy egy `Counter` Entity függvényt használ a pontszám online játékokban való megőrzésére. A játék minden példánya egyedi AZONOSÍTÓJÚ entitást tartalmaz, például: `@Counter@Game1` és `@Counter@Game2` . Egy adott entitást megcélzó összes művelethez meg kell adni egy entitás AZONOSÍTÓját paraméterként.

### <a name="entity-operations"></a>Entitás-műveletek ###

Egy művelet egy entitáson való meghívásához a következőt kell megadnia:

* A célként megadott entitás **azonosítója** .
* A **művelet neve**, amely egy karakterlánc, amely meghatározza a végrehajtandó műveletet. Az `Counter` entitás például támogathatja a `add` vagy a `get` `reset` műveleteket.
* A **művelet bemenete**, amely egy opcionális bemeneti paraméter a művelethez. A hozzáadási művelet például egész számot vehet igénybe bemenetként.
* Az **ütemezett időpont**, amely egy opcionális paraméter a művelet kézbesítési idejének megadásához. Egy művelet például megbízhatóan ütemezhető úgy, hogy több napot is futtasson a jövőben.

A műveletek visszaadhatják az eredmény értékét vagy a hiba eredményét, például JavaScript-hibát vagy .NET-kivételt. Ezt az eredményt vagy hibát megfigyelheti a műveletet meghívó Összehangolók.

Az entitások művelete az entitás állapotának létrehozását, olvasását, frissítését és törlését is elvégezheti. Az entitás állapota mindig tartósan marad a tárolóban.

## <a name="define-entities"></a>Entitások definiálása

Az entitások definiálásának két különböző API-jának jelenleg a következő:

**Function-alapú szintaxis**, amelyben az entitások függvényekként és műveletként vannak ábrázolva, az alkalmazás explicit módon elküldi őket. Ez a szintaxis jól működik az egyszerű állapottal rendelkező entitások, néhány művelet vagy az alkalmazás-keretrendszerek, például az alkalmazások dinamikus készlete esetében. Ez a szintaxis unalmas lehet a karbantartáshoz, mert a fordítási idő során nem kerül be a gépelési hibák.

**Osztály-alapú szintaxis (csak .net)**, ahol az entitásokat és a műveleteket osztályok és metódusok jelölik. Ez a szintaxis könnyebben olvasható kódot hoz létre, és lehetővé teszi a műveletek típusos biztonságos módon történő meghívását. Az osztály-alapú szintaxis egy vékony réteg a függvény-alapú szintaxisban, így mindkét változat felhasználható ugyanabban az alkalmazásban.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Példa: függvény-alapú szintaxis – C #

A következő kód egy egyszerű entitás, amely `Counter` tartós függvényként van megvalósítva. Ez a függvény három műveletet határoz meg:, `add` `reset` , és `get` , amelyek mindegyike egész számú állapotban működik.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

A függvény-alapú szintaxissal és annak használatával kapcsolatos további információkért lásd a [Function-based szintaxist](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Példa: osztály alapú szintaxis – C #

A következő példa az entitás egyenértékű implementációját `Counter` osztályok és metódusok használatával.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Az entitás állapota egy típusú objektum `Counter` , amely egy olyan mezőt tartalmaz, amely a számláló aktuális értékét tárolja. Ha meg szeretné őrizni ezt az objektumot a tárolóban, a rendszer szerializálja és deszerializálja a [JSON.net](https://www.newtonsoft.com/json) -könyvtár. 

Az osztály-alapú szintaxissal és annak használatával kapcsolatos további információkért lásd: entitás- [osztályok meghatározása](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Példa: JavaScript-entitás

A NPM-csomag **1.3.0** kezdődően tartós entitások érhetők el a JavaScriptben `durable-functions` . A következő kód az `Counter` entitás, amely a JavaScriptben írt tartós függvényként van megvalósítva.

**Számláló/function.jsbekapcsolva**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Számláló/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Példa: Python-entitás

A következő kód a `Counter` Pythonban írt tartós függvényként megvalósított entitás.

**Számláló/function.jsbekapcsolva**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Számláló/__init__. a**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)



main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Hozzáférési entitások

Az entitások egyirányú vagy kétirányú kommunikációval érhetők el. A következő terminológia megkülönbözteti a kommunikáció két formáját: 

* Az entitások **hívása** kétirányú (egyirányú) kommunikációt használ. A művelet üzenetet küld az entitásnak, majd a folytatás előtt várja meg a válaszüzenetet. A válaszüzenet egy eredmény értékét vagy egy hiba eredményét, például JavaScript-hibát vagy .NET-kivételt is biztosíthat. Ezt az eredményt vagy hibát a hívó észleli.
* Egy entitás **jelzése** egyirányú (tűz-és elfelejti) kommunikációt használ. Elküld egy műveleti üzenetet, de nem vár a válaszra. Amíg az üzenet garantáltan kézbesíthető, a küldő nem tudja, hogy mikor és nem észleli az eredményeket és a hibákat.

Az entitások a Orchestrator függvényeken belül vagy az Entity functions szolgáltatásból érhetők el az ügyfél-függvényekből. Az összes kontextus nem támogatja a kommunikáció összes formáját:

* Az ügyfeleken belül megadhatja az entitásokat, és elolvashatja az entitás állapotát.
* A folyamatokon belül megadhatja az entitásokat, és meghívhatja az entitásokat.
* Az entitásokon belül megadhatja az entitásokat.

Az alábbi példák az entitások elérésének különféle módjait szemléltetik.

### <a name="example-client-signals-an-entity"></a>Példa: az ügyfél jelzi az entitást

Ha az entitásokat egy általános Azure-függvényből kívánja elérni, amely más néven ügyfél-függvény, használja az [entitás-ügyfél kötését](durable-functions-bindings.md#entity-client). Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely a kötést használó entitást jelez.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Az egyszerűség kedvéért az alábbi példák a lazán beírt szintaxist mutatják be az entitásokhoz való hozzáféréshez. Általánosságban azt javasoljuk, hogy az [entitásokat a felületeken keresztül érheti](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) el, mivel több típusú ellenőrzést is biztosít.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

A *jel* kifejezés azt jelenti, hogy az entitás API-hívása egyirányú és aszinkron. Az ügyfél nem tudja tudni, hogy mikor dolgozza fel a műveletet az entitás. Emellett az ügyfél függvény nem tudja megfigyelni az eredmények értékét vagy kivételeit. 

### <a name="example-client-reads-an-entity-state"></a>Példa: az ügyfél beolvas egy entitás állapotát

Az ügyfél functions egy entitás állapotát is lekérdezheti az alábbi példában látható módon:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> A Python jelenleg nem támogatja az entitások állapotának olvasását az ügyfélről. Használjon `callEntity` helyette egy Orchestrator.

---

Az entitások állapotának lekérdezéseit a rendszer a tartós nyomkövetési tárolóba küldi, és az entitás legutóbb megőrzött állapotát adja vissza. Ez az állapot mindig "véglegesített" állapot, azaz soha nem ideiglenes közbenső állapot, amelyet a művelet végrehajtásának közepén feltételeznek. Azonban lehetséges, hogy ez az állapot elavult az entitás memórián belüli állapotához képest. A következő szakaszban leírtak szerint csak a bevezetések tudják olvasni az entitás memóriában lévő állapotát.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Példa: előkészítési jelek és entitások meghívása

A Orchestrator függvények API-k használatával férhetnek hozzá az entitásokhoz a előkészítési [trigger kötésében](durable-functions-bindings.md#orchestration-trigger). A következő példa egy Orchestrator-függvényt mutat be, amely egy entitást hív meg és jelez `Counter` .

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> A JavaScript jelenleg nem támogatja az entitások Orchestrator való jelzését. A `callEntity` használható helyette.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Csak a bevezetések képesek az entitások meghívására és a válasz lekérésére, ami lehet visszatérési érték vagy kivétel. Az [ügyfél-kötést](durable-functions-bindings.md#entity-client) használó ügyfél-függvények csak az entitásokat jelezhetik.

> [!NOTE]
> Egy entitás Orchestrator-függvényből való meghívása hasonló a Orchestrator függvény egy [tevékenységi függvényének](durable-functions-types-features-overview.md#activity-functions) meghívásához. A fő különbség az, hogy az Entity functions olyan tartós objektumok, amelyeknek van egy címe, amely az entitás azonosítója. Az Entity functions támogatja a művelet nevének megadását. A Activity functions azonban állapot nélküli, és nem rendelkezik a műveletek fogalmával.

### <a name="example-entity-signals-an-entity"></a>Példa: az entitás egy entitást jelzi

Az Entity függvény jeleket küldhet más entitásoknak, vagy akár saját maga is, miközben végrehajt egy műveletet.
Például módosíthatjuk az előző `Counter` entitást például úgy, hogy egy "mérföldkő által elért" jelet küld egy figyelő entitásnak, amikor a számláló eléri a 100 értéket.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> A Python jelenleg nem támogatja az entitások közötti jeleket. Ehelyett használjon Orchestrator a jelző entitásokhoz.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Entitások koordinálása (jelenleg csak .NET)

Előfordulhatnak olyan időpontok, amikor több entitáson keresztül kell összehangolni a műveleteket. Egy banki alkalmazásban például lehetnek olyan entitások, amelyek egyéni bankszámlákat jelképeznek. Ha az egyik fiókból a másikba helyezi át a forrásokat, gondoskodnia kell arról, hogy a forrásoldali fiók elegendő összegű legyen. Emellett biztosítania kell, hogy a forrás-és a célhelyek frissítései tranzakciós szempontból konzisztens módon történjenek.

### <a name="example-transfer-funds-c"></a>Példa: átutalási alapok (C#)

A következő példában a kód egy Orchestrator függvény használatával továbbítja az alapokat a két fiók entitásai között. Az entitások frissítéseinek koordinálásához a `LockAsync` metódus használatával kell létrehozni egy _kritikus szakaszt_ a koordinációban.

> [!NOTE]
> Az egyszerűség kedvéért ez a példa a `Counter` korábban definiált entitást használja. Egy valós alkalmazásban jobb lenne egy részletesebb `BankAccount` entitás meghatározása.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

A .NET-ben a `LockAsync` `IDisposable` visszaadott érték a kritikus szakaszt adja vissza, ha a művelet el van távolítva. Ez `IDisposable` az eredmény együtt használható egy `using` blokkal a kritikus szakasz szintaktikai ábrázolásának beolvasásához.

Az előző példában egy Orchestrator-függvény a forrás entitásból egy célként megadott entitásba ruházta át a forrásokat. A `LockAsync` metódus a forrás és a cél fiók entitásait is zárolta. Ez a zárolás gondoskodik arról, hogy egyetlen másik ügyfél sem tudja lekérdezni vagy módosítani a fiók állapotát, amíg az utasítás végén kilépett a kritikus szakaszra `using` . Ez a viselkedés megakadályozza a forrás fiókból való túllépés lehetőségét.

> [!NOTE] 
> Ha egy összehangolás általában vagy hibával leáll, a folyamatban lévő kritikus fejezetek implicit módon véget ér, és minden zárolás megjelent.

### <a name="critical-section-behavior"></a>Kritikus szakasz viselkedése

A `LockAsync` metódus kritikus szakaszt hoz létre egy előkészítési folyamatban. Ezekkel a kritikus részekkel megakadályozható, hogy más összehangolt módosításokat hozzon létre egy adott entitáson. Belsőleg az `LockAsync` API "zárolási" műveleteket küld az entitásoknak, és visszatér, ha "zárolási beszerzett" válaszüzenetet kap ezekről az entitásokról. A zárolás és a feloldás is az összes entitás által támogatott beépített művelet.

A más ügyfelektől érkező műveletek nem engedélyezettek egy entitáson, amíg a zárolt állapotban van. Ez a viselkedés garantálja, hogy egyszerre csak egy összehangoló példány tud zárolni egy entitást. Ha a hívó egy művelettel próbál meg meghívást alkalmazni egy entitáson, miközben azt egy előkészítés zárolta, a művelet egy függőben lévő műveleti várólistába kerül. A függőben lévő műveletek addig nem lesznek feldolgozva, amíg a Holding-előkészítés fel nem oldja a zárolást.

> [!NOTE] 
> Ez a viselkedés némileg eltér a legtöbb programozási nyelvben használt szinkronizálási primitívekkel, például a `lock` C# utasításban. A C# nyelvben például az `lock` utasítást minden szálnak használnia kell, hogy biztosítsa a megfelelő szinkronizálást több szálon. Az entitásoknak azonban nincs szükségük arra, hogy az entitások explicit módon zárolják az összes hívót. Ha bármelyik hívó zárol egy entitást, az adott entitáson végzett összes egyéb művelet blokkolva lesz, és a zárolás mögött van.

Az entitásokra vonatkozó zárolások tartósak, így azok még akkor is megmaradnak, ha a végrehajtó folyamat újra lett hasznosítva. A zárolások belsőleg megmaradnak az entitás tartós állapotának részeként.

A tranzakcióktól eltérően a kritikus részekben nem történik meg automatikusan a változások visszaállítása a hibák esetén. Ehelyett a hibákat, például a visszaállítást vagy az újrapróbálkozást explicit módon kell kódolni, például hibák vagy kivételek kifogásával. Ez a tervezési lehetőség szándékos. Az előkészítési folyamat összes hatásának automatikus visszaállítása általában nehéz vagy lehetetlen, mivel a munkafolyamatok tevékenységeket futtathatnak, és hívásokat végezhetnek olyan külső szolgáltatásokra, amelyek nem állíthatók vissza. A visszaállítási kísérletek is sikertelenek lehetnek, és további hibakezelés szükségesek.

### <a name="critical-section-rules"></a>Kritikus szakasz szabályai

Az alacsony szintű zárolási primitívek a legtöbb programozási nyelven eltérően a kritikus fontosságú szakaszt *nem a holtpontra kell biztosítani*. A holtpontok megelőzése érdekében a következő korlátozásokat kell kikényszeríteni: 

* A kritikus szakaszt nem lehet beágyazni.
* A kritikus szakaszban nem hozhatók létre alfolyamatok.
* A kritikus szakaszt csak a zárolt entitások hívhatják meg.
* A kritikus szakaszban nem hívható meg ugyanaz az entitás több párhuzamos hívás használatával.
* A kritikus szakaszban csak azok az entitások jelezhetnek, amelyek nincsenek zárolva.

Ezeknek a szabályoknak a megsértése futásidejű hibát okoz, például `LockingRulesViolationException` a .net-ben, amely egy üzenetet tartalmaz, amely elmagyarázza, hogy milyen szabály lett megszakítva.

## <a name="comparison-with-virtual-actors"></a>Összehasonlítás virtuális szereplőkkel

A tartós entitások számos funkcióját a [színészi modell](https://en.wikipedia.org/wiki/Actor_model)ihlette. Ha már ismeri a szereplőket, felismerheti a cikkben ismertetett fogalmakat. A tartós entitások különösen hasonlók a [virtuális szereplőkkel](https://research.microsoft.com/projects/orleans/)vagy a gabonához, ahogyan az [Orleans-projekt](http://dotnet.github.io/orleans/)népszerűsítette. Például:

* A tartós entitások az entitás AZONOSÍTÓján keresztül címezhető.
* A tartós entitások műveletei a verseny feltételeinek megelőzése érdekében egyszerre hajtanak végre sorosan.
* A tartós entitások implicit módon jönnek létre, amikor a rendszer meghívja vagy jelzi őket.
* Ha nem hajtja végre a műveleteket, a tartós entitások csendesen törlődnek a memóriából.

Fontos különbségek vannak, amelyeket érdemes megjegyezni:

* A tartós entitások a késleltetést rangsorolják, ezért előfordulhat, hogy a szigorú késési követelményekkel rendelkező alkalmazások esetében nem megfelelő.
* A tartós entitások nem rendelkeznek beépített időtúllépéssel az üzenetekhez. Orleans-ban minden üzenet időtúllépést eredményezett a konfigurálható idő után. Az alapértelmezett érték 30 mp.
* Az entitások között küldött üzenetek megbízhatóan és sorrendben lesznek kézbesítve. Orleans-ban megbízható vagy rendezett kézbesítés támogatott a streameken keresztül küldött tartalmak esetében, de a gabonák közötti összes üzenet esetében nem garantált.
* Az entitásokban a kérelem-válasz mintázatok csak a bevezetésekre korlátozódnak. Az entitásokon belül csak egyirányú üzenetküldés (más néven jelzés) engedélyezett, ahogy az eredeti modellben is, és ellentétben a magokkal a Orleansban. 
* Tartós entitások nem holtpontos. Orleans-ban holtpontok léphetnek fel, és nem oldhatók fel az üzenetek időtúllépése.
* A tartós entitások tartós felépítéssel és az elosztott zárolási mechanizmusok támogatásával használhatók. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Olvassa el a fejlesztői útmutató a tartós entitásokhoz a .NET-ben](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Tudnivalók a feladatok hubokról](durable-functions-task-hubs.md)
