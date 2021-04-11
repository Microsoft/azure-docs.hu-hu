---
title: Kötések a Durable Functionshoz – Azure
description: Eseményindítók és kötések használata a Azure Functions Durable Functions bővítményéhez.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 26c9da85b27ba46e9c48bdc7872a65d452a7acb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607090"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions kötései (Azure Functions)

A [Durable functions](durable-functions-overview.md) bővítmény két új trigger kötést vezet be, amelyek a Orchestrator és a Activity függvények végrehajtását vezérlik. Emellett egy kimeneti kötést is bevezet, amely ügyfélként funkcionál az Durable Functions futtatókörnyezet számára.

## <a name="orchestration-trigger"></a>Előkészítési trigger

Az előkészítési trigger lehetővé teszi [tartós Orchestrator függvények](durable-functions-types-features-overview.md#orchestrator-functions)készítését. Ez az aktiválás támogatja az új Orchestrator-függvények indítását és a meglévő Orchestrator-függvények lefolytatását, amelyek "várnak" egy feladatot.

Ha a Visual Studio-eszközöket használja a Azure Functionshoz, a [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .net-attribútummal konfigurálhatja a előkészítési triggert.

Ha Orchestrator függvényeket ír a parancsfájlkezelési nyelveken (például JavaScript vagy C# parancsfájlkezelés), a (z) a következő JSON-objektum határozza meg a `bindings` *function.js* fájljában található tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` annak a FELÁLLÍTÁSNAK a neve, amelyet az ügyfeleknek használniuk kell, amikor szeretnék elindítani a Orchestrator függvény új példányait. Ez a tulajdonság nem kötelező. Ha nincs megadva, a rendszer a függvény nevét használja.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólisták sorát kérdezi le. Ezek a várólisták a bővítmény belső implementációjának részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány Megjegyzés az előkészítési triggerről:

* **Egyszálas** – egyetlen kiosztó szál használatos egyetlen gazdagép-példányon az összes Orchestrator függvény végrehajtásához. Ezért fontos, hogy a Orchestrator funkció kódja hatékony legyen, és nem hajt végre semmilyen I/O-műveletet. Fontos továbbá, hogy a szál ne végezzen aszinkron munkát, kivéve, ha Durable Functions-specifikus feladattípusra vár.
* **Méreg – üzenetkezelés** – a rendszer nem támogatja a hangfeldolgozási eseményindítók használatát.
* **Üzenet láthatósága** – a hangelőkészítési trigger üzenetei el vannak különítve, és a konfigurálható időtartamra láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – a visszatérési értékek a JSON-ba vannak szerializálva, és az Azure Table Storage-ben megőrzött az előkészítési előzmények táblázata. Ezeket a visszaadott értékeket az összehangoló ügyfél kötése kérdezheti le, amely később van leírva.

> [!WARNING]
> A Orchestrator függvények soha nem használhatnak semmilyen bemeneti vagy kimeneti kötést, amely nem az előkészítési trigger kötése. Ennek köszönhetően problémák léphetnek fel a tartós feladattal, mivel ezek a kötések nem engedelmeskednek az egyszálas és az I/O-szabályoknak. Ha más kötéseket szeretne használni, vegye fel őket a Orchestrator függvény által hívott tevékenység-függvénybe.

> [!WARNING]
> A JavaScript Orchestrator funkcióit soha nem lehet deklarálni `async` .

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

A előkészítési trigger kötése a bemeneteket és kimeneteket is támogatja. Íme néhány tudnivaló a bemeneti és kimeneti használatról:

* **bemenetek** – a .net-hangolási függvények csak `DurableOrchestrationContext` paraméter típusúként támogatottak. A bemenetek közvetlenül a függvény aláírásában való deszerializálása nem támogatott. A kódnak a `GetInput<T>` (.net) vagy `getInput` (JavaScript) metódust kell használnia a Orchestrator függvény bemenetének beolvasásához. Ezeknek a bemeneteknek JSON-szerializálható típusúnak kell lenniük.
* **kimenetek** – a hangelőkészítési eseményindítók támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és csak JSON-szerializálható lehet. Ha egy .NET-függvény visszaadja a `Task` `void` értéket, a rendszer `null` a kimenetként menti az értékeket.

### <a name="trigger-sample"></a>Példa triggerre

A következő mintakód azt szemlélteti, hogy a legegyszerűbb "Hello World" Orchestrator függvény a következőképpen néz ki:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> A `context` JavaScriptben lévő objektum nem a DurableOrchestrationContext, hanem az [egész függvény kontextusát](../functions-reference-node.md#context-object)jelöli. A koordináló metódusok az `context` objektum tulajdonságán keresztül érhetők el `df` .

> [!NOTE]
> A JavaScript-rendszerszervezőket érdemes használni `return` . A `durable-functions` kódtár gondoskodik a metódus meghívásáról `context.done` .

A legtöbb Orchestrator függvény hívja a Activity functions funkciót, ezért itt látható egy "Hello World" példa, amely bemutatja, hogyan hívhat meg egy tevékenységi függvényt:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Tevékenység-trigger

A tevékenység-trigger lehetővé teszi olyan függvények készítését, amelyeket a Orchestrator függvények, más néven a [tevékenység-függvények](durable-functions-types-features-overview.md#activity-functions)hívnak.

A Visual Studio használata esetén a tevékenység-trigger a .NET attribútum használatával van konfigurálva `ActivityTriggerAttribute` .

Ha a VS Code-ot vagy a Azure Portal fejlesztési célokra használja, a tevékenység-triggert a következő JSON-objektum határozza meg a `bindings` *function.js* tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` a tevékenység neve. Ez az érték annak a névnek a neve, amelyet a Orchestrator függvények a tevékenység függvényének meghívására használnak. Ez a tulajdonság nem kötelező. Ha nincs megadva, a rendszer a függvény nevét használja.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólistát kérdezi le. Ez a várólista a bővítmény belső implementációjának részletes adatai, ezért nincs explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány Megjegyzés a tevékenység-triggerről:

* **Szál** – a előkészítési eseményindítótól eltérően a tevékenység-eseményindítók nem rendelkeznek a Threading vagy az I/O műveletekkel kapcsolatos korlátozásokkal. Ugyanúgy kezelhetők, mint a hagyományos függvények.
* **Méreg – üzenetkezelés** – a tevékenység-eseményindítók nem rendelkeznek a Megmérgező üzenet támogatásával.
* **Üzenet láthatósága** – a tevékenység-trigger üzenetei el vannak különítve, és a konfigurálható időtartamig láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – a visszatérési értékek a JSON-ba vannak szerializálva, és az Azure Table Storage-ben megőrzött az előkészítési előzmények táblázata.

> [!WARNING]
> A Activity functions tárolási háttere a megvalósítás részletei, és a felhasználói kód nem használható közvetlenül ezekkel a tároló entitásokkal.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

A tevékenység-trigger kötés támogatja a bemeneteket és a kimeneteket is, ugyanúgy, mint a előkészítési trigger. Íme néhány tudnivaló a bemeneti és kimeneti használatról:

* **bemenetek** – a .net-tevékenységek natív `DurableActivityContext` módon használhatók paraméter típusúként. Azt is megteheti, hogy egy tevékenység-függvény deklarálható bármely olyan típusparaméter-típussal, amely JSON-szerializálható. A használatakor `DurableActivityContext` meghívhatja, `GetInput<T>` hogy beolvassa és deszerializálja a tevékenységi függvény bemenetét.
* **kimenetek** – a tevékenységi függvények támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és csak JSON-szerializálható lehet. Ha egy .NET-függvény visszaadja a `Task` `void` értéket, a rendszer `null` a kimenetként menti az értékeket.
* **metaadatok** – a .net-tevékenység functions kötést tud kötni egy `string instanceId` paraméterrel, hogy lekérje a szülő-előkészítés példányának azonosítóját.

### <a name="trigger-sample"></a>Példa triggerre

Az alábbi mintakód azt mutatja be, hogy egy egyszerű "Hello World" tevékenységi funkció hogyan nézhet ki:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableActivityContext` `IDurableActivityContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

A .NET-kötés alapértelmezett paramétere a következő: `ActivityTriggerAttribute` `IDurableActivityContext` . A .NET-tevékenység-eseményindítók azonban közvetlenül a JSON-serializeable-típusokhoz is támogatják a kötéseket (beleértve az egyszerű típusokat is), így ugyanezt a funkciót a következőképpen egyszerűsítheti:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

A JavaScript-kötések további paraméterekként is átadhatók, így az alábbi módon lehet egyszerűsíteni a műveletet:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Bemeneti és kimeneti kötések használata

A tevékenység-trigger kötés mellett normál bemeneti és kimeneti kötéseket is használhat. Például megteheti a bemenetet a tevékenység kötéséhez, és üzenetet küldhet egy EventHub a EventHub kimeneti kötés használatával:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Előkészítési ügyfél

A hangelőkészítési ügyfél kötése lehetővé teszi a Orchestrator functions szolgáltatással kommunikáló függvények írását. Ezeket a függvényeket időnként ügyfél- [függvénynek](durable-functions-types-features-overview.md#client-functions)is nevezzük. Például a következő módokon végezheti el az előkészítési példányok megszervezését:

* Indítsa el őket.
* Az állapotuk lekérdezése.
* Megszakítja őket.
* Események küldése a futtatásuk közben.
* Példányok előzményeinek kiürítése.

Ha a Visual studiót használja, akkor a `OrchestrationClientAttribute` Durable Functions 1,0 .net-attribútumának használatával köthető az előkészítési ügyfélhez. A Durable Functions a 2,0-es verziótól kezdődően a .NET-attribútum használatával köthető az előkészítési ügyfélhez `DurableClientAttribute` .

Ha programozási nyelveket (például *. CSX* vagy *. js* fájlt) használ a fejlesztéshez, az összehangoló triggert a következő JSON-objektum határozza meg a `bindings` *function.js* tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – Olyan helyzetekben használatos, amikor több Function-alkalmazás osztozik ugyanazzal a Storage-fiókkal, de el kell különíteni egymástól. Ha nincs megadva, a rendszer az alapértelmezett értéket `host.json` használja. Ennek az értéknek meg kell egyeznie a cél Orchestrator függvények által használt értékkel.
* `connectionName` – Egy, a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazó Alkalmazásbeállítás neve. A megadott Orchestrator függvények által használt Storage-fióknak meg kell egyeznie. Ha nincs megadva, a rendszer az alapértelmezett Storage-fiókhoz tartozó kapcsolatok karakterláncot használja a Function alkalmazáshoz.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki ezeket a tulajdonságokat, és használja az alapértelmezett viselkedést.

### <a name="client-usage"></a>Ügyfél használata

A .NET functions szolgáltatásban általában a következőhöz kötődik `IDurableOrchestrationClient` , ami teljes hozzáférést biztosít a Durable functions által támogatott összes összehangoló ügyféloldali API-hoz. A régebbi Durable Functions 2. x kiadásokban Ehelyett a osztályhoz kell kötni `DurableOrchestrationClient` . A JavaScriptben ugyanazokat az API-kat teszi elérhetővé a rendszer által visszaadott objektum `getClient` . Az ügyfél-objektum API-jai a következők:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Másik megoldásként a .NET-függvények a következő `IAsyncCollector<T>` helyhez köthetők: `T` `StartOrchestrationArgs` vagy `JObject` .

A műveletekkel kapcsolatos további információkért tekintse meg az `IDurableOrchestrationClient` API dokumentációját.

### <a name="client-sample-visual-studio-development"></a>Ügyfél minta (Visual Studio Development)

Itt látható egy üzenetsor által aktivált függvény, amely egy "HelloWorld" előkészítést indít el.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Az előző C#-kód Durable Functions 2. x. Durable Functions 1. x esetén az attribútum `OrchestrationClient` helyett attribútumot kell használnia `DurableClient` , és a paraméter típusát kell használnia a `DurableOrchestrationClient` helyett `IDurableOrchestrationClient` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="client-sample-not-visual-studio"></a>Ügyfél minta (nem Visual Studio)

Ha nem a Visual studiót használja a fejlesztéshez, a következőfunction.jsis létrehozhatja *a* fájlon. Ebből a példából megtudhatja, hogyan konfigurálhat egy üzenetsor által aktivált függvényt, amely a tartós előkészítési ügyfél kötését használja:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Az előző JSON a Durable Functions 2. x. Durable Functions 1. x esetében az `orchestrationClient` trigger típusa helyett a értéket kell használnia `durableClient` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

Az alábbiakban olyan nyelvspecifikus példákat találhat, amelyek elindítják az új Orchestrator-függvények példányait.

#### <a name="c-script-sample"></a>C#-parancsfájl mintája

Az alábbi minta azt mutatja be, hogyan használható a tartós előkészítési ügyfél kötése egy új Function-példány elindításához egy üzenetsor által aktivált C#-függvényből:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a paraméter típusát kell használnia a `DurableOrchestrationClient` helyett `IDurableOrchestrationClient` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-sample"></a>JavaScript-minta

Az alábbi minta azt mutatja be, hogyan használható a tartós előkészítési ügyfél kötése egy új Function-példány elindításához egy JavaScript-függvényből:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

A példányok elindításával kapcsolatos további részletek a [példányok kezelése](durable-functions-instance-management.md)oldalon találhatók.

## <a name="entity-trigger"></a>Entitás-trigger

Az entitás-eseményindítók lehetővé teszik az [Entity functions](durable-functions-entities.md)létrehozását. Ez az aktiválás támogatja az adott entitás-példányok feldolgozási eseményeit.

Ha a Visual Studio-eszközöket használja a Azure Functionshoz, az entitás-trigger a .NET attribútum használatával van konfigurálva `EntityTriggerAttribute` .

> [!NOTE]
> Az entitás-eseményindítók a Durable Functions 2. x verziótól kezdődően érhetők el.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólisták sorát kérdezi le. Ezek a várólisták a bővítmény belső implementációjának részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány Megjegyzés az entitás-triggerről:

* **Egyszálas**: egyetlen diszpécser szál egy adott entitás műveleteinek feldolgozásához. Ha egyszerre több üzenetet küld egyetlen entitásnak, a rendszer a műveleteket egy-egy időben dolgozza fel.
* **Méreg – üzenetkezelés** – az entitás-eseményindítók nem támogatják az üzenetek megmérgezését.
* **Üzenet láthatósága** – az entitás-trigger üzenetei el vannak különítve, és a konfigurálható időtartamra láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – az Entity functions nem támogatja a visszatérési értékeket. Vannak olyan API-k, amelyek segítségével az állapot menthető, vagy visszaadható az értékek az előkészítési folyamatoknak.

A végrehajtás során a rendszer automatikusan megőrzi a entitásban végrehajtott összes állapotot a végrehajtás befejezése után.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

Minden Entity függvényhez tartozik egy paraméter `IDurableEntityContext` , amely a következő tagokkal rendelkezik:

* **EntityName**: az aktuálisan végrehajtó entitás neve.
* **EntityKey**: a jelenleg végrehajtó entitás kulcsa.
* **EntityId**: a jelenleg végrehajtó entitás azonosítója.
* **OperationName**: az aktuális művelet neve.
* **HasState**: azt határozza meg, hogy az entitás létezik-e, azaz van-e valamilyen állapota. 
* **GetState \<TState> ()**: az entitás aktuális állapotát kapja meg. Ha még nem létezik, a rendszer létrehozza és inicializálja a következőre: `default<TState>` . A `TState` paraméternek primitív vagy JSON-serializeable típusúnak kell lennie. 
* **GetState \<TState> (initfunction)**: az entitás aktuális állapotát kapja meg. Ha még nem létezik, a rendszer a megadott paraméter meghívásával hozza létre `initfunction` . A `TState` paraméternek primitív vagy JSON-serializeable típusúnak kell lennie. 
* **SetState (ARG)**: az entitás állapotát hozza létre vagy frissíti. A `arg` paraméternek JSON-serializeable objektumnak vagy primitívnek kell lennie.
* **DeleteState ()**: törli az entitás állapotát. 
* **GetInput \<TInput> ()**: az aktuális művelet bemenetének beolvasása. A `TInput` Type paraméternek primitív vagy JSON-serializeable típusúnak kell lennie.
* **Return (ARG)**: egy értéket ad vissza a műveletnek nevezett eljáráshoz. A `arg` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.
* **SignalEntity (EntityId, scheduledTimeUtc, művelet, bemenet)**: egyirányú üzenet küldése egy entitásnak. A `operation` paraméternek nem null értékű sztringnek kell lennie, a választható értéknek olyan UTC datetime értéknek kell lennie, `scheduledTimeUtc` amelyen a műveletet meg kell hívni, és a `input` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.
* **CreateNewOrchestration (orchestratorFunctionName, bemenet)**: új előkészítést indít el. A `input` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.

Az `IDurableEntityContext` Entity függvénynek átadott objektum az `Entity.Current` aszinkron-local tulajdonság használatával érhető el. Ez a megközelítés az osztály alapú programozási modell használata esetén hasznos.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger minta (C# függvény-alapú szintaxis)

A következő kód egy egyszerű, tartós funkcióként megvalósított *számlálós* entitásra mutat példát. Ez a függvény három műveletet határoz meg:, `add` `reset` , és `get` , amelyek mindegyike egész állapotra működik.

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
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

A függvény-alapú szintaxissal és annak használatával kapcsolatos további információkért lásd a [Function-based szintaxist](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger minta (C# osztály alapú szintaxis)

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

> [!NOTE]
> A függvény belépési pontjának metódusát az `[FunctionName]` attribútummal deklarálni *kell az* `static` Entity classs használatakor. A nem statikus belépési pontok metódusai több objektum inicializálását és esetleg más nem definiált viselkedést okozhatnak.

Az entitások osztályai speciális mechanizmusokkal rendelkeznek a kötések és a .NET-függőségek befecskendezésével való interakcióhoz. További információ: [entitások kialakítása](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Példa a triggerre (JavaScript)

A következő kód egy egyszerű *számláló* entitás, amely a JavaScriptben írt tartós függvényként van megvalósítva. Ez a függvény három műveletet határoz meg:, `add` `reset` , és `get` , amelyek mindegyike egész állapotra működik.

**function.json**
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

**index.js**
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

> [!NOTE]
> A NPM-csomag **1.3.0** kezdődően tartós entitások érhetők el a JavaScriptben `durable-functions` .

## <a name="entity-client"></a>Entitás ügyfele

Az entitás-ügyfél kötése lehetővé teszi az [Entity functions](#entity-trigger)aszinkron aktiválását. Ezeket a függvényeket időnként ügyfél- [függvénynek](durable-functions-types-features-overview.md#client-functions)is nevezzük.

Ha a Visual studiót használja, az entitás-ügyfélhez a .NET-attribútum használatával lehet kötni `DurableClientAttribute` .

> [!NOTE]
> A `[DurableClientAttribute]` is használható a koordináló [ügyfélhez](#orchestration-client)való kötéshez.

Ha programozási nyelveket (például *. CSX* vagy *. js* fájlt) használ a fejlesztéshez, az entitás-triggert a következő JSON-objektum határozza meg a `bindings` *function.js* tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` – Olyan helyzetekben használatos, amikor több Function-alkalmazás osztozik ugyanazzal a Storage-fiókkal, de el kell különíteni egymástól. Ha nincs megadva, a rendszer az alapértelmezett értéket `host.json` használja. Ennek az értéknek meg kell egyeznie a cél entitás függvények által használt értékkel.
* `connectionName` – Egy, a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazó Alkalmazásbeállítás neve. A relációs sztring által képviselt Storage-fióknak meg kell egyeznie a TARGET Entity functions által használttal. Ha nincs megadva, a rendszer az alapértelmezett Storage-fiókhoz tartozó kapcsolatok karakterláncot használja a Function alkalmazáshoz.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki a nem kötelező tulajdonságokat, és használja az alapértelmezett viselkedést.

### <a name="entity-client-usage"></a>Entitás-ügyfél használata

A .NET functions szolgáltatásban általában a következőhöz kötődik `IDurableEntityClient` , ami teljes hozzáférést biztosít a tartós entitások által támogatott összes ügyféloldali API-hoz. Emellett az interfészhez is köthető `IDurableOrchestrationClient` , amely hozzáférést biztosít az ügyfél API-khoz mindkét entitáshoz és a munkafolyamatokhoz. Az ügyfél-objektum API-jai a következők:

* **ReadEntityStateAsync \<T>**: egy entitás állapotának beolvasása. Egy olyan választ ad vissza, amely jelzi, hogy létezik-e a célként megadott entitás, és ha igen, milyen állapotban van.
* **SignalEntityAsync**: egyirányú üzenetet küld egy entitásnak, és megvárja, amíg a várólistán lévő.
* **ListEntitiesAsync**: több entitás állapotának lekérdezése. Az entitásokat *név* és *utolsó működési idő* szerint lehet lekérdezni.

Nem kell létrehoznia a cél entitást a jel elküldése előtt – az entitás állapota a jelet kezelő entitás függvényből hozható létre.

> [!NOTE]
> Fontos tisztában lenni azzal, hogy az ügyféltől érkező "jelzések" egyszerűen várólistán lévő, és a későbbiekben aszinkron módon kell feldolgozni. `SignalEntityAsync`Általában az entitás még azelőtt adja vissza a műveletet, hogy a művelet megkezdődik, és nem lehet visszakapni a visszaadott értéket, vagy meg kell figyelnie a kivételeket. Ha erősebb biztosítékokra van szükség (például a munkafolyamatok esetében), a *Orchestrator függvényeket* kell használni, amelyek megvárhatják az entitások műveleteinek befejeződését, és feldolgozhatják a visszatérési értékeket, és megfigyelheti a kivételeket.

### <a name="example-client-signals-entity-directly---c"></a>Példa: közvetlen ügyfél-jelzési entitás – C #

Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely egy "számláló" entitást hív meg.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Példa: ügyfél-jeleket kezelő entitás a felületén keresztül – C #

Ha lehetséges, javasoljuk, hogy az [entitásokat a felületeken keresztül használja](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , mert több típusú ellenőrzést is biztosít. Tegyük fel például, hogy a `Counter` korábban említett entitás egy `ICounter` felületet adott meg, amelyet a következőképpen határoz meg:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Az ügyfél kódja ezután a `SignalEntityAsync<ICounter>` következő típusú biztonságos proxy létrehozásához használható:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

A (z `proxy` ) paraméter a dinamikusan generált példánya `ICounter` , amely belsőleg lefordítja a hívást `Add` az egyenértékű (nem típusos) híváshoz `SignalEntityAsync` a következőre:.

> [!NOTE]
> Az `SignalEntityAsync` API-k egyirányú műveleteket jelentenek. Ha az entitás-illesztőfelületek visszatérnek `Task<T>` , a `T` paraméter értéke mindig null vagy lesz `default` .

Különösen nem érdemes jelezni a `Get` műveletet, mivel a rendszer nem ad vissza értéket. Ehelyett az ügyfelek `ReadStateAsync` közvetlenül is hozzáférhetnek a számláló állapotához, vagy elindíthatnak egy Orchestrator-függvényt, amely meghívja a `Get` műveletet.

### <a name="example-client-signals-entity---javascript"></a>Példa: ügyfél-jelek entitás – JavaScript

Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely "számláló" entitást mutat be a JavaScriptben.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> A NPM-csomag **1.3.0** kezdődően tartós entitások érhetők el a JavaScriptben `durable-functions` .

<a name="host-json"></a>
## <a name="hostjson-settings"></a>Beállítások host.js

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beépített HTTP API-referenciák a példányok kezeléséhez](durable-functions-http-api.md)
