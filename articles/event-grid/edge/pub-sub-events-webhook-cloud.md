---
title: Közzététel, előfizetés a Felhőbeli eseményekre – Azure Event Grid IoT Edge | Microsoft Docs
description: Közzététel, előfizetés a Felhőbeli eseményekre webhook használatával Event Grid on IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: ec7ecb77d37ed1cdf1d13aa7191f5d50e0008c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790799"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Oktatóanyag: közzététel, előfizetés a Felhőbeli eseményekre

Ez a cikk végigvezeti az eseményeknek a Event Grid on IoT Edge használatával történő közzétételéhez és előfizetéséhez szükséges lépéseken. Ez az oktatóanyag az Azure-függvényt használja eseménykezelőként. További célhelyek: [eseménykezelők](event-handlers.md).

A folytatás előtt tekintse meg az [Event Grid fogalmakat](concepts.md) , amelyekből megtudhatja, mi az Event Grid-témakör és az előfizetés.

## <a name="prerequisites"></a>Előfeltételek 
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) , ha még nem rendelkezik ilyennel. 
* **Azure IoT hub és IoT Edge eszköz** – kövesse a [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows rendszerű eszközök](../../iot-edge/quickstart.md) gyors üzembe helyezésének lépéseit, ha még nem rendelkezik ilyennel.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Azure-függvény létrehozása a Azure Portalban

Az [oktatóanyagban](../../azure-functions/functions-get-started.md) ismertetett lépéseket követve hozzon létre egy Azure-függvényt. 

Cserélje le a kódrészletet a következő kódra:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Az új függvényben kattintson a jobb felső sarokban található **függvény URL-címének beolvasása** lehetőségre, válassza az alapértelmezett (**Function Key**) lehetőséget, majd válassza a **Másolás** lehetőséget. Az oktatóanyagban később a függvény URL-címének értékét fogja használni.

> [!NOTE]
> Tekintse meg az [Azure functions](../../azure-functions/functions-overview.md) dokumentációját, amely további példákat és oktatóanyagokat tartalmaz a EventGrid-eseményindítók használatával végzett eseményekre való reagáláshoz.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

Egy esemény kiadójaként létre kell hoznia egy Event Grid-témakört. A témakör egy olyan végpontra hivatkozik, ahol a közzétevők eseményeket küldhetnek a alkalmazásba.

1. Hozzon létre topic2.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API-dokumentációt](api.md) .

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Futtassa a következő parancsot a témakör létrehozásához. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. A következő parancs futtatásával ellenőrizheti, hogy a témakör sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Esemény-előfizetés létrehozása

Az előfizetők regisztrálhatnak a témakörben közzétett eseményekre. Ha bármilyen eseményt szeretne kapni, az előfizetőknek létre kell hozniuk egy Event Grid-előfizetést egy érdekes témakörben.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription2.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API dokumentációját](api.md) .

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > A **endpointType** azt adja meg, hogy az előfizető webhook.  A **endpointUrl** meghatározza azt az URL-címet, amelyen az előfizető eseményeket figyel. Ez az URL-cím megfelel a korábban beállított Azure-függvény mintájának.
2. A következő parancs futtatásával hozza létre az előfizetést. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. A következő parancs futtatásával ellenőrizheti, hogy az előfizetés sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Esemény közzététele

1. Hozzon létre event2.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API dokumentációját](api.md) .

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Az esemény közzétételéhez futtassa a következő parancsot

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Esemény-kézbesítés ellenőrzése

A Azure Portal a függvény **figyelése** lehetőség alatt megtekintheti a leszállított eseményt.

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* Futtassa a következő parancsot a témakör és az összes előfizetésének törléséhez

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Törölje a Azure Portal létrehozott Azure-függvényt.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Event Grid-témakört, előfizetést és közzétett eseményeket. Most, hogy már ismeri az alapszintű lépéseket, tekintse meg a következő cikkeket:

* A IoT Edge Azure Event Grid használatával kapcsolatos problémák elhárításához tekintse meg a [hibaelhárítási útmutatót](troubleshoot.md).
* Előfizetés létrehozása/frissítése [szűrőkkel](advanced-filtering.md).
* Event Grid modul megőrzésének beállítása [Linux](persist-state-linux.md) vagy [Windows](persist-state-windows.md) rendszeren
* Az ügyfél-hitelesítés konfigurálásához kövesse a [dokumentációt](configure-client-auth.md)
* Események továbbítása Azure Event Grid a felhőben az [oktatóanyag](forward-events-event-grid-cloud.md) követésével
* [Témakörök és előfizetések figyelése a peremhálózat szélén](monitor-topics-subscriptions.md)