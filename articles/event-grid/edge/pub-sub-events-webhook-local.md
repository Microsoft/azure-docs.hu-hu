---
title: Közzététel, előfizethet az eseményekre helyileg – Azure Event Grid IoT Edge | Microsoft Docs
description: Közzététel, előfizethet az eseményekre helyileg a webhook Event Grid használatával IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2a7cc864366bd9a35c96dd453c0dc68f77d8abd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171448"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Oktatóanyag: közzététel, előfizetés helyi eseményekre

Ez a cikk végigvezeti az eseményeknek a Event Grid on IoT Edge használatával történő közzétételéhez és előfizetéséhez szükséges lépéseken.

> [!NOTE]
> A Azure Event Grid témakörökről és előfizetésekről a [Event Grid fogalmak](concepts.md)című témakörben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek 
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) , ha még nem rendelkezik ilyennel. 
* **Azure IoT hub és IoT Edge eszköz** – kövesse a [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows rendszerű eszközök](../../iot-edge/quickstart.md) gyors üzembe helyezésének lépéseit, ha még nem rendelkezik ilyennel.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge modul üzembe helyezése

A modulokat többféleképpen is telepítheti egy IoT Edge eszközre, és mindegyik a IoT Edge Azure Event Grid. Ez a cikk azokat a lépéseket ismerteti, amelyekkel telepítheti a Event Gridt a Azure Portal IoT Edge.

>[!NOTE]
> Ebben az oktatóanyagban a Event Grid-modult az adatmegőrzés nélkül fogja telepíteni. Ez azt jelenti, hogy a jelen oktatóanyagban létrehozott összes témakört és előfizetést a modul újbóli üzembe helyezésekor törli a rendszer. Az adatmegőrzés beállításával kapcsolatos további információkért tekintse meg a következő cikkeket: állapot megtartása [Linuxon](persist-state-linux.md) vagy [az állapot megőrzése a Windowsban](persist-state-windows.md). Éles számítási feladatokhoz javasoljuk, hogy az Event Grid modult az adatmegőrzéssel telepítse.


### <a name="select-your-iot-edge-device"></a>IoT Edge eszköz kiválasztása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Navigáljon a IoT Hub.
1. Válassza a **IoT Edge** lehetőséget az **automatikus eszközkezelés** szakasz menüjében. 
1. Kattintson a céleszköz AZONOSÍTÓjának az eszközök listájáról
1. Válassza a **modulok beállítása** lehetőséget. Tartsa meg a lapot. A következő szakaszban ismertetett lépésekkel folytathatja a lépéseket.

### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. A Azure Portal tartalmaz egy varázslót, amely végigvezeti az üzembe helyezési jegyzék létrehozásán, a JSON-dokumentum manuális létrehozása helyett.  Három lépésből áll: **modulok hozzáadása**, **útvonalak megadása** és az **üzembe helyezés áttekintése**.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **telepítési modulok** szakaszban válassza a **Hozzáadás** lehetőséget.
1. A legördülő listában válassza ki a modulok típusait **IoT Edge modult**
1. Adja meg a tároló nevét, képét, tároló-létrehozási beállításait:

   * **Név**: eventgridmodule
   * **Rendszerkép URI-ja**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Tároló-létrehozási beállítások**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Kattintson a **Mentés** gombra.
 1. Folytassa a következő szakasszal, és vegye fel a Azure Event Grid előfizető modult, mielőtt együtt telepítené őket.

    >[!IMPORTANT]
    > Ebben az oktatóanyagban a Event Grid-modult az ügyfél-hitelesítés letiltásával fogja telepíteni. Az éles számítási feladatokhoz javasoljuk, hogy engedélyezze az ügyfél-hitelesítést. A Event Grid modul biztonságos konfigurálásával kapcsolatos további információkért lásd: [Biztonság és hitelesítés](security-authentication.md).
    > 
    > Ha Azure-beli virtuális gépet használ peremhálózati eszközként, vegyen fel egy bejövő portszabály, hogy engedélyezze a bejövő forgalmat a 4438-es porton. A szabály hozzáadásával kapcsolatos útmutatásért lásd: [portok megnyitása virtuális géphez](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid előfizető IoT Edge modul üzembe helyezése

Ebből a szakaszból megtudhatja, hogyan helyezhet üzembe egy másik IoT modult, amely az események kézbesítéséhez használható eseménykezelőként fog működni.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **telepítési modulok** szakaszban válassza a **Hozzáadás** újra lehetőséget. 
1. A legördülő listában válassza ki a modulok típusait **IoT Edge modult**
1. Adja meg a tároló nevét, képét és tároló-létrehozási beállításait:

   * **Név**: előfizető
   * **Rendszerkép URI-ja**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Tároló-létrehozási beállítások**: nincs
1. Kattintson a **Mentés** gombra.
1. Az útvonalak szakasz folytatásához kattintson a **tovább** gombra.

 ### <a name="setup-routes"></a>Telepítési útvonalak

Tartsa meg az alapértelmezett útvonalakat, és kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

### <a name="submit-the-deployment-request"></a>A központi telepítési kérelem elküldése

1. A felülvizsgálati szakasz megjeleníti a JSON-telepítési jegyzékfájlt, amelyet az előző szakaszban megadott beállítások alapján hoztak létre. Győződjön meg arról, hogy a következő modulok szerepelnek a JSON-ban: **eventgridmodule** és **előfizető** . 
1. Tekintse át az üzembe helyezési adatokat, majd válassza a **Küldés** lehetőséget. A telepítés elküldése után visszatérhet az **eszköz** oldalára.
1. A **modulok szakaszban** ellenőrizze, hogy a **eventgrid** és az **előfizetői** modulok is szerepelnek-e a listáján. És ellenőrizze, hogy a **telepítésben megadott** és az **eszköz oszlopai által jelentett** oszlopok értéke **Igen**.

    Néhány percet is igénybe vehet, amíg a modul elindult az eszközön, majd visszaküldhető a IoT Hubra. Frissítse az oldalt, és tekintse meg a frissített állapotot.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

Egy esemény kiadójaként létre kell hoznia egy Event Grid-témakört. Azure Event Grid a témakör egy olyan végpontra hivatkozik, ahol a közzétevők eseményeket küldhetnek a alkalmazásba.

1. Hozzon létre topic.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API dokumentációját](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. A következő parancs futtatásával hozzon létre egy Event Grid-témakört. Ellenőrizze, hogy megjelenik-e a HTTP-állapotkód `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. A következő parancs futtatásával ellenőrizheti, hogy a témakör sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Esemény-előfizetés létrehozása

Az előfizetők regisztrálhatnak a témakörben közzétett eseményekre. Ha bármilyen eseményt szeretne kapni, létre kell hoznia egy Event Grid-előfizetést egy érdekes témakörhöz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API-dokumentációt](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > A **endpointType** tulajdonság azt adja meg, hogy az előfizető egy **webhook**.  A **endpointUrl** meghatározza azt az URL-címet, amelyen az előfizető eseményeket figyel. Ez az URL-cím megfelel a korábban üzembe helyezett Azure-előfizetői mintának.
2. A következő parancs futtatásával hozzon létre egy előfizetést a témakörhöz. Ellenőrizze, hogy megjelenik-e a HTTP-állapotkód `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. A következő parancs futtatásával ellenőrizheti, hogy az előfizetés sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Esemény közzététele

1. Hozzon létre event.jsa következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API dokumentációját](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Egy esemény közzétételéhez futtassa a következő parancsot.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Esemény-kézbesítés ellenőrzése

1. SSH vagy RDP a IoT Edge virtuális gépre.
1. Az előfizetői naplók keresése:

    Windows rendszeren futtassa a következő parancsot:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Linux rendszeren futtassa a következő parancsot:

    ```sh
    sudo docker logs subscriber
    ```

    Példa a kimenetre:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* Futtassa a következő parancsot a témakör és az összes előfizetésének törléséhez.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Törölje az előfizetői modult a IoT Edge eszközről.


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy Event Grid-témakört, előfizetést és közzétett eseményeket. Most, hogy már ismeri az alapszintű lépéseket, tekintse meg a következő cikkeket: 

- A IoT Edge Azure Event Grid használatával kapcsolatos problémák elhárításához tekintse meg a [hibaelhárítási útmutatót](troubleshoot.md).
- Előfizetés létrehozása/frissítése [szűrőkkel](advanced-filtering.md).
- Event Grid modul megőrzésének engedélyezése [Linux](persist-state-linux.md) vagy [Windows](persist-state-windows.md) rendszeren
- Az ügyfél-hitelesítés konfigurálásához kövesse a [dokumentációt](configure-client-auth.md)
- Események továbbítása Azure Functions a felhőben az [oktatóanyag](pub-sub-events-webhook-cloud.md) követésével
- [Reagálás Blob Storage eseményekre IoT Edge](react-blob-storage-events-locally.md)
- [Témakörök és előfizetések figyelése a peremhálózat szélén](monitor-topics-subscriptions.md)

