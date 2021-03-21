---
title: Hibák Durable Functions-Azure-ban való kezelésére
description: Megtudhatja, hogyan kezelheti a hibákat a Azure Functions Durable Functions bővítményében.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673647"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hibák feldolgozása a Durable Functionsban (Azure Functions)

A tartós függvények összehangolása programkódban valósul meg, és a programozási nyelv beépített hibák kezelésére szolgáló funkcióit használhatja. Valójában nincs olyan új fogalma, amelyet meg kell tanulnia a hibák kezelésére és a kompenzációba való felvételre. Vannak azonban olyan viselkedések, amelyeket érdemes figyelembe vennie.

## <a name="errors-in-activity-functions"></a>Hibák a Activity functions szolgáltatásban

A tevékenységi függvényekben felmerülő kivételeket a rendszer visszaküldi a Orchestrator függvénynek `FunctionFailedException` . A Orchestrator függvényben az igényeinek megfelelő hibakezelés és kompenzációs kód is írható.

Vegyük például a következő Orchestrator függvényt, amely az egyik fiókból a másikba továbbítja a forrásokat:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Az előző C#-példák a Durable Functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

Ha az első **CreditAccount** -függvény hívása sikertelen, a Orchestrator függvény kompenzálja a források visszaküldését a forrás fiókba.

## <a name="automatic-retry-on-failure"></a>Sikertelen automatikus újrapróbálkozás

A Activity functions vagy a beosztási függvények meghívásakor megadhat egy automatikus újrapróbálkozási házirendet. A következő példa legfeljebb háromszor próbálkozik egy függvény hívásával, és minden újrapróbálkozás után 5 másodpercet vár:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Az előző C#-példák a Durable Functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

Az előző példában szereplő tevékenység függvény hívása egy paramétert fogad el az automatikus újrapróbálkozási házirend konfigurálásához. Az automatikus újrapróbálkozási házirend testreszabására több lehetőség is van:

* **Kísérletek maximális száma**: az újrapróbálkozási kísérletek maximális száma.
* **Első újrapróbálkozás időköze**: az első újrapróbálkozási kísérlet előtti várakozási idő.
* **Leállítási együttható**: a leállítási növelésének mértékét meghatározó együttható. Az alapértelmezett érték 1.
* **Maximális újrapróbálkozási időköz**: az újrapróbálkozási kísérletek között elvárt maximális időtartam.
* **Újrapróbálkozás időtúllépése**: az újrapróbálkozások maximálisan elkölthető ideje. Az alapértelmezett viselkedés az, ha határozatlan ideig próbálkozik.
* **Leíró**: a felhasználó által definiált visszahívás megadható annak megállapításához, hogy egy függvényt újra kell-e próbálni. 

> [!NOTE]
> A JavaScript () Durable Functions jelenleg nem támogatja a felhasználó által definiált visszahívásokat `context.df.RetryOptions` .


## <a name="function-timeouts"></a>Függvények időtúllépései

Előfordulhat, hogy egy Orchestrator függvény hívását is el szeretné hagyni, ha túl sokáig tart a Befejezés. Ez a megfelelő módszer a (z) (.net) [](durable-functions-timers.md) , `context.CreateTimer` `context.df.createTimer` (JavaScript) vagy `context.create_timer` (Python) ( `Task.WhenAny` .net), `context.df.Task.any` (JavaScript) vagy (Python) együttes használatával történő tartós időzítő létrehozásával `context.task_any` , ahogy az alábbi példában is látható:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Az előző C#-példák a Durable Functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
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
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Ez a mechanizmus valójában nem szakítja meg a folyamatban lévő tevékenységek működésének végrehajtását. Ehelyett egyszerűen lehetővé teszi, hogy a Orchestrator függvény figyelmen kívül hagyja az eredményt, és továbblép. További információ: [időzítők](durable-functions-timers.md#usage-for-timeout) dokumentációja.

## <a name="unhandled-exceptions"></a>Nem kezelt kivételek

Ha egy Orchestrator függvény nem kezelt kivétel miatt meghiúsul, a rendszer naplózza a kivétel részleteit, és a példány `Failed` állapota állapottal fejeződik be.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg az örök összeszereléseket](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan diagnosztizálhatja a problémákat](durable-functions-diagnostics.md)
