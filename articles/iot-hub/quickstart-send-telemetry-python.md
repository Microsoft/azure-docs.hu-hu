---
title: Rövid útmutató – Telemetria küldése Azure IoT Hub (Python) | Microsoft Docs
description: Ebben a rövid útmutatóban egy Python-mintaalkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez, és egy segédprogrammal telemetriát olvas az IoT Hubról.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/16/2020
ms.openlocfilehash: 18163c3e5cdf83d736cdb52a3de028bf8890e728
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568573"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra, és annak olvasása háttéralkalmazással (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Ebben a rövid útmutatóban telemetriát fog küldeni egy szimulálteszköz-alkalmazásból egy Azure IoT Hub egy háttéralkalmazásba feldolgozásra. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató két előre megírt Python-alkalmazást használ: egyet a telemetria elküldéhez, egyet pedig a telemetria olvas a központból. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.7+](https://www.python.org/downloads/). További támogatott Python-verziókért lásd: [Azure IoT Device Features (Azure IoT-eszköz funkciói).](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)

* [Python-mintaprojekt a](https://github.com/Azure-Samples/azure-iot-samples-python/) GitHubról. Töltse le vagy klónozza  a mintákat a GitHub-adattár Kód gombjával.

* A 8883-as port nyitva van a tűzfalon. A rövid útmutatóban található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetekben ez a port le van tiltva. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot a Azure Cloud Shell az eszközidentitás létrehozásához.

    **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    **MyPythonDevice:** Ez a regisztrált eszköz neve. Ajánlott a **MyPythonDevice-et használni** az itt látható módon. Ha másik nevet választ az eszközhöz, akkor a cikk során is ezt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell az eszköz nevét.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Futtassa a következő parancsot a Azure Cloud Shell a regisztrált eszköz _eszközkapcsolati_ sztringjéhez:

    **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket a rövid útmutató későbbi, későbbi, későbbiekben fogja használni.

1. Szüksége lesz a _Event Hubs-kompatibilis_ végpontra, _Event Hubs-kompatibilis_ elérési útra és  szolgáltatás elsődleges kulcsára az IoT Hubról, hogy a háttéralkalmazás csatlakozik az IoT Hubhoz, és lekérje az üzeneteket. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Jegyezze fel ezt a három értéket, amelyet a rövid útmutató későbbi része fog használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Töltse le vagy klónozza az azure-iot-samples-python adattárat az [azure-iot-samples-python adattár oldalán](https://github.com/Azure-Samples/azure-iot-samples-python/)található Kód gombbal. 

1. Egy helyi terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.py** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a változó `CONNECTION_STRING` értékét arra az eszközkapcsolati sztringre, amelyről korábban feljegy tette. Ezután mentse a módosításokat a **SimulatedDevice.py.**

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

A háttéralkalmazás a szolgáltatásoldali **Események** végponthoz csatlakozik az IoT Hubon. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

> [!NOTE]
> A következő lépések a **read_device_to_cloud_messages_sync.py** szinkron mintát használják. Ugyanezeket a lépéseket a következő aszinkron mintával is végrehajthatja: **read_device_to_cloud_messages_async.py**.

1. Egy másik helyi terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\read-d2c-messages** mappába.

2. Nyissa meg **read_device_to_cloud_messages_sync.py fájlt** egy Ön által választott szövegszerkesztőben. Frissítse a következő változókat, és mentse a fájlon végrehajtott módosításait.

    | Változó | Érték |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Cserélje le a változó értékét a Event Hubs-kompatibilis végpontra, amit korábban feljegy tett. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Cserélje le a változó értékét a Event Hubs az elérési útra, amit korábban feljegy tett. |
    | `IOTHUB_SAS_KEY`                | Cserélje le a változó értékét a szolgáltatás korábban lekért elsődleges kulcsára. |

3. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![A háttéralkalmazás futtatása](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a hubra egy Python-alkalmazással, és beolvasta a telemetriát a hubról egy egyszerű háttéralkalmazással.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-python.md)
