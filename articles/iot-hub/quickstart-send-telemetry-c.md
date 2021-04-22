---
title: Rövid útmutató – Telemetria küldése Azure IoT Hub rövid útmutató (C) | Microsoft Docs
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
ms.openlocfilehash: a9478948cd5232c863014631acb884cd637a2f7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863996"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra, és annak olvasása háttéralkalmazással (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

A rövid útmutató a [C nyelvhez készült Azure IoT eszközoldali SDK](iot-hub-device-sdk-c-intro.md) egy C-mintaalkalmazását használja telemetriai adatoknak az IoT hubra küldésére. Az Azure IoT eszközoldali SDK-t [ANSI C-ben (C99)](https://wikipedia.org/wiki/C99) írták a hordozhatóságot és a széles körű platformokon történő használhatóságot szem előtt tartva. A mintakód futtatása előtt létrehozza az IoT hubot és regisztrálja rajta a szimulált eszközt.

Ez a cikk Windowsra íródott, de linuxos rendszeren is befejezheti ezt a rövid útmutatót.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse [Visual Studio 2019-es](https://www.visualstudio.com/vs/) verzióra az ["Asztali fejlesztés C++ alkalmazással" számítási feladat](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) engedélyezése mellett.

* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A rövid útmutatóban található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Ez a port blokkolható egyes vállalati és oktatási hálózati környezetekben. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a [C-hez készült Azure IoT eszközoldali SDK-t fogja használni.](iot-hub-device-sdk-c-intro.md) 

A következő környezetekben az SDK-t a következő csomagok és kódtárak telepítésével használhatja:

* **Linux:** Az apt-get csomagok az Ubuntu 16.04-es és 18.04-es rendszerekhez a következő processzorarchitektúrák használatával érhetők el: amd64, arm64, armhf és i386. További információkért lásd: [Az apt-get használata C eszköz ügyfél-projekt létrehozására az Ubuntu rendszeren](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed:** Az mbed platformon eszközalkalmazásokat fejlesztő fejlesztők számára közzétettünk egy kódtárat és mintákat, amelyek percek alatt elérhetővé Azure IoT Hub. További információk: [Az mbed könyvtár használata](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino:** Ha az Arduino-t fejleszti, használhatja az Arduino IDE kódtárkezelőben elérhető Azure IoT-kódtárat. Bővebb információk: [Az Azure IoT Hub könyvtár – Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Mac és iOS rendszerű eszközfejlesztéshez az IoT Hub eszközoldali SDK mint CocoaPods érhető el. További információ: [iOS példák Microsoft Azure IoT számára](https://cocoapods.org/pods/AzureIoTHubClient).

Ebben a rövid útmutatóban azonban elő fog készíteni egy fejlesztési környezetet, amely az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHubról való klónozásához és felépítéséhez használható. A rövid útmutató mintakódokat az SDK tartalmazza a GitHubon.

1. Töltse le a [CMake buildrendszert.](https://cmake.org/download/)

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattár legújabb kiadásának klónozásához. Használja az előző lépésben talált címkét a paraméter `-b` értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a következő parancsokat a `azure-iot-sdk-c` könyvtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa a következő parancsot az SDK fejlesztési ügyfélplatformra vonatkozó verziójának buildjéhez. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre.

    ```cmd
    cmake ..
    ```

    Ha nem találja a C++ fordítót, a fenti parancs futtatásakor `cmake` buildhibákba eshet. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a virtuálisgép-Azure Cloud Shell fog használni egy [szimulált](/cli/azure/iot) eszköz regisztrálására az IoT-bővítvével.

1. Futtassa a következő parancsot a Azure Cloud Shell az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

   **MyCDevice:** Ez a regisztrált eszköz neve. Javasoljuk a **MyCDevice használatát** az itt látható módon. Ha másik nevet választ az eszköznek, akkor a cikk során is ezt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell az eszköz nevét.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Futtassa a következő parancsot Azure Cloud Shell a regisztrált eszköz _eszközkapcsolati_ sztringjéhez:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Erre az értékre a rövid útmutató későbbi, még nem használt értéke lesz.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és sztringet küld szimulált telemetriaként.

1. Szövegszerkesztővel nyissa meg az iothub_convenience_sample.c forrásfájlt, és tekintse át a telemetriai adatok küldését végző mintakódot. A fájl a következő helyen található abban a munkakönyvtárban, amelybe klónozta az Azure IoT C SDK-t:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Keresse meg a `connectionString` konstans deklarációját:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Cserélje le a konstans értékét a korábban `connectionString` feljegyált eszközkapcsolati sztringre. Utána mentse el az **iothub_convenience_sample.c** módosításait.

3. Egy helyi terminálablakban keresse meg az *iothub_convenience_sample* projektkönyvtárat az Azure IoT C SDK-ban létrehozott CMake könyvtárban. Írja be a következő parancsot a munkakönyvtárból:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Futtassa a CMake parancsot a helyi terminálablakban a minta frissített `connectionString` értékkel való buildeléséhez:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Futtassa a következő parancsot a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Ebben a szakaszban a szimulált eszköz által Azure Cloud Shell eszköz által küldött eszközüzeneteket fogja monitorozza a virtuális hálózat és az [IoT-bővítmény](/cli/azure/iot) használatával.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a hubra egy C-alkalmazással, és beolvassa a telemetriát a hubról a Azure Cloud Shell.

Az Azure IoT Hub C SDK-val történő fejlesztéssel kapcsolatos további információkért folytassa a következő útmutatóval:

> [!div class="nextstepaction"]
> [Fejlesztés az Azure IoT Hub C SDK használatával](iot-hub-devguide-develop-for-constrained-devices.md)
