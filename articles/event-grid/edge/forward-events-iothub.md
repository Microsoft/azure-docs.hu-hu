---
title: Event Grid események továbbítása a IoTHub-Azure Event Grid IoT Edgehoz | Microsoft Docs
description: Event Grid események továbbítása a IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171550"
---
# <a name="tutorial-forward-events-to-iothub"></a>Oktatóanyag: események továbbítása a IoTHub

Ez a cikk végigvezeti az Event Grid események más IoT Edge modulokra való továbbításához szükséges összes lépésen, IoTHub útvonalak használatával. A következő okok miatt lehet szükség:

* Továbbra is használhatja a már meglévő, a edgeHub útválasztással rendelkező befektetéseit
* Az összes eseményt csak IoT Hub használatával irányíthatja át az eszközről

Az oktatóanyag elvégzéséhez ismernie kell a következő fogalmakat:

- [Event Grid fogalmak](concepts.md)
- [IoT Edge hub](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Előfeltételek 
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) , ha még nem rendelkezik ilyennel. 
* **Azure IoT hub és IoT Edge eszköz** – kövesse a [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows rendszerű eszközök](../../iot-edge/quickstart.md) gyors üzembe helyezésének lépéseit, ha még nem rendelkezik ilyennel.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Témakör létrehozása

Egy esemény kiadójaként létre kell hoznia egy Event Grid-témakört. A témakör egy olyan végpontra hivatkozik, ahol a közzétevők küldhetnek eseményeket.

1. Hozzon létre topic4.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API-dokumentációt](api.md) .

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Futtassa a következő parancsot a témakör létrehozásához. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. A következő parancs futtatásával ellenőrizheti, hogy a témakör sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Esemény-előfizetés létrehozása

Az előfizetők regisztrálhatnak a témakörben közzétett eseményekre. Ha bármilyen eseményt szeretne kapni, létre kell hoznia egy Event Grid-előfizetést egy érdekes témakörben.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription4.jsaz alábbi tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API dokumentációját](api.md) .

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > A `endpointType` azt adja meg, hogy az előfizető `edgeHub` . A `outputName` meghatározza azt a kimenetet, amelyen a Event Grid modul továbbítja az ehhez az előfizetéshez tartozó eseményeket a edgeHub. A fenti előfizetésnek megfelelő események például a következőre lesznek írva: `/messages/modules/eventgridmodule/outputs/sampleSub4` .
2. A következő parancs futtatásával hozza létre az előfizetést. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. A következő parancs futtatásával ellenőrizheti, hogy az előfizetés sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Az Edge hub útvonalának beállítása

Frissítse az Edge hub útvonalát az esemény-előfizetés eseményeinek továbbításához a IoTHub a következőképpen:

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com)
1. Navigáljon a **IoT hub**.
1. **IoT Edge** kiválasztása a menüből
1. Válassza ki a cél eszköz AZONOSÍTÓját az eszközök listájából.
1. Válassza a **modulok beállítása** lehetőséget.
1. Válassza a **Next (tovább** ) és az útvonalak szakaszt.
1. Az útvonalakban adjon hozzá egy új útvonalat

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Példa:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > A fenti útvonal továbbítja az előfizetéshez tartozó összes eseményt az IoT hub felé. Az [Edge hub útválasztási](../../iot-edge/module-composition.md) funkciói segítségével további szűrést végezhet, és átirányíthatja a Event Grid eseményeket más IoT Edge modulokhoz.

## <a name="setup-iot-hub-route"></a>IoT Hub útvonal beállítása

Tekintse meg a [IoT hub útválasztási oktatóanyagot](../../iot-hub/tutorial-routing.md) az IoT hub útvonalának beállításához, így megtekintheti a Event Grid modulból továbbított eseményeket. A `true` lekérdezés használatával egyszerűen megtarthatja az oktatóanyagot.  



## <a name="publish-an-event"></a>Esemény közzététele

1. Hozzon létre event4.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API-dokumentációt](api.md) .

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Az esemény közzétételéhez futtassa a következő parancsot:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Esemény-kézbesítés ellenőrzése

Az események megtekintéséhez szükséges lépéseket a IoT Hub [útválasztási oktatóanyagban](../../iot-hub/tutorial-routing.md) találhatja meg.

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* Futtassa a következő parancsot a témakör és az összes előfizetésének törléséhez az Edge-ben:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Törölje az IoTHub-útválasztás felhőben való beállítása során létrehozott erőforrásokat is.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Event Grid-témakört, az Edge hub-előfizetést és a közzétett eseményeket. Most, hogy már ismeri az egy peremhálózati hubhoz való továbbítás alapvető lépéseit, tekintse meg a következő cikkeket:

* A IoT Edge Azure Event Grid használatával kapcsolatos problémák elhárításához tekintse meg a [hibaelhárítási útmutatót](troubleshoot.md).
* Az [Edge hub](../../iot-edge/module-composition.md) Route Filters használata az események particionálásához
* Event Grid modul megőrzésének beállítása [Linux](persist-state-linux.md) vagy [Windows](persist-state-windows.md) rendszeren
* Az ügyfél-hitelesítés konfigurálásához kövesse a [dokumentációt](configure-client-auth.md)
* Események továbbítása Azure Event Grid a felhőben az [oktatóanyag](forward-events-event-grid-cloud.md) követésével
* [Témakörök és előfizetések figyelése a peremhálózat szélén](monitor-topics-subscriptions.md)