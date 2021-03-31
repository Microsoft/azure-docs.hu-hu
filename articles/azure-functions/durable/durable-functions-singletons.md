---
title: Durable Functions – Azure
description: Az Durable Functions bővítmény használata Azure Functionshoz.
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 50ed473d61dff19f41f77a79513c0ddab521e56f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325742"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions)

A háttérben felmerülő feladatok esetében gyakran biztosítania kell, hogy egy adott Orchestrator csak egy példánya fusson egyszerre. Ezt a fajta egyedi viselkedést [Durable functions](durable-functions-overview.md) úgy is biztosíthatja, hogy egy adott Orchestrator rendel hozzá egy adott példányhoz, amikor létrehozza azt.

## <a name="singleton-example"></a>Egyszeres példa

Az alábbi példa egy olyan HTTP-trigger függvényt mutat be, amely létrehoz egy egypéldányos háttér-előkészítési feladatot. A kód biztosítja, hogy csak egy példány létezik egy adott példány-AZONOSÍTÓhoz.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Completed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Failed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> Az előző C#-kód Durable Functions 2. x. Durable Functions 1. x esetén az attribútum `OrchestrationClient` helyett attribútumot kell használnia `DurableClient` , és a paraméter típusát kell használnia a `DurableOrchestrationClient` helyett `IDurableOrchestrationClient` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance 
        || existingInstance.runtimeStatus == "Completed" 
        || existingInstance.runtimeStatus == "Failed" 
        || existingInstance.runtimeStatus == "Terminated") {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**__init__. a**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None or existing_instance.runtime_status in ["Completed", "Failed", "Terminated"]:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

Alapértelmezés szerint a példány-azonosítók véletlenszerűen generált GUID azonosítók. Az előző példában azonban a példány AZONOSÍTÓját átadja a rendszer az URL-ből származó útvonal-adatok között. A kód meghívja `GetStatusAsync` (C#), `getStatus` (JavaScript) vagy `get_status` (Python) annak ellenőrzését, hogy a megadott azonosítójú példány már fut-e. Ha nem fut ilyen példány, a rendszer létrehoz egy új példányt az AZONOSÍTÓval.

> [!NOTE]
> Ebben a példában a verseny feltétele lehetséges. Ha a **HttpStartSingle** két példánya egyidejű végrehajtást hajt végre, mindkét függvényhívás sikeres lesz, de a rendszer csak egy hanghívási példányt fog elindulni. A követelményektől függően előfordulhat, hogy ez nem lenne lehetséges mellékhatása. Ezért fontos annak biztosítása, hogy ne lehessen egyszerre két kérelmet végrehajtani az trigger-függvényt.

A Orchestrator függvény implementációjának részletei valójában nem számítanak. Ez lehet egy normál Orchestrator-függvény, amely megkezdi és befejeződik, vagy az is lehet, hogy örökre fut (azaz egy [örök](durable-functions-eternal-orchestrations.md)kialakítás). A lényeg az, hogy egyszerre csak egy példány fut egyszerre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg a rendszerelőkészítések natív HTTP-funkcióit](durable-functions-http-features.md)
