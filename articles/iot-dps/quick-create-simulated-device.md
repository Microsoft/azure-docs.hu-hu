---
title: 'Gyors útmutató: szimulált TPM-eszköz kiépítése az Azure-IoT Hub C használatával'
description: Ez a rövid útmutató egyéni regisztrációkat használ. Ebben a rövid útmutatóban egy szimulált TPM-eszközt hoz létre és helyez üzembe az Azure-hoz készült C Device SDK-IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: e2930a3ca2ecb9d8217fdfea1cbcb0e669f61775
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960036"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Rövid útmutató: Szimulált TPM-eszköz kiépítése az Azure IoT C SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat platformmegbízhatósági modul (TPM) alapú eszközszimulátort Windows rendszerű fejlesztői gépeken. A szimulált eszközt egy IoT Hubhoz csatlakoztatja egy Device Provisioning Service-példánnyal. Az eszköz egy Device Provisioning Service-példánnyal való regisztrálásához és az eszköz rendszerindításának szimulálásához az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) egy mintakódját használjuk majd.

Ha nem ismeri az kiépítés folyamatát, tekintse át a [kiépítés](about-iot-dps.md#provisioning-process) áttekintését. A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztési környezetéhez szükségesek. Linux vagy macOS esetén tekintse meg a [fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) az SDK-ban című dokumentáció megfelelő szakaszát.

* A [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019-es verziójában engedélyezve van az ["asztali fejlesztés C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) -ban" számítási feladattal. A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Fejlesztői környezet előkészítése az Azure IoT C SDK-hoz

Ebben a szakaszban egy fejlesztői környezetet készítünk elő az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) és a [TPM](/windows/device-security/tpm/trusted-platform-module-overview) eszközszimulátor mintájának létrehozásához.

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

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Az SDK létrehozása és a TPM-eszközszimulátor futtatása

Ebben a szakaszban létrehozzuk az Azure IoT SDK-t, amely tartalmazza a TPM-eszközszimulátor mintakódját. Ez a minta TPM [igazolási mechanizmust](concepts-service.md#attestation-mechanism) nyújt közös hozzáférésű jogosultságkód- (SAS-) jogkivonat hitelesítésével.

1. Az azure-iot-sdk-c Git-adattárban létrehozott `cmake` alkönyvtárból futtassa a következő parancsot a minta létrehozásához. Ez a buildelési parancs egy Visual Studio-megoldást is létrehoz a szimulált eszközhöz.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Keresse meg a klónozott Git-adattár gyökérmappájához, és futtassa a [TPM](/windows/device-security/tpm/trusted-platform-module-overview)-szimulátort az alább látható elérési úttal. Ez a szimulátor a 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárja be ezt a parancsablakot; a rövid útmutató végéig futnia kell ennek a szimulátornak. 

   A *cmake* mappában futtassa a következő parancsokat:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Nem jelenik meg kimenet a szimulátorból. Hagyja tovább futni a TPM-eszköz szimulálását.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Titkosítási kulcsok olvasása a TPM-eszközből

Ebben a szakaszban olyan mintát hoz létre és futtat, amely beolvassa a futó TPM-szimulátor ellenőrzőkulcsát és regisztrációs azonosítóját, továbbá a 2321-es és a 2322-es portokon figyel. A rendszer ezeket az értékeket használja az eszköz regisztrációjához a Device Provisioning Service-példány esetében.

1. Indítsa el a Visual Studiót, és nyissa meg az `azure_iot_sdks.sln` nevű új megoldásfájlt. A megoldásfájl az azure-iot-sdk-c Git-adattár gyökérkönyvtárában korábban létrehozott `cmake` mappában található.

2. A Visual Studio menüjében válassza a **Build**  >  -**megoldás** létrehozása lehetőséget a megoldásban lévő összes projekt létrehozásához.

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Tools** (Kiépítés > Eszközök) mappára. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget. 

4. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a megoldás futtatásához. Az alkalmazás beolvas és megjelenít egy **_regisztrációs azonosítót_** és egy **_hátirat-kulcsot_**. Jegyezze fel vagy másolja ezeket az értékeket. Ezeket a következő szakaszban, az eszközregisztráció során fogjuk használni. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Eszközregisztrációs bejegyzés létrehozása a portálon

1. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg az eszköz kiépítési szolgáltatását.

1. Válassza a **regisztrációk kezelése** fület, majd válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

1. A **beléptetés hozzáadása** panelen adja meg a következő adatokat:
   - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
   - Adja meg a TPM-eszköz *regisztrációs azonosítóját* és a *jóváhagyás kulcsát* a korábban feljegyzett értékek közül.
   - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Adjon meg egy egyedi *eszköz-azonosítót* (a javasolt **test-docs-Device** vagy a sajátját is használhatja). Ne használjon bizalmas adatokat az eszköz elnevezésekor. Ha úgy dönt, hogy nem ad meg egyet, a rendszer a regisztrációs azonosítót fogja használni az eszköz azonosítására.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Ha elkészült, kattintson a **Save (Mentés** ) gombra. 

      ![Írja be az eszköz beléptetési információit a portálon](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni beléptetések* lapon lévő listában. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Első rendszerindítás szimulálása az eszközhöz

Ebben a szakaszban konfigurálja a mintakódot, hogy az [Advanced Message Queueing Protocol (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) használatával küldje el az eszköz rendszerindítási szekvenciáját a Device Provisioning Service-példánynak. A rendszerindítási szekvenciának köszönhetően a rendszer felismeri majd az eszközt, és hozzárendeli egy, a Device Provisioning Service-példányhoz társított IoT Hubhoz.

1. Az Azure Portalon válassza ki az eszközregisztrációs szolgáltatás **Áttekintés** lapját, és másolja ki az **_Azonosító hatóköre_** értéket.

    ![A Device Provisioning Service végpont-információinak kinyerése a portálról](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

3. A fájl elején keresse meg az eszközprotokollok `#define` utasításait az alábbi módon. Győződjön meg arról, hogy csak a `SAMPLE_AMQP` ne legyen ellátva megjegyzéssel.

    Jelenleg az [MQTT protokoll nem támogatott az egyéni TPM-regisztrációhoz](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Az alább látható módon győződjön meg róla, hogy az `hsm_type` változó értéke `SECURE_DEVICE_TYPE_TPM`, és nem `SECURE_DEVICE_TYPE_X509`.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

7. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésének megadásához válassza az **Igen** lehetőséget, ha a Futtatás előtt szeretné újraépíteni a projektet.

    Az alábbi példakimeneten az látható, hogy az eszközregisztrációs mintaügyfél sikeresen elindul, és csatalakozik a Device Provisioning Service példányához az IoT Hub adatainak lekérése és a regisztráció céljából:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Miután a kiépítési szolgáltatás kiépíti a szimulált eszközt az IoT hubhoz, az eszköz azonosítója megjelenik a hub **IoT-eszközeivel**. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. Zárja be a TPM szimulátor ablakát a gépen.
3. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációinak kezelése** elemet, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
4. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközöket** a központhoz, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy TPM-mel szimulált eszközt a gépen, és kiosztotta azt az IoT hubhoz a IoT Hub Device Provisioning Service használatával. A TPM-eszköz programozott módon történő regisztrálásának megismeréséhez folytassa a TPM-eszköz programozott beléptetését bemutató rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – TPM-eszköz regisztrálása az Azure-ban IoT Hub Device Provisioning Service](quick-enroll-device-tpm-java.md)