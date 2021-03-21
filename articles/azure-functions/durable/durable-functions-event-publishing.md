---
title: Durable Functions Publishing to Azure Event Grid (előzetes verzió)
description: Ismerje meg, hogyan konfigurálhatja a Durable Functions automatikus Azure Event Grid közzétételét.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175957"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions Publishing to Azure Event Grid (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan állíthatja be a Durable Functionst a munkafolyamatok életciklusával kapcsolatos események (például a létrehozás, a Befejezés és a sikertelen) közzétételéhez egy egyéni [Azure Event Grid témakörben](../../event-grid/overview.md).

Az alábbiakban néhány olyan forgatókönyvet ismertetünk, amelyekben ez a funkció hasznos:

* **DevOps-forgatókönyvek, például kék/zöld környezetek**: érdemes tudnia, hogy a [párhuzamos üzembe helyezési stratégia](durable-functions-versioning.md#side-by-side-deployments)megvalósítása előtt futnak-e a feladatok.

* **Speciális monitorozási és diagnosztikai támogatás**: a munkafolyamatok állapotával kapcsolatos információkat a lekérdezésekhez optimalizált külső tárolóban, például Azure SQL Database vagy Azure Cosmos db is nyomon követheti.

* **Hosszan futó háttérbeli tevékenység**: ha a Durable Functionst hosszan futó háttérbeli tevékenységhez használja, ez a funkció segít megismerni az aktuális állapotot.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) programot a Durable functions-projektbe.
* Telepítse az [Azure Storage emulatort](../../storage/common/storage-use-emulator.md) (csak Windows), vagy használjon egy meglévő Azure Storage-fiókot.
* Az [Azure CLI](/cli/azure/) telepítése vagy a [Azure Cloud Shell](../../cloud-shell/overview.md) használata

## <a name="create-a-custom-event-grid-topic"></a>Egyéni Event Grid-témakör létrehozása

Hozzon létre egy Event Grid témakört az események Durable Functionsból való küldéséhez. Az alábbi utasítások bemutatják, hogyan hozhat létre egy témakört az Azure CLI használatával. A témakört a [PowerShell](../../event-grid/custom-event-quickstart-powershell.md) vagy [a Azure Portal](../../event-grid/custom-event-quickstart-portal.md)használatával is létrehozhatja.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. A Azure Event Grid jelenleg nem támogatja az összes régiót. További információ a támogatott régiókról: [Azure Event Grid Overview (áttekintés](../../event-grid/overview.md)).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Egy Event Grid témakör egy felhasználó által definiált végpontot biztosít, amelyre az eseményt közzéteszi. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert DNS-bejegyzés lesz.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>A végpont és a kulcs lekérése

A témakör végpontjának beolvasása. Cserélje le `<topic_name>` a nevet a kiválasztott névre.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

A témakör kulcsának beolvasása. Cserélje le `<topic_name>` a nevet a kiválasztott névre.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Most már küldhet eseményeket a témakörbe.

## <a name="configure-event-grid-publishing"></a>Event Grid közzétételének konfigurálása

A Durable Functions-projektben keresse meg a `host.json` fájlt.

### <a name="durable-functions-1x"></a>Durable Functions 1. x

Adjon hozzá `eventGridTopicEndpoint` és `eventGridKeySettingName` egy `durableTask` tulajdonságot.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2. x

Adjon hozzá egy `notifications` szakaszt a `durableTask` fájl tulajdonságához, és cserélje `<topic_name>` le a kifejezést a kiválasztott névre. Ha a `durableTask` vagy a `extensions` tulajdonság nem létezik, hozza létre őket a következő példához hasonlóan:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

