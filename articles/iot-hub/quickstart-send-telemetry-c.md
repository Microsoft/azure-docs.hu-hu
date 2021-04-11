---
title: Gyors útmutató – telemetria küldése az Azure-IoT Hub rövid útmutató (C) | Microsoft Docs
description: Ebben a rövid útmutatóban két C-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról a felhőben történő feldolgozás érdekében.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 6cc20a362ceebe56beb0d2baa48ea479d3b322a7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067639"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Gyors útmutató: telemetria küldése egy eszközről egy IoT-hubhoz, és olvasása háttérbeli alkalmazással (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

A rövid útmutató a [C nyelvhez készült Azure IoT eszközoldali SDK](iot-hub-device-sdk-c-intro.md) egy C-mintaalkalmazását használja telemetriai adatoknak az IoT hubra küldésére. Az Azure IoT eszközoldali SDK-t [ANSI C-ben (C99)](https://wikipedia.org/wiki/C99) írták a hordozhatóságot és a széles körű platformokon történő használhatóságot szem előtt tartva. A mintakód futtatása előtt létrehozza az IoT hubot és regisztrálja rajta a szimulált eszközt.

Ez a cikk a Windowshoz készült, de a gyors útmutató a Linuxon is elvégezhető.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/vs/) -es verzióját az ["asztali fejlesztés C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladattal.

* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a [C Azure IoT Device SDK](iot-hub-device-sdk-c-intro.md)-t fogja használni. 

A következő környezetekben az SDK-t használhatja a csomagok és könyvtárak telepítésével:

* **Linux**: az apt-get csomagok Ubuntu 16,04-es és 18,04-as csomaggal érhetők el az alábbi CPU-architektúrák használatával: amd64, arm64, armhf és i386. További információkért lásd: [Az apt-get használata C eszköz ügyfél-projekt létrehozására az Ubuntu rendszeren](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: ahhoz, hogy a fejlesztők mbed platformon alkalmazásokat hozzanak létre, közzétettünk egy könyvtárat és mintákat, amelyekkel percek alatt elsajátíthatja az Azure IoT hub. További információk: [Az mbed könyvtár használata](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Ha Arduino-fejlesztést végez, kihasználhatja az Arduino IDE Library Managerben elérhető Azure IoT könyvtárat. Bővebb információk: [Az Azure IoT Hub könyvtár – Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Mac és iOS rendszerű eszközfejlesztéshez az IoT Hub eszközoldali SDK mint CocoaPods érhető el. További információ: [iOS példák Microsoft Azure IoT számára](https://cocoapods.org/pods/AzureIoTHubClient).

Ebben a rövid útmutatóban azonban elő fog készíteni egy fejlesztési környezetet, amellyel klónozott és felépíthető az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a githubról. A rövid útmutató mintakódokat az SDK tartalmazza a GitHubon.

1. Töltse le a [Csatlakozáskezelő felügyeleti csomag Build-szolgáltatását](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásához](https://github.com/Azure/azure-iot-sdk-c/releases/latest) tartozó címke nevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben megtalált címkét a paraméter értékeként `-b` :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a következő parancsokat a `azure-iot-sdk-c` címtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa a következő parancsot, hogy az SDK egy olyan verzióját hozza létre, amely a fejlesztői ügyféloldali platformra jellemző. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre.

    ```cmd
    cmake ..
    ```

    Ha `cmake` nem találja a C++ fordítót, a fenti parancs futtatásakor hibákba ütközhet. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a Azure Cloud Shell és a [IoT bővítmény](/cli/azure/ext/azure-iot/iot) használatával regisztrálhat egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyCDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyCDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és sztringet küld szimulált telemetriaként.

1. Szövegszerkesztővel nyissa meg az iothub_convenience_sample.c forrásfájlt, és tekintse át a telemetriai adatok küldését végző mintakódot. A fájl a következő helyen található a munkakönyvtárban, ahol klónozotta az Azure IoT C SDK-t:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Keresse meg a `connectionString` konstans deklarációját:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Cserélje le az állandó értékét `connectionString` a korábban jegyzett eszköz-összekapcsolási sztringre. Utána mentse el az **iothub_convenience_sample.c** módosításait.

3. Egy helyi terminálablakban keresse meg az *iothub_convenience_sample* projektkönyvtárat az Azure IoT C SDK-ban létrehozott CMake könyvtárban. Írja be a következő parancsot a munkakönyvtárból:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Futtassa a CMake parancsot a helyi terminálablakban a minta frissített `connectionString` értékkel való buildeléséhez:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. A helyi terminál ablakban futtassa a következő parancsot a szimulált eszköz alkalmazás futtatásához:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Ebben a szakaszban a Azure Cloud Shell és a [IoT bővítmény](/cli/azure/ext/azure-iot/iot) használatával figyeli a szimulált eszköz által küldött üzeneteket.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldése a hubhoz C alkalmazás használatával, valamint a telemetria olvasása a központban a Azure Cloud Shell használatával.

Az Azure IoT Hub C SDK-val történő fejlesztéssel kapcsolatos további információkért folytassa a következő útmutatóval:

> [!div class="nextstepaction"]
> [Fejlesztés az Azure IoT Hub C SDK használatával](iot-hub-devguide-develop-for-constrained-devices.md)
