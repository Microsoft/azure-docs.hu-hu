---
title: Időzítők a Durable Functionsban – Azure
description: Megtudhatja, hogyan implementálhatja a tartós időzítőket a Azure Functions Durable Functions-bővítményében.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91876447"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Időzítők Durable Functions (Azure Functions)

[](durable-functions-overview.md) A Durable functions *tartós időzítőket* biztosít a Orchestrator függvényekben a késések megvalósításához, illetve az aszinkron műveletek időtúllépésének beállításához. Tartós időzítőket kell használni a és a ( `Thread.Sleep` `Task.Delay` C#) és a `setTimeout()` `setInterval()` (JavaScript), vagy `time.sleep()` (Python) helyett a Orchestrator függvényekben.

Egy tartós időzítőt úgy hozhat létre, hogy meghívja a `CreateTimer` (.net) metódust vagy a `createTimer` (JavaScript) metódust a koordinációs [trigger kötéséhez](durable-functions-bindings.md#orchestration-trigger). A metódus egy olyan feladatot ad vissza, amely egy adott dátumon és időpontban fejeződik be.

## <a name="timer-limitations"></a>Időzítő korlátozásai

Amikor a 4:30 órakor lejáró időzítőt hoz létre, az alapul szolgáló tartós feladati keretrendszer enqueues egy olyan üzenetet, amely csak a 4:30 órakor lesz látható. Ha a Azure Functions használati tervben fut, akkor az újonnan látható időzítő üzenet biztosítja, hogy a Function alkalmazás aktiválva legyen egy megfelelő virtuális gépen.

> [!NOTE]
> * A tartós [2.3.0 verziójától](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) kezdve a tartós időzítők korlátlanok. A bővítmény korábbi verzióiban a tartós időzítők legfeljebb hét napig tartanak. Ha egy korábbi verziót használ, és hét napnál hosszabb késleltetésre van szüksége, használja az időzítő API-kat egy `while` hurokban a késleltetés szimulálása érdekében.
> * Mindig a `CurrentUtcDateTime` .net- `DateTime.UtcNow` ben vagy a `currentUtcDateTime` `Date.now` `Date.UTC` -ban vagy a-ben, illetve a-ban vagy a-ben, a tartós időzítők esetében pedig a tűz idejére További információ: [Orchestrator Function Code megkötések](durable-functions-code-constraints.md) cikk.

## <a name="usage-for-delay"></a>Használat késleltetéshez

Az alábbi példa azt szemlélteti, hogyan használható a tartós időzítő a végrehajtás késleltetéséhez. A példa minden nap 10 napra szóló számlázási értesítést állít ki.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Az előző C# példa Durable Functions 2. x-et céloz meg. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> Kerülje a végtelen hurkokat a Orchestrator functions szolgáltatásban. A végtelen hurkos forgatókönyvek biztonságos és hatékony megvalósításával kapcsolatos további információkért lásd: [örök összeszerelések](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Használat időtúllépés esetén

Ez a példa azt szemlélteti, hogyan használhatók a tartós időzítők az időtúllépések megvalósításához.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Az előző C# példa Durable Functions 2. x-et céloz meg. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> A (z) `CancellationTokenSource` (.net) vagy `cancel()` a visszaadott hívás `TimerTask` (JavaScript) használatával szakítsa meg a tartós időzítőt, ha a kód nem várja meg a betöltését. A tartós feladatok keretrendszere nem változtatja meg a "befejezett" állapotot, amíg az összes fennmaradó feladat be nem fejeződik vagy meg nem szakítva.

Ez a lemondási mechanizmus nem szakítja meg a folyamatban lévő tevékenység vagy az alhálózatok végrehajtásának folyamatát. Ehelyett egyszerűen lehetővé teszi, hogy a Orchestrator függvény figyelmen kívül hagyja az eredményt, és továbblép. Ha a Function alkalmazás a használati tervet használja, akkor az elhagyott tevékenység által felhasznált időt és memóriát továbbra is számlázza. Alapértelmezés szerint a használati tervben futó függvények öt percen belül időtúllépéssel rendelkeznek. Ha túllépi ezt a korlátot, a rendszer újrahasznosítja a Azure Functions gazdagépet az összes végrehajtás leállításához és a elszabadult számlázási helyzet elkerüléséhez. A [függvény időtúllépése konfigurálható](../functions-host-json.md#functiontimeout).

A Orchestrator functions-időtúllépések megvalósításának alaposabb példáját az [emberi interakció & időtúllépések – telefonos ellenőrzés](durable-functions-phone-verification.md) című cikkben találja.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a külső események növeléséről és kezeléséről](durable-functions-external-events.md)