A lehetséges Azure Event Grid konfigurációs tulajdonságok a [host.jsdokumentációjában](../functions-host-json.md#durabletask)találhatók. A fájl konfigurálása után `host.json` a Function alkalmazás életciklus-eseményeket küld a Event Grid témakörnek. Ez a művelet akkor indul el, ha a Function alkalmazást helyileg és az Azure-ban futtatja.

Adja meg a témakörben a függvényalkalmazás és a témakörhöz tartozó Alkalmazásbeállítások beállításait `local.settings.json` . A következő JSON egy minta a `local.settings.json` helyi hibakereséshez. Cserélje le `<topic_key>` a billentyűt a témakörre.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Ha a [Storage emulatort](../../storage/common/storage-use-emulator.md) használja (csak Windows), ellenőrizze, hogy működik-e. A `AzureStorageEmulator.exe clear all` parancs végrehajtása előtt érdemes futtatni a parancsot.

Ha meglévő Azure Storage-fiókot használ, cserélje le a `UseDevelopmentStorage=true` - `local.settings.json` t a kapcsolati karakterláncára.

## <a name="create-functions-that-listen-for-events"></a>Eseményeket figyelő függvények létrehozása

A Azure Portal használatával hozzon létre egy másik Function alkalmazást a Durable Functions alkalmazás által közzétett események figyelésére. A legjobb megoldás, ha ugyanabban a régióban található, mint a Event Grid témakör.

### <a name="create-an-event-grid-trigger-function"></a>Event Grid trigger függvény létrehozása

1. A Function alkalmazásban válassza a **függvények**, majd a **+ Hozzáadás** lehetőséget. 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Adjon hozzá egy függvényt a Azure Portal." border="true":::

1. Keresse meg **Event Grid**, majd válassza ki a **Azure Event Grid trigger** sablont. 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Válassza ki az Event Grid eseményindító sablont a Azure Portal." border="true":::

1. Nevezze el az új triggert, majd válassza a **create Function (függvény létrehozása**) lehetőséget.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Nevezze el az Event Grid eseményindítót a Azure Portal." border="true":::


    A rendszer létrehoz egy függvényt a következő kóddal:

    # <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

    ```csharp
    #r "Newtonsoft.Json"
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(JObject eventGridEvent, ILogger log)
    {
        log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
    }
    ```

   # <a name="javascript"></a>[JavaScript](#tab/javascript)

   ```javascript
   module.exports = async function(context, eventGridEvent) {
       context.log(typeof eventGridEvent);
       context.log(eventGridEvent);
   }
   ```

---

### <a name="add-an-event-grid-subscription"></a>Event Grid előfizetés hozzáadása

Mostantól hozzáadhat egy Event Grid-előfizetést a létrehozott Event Grid témakörhöz. További információkért tekintse [meg a Azure Event Grid fogalmak](../../event-grid/concepts.md)című témakört.

1. Az új függvényben válassza az **integráció** , majd a **Event Grid trigger (eventGridEvent)** lehetőséget. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Válassza ki a Event Grid trigger hivatkozást." border="true":::

1. Válassza a **létrehozás Event Grid Leírás** lehetőséget.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Hozza létre a Event Grid-előfizetést." border="true":::

1. Nevezze el az esemény-előfizetést, és válassza ki a **Event Grid témakörök** típust. 

1. Válassza ki az előfizetést. Ezután válassza ki az Event Grid témakörhöz létrehozott erőforráscsoportot és erőforrást. 

1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Event Grid-előfizetés létrehozása." border="true":::

Most már készen áll az életciklus-események fogadására.

## <a name="run-durable-functions-app-to-send-the-events"></a>Durable Functions alkalmazás futtatása az események elküldéséhez

A korábban konfigurált Durable Functions-projektben indítsa el a hibakeresést a helyi gépen, és indítson el egy előkészítést. Az alkalmazás Durable Functions életciklus-eseményeket tesz közzé a Event Grid. Ellenőrizze, hogy a Event Grid elindítja-e az Ön által létrehozott figyelő-függvényt, ha ellenőrzi a naplókat a Azure Portalban.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esemény sémája

Az alábbi lista az életciklus-események sémáját mutatja be:

* **`id`**: A Event Grid esemény egyedi azonosítója.
* **`subject`**: Az esemény tárgyának elérési útja. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` Ekkor:,, `Running` `Completed` `Failed` és `Terminated` .  
* **`data`**: Durable Functions megadott paraméterek.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) neve.
  * **`functionName`**: A Orchestrator függvény neve.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: A nyomkövetési eseményhez kapcsolódó további információk. További információ: [diagnosztika a Durable functions (Azure functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: A hangolási futtatókörnyezet állapota. Fut, befejezett, sikertelen, megszakítva.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Esemény időpontja (UTC).
* **`dataVersion`**: Az életciklus-esemény sémájának verziója.
* **`metadataVersion`**: A metaadatok verziója.
* **`topic`**: Event Grid-témakör erőforrása.

## <a name="how-to-test-locally"></a>Helyi tesztelés

A helyi teszteléshez olvassa el az [Azure Function Event Grid a helyi hibakeresés elindítása](../functions-debug-event-grid-trigger-local.md)című cikkét.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A példányok kezelésének megismerése Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [A verziószámozás megismerése Durable Functions](durable-functions-versioning.md)
