---
title: Függvények láncolása Durable Functionsban – Azure
description: Megtudhatja, hogyan futtathat egy olyan Durable Functions mintát, amely függvények sorozatát hajtja végre.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028250"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Függvények láncolása Durable Functions-Hello Sequence minta

A függvények láncolása egy adott sorrendben végrehajtott függvények sorrendjének a mintáját jelöli. Egy függvény kimenetét gyakran egy másik függvény bemenetére kell alkalmazni. Ez a cikk a Durable functions rövid útmutató ([C#](durable-functions-create-first-csharp.md),  [JavaScript](quickstart-js-vscode.md)vagy [Python](quickstart-python-vscode.md)) befejezése után létrehozott láncolási sorozatot ismerteti. További információ a Durable Functionsről: [Durable functions áttekintése](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E1_HelloSequence`: Egy [Orchestrator függvény](durable-functions-bindings.md#orchestration-trigger) , amely `E1_SayHello` többször is meghívja a sorozatot. Tárolja a hívások kimeneteit `E1_SayHello` , és rögzíti az eredményeket.
* `E1_SayHello`: Egy [tevékenység-függvény](durable-functions-bindings.md#activity-trigger) , amely paraméterként megadott egy "Hello" karakterláncot.
* `HttpStart`: Egy HTTP által aktivált [tartós ügyfél](durable-functions-bindings.md#orchestration-client) -függvény, amely elindítja a Orchestrator egy példányát.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence Orchestrator függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Minden C#-előkészítési függvénynek rendelkeznie kell egy típusú paraméterrel `DurableOrchestrationContext` , amely létezik a `Microsoft.Azure.WebJobs.Extensions.DurableTask` szerelvényben. Ez a környezeti objektum lehetővé teszi más *tevékenységi* függvények meghívását és a bemeneti paraméterek átadását a `CallActivityAsync` metódusának használatával.

A kód háromszor meghívja a `E1_SayHello` különböző paraméterek értékeit. Az egyes hívások visszatérési értéke hozzáadódik a `outputs` listához, amelyet a függvény végén adnak vissza.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> A JavaScript-Durable Functions csak a 3,0 Runtime funkcióhoz érhető el.

#### <a name="functionjson"></a>function.json

Ha a Visual Studio Code-ot vagy a Azure Portalt használja a fejlesztéshez, itt látható a Orchestrator függvény *function.js* fájljának tartalma. A legtöbb Orchestrator- *function.jsa* fájlokon majdnem így néz ki.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

A lényeg a `orchestrationTrigger` kötés típusa. Az összes Orchestrator függvénynek ezt az trigger-típust kell használnia.

> [!WARNING]
> Az Orchestrator függvények "nincs I/O" szabályának betartásához ne használjon semmilyen bemeneti vagy kimeneti kötést az `orchestrationTrigger` trigger kötésének használatakor.  Ha más bemeneti vagy kimeneti kötésekre van szükség, azokat a függvények kontextusában kell használni `activityTrigger` , amelyeket a Orchestrator hívnak. További információ: [Orchestrator Function Code megkötések](durable-functions-code-constraints.md) cikk.

#### <a name="indexjs"></a>index.js

Itt látható a Orchestrator függvény:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Minden JavaScript-előkészítési függvénynek tartalmaznia kell a [ `durable-functions` modult](https://www.npmjs.com/package/durable-functions). Ez egy olyan könyvtár, amely lehetővé teszi Durable Functions a JavaScriptben való írását. A Orchestrator függvény és más JavaScript-függvények között három jelentős különbség van:

1. A Orchestrator függvény egy [Generator függvény](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. A függvény a `durable-functions` modul `orchestrator` metódusának (itt) hívásával van becsomagolva `df` .
3. A függvénynek szinkronnak kell lennie. Mivel a "Orchestrator" metódus kezeli a "Context. Done" hívását, a függvénynek egyszerűen "Return" értéknek kell lennie.

Az `context` objektum egy tartós előkészítési `df` környezeti objektumot tartalmaz, amely lehetővé teszi más *tevékenységi* funkciók meghívását és a bemeneti paraméterek átadását a `callActivity` metódusának használatával. A kód háromszor meghívja a `E1_SayHello` különböző paraméterek értékeit, ezzel `yield` jelezve, hogy a végrehajtásnak meg kell várnia a visszaadott aszinkron tevékenység függvényének hívását. Az egyes hívások visszatérési értéke hozzáadódik a `outputs` tömbhöz, amelyet a függvény végén adnak vissza.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> A Python Durable Functions csak a 3,0 futtatókörnyezethez érhető el.


#### <a name="functionjson"></a>function.json

Ha a Visual Studio Code-ot vagy a Azure Portalt használja a fejlesztéshez, itt látható a Orchestrator függvény *function.js* fájljának tartalma. A legtöbb Orchestrator- *function.jsa* fájlokon majdnem így néz ki.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

A lényeg a `orchestrationTrigger` kötés típusa. Az összes Orchestrator függvénynek ezt az trigger-típust kell használnia.

> [!WARNING]
> Az Orchestrator függvények "nincs I/O" szabályának betartásához ne használjon semmilyen bemeneti vagy kimeneti kötést az `orchestrationTrigger` trigger kötésének használatakor.  Ha más bemeneti vagy kimeneti kötésekre van szükség, azokat a függvények kontextusában kell használni `activityTrigger` , amelyeket a Orchestrator hívnak. További információ: [Orchestrator Function Code megkötések](durable-functions-code-constraints.md) cikk.

#### <a name="__init__py"></a>\_\_init \_ \_ . a

Itt látható a Orchestrator függvény:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Minden Python- [ `durable-functions` előkészítési](https://pypi.org/project/azure-functions-durable)függvénynek tartalmaznia kell a csomagot. Ez egy olyan könyvtár, amely lehetővé teszi Durable Functions a Pythonban való írását. A Orchestrator függvény és más Python-függvények között két jelentős különbség van:

1. A Orchestrator függvény egy [Generator függvény](https://wiki.python.org/moin/Generators).
2. A _fájlnak_ a fájl végén lévő Orchestrator kell regisztrálnia a Orchestrator függvényt `main = df.Orchestrator.create(<orchestrator function name>)` . Ez segít megkülönböztetni más, segítő, a fájlban deklarált funkciókat.

Az `context` objektum lehetővé teszi más *tevékenységi* függvények meghívását és a bemeneti paraméterek átadását a `call_activity` metódusának használatával. A kód háromszor meghívja a `E1_SayHello` különböző paraméterek értékeit, ezzel `yield` jelezve, hogy a végrehajtásnak meg kell várnia a visszaadott aszinkron tevékenység függvényének hívását. Az egyes hívások visszatérési értékét a függvény végén adja vissza a rendszer.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello Activity függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

A tevékenységek az `ActivityTrigger` attribútumot használják. A megadott művelettel `IDurableActivityContext` kapcsolatos műveleteket hajthat végre, például elérheti a bemeneti értéket a használatával `GetInput<T>` .

A megvalósítása `E1_SayHello` viszonylag triviális karakterlánc-formázási művelet.

Az-hoz való kötés helyett `IDurableActivityContext` közvetlenül a tevékenység függvénynek átadott típushoz köthető. Például:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsbekapcsolva

A Activity függvényhez tartozó *function.js* hasonló a (z) rendszerhez, azzal a kivétellel, hogy `E1_SayHello` kötési típus `E1_HelloSequence` `activityTrigger` helyett kötési típust használ `orchestrationTrigger` .

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> A hanghívási függvény által meghívott összes tevékenység-függvénynek a kötést kell használnia `activityTrigger` .

A megvalósítása `E1_SayHello` viszonylag triviális karakterlánc-formázási művelet.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

A hangminta függvénytől eltérően a Activity függvénynek nincs szüksége speciális beállításra. A Orchestrator függvény által átadott bemenet a `context.bindings` kötés neve alatt található objektumon szerepel `activityTrigger` – ebben az esetben `context.bindings.name` . A kötési név beállítható az exportált függvény paramétereként, és közvetlenül is elérhető, ami a mintakód.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsbekapcsolva

A Activity függvényhez tartozó *function.js* hasonló a (z) rendszerhez, azzal a kivétellel, hogy `E1_SayHello` kötési típus `E1_HelloSequence` `activityTrigger` helyett kötési típust használ `orchestrationTrigger` .

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> A hanghívási függvény által meghívott összes tevékenység-függvénynek a kötést kell használnia `activityTrigger` .

A megvalósítása `E1_SayHello` viszonylag triviális karakterlánc-formázási művelet.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ init \_ \_ . a

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

A Orchestrator függvénytől eltérően a Activity függvénynek nincs szüksége speciális beállításra. A Orchestrator függvény által átadott bemenet közvetlenül elérhető paraméterként a függvény számára.

---

### <a name="httpstart-client-function"></a>HttpStart-ügyfél funkció

A Orchestrator függvény egy példányát elindíthatja egy ügyféloldali függvény használatával. A `HttpStart` http által aktivált függvény használatával indíthatja el a példányait `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

A feladatokkal való kommunikációhoz a függvénynek tartalmaznia kell egy `DurableClient` bemeneti kötést. A-ügyfelet egy előkészítés elindítására használhatja. Emellett segítséget nyújthat egy olyan HTTP-válasz visszaküldéséhez, amely URL-címeket tartalmaz az új hangolás állapotának ellenőrzéséhez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsbekapcsolva

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

A feladatokkal való kommunikációhoz a függvénynek tartalmaznia kell egy `durableClient` bemeneti kötést.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

`df.getClient`Egy objektum beszerzésére használható `DurableOrchestrationClient` . A-ügyfelet egy előkészítés elindítására használhatja. Emellett segítséget nyújthat egy olyan HTTP-válasz visszaküldéséhez, amely URL-címeket tartalmaz az új hangolás állapotának ellenőrzéséhez.

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsbekapcsolva

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

A feladatokkal való kommunikációhoz a függvénynek tartalmaznia kell egy `durableClient` bemeneti kötést.

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ . a

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

`DurableOrchestrationClient`Durable functions-ügyfél beszerzéséhez használja a konstruktort. A-ügyfelet egy előkészítés elindítására használhatja. Emellett segítséget nyújthat egy olyan HTTP-válasz visszaküldéséhez, amely URL-címeket tartalmaz az új hangolás állapotának ellenőrzéséhez.

---

## <a name="run-the-sample"></a>Minta futtatása

Az előkészítés végrehajtásához `E1_HelloSequence` küldje el az alábbi http post-kérelmet a `HttpStart` függvénynek.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Az előző HTTP-kódrészlet feltételezi, hogy van egy bejegyzés a `host.json` fájlban, amely eltávolítja az alapértelmezett `api/` előtagot a http-trigger összes funkciójának URL-címéről. A konfigurációhoz tartozó jelölést a `host.json` mintákban található fájlban találja.

Ha például egy "myfunctionapp" nevű Function alkalmazásban futtatja a mintát, cserélje le a "{host}" kifejezést "myfunctionapp.azurewebsites.net" értékre.

Az eredmény egy HTTP 202-válasz, amely a következőhöz hasonlóan van (rövidítve):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Ezen a ponton a rendszer várólistára helyezi az előkészítést, és azonnal elindul. A fejlécben található URL-cím segítségével `Location` ellenőrizhető a végrehajtás állapota.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Az eredmény az előkészítés állapota. A rendszer gyorsan futtatja és befejezi, így a *befejezett* állapotú, a következőhöz hasonló választ kell látnia (rövidítve):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Amint láthatja, a `runtimeStatus` példány *befejeződik* , és a tartalmazza a `output` Orchestrator függvény végrehajtásának JSON-szerializált eredményét.

> [!NOTE]
> Hasonló indító logikát alkalmazhat más típusú triggerekhez, például, `queueTrigger` `eventHubTrigger` vagy `timerTrigger` .

Tekintse meg a függvény-végrehajtási naplókat. A `E1_HelloSequence` függvény többször indult el és fejeződött be, mert a folyamat [megbízhatósága](durable-functions-orchestrations.md#reliability) című témakörben leírt újrajátszás viselkedése történt. Másfelől azonban csak három végrehajtás történt, `E1_SayHello` mivel ezek a függvények végrehajtása nem kerül újra lejátszásra.

## <a name="next-steps"></a>Következő lépések

Ez a minta egy egyszerű függvény-láncolási előkészítést mutat be. A következő minta bemutatja, hogyan valósítható meg a ventilátor-out/Fan-in minta.

> [!div class="nextstepaction"]
> [A fan-out/Fan-in minta futtatása](durable-functions-cloud-backup.md)
