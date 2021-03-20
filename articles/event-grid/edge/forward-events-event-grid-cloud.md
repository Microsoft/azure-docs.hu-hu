---
title: Peremhálózati események továbbítása Event Grid Cloud-Azure Event Grid IoT Edge | Microsoft Docs
description: Peremhálózati események továbbítása Event Grid felhőbe
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171601"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Oktatóanyag: események továbbítása Event Grid felhőbe

Ez a cikk végigvezeti az Azure-felhőben Event Grid peremhálózati események továbbításához szükséges lépéseken. A következő okok miatt lehet szükség:

* Reagáljon a Felhőbeli peremhálózati eseményekre.
* Továbbítsa az eseményeket a felhőben Event Gridre, és az Azure Event Hubs vagy az Azure Storage-várólistákat használja az események puffereléséhez a felhőben való feldolgozás előtt.

 Az oktatóanyag elvégzéséhez ismernie kell Event Grid fogalmakat a [Edge](concepts.md) -ben és az [Azure](../concepts.md)-ban. További célhelyek: [eseménykezelők](event-handlers.md). 

## <a name="prerequisites"></a>Előfeltételek 
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) , ha még nem rendelkezik ilyennel. 
* **Azure IoT hub és IoT Edge eszköz** – kövesse a [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows rendszerű eszközök](../../iot-edge/quickstart.md) gyors üzembe helyezésének lépéseit, ha még nem rendelkezik ilyennel.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Event Grid-témakör és-előfizetés létrehozása a felhőben

Az [oktatóanyag](../custom-event-quickstart-portal.md)követésével hozzon létre egy Event Grid-témakört és-előfizetést a felhőben. Jegyezze fel `topicURL` , `sasKey` és az `topicName` újonnan létrehozott témakört, amelyet később az oktatóanyagban fog használni.

Ha például létrehozta az `testegcloudtopic` USA nyugati régiójában elnevezett témakört, az értékek a következőképpen néznek ki:

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**: `testegcloudtopic`
* **SasKey**: a témakör **AccessKey** alatt érhető el. **Key1** használata.

## <a name="create-event-grid-topic-at-the-edge"></a>Event Grid-témakör létrehozása a peremhálózat szélén

1. Hozzon létre topic3.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API-dokumentációt](api.md) .

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Futtassa a következő parancsot a témakör létrehozásához. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. A következő parancs futtatásával ellenőrizheti, hogy a témakör sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Event Grid-előfizetés létrehozása a peremhálózat szélén

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription3.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API-dokumentációt](api.md) .

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > A **endpointUrl** megadja, hogy a Event Grid témakör URL-címe a felhőben. A **sasKey** Event Grid Felhőbeli témakör kulcsára hivatkozik. A rendszer az **topicName** -ben lévő értéket fogja használni az összes kimenő esemény Event Grid. Ez akkor lehet hasznos, ha Event Grid tartományi témakörbe való feladást végez. További információ a Event Grid tartományi témakörről: [esemény-tartományok](../event-domains.md)

    Példa:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. A következő parancs futtatásával hozza létre az előfizetést. Az 200-es HTTP-állapotkódot vissza kell adni.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. A következő parancs futtatásával ellenőrizheti, hogy az előfizetés sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Esemény közzététele az Edge-ben

1. Hozzon létre event3.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért lásd az [API dokumentációját](api.md) .

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Futtassa az alábbi parancsot:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Edge-esemény ellenőrzése a felhőben

A Felhőbeli témakör által megjelenített események megtekintésével kapcsolatos információkért tekintse meg az [oktatóanyagot](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* Futtassa a következő parancsot a témakör és az összes előfizetésének törléséhez

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* A felhőben (Azure Event Grid) létrehozott témaköröket és előfizetéseket is törölheti.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy eseményt tett közzé az Edge-ben, és továbbítottuk az Azure-felhőben lévő Event Gridra. Most, hogy ismeri a felhőben való Event Gridre való továbbítás alapvető lépéseit:

* A IoT Edge Azure Event Grid használatával kapcsolatos problémák elhárításához tekintse meg a [hibaelhárítási útmutatót](troubleshoot.md).
* Események továbbítása a IoTHub az [oktatóanyag](forward-events-iothub.md) követésével
* Események továbbítása a webhookba a felhőben az [oktatóanyag](pub-sub-events-webhook-cloud.md) követésével
* [Témakörök és előfizetések figyelése a peremhálózat szélén](monitor-topics-subscriptions.md)
