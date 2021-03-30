---
title: Rövid útmutató – szimmetrikus kulcs használata eszköz Azure-IoT Hub való kiépítéséhez a Python használatával
description: Ebben a rövid útmutatóban az Azure IoT Python SDK-val kell kiépíteni egy szimmetrikus kulcsú eszközt egy IoT hubhoz az Azure IoT Hub Device Provisioning Service (DPS) használatával
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 8b87d9d487257504a438cf13a5b94e3ca4f9233d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92426378"
---
# <a name="quickstart-provision-a-python-device-with-symmetric-keys"></a>Gyors útmutató: Python-eszköz kiépítése szimmetrikus kulcsokkal

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy Windows-fejlesztő gépet eszközként egy IoT hub-ban a Python használatával. Ez az eszköz egy szimmetrikus kulcsot és egy egyéni regisztrációt használ egy eszköz-kiépítési szolgáltatás (DPS) példányának hitelesítéséhez ahhoz, hogy IoT hubhoz rendeljen hozzá. Az eszköz kiépítéséhez az [Azure IoT PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python) -ból származó mintakód lesz használva. 

Bár ez a cikk azt mutatja be, hogy a kiépítés egyedi regisztrációval történt, a beléptetési csoportokat is használhatja. A regisztrációs csoportok használata néhány eltérést is igénybe vehet. Például egy származtatott eszköz kulcsát kell használnia az eszköz egyedi regisztrációs azonosítójával. Bár a szimmetrikus kulcsot használó regisztrációs csoportok nem csak örökölt eszközök esetében használhatóak, a [Szimmetrikus kulcsok használata örökölt eszközök kiépítéséhez](how-to-legacy-device-symm-key.md) című cikk példája jól szemlélteti a regisztrációs csoportok használatát. További információért lásd: [Csoportos beléptetés használata szimmetrikus kulcsú igazolásnál](concepts-symmetric-key-attestation.md#group-enrollments)

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át a [kiépítés](about-iot-dps.md#provisioning-process) áttekintését. 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a [Python 3,7](https://www.python.org/downloads/) -es vagy újabb verziója telepítve van a Windows-alapú gépen. A Python verzióját a futtatásával is megtekintheti `python --version` .

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>

## <a name="prepare-the-python-sdk-environment"></a>A Python SDK-környezet előkészítése 

1. Győződjön meg róla, hogy a Git telepítve van a gépen, és hogy hozzá lett adva a parancsablakból elérhető környezeti változókhoz. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

2. Nyisson meg egy parancssort. Az Azure IoT Python SDK-hoz készült GitHub-tárház klónozása:
    
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```
3. Navigáljon ahhoz a `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` könyvtárhoz, ahol a _provision_symmetric_key. a._ a minta fájl található.
   
   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```
4. Telepítse az _Azure-IOT-Device_ könyvtárat az alábbi parancs futtatásával.

    ```console
    pip install azure-iot-device
    ```


## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza a bal oldali menüben az **összes erőforrás** gombot, és nyissa meg az eszköz kiépítési szolgáltatásának (DPS) példányát.

2. Válassza a **regisztrációk kezelése** fület, majd válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

3. A **beléptetés hozzáadása** panelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Kulcsok automatikus generálása**: jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például: **Symm-Key-Python-Device-008**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. Például: **Python-Device-008**.

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-device-symm-key-python/create-individual-enrollment-python.png)

4. Miután mentette a regisztrációt, a rendszer létrehozza az **elsődleges kulcsot** és a **másodlagos kulcsot** , és hozzáadja a beléptetési bejegyzéshez. A szimmetrikus kulcsú eszközök beléptetése **Symm-Key-Python-Device-008** néven jelenik meg az *Egyéni* regisztrációk lapon a *regisztrációs azonosító* oszlopban. 

5. Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**. Ezt a kulcs értéket és a **regisztrációs azonosítót** később fogja használni, amikor környezeti változókat ad hozzá az eszköz kiépítési mintakód használatával történő használathoz.



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Az eszköz üzembe helyezési kódjának előkészítése

Ebben a szakaszban a következő négy környezeti változót fogja hozzáadni, amelyek paraméterként lesznek felhasználva az eszköz kiépítési mintakód számára a szimmetrikus kulcsos eszközhöz. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

A kiépítési kód az eszköz hitelesítéséhez a következő változók alapján felveszi a kapcsolatot a DPS-példánnyal. Az eszköz ezután hozzá lesz rendelve egy olyan IoT hubhoz, amely az egyéni regisztrációs konfiguráció alapján már össze van kapcsolva a DPS-példánnyal. A kiépítés után a mintakód egy teszt telemetria küld az IoT hub-nak.

1. A [Azure Portal](https://portal.azure.com)eszköz kiépítési szolgáltatás menüjében válassza az **Áttekintés** lehetőséget, és másolja a _szolgáltatási végpontot_ és az _azonosító hatókörét_. Ezeket az értékeket fogja használni a `PROVISIONING_HOST` és `PROVISIONING_IDSCOPE` környezeti változókhoz.

    ![Szolgáltatás adatai](./media/quick-create-device-symm-key-python/extract-dps-endpoints.png)

2. A Python-parancssorban adja hozzá a környezeti változókat a másolt értékek alapján. 

    A következő parancsok példák a parancs szintaxisának megjelenítésére. Ügyeljen arra, hogy a megfelelő értékeket használja.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

3. A Python-parancssorban adja hozzá a környezeti változókat az előző szakaszban szereplő egyéni regisztrációból másolt regisztrációs AZONOSÍTÓhoz és szimmetrikus kulcshoz. 

    A következő parancsok példák a parancs szintaxisának megjelenítésére. Ügyeljen arra, hogy a megfelelő értékeket használja.

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-python-device-008
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

4. Futtassa a Python-mintakód _provision_symmetric_key.._......

    ```console
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    ```

5. A várt kimenetnek az alábbihoz hasonlóan kell kinéznie, amely megjeleníti a csatolt IoT hubot, amelyet az eszköz az egyéni regisztrációs beállítások alapján rendelt hozzá. Néhány példa a Szélsebesség telemetria-üzeneteinek küldésére is tesztként kerül a középpontba:

    ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```
    
6. A Azure Portal navigáljon a kiépítési szolgáltatáshoz társított IoT hubhoz, és nyissa meg a **IoT-eszközök** panelt. Miután sikeresen kiépítte a szimmetrikus kulcs eszközét a központba, az eszköz azonosítója **engedélyezett** *állapottal* jelenik meg. Előfordulhat, hogy a felül lévő **frissítés** gombra kell kattintania, ha már megnyitotta a panelt az eszköz mintakód futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-device-symm-key-python/hub-registration-python.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációinak kezelése** elemet, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközöket** a központhoz, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Windows-alapú szimmetrikus kulcsú eszközt telepített a IoT hub-hoz a IoT Hub Device Provisioning Service használatával. Az X. 509 tanúsítványok Python használatával történő kiépítésének megismeréséhez folytassa az alábbi rövid útmutatót X. 509 eszközök esetén. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök kiépítése a DPS és a Python használatával](quick-create-simulated-device-x509-python.md)
