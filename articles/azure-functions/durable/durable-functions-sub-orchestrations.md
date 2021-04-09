---
title: Durable Functions – Azure
description: A Azure Functionshoz Durable Functions-bővítményben lévő előkészítési folyamatokat hívhatja.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85340818"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions)

A Orchestrator függvények más Orchestrator-függvényeket is hívhatnak. Létrehozhat például egy kisebb Orchestrator-függvények könyvtárainak nagyobb előkészítését. Vagy egy Orchestrator függvény több példányát is futtathatja párhuzamosan.

Egy Orchestrator függvény meghívhat egy másik Orchestrator-függvényt a `CallSubOrchestratorAsync` `CallSubOrchestratorWithRetryAsync` .net-ben lévő metódusok vagy a `callSubOrchestrator` JavaScript-metódusok használatával `callSubOrchestratorWithRetry` . A [& Compensation](durable-functions-error-handling.md#automatic-retry-on-failure) szolgáltatással kapcsolatos hiba további információkat tartalmaz az automatikus Újrapróbálkozással kapcsolatban.

Az Orchestrator függvények a hívó szemszögéből hasonlóan működnek a tevékenységi funkciókkal. Egy értéket adhatnak vissza, kivételt jeleznek, és a szülő Orchestrator függvénytől is megtekinthetők. 

> [!NOTE]
> A .NET és a JavaScript jelenleg támogatja a alfolyamatokat.

## <a name="example"></a>Példa

Az alábbi példa egy IoT ("eszközök internetes hálózata") forgatókönyvet mutat be, ahol több eszközt kell kiépíteni. A következő függvény az egyes eszközökön végrehajtandó kiépítési munkafolyamatot jelöli:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Ez a Orchestrator függvény használható az egyszeri eszköz kiosztásához, vagy egy nagyobb előkészítés része lehet. Az utóbbi esetben a szülő Orchestrator függvény `DeviceProvisioningOrchestration` a `CallSubOrchestratorAsync` (.net) vagy a `callSubOrchestrator` (JavaScript) API használatával is ütemezhet példányokat.

Íme egy példa, amely bemutatja, hogyan futtathat párhuzamosan több Orchestrator-függvényt.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Az előző C#-példák a Durable Functions 2. x verzióra vonatkoznak. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Az alrendszereket a szülő-összehangolás során megegyező Function alkalmazásban kell meghatározni. Ha egy másik Function-alkalmazásban kell meghívnia és várnia a meghívást, érdemes lehet használni a HTTP API-k beépített támogatását és a HTTP 202 lekérdezési fogyasztói mintát. További információkért lásd a http- [funkciók](durable-functions-http-features.md) témakört.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan állíthat be egyéni előkészítési állapotot](durable-functions-custom-orchestration-status.md)
