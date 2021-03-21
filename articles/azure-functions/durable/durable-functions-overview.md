---
title: Durable Functions áttekintése – Azure
description: A Azure Functions Durable Functions bővítményének bemutatása.
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: f6199cb20cd56538823f7f7d0967a9cfe59f7099
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636657"
---
# <a name="what-are-durable-functions"></a>Mik azok a tartós függvények?

A *Durable Functions* [Azure functions](../functions-overview.md) , amely lehetővé teszi állapot-nyilvántartó függvények írását kiszolgáló nélküli számítási környezetben. A bővítmény lehetővé teszi az állapot-nyilvántartó munkafolyamatok definiálását a [*Orchestrator függvények*](durable-functions-orchestrations.md) és az állapot-nyilvántartó entitások írásával az Azure functions programozási modellel az [*Entity functions*](durable-functions-entities.md) használatával. A háttérben a bővítmény kezeli az állapotot, az ellenőrzőpontokat és az újraindításokat, így Ön az üzleti logikára koncentrálhat.

## <a name="supported-languages"></a><a name="language-support"></a>Támogatott nyelvek

A Durable Functions jelenleg a következő nyelveket támogatja:

* **C#**: mindkét előre [lefordított osztály kódtára](../functions-dotnet-class-library.md) és [C# parancsfájl](../functions-reference-csharp.md).
* **JavaScript**: csak az Azure functions futtatókörnyezet 2. x verziójára támogatott. A Durable Functions-bővítmény vagy újabb verzió 1.7.0 szükséges. 
* **Python**: az Durable functions bővítmény 2.3.1-es vagy újabb verziójára van szükség.
* **F #**: előre lefordított osztály kódtárak és F # szkript. Az F # parancsfájl csak az Azure Functions futtatókörnyezet 1. x verziójában támogatott.
* **PowerShell**: a Durable functions támogatása jelenleg nyilvános előzetes verzióban érhető el. Csak a Azure Functions futtatókörnyezet 3. x verziójára és a PowerShell 7 verzióra támogatott. A Durable Functions-bővítmény, illetve egy újabb verzió 2.2.2-es verziójának megadását igényli. Jelenleg csak a következő minták támogatottak: [függvények láncolása](#chaining), [ventilátor-kijelentkezés/ventilátor](#fan-in-out), [aszinkron http API](#async-http)-k.

A legújabb szolgáltatások és frissítések eléréséhez ajánlott a Durable Functions bővítmény legújabb verzióinak és a nyelvspecifikus Durable Functions kódtáraknak a használata. További információ a [Durable functions verziókról](durable-functions-versions.md).

Durable Functions célja az összes [Azure functions nyelv](../supported-languages.md)támogatása. További nyelvek támogatásához tekintse meg a [Durable functions problémák listáját](https://github.com/Azure/azure-functions-durable-extension/issues) a legújabb munkaállapotról.

A Azure Functionshoz hasonlóan a [Visual studio 2019](durable-functions-create-first-csharp.md), a [Visual Studio Code](quickstart-js-vscode.md)és a [Azure Portal](durable-functions-create-portal.md)használatával is készíthet durable Functionseket.

## <a name="application-patterns"></a>Alkalmazásminták

Durable Functions elsődleges használati esete az összetett, állapot-nyilvántartó koordinációs követelmények leegyszerűsítése a kiszolgáló nélküli alkalmazásokban. A következő szakaszok ismertetik azokat a tipikus alkalmazási mintákat, amelyek a Durable Functions számára hasznosak lehetnek:

* [Függvényláncolás](#chaining)
* [Elosztás/összevonás](#fan-in-out)
* [Aszinkron HTTP API-k](#async-http)
* [Figyelés](#monitoring)
* [Emberi beavatkozás](#human)
* [Gyűjtő (állapot-nyilvántartó entitások)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Minta #1: függvény láncolása

A függvény láncolása mintában a függvények sorrendje egy adott sorrendben fut le. Ebben a mintában egy függvény kimenetét egy másik függvény bemenetére alkalmazza a rendszer.

![A függvény láncolási mintájának ábrája](./media/durable-functions-concepts/function-chaining.png)

Az alábbi példában látható módon az Durable Functions használatával végezheti el a függvény láncolási mintájának tömör megvalósítását.

Ebben a példában az értékek, a, `F1` `F2` és a `F3` `F4` más függvények neve ugyanabban a Function alkalmazásban. A vezérlési folyamat normál, kötelező kódolási szerkezetek használatával valósítható meg. A kód felülről lefelé fut. A kód a meglévő nyelvi vezérlési folyamatokat, például a feltételes és a hurkokat is magában foglalja. A `try` / `catch` / `finally` blokkokban a hibák kezelésére szolgáló logika is felvehető.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

A `context` paraméterrel más függvények is meghívhatók név, pass paraméterek és visszatérési függvény kimenete alapján. A kód minden egyes meghívásakor `await` a Durable functions Framework az aktuális függvény példányának előrehaladását ellenőrzőpontra helyezi. Ha a folyamat vagy a virtuális gép a végrehajtás közben újraindul, a függvény példánya az előző `await` hívásból folytatódik. További információkért tekintse meg a következő, minta #2: fan out/Fan in című szakaszt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Az `context.df` objektum használatával más függvények is meghívhatók név, pass paraméterek és visszatérési függvény kimenete alapján. A kód minden egyes meghívásakor `yield` a Durable functions Framework az aktuális függvény példányának előrehaladását ellenőrzőpontra helyezi. Ha a folyamat vagy a virtuális gép a végrehajtás közben újraindul, a függvény példánya az előző `yield` hívásból folytatódik. További információkért tekintse meg a következő, minta #2: fan out/Fan in című szakaszt.

> [!NOTE]
> A `context` JavaScriptben lévő objektum a teljes [függvény kontextusát](../functions-reference-node.md#context-object)jelenti. A Durable Functions környezet elérése a `df` fő környezet tulajdonságával.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

Az `context` objektum használatával más függvények is meghívhatók név, pass paraméterek és visszatérési függvény kimenete alapján. A kód minden egyes meghívásakor `yield` a Durable functions Framework az aktuális függvény példányának előrehaladását ellenőrzőpontra helyezi. Ha a folyamat vagy a virtuális gép a végrehajtás közben újraindul, a függvény példánya az előző `yield` hívásból folytatódik. További információkért tekintse meg a következő, minta #2: fan out/Fan in című szakaszt.

> [!NOTE]
> A `context` Pythonban található objektum a koordináló környezetet jelöli. A fő Azure Functions környezet eléréséhez használja a következőt: a (z `function_context` ).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

A `Invoke-ActivityFunction` paranccsal más függvények is meghívhatók név, pass paraméterek és visszatérési függvény kimenete alapján. Minden alkalommal, amikor a kód meghívja `Invoke-ActivityFunction` a `NoWait` kapcsolót anélkül, hogy az Durable functions-keretrendszer ellenőrzőpontja az aktuális függvény példányának előrehaladását. Ha a folyamat vagy a virtuális gép a végrehajtás közben újraindul, a függvény példánya az előző `Invoke-ActivityFunction` hívásból folytatódik. További információkért tekintse meg a következő, minta #2: fan out/Fan in című szakaszt.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Minta #2: ventilátor kivezetése/ventilátor

A fan out/Fan in mintában több függvényt hajt végre párhuzamosan, majd várjon, amíg az összes függvény befejeződik. Gyakran előfordul, hogy egyes összesítési műveletek a függvények által visszaadott eredményeken is megtörténik.

![A ventilátor kikapcsolási/ventilátor mintázatának ábrája](./media/durable-functions-concepts/fan-out-fan-in.png)

A normál függvények segítségével kipróbálhatja, hogy a függvény több üzenetet küld egy várólistára. A Fanning sokkal nagyobb kihívást jelent. Ahhoz, hogy egy normál függvényben bekapcsolja a ventilátort, kódot írhat, amely nyomon követheti a várólista által aktivált függvények befejezését, majd tárolhatja a függvény kimeneteit.

A Durable Functions bővítmény ezt a mintát viszonylag egyszerű kóddal kezeli:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

A rendszer a függvény több példányára terjeszti a kivezetési munkát `F2` . A rendszer a feladatok dinamikus listájának használatával követi nyomon a munkát. `Task.WhenAll` a rendszer úgy hívja, hogy várjon, amíg az összes hívott függvény befejeződik. Ezután a `F2` rendszer összesíti a függvény kimeneteit a dinamikus feladatlistából, és átadja a `F3` függvénynek.

A híváskor megjelenő automatikus ellenőrzőpontok `await` `Task.WhenAll` biztosítják, hogy egy lehetséges Midway-összeomlás vagy-újraindítás esetén nem szükséges a már befejezett feladatok újraindítása.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

A rendszer a függvény több példányára terjeszti a kivezetési munkát `F2` . A rendszer a feladatok dinamikus listájának használatával követi nyomon a munkát. `context.df.Task.all` Az API-t úgy kell meghívni, hogy várjon, amíg az összes meghívott függvény befejeződik. Ezután a `F2` rendszer összesíti a függvény kimeneteit a dinamikus feladatlistából, és átadja a `F3` függvénynek.

A híváskor megjelenő automatikus ellenőrzőpontok `yield` `context.df.Task.all` biztosítják, hogy egy lehetséges Midway-összeomlás vagy-újraindítás esetén nem szükséges a már befejezett feladatok újraindítása.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

A rendszer a függvény több példányára terjeszti a kivezetési munkát `F2` . A rendszer a feladatok dinamikus listájának használatával követi nyomon a munkát. `context.task_all` Az API-t úgy kell meghívni, hogy várjon, amíg az összes meghívott függvény befejeződik. Ezután a `F2` rendszer összesíti a függvény kimeneteit a dinamikus feladatlistából, és átadja a `F3` függvénynek.

A híváskor megjelenő automatikus ellenőrzőpontok `yield` `context.task_all` biztosítják, hogy egy lehetséges Midway-összeomlás vagy-újraindítás esetén nem szükséges a már befejezett feladatok újraindítása.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

A rendszer a függvény több példányára terjeszti a kivezetési munkát `F2` . Jegyezze fel a kapcsoló használatát a `NoWait` `F2` függvény meghívásakor: Ez a kapcsoló lehetővé teszi, hogy a Orchestrator a `F2` tevékenység befejezése nélkül folytassa a műveletet. A rendszer a feladatok dinamikus listájának használatával követi nyomon a munkát. A `Wait-ActivityFunction` parancs hívása megvárja, amíg az összes hívott függvény befejeződik. Ezután a `F2` rendszer összesíti a függvény kimeneteit a dinamikus feladatlistából, és átadja a `F3` függvénynek.

A híváskor megjelenő automatikus ellenőrzőpontok `Wait-ActivityFunction` biztosítják, hogy egy lehetséges Midway-összeomlás vagy-újraindítás esetén nem szükséges újraindítani egy már befejezett feladatot.

---

> [!NOTE]
> Ritka körülmények között előfordulhat, hogy az ablak összeomlása egy tevékenységi függvény befejeződése után következik be, de a befejezését a rendszer a előkészítési előzményekbe menti. Ha ez történik, a tevékenység függvény a folyamat helyreállítása után újraindul.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Minta #3: aszinkron HTTP API-k

Az aszinkron HTTP API-minta a hosszan futó műveletek állapotának a külső ügyfelekkel való koordinálásával kapcsolatos problémát orvosolja. Ennek a mintának a megvalósításának általános módja, ha egy HTTP-végpont aktiválja a hosszan futó műveletet. Ezt követően irányítsa át az ügyfelet egy olyan állapot-végpontra, amelyet az ügyfél lekérdez, hogy megtudja, mikor fejeződött be a művelet.

![A HTTP API mintájának ábrája](./media/durable-functions-concepts/async-http-api.png)

A Durable Functions **beépített támogatást** nyújt ehhez a mintához, egyszerűsítve vagy akár el is távolítja a kódot, amelyet a hosszú ideig futó függvények végrehajtásához kell írnia. Például a Durable Functions gyors üzembe helyezési minták ([C#](durable-functions-create-first-csharp.md) és [JavaScript](quickstart-js-vscode.md)) egy egyszerű Rest-parancsot mutatnak be, amelyet az új Orchestrator-függvények indításához használhat. Egy példány elindítása után a bővítmény elérhetővé teszi a Orchestrator függvény állapotát lekérdező webhook HTTP API-kat. 

Az alábbi példa olyan REST-parancsokat mutat be, amelyek elindítják a Orchestrator, és lekérdezik az állapotát. Az érthetőség kedvéért a protokollok egyes részletei kimaradnak a példából.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Mivel a Durable Functions futtatókörnyezet kezeli az állapotot, nincs szükség saját állapot-követési mechanizmus megvalósítására.

A Durable Functions bővítmény elérhetővé teszi a hosszú ideig futó munkafolyamatokat kezelő beépített HTTP API-kat. Azt is megteheti, hogy saját függvény-eseményindítókat (például HTTP, üzenetsor vagy Azure Event Hubs) és a koordináló ügyfél- [kötést](durable-functions-bindings.md#orchestration-client)használva saját maga is megvalósíthatja ezt a mintát. Előfordulhat például, hogy üzenetsor-üzenetet használ a megszakítás elindításához. Vagy használhat olyan HTTP-triggert, amelyet egy Azure Active Directory hitelesítési házirend véd, és nem a beépített HTTP API-kat, amelyek a hitelesítéshez generált kulcsot használnak.

További információkért lásd a http- [szolgáltatások](durable-functions-http-features.md) című cikket, amely ismerteti, hogyan teheti elérhetővé az aszinkron, hosszan futó FOLYAMATokat http-n keresztül a Durable functions bővítménnyel.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Minta #4: figyelő

A figyelő minta egy rugalmas, ismétlődő folyamatra hivatkozik egy munkafolyamatban. Egy példa egy lekérdezésre, amíg az adott feltételek teljesülnek. Egy alapszintű forgatókönyv, például [egy rendszeres karbantartási](../functions-bindings-timer.md) feladat, az intervallum statikus, a példányok élettartamának kezelése pedig összetett lesz. A Durable Functions használatával rugalmas ismétlődési időközöket hozhat létre, kezelheti a feladatok élettartamát, és több figyelő folyamat is létrehozható egyetlen előkészítéssel.

A figyelő minta példája a korábbi aszinkron HTTP API-forgatókönyv fordítottja. Ahelyett, hogy egy külső ügyfél végpontját kitéve egy hosszan futó művelet figyelésére, a hosszan futó figyelő külső végpontot használ, majd megvárja az állapot változását.

![A figyelő mintájának ábrája](./media/durable-functions-concepts/monitor.png)

Néhány sornyi kódban a Durable Functions használatával több, tetszőleges végpontokat figyelő figyelőt hozhat létre. A figyelők egy feltétel teljesülése esetén is letölthetik a végrehajtást, vagy egy másik függvény használhatja a tartós előkészítési ügyfelet a figyelők megszakítására. Egy `wait` adott feltétel alapján módosíthatja a figyelő intervallumát (például exponenciális leállítási.) 

A következő kód egy alapszintű figyelőt valósít meg:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A figyelő jelenleg nem támogatott a PowerShellben.

---

A kérés fogadásakor a rendszer létrehoz egy új előkészítési példányt az adott AZONOSÍTÓJÚ feladatokhoz. A példány lekérdezi az állapotot, amíg a feltétel teljesül, és a hurok ki van zárva. A tartós időzítő vezérli a lekérdezési időközt. Ezt követően több munka is elvégezhető, vagy az előkészítés véget ért. Ha `nextCheck` meghaladja `expiryTime` a t, a figyelő véget ér.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Minta #5: emberi interakció

Számos automatizált folyamat tartalmaz valamilyen emberi interakciót. Az emberek automatikus folyamatba való bevonása trükkös, mert az emberek nem a lehető legszélesebb körben elérhetők és a Cloud Services-ként reagálnak Az automatikus folyamat az időtúllépések és a kompenzációs logika használatával lehetővé teheti az ilyen interakciókat.

A jóváhagyási folyamat olyan üzleti folyamatra mutat példát, amely emberi interakciót is magában foglal. Előfordulhat, hogy a felettes jóváhagyása egy olyan Költségjelentés esetében szükséges, amely meghaladja az adott dollár mennyiségét. Ha a kezelő nem hagyja jóvá a költségjelentés 72 órán belül történő jóváhagyását (lehet, hogy a felettes a vakáción ment), a eszkalációs folyamat beolvassa a-t, hogy valaki más (például a felettes felettese) jóváhagyást kapjon.

![Az emberi interakció mintájának ábrája](./media/durable-functions-concepts/approval.png)

Ebben a példában egy Orchestrator függvény használatával valósítható meg a minta. A Orchestrator [tartós időzítőt](durable-functions-timers.md) használ a jóváhagyás kéréséhez. Ha időtúllépés történik, a Orchestrator megnőnek. A Orchestrator egy [külső eseményt](durable-functions-external-events.md)vár, például egy emberi interakció által generált értesítést.

Ezek a példák jóváhagyási folyamatot hoznak létre az emberi interakciós minta bemutatásához:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

A tartós időzítő létrehozásához hívja a következőt: `context.CreateTimer` . Az értesítés fogadása: `context.WaitForExternalEvent` . Ezt követően el kell döntenie, hogy a `Task.WhenAny` rendszer meghívja-e a kiterjesztést (időtúllépés történik) vagy feldolgozza a jóváhagyást (a jóváhagyás az időkorlát előtt érkezik).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

A tartós időzítő létrehozásához hívja a következőt: `context.df.createTimer` . Az értesítés fogadása: `context.df.waitForExternalEvent` . Ezt követően el kell döntenie, hogy a `context.df.Task.any` rendszer meghívja-e a kiterjesztést (időtúllépés történik) vagy feldolgozza a jóváhagyást (a jóváhagyás az időkorlát előtt érkezik).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

A tartós időzítő létrehozásához hívja a következőt: `context.create_timer` . Az értesítés fogadása: `context.wait_for_external_event` . Ezt követően el kell döntenie, hogy a `context.task_any` rendszer meghívja-e a kiterjesztést (időtúllépés történik) vagy feldolgozza a jóváhagyást (a jóváhagyás az időkorlát előtt érkezik).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az emberi interakció jelenleg nem támogatott a PowerShellben.

---

Egy külső ügyfél a [beépített http API](durable-functions-http-api.md#raise-event)-k használatával kézbesítheti az esemény értesítését a várakozó Orchestrator függvénynek:

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Egy esemény is kiemelhető a tartós összehangoló ügyféllel egy másik függvényből ugyanabban a Function alkalmazásban:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az emberi interakció jelenleg nem támogatott a PowerShellben.

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Minta #6: aggregátor (állapot-nyilvántartó entitások)

A hatodik minta az események adatainak egy adott, címezhető *entitásba* való összesítésére szolgál. Ebben a mintában az összesíteni kívánt adatok több forrásból származhatnak, a kötegekben is elhelyezhetők, vagy hosszú időn keresztül elszórtan lehetnek. Előfordulhat, hogy a gyűjtőnek műveleteket kell végeznie az események érkezésekor, és előfordulhat, hogy a külső ügyfeleknek le kell kérdezni az összesített adatokat.

![Összesítő diagram](./media/durable-functions-concepts/aggregator.png)

A minta a normál, állapot nélküli függvények használatával történő megvalósítására tett kísérlet során az, hogy a Egyidejűség-vezérlés óriási kihívás lesz. Nem csupán annyit kell aggódnia, hogy ugyanazokat az információkat egyszerre több szálra módosítja, azonban azt is meg kell aggódnia, hogy a gyűjtő egyszerre csak egyetlen virtuális gépen fut.

[Tartós entitások](durable-functions-entities.md) használatával egyszerűen implementálhatja ezt a mintát egyetlen függvényként.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

A tartós entitások osztályként is modellezése a .NET-ben. Ez a modell akkor lehet hasznos, ha a műveletek listája rögzített, és nagy lesz. A következő példa az entitás egyenértékű implementációját `Counter` használja .net-osztályok és-metódusok használatával.

```csharp
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

```python
import logging
import json

import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableOrchestrationContext):

    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
        context.set_result(current_value)
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    
    context.set_state(current_value)

main = df.Entity.create(entity_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Tartós entitások jelenleg nem támogatottak a PowerShellben.

---

Az ügyfelek az entitás- [ügyfél kötésének](durable-functions-bindings.md#entity-client)használatával sorba helyezni *műveleteket* (más néven "jelzés").

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> A dinamikusan generált proxyk a .NET-keretrendszerben is elérhetők, ha az entitások típus-biztonságos módon vannak jelezve. Továbbá a jelzésen felül az ügyfelek az entitások függvényének állapotáról is lekérhetik a [típus-biztonságos metódusok](durable-functions-bindings.md#entity-client-usage) használatával.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    entity_id = df.EntityId("Counter", "myCounter")
    instance_id = await client.signal_entity(entity_id, "add", 1)
    return func.HttpResponse("Entity signaled")
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Tartós entitások jelenleg nem támogatottak a PowerShellben.

---

Az Entity functions a C#, a JavaScript és a Python [Durable Functions 2,0](durable-functions-versions.md) -es és újabb verziójában érhető el.

## <a name="the-technology"></a>A technológia

A színfalak mögött a Durable Functions-bővítmény a [tartós feladatok keretrendszerére](https://github.com/Azure/durabletask)épül, amely egy nyílt forráskódú, a githubon található, a munkafolyamatokat a kódban felépítő könyvtár. Például a Azure Functions a Azure WebJobs kiszolgáló nélküli fejlődése, Durable Functions a tartós feladat-keretrendszer kiszolgáló nélküli fejlődése. A Microsoft és más szervezetek széles körben használják a tartós feladatok keretrendszerét a kritikus fontosságú folyamatok automatizálására. A kiszolgáló nélküli Azure Functions környezet számára természetes módon illeszkedik.

## <a name="code-constraints"></a>A kód megkötései

Ahhoz, hogy megbízható és hosszan futó végrehajtási garanciákat lehessen biztosítani, a Orchestrator függvényeknek követniük kell a kódolási szabályok készletét. További információ: [Orchestrator Function Code megkötések](durable-functions-code-constraints.md) cikk.

## <a name="billing"></a>Számlázás

Durable Functions számlázása ugyanaz, mint Azure Functions. További információ: [Azure functions díjszabása](https://azure.microsoft.com/pricing/details/functions/). A Azure Functions használati [tervben](../consumption-plan.md)szereplő Orchestrator-függvények végrehajtásakor bizonyos számlázási viselkedések ismerete szükséges. További információ ezekről a viselkedésekről: [Durable functions számlázási](durable-functions-billing.md) cikk.

## <a name="jump-right-in"></a>Ugrás közvetlenül a

A következő, a nyelvfüggő gyors útmutatók egyikének elvégzésével megkezdheti a Durable Functions használatának megkezdését 10 perc alatt:

* [C# a Visual Studio 2019 használatával](durable-functions-create-first-csharp.md)
* [JavaScript a Visual Studio Code használatával](quickstart-js-vscode.md)
* [Python a Visual Studio Code használatával](quickstart-python-vscode.md)
* [PowerShell a Visual Studio Code használatával](quickstart-powershell-vscode.md)

Ezekben a gyors útmutatókban helyileg létrehozhatja és tesztelheti a "Hello World" tartós funkciót. Ezután közzéteheti a függvénykódot az Azure-ban. Az Ön által létrehozott függvény összehangolja és láncokba rendezi a más függvények hívásait.

## <a name="learn-more"></a>Tudjon meg többet

Az alábbi videó a Durable Functions előnyeit mutatja be:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

A Durable Functions és a mögöttes technológia részletes ismertetését lásd az alábbi videóban (ez a .NET-re összpontosít, de a fogalmak más támogatott nyelvekre is érvényesek):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Mivel a Durable Functions a [Azure functions](../functions-overview.md)speciális bővítménye, az összes alkalmazás esetében nem megfelelő. Az egyéb Azure-előkészítési technológiákkal való összehasonlításért lásd: [Azure functions és Azure Logic apps összehasonlítása](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Durable Functions függvények típusai és funkciói](durable-functions-types-features-overview.md)
