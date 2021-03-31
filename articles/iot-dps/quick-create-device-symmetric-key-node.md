---
title: Rövid útmutató – szimmetrikus kulcs használata eszköz Azure-beli IoT Hub való kiépítéséhez Node.js
description: Ebben a rövid útmutatóban a Node.js Azure IoT SDK-t fogja használni az eszköz kiépítési szolgáltatásával (DPS) egy szimmetrikus kulcsú eszköz kiépítéséhez az IoT hub-ban
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 78005ba46952bcf05b19c7627feecb1ec30ac651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92429352"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-nodejs"></a>Gyors útmutató: szimmetrikus kulcsú eszköz kiépítése Node.js használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy Windows-fejlesztő gépet eszközként az IoT hub Node.js használatával. Ez az eszköz egy szimmetrikus kulcsot és egy egyéni regisztrációt használ egy eszköz-kiépítési szolgáltatás (DPS) példányának hitelesítéséhez ahhoz, hogy IoT hubhoz rendeljen hozzá. A rendszer a [Node.jsAzure IOT SDK ](https://github.com/Azure/azure-iot-sdk-node.git) -ból származó mintakód használatával hozza létre az eszközt. 

Bár ez a cikk azt mutatja be, hogy a kiépítés egyedi regisztrációval történt, a beléptetési csoportokat is használhatja. A regisztrációs csoportok használata néhány eltérést is igénybe vehet. Például egy származtatott eszköz kulcsát kell használnia az eszköz egyedi regisztrációs azonosítójával. [A szimmetrikus kulccsal rendelkező eszközök kiépítése](how-to-legacy-device-symm-key.md) beléptetési csoportra vonatkozó példát tartalmaz. A beléptetési csoportokkal kapcsolatos további információkért lásd: a [szimmetrikus kulcsú tanúsítványok igénylésének csoportosítása](concepts-symmetric-key-attestation.md#group-enrollments).

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át a [kiépítés](about-iot-dps.md#provisioning-process) áttekintését. 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-Példákért lásd: [bérlős létesítése](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

- Ismerje meg a [kiépítési](about-iot-dps.md#provisioning-process) fogalmakat.
- [A IoT hub Device Provisioning Service beállításának befejezése a Azure Portal](./quick-setup-auto-provision.md).
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org).
- [Git](https://git-scm.com/download/).


## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza a bal oldali menüben az **összes erőforrás** gombot, és nyissa meg az eszköz kiépítési szolgáltatásának (DPS) példányát.

2. Válassza a **regisztrációk kezelése** fület, majd válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

3. A **beléptetés hozzáadása** panelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Kulcsok automatikus generálása**: jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például: **Symm-Key-NodeJS-Device-01**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. Például: **NodeJS-Device-01**.

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-device-symmetric-key-node/create-individual-enrollment-node.png)

4. Miután mentette a regisztrációt, a rendszer létrehozza az **elsődleges kulcsot** és a **másodlagos kulcsot** , és hozzáadja a beléptetési bejegyzéshez. A szimmetrikus kulcsú eszközök beléptetése **Symm-Key-NodeJS-Device-01** néven jelenik meg az *Egyéni* regisztrációk lap *regisztrációs azonosító* oszlopában. 

5. Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**. Ezt a kulcs értéket és a **regisztrációs azonosítót** később fogja használni, amikor környezeti változókat ad hozzá az eszköz kiépítési mintakód használatával történő használathoz.



## <a name="prepare-the-nodejs-environment"></a>A Node.js-környezet előkészítése 

1. Nyisson meg egy git CMD vagy git bash parancssori környezetet. A Node.jsGitHub-adattárhoz készült [Azure IOT SDK ](https://github.com/Azure/azure-iot-sdk-node.git) klónozása a következő paranccsal:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```


<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Az eszköz üzembe helyezési kódjának előkészítése

Ebben a szakaszban a következő négy környezeti változót adja hozzá, amelyek paraméterként lesznek felhasználva az eszköz kiépítési mintakód számára a szimmetrikus kulcsos eszköz kiépítéséhez. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

A kiépítési kód az eszköz hitelesítéséhez a következő változók alapján felveszi a kapcsolatot a DPS-példánnyal. Az eszköz ezután hozzá lesz rendelve egy olyan IoT hubhoz, amely az egyéni regisztrációs konfiguráció alapján már össze van kapcsolva a DPS-példánnyal. A kiépítés után a mintakód egy teszt telemetria küld az IoT hub-nak.

1. A [Azure Portal](https://portal.azure.com)eszköz kiépítési szolgáltatás menüjében válassza az **Áttekintés** lehetőséget, és másolja a _szolgáltatási végpontot_ és az _azonosító hatókörét_. Ezeket az értékeket fogja használni a `PROVISIONING_HOST` és `PROVISIONING_IDSCOPE` környezeti változókhoz.

    ![Szolgáltatás adatai](./media/quick-create-device-symmetric-key-node/extract-dps-endpoints.png)

2. Nyisson meg egy parancssort Node.js parancsok végrehajtásához, és navigáljon a következő *kiépítési/eszköz/minta* könyvtárba.

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

3. A *kiépítés/eszköz/minták* mappában Nyissa meg *register_symkey.js* és tekintse át a kódot. 

    Figyelje meg, hogy a mintakód egyéni adattartalmat állít be...

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Ez a kód nem szükséges ehhez a rövid útmutatóhoz. Ez a kód egy egyéni adattartalom beállítására mutat példát, ha egyéni foglalási függvénnyel kívánja hozzárendelni az eszközt egy IoT Hubhoz. További információ: [oktatóanyag: egyéni foglalási szabályzatok használata](tutorial-custom-allocation-policies.md).

    A `provisioningClient.register()` metódus megkísérli az eszköz regisztrációját.

    Az eszköz regisztrálásához a mintakód nem lesz szükséges.

4. A parancssorban adja hozzá a környezeti változókat a kiépítési gazdagéphez, az azonosító hatóköréhez, a regisztrációs AZONOSÍTÓhoz és az elsődleges szimmetrikus kulcshoz, amelyet az előző szakaszban lévő egyéni regisztrációból másolt.  

    A következő parancsok példák a parancs szintaxisának megjelenítésére. Ügyeljen arra, hogy a megfelelő értékeket használja.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-nodejs-device-01
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```


4. Hozza létre és futtassa a mintakód használatát a következő parancsokkal.

    ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

5. A várt kimenetnek az alábbihoz hasonlóan kell kinéznie, amely megjeleníti a csatolt IoT hubot, amelyhez az eszköz hozzá lett rendelve az egyes regisztrációs beállítások alapján. A rendszer tesztüzenetként egy "Hello World" karakterláncot küld a hubhoz:

    ```output
    D:\Docs\test\azure-iot-sdk-node\provisioning\device\samples>node register_symkey.js
    registration succeeded
    assigned hub=docs-test-iot-hub.azure-devices.net
    deviceId=nodejs-device-01
    payload=undefined
    Client connected
    send status: MessageEnqueued    
    ```
    
6. A Azure Portal navigáljon a kiépítési szolgáltatáshoz társított IoT hubhoz, és nyissa meg a **IoT-eszközök** panelt. Miután sikeresen kiépítte a szimmetrikus kulcs eszközét a központba, az eszköz azonosítója **engedélyezett** *állapottal* jelenik meg. Előfordulhat, hogy a felül lévő **frissítés** gombra kell kattintania, ha már megnyitotta a panelt az eszköz mintakód futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-device-symmetric-key-node/hub-registration-node.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációinak kezelése** elemet, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközöket** a központhoz, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Windows-alapú szimmetrikus kulcsú eszközt telepített a IoT hub-hoz a IoT Hub Device Provisioning Service használatával. Ha meg szeretné tudni, hogyan kell X. 509 tanúsítványokat kiépíteni a Node.js használatával, folytassa az alábbi rövid útmutatóval X. 509 eszközök esetén. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök kiépítése a DPS és a Node.jshasználatával ](quick-create-simulated-device-x509-node.md)
