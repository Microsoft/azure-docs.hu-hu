---
title: Rövid útmutató – szimmetrikus kulcs használata szimulált eszköz Azure-IoT Hub való kiépítéséhez Java használatával
description: Ebben a rövid útmutatóban a Java eszközoldali SDK-val létrehoz egy szimulált eszközt, amely szimmetrikus kulcsot használ az Azure IoT Hub Device Provisioning Service (DPS) használatával
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc, devx-track-java
ms.openlocfilehash: a32811b439a569a8f8f82fcc046e0f4b89e47a38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966700"
---
# <a name="quickstart-provision-a-simulated-device-to-iot-hub-with-symmetric-keys"></a>Gyors útmutató: szimulált eszköz kiépítése szimmetrikus kulcsokkal IoT Hub

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat eszközszimulátort a Windows rendszerű fejlesztői gépeken. Ezt a szimulált eszközt úgy konfigurálja, hogy szimmetrikus kulcs használatával hitelesítse az eszközt a kiépítési szolgáltatás (DPS) példányával, és hozzá legyen rendelve egy IoT hubhoz. A [Microsoft Azure IoT SDK-k Javához](https://github.com/Azure/azure-iot-sdk-java) készült mintakód az üzembe helyezést kezdeményező eszköz rendszerindítási sorrendjének szimulálására szolgál. Az eszköz felismerése a DPS szolgáltatási példánnyal való egyéni regisztráció és egy IoT hub számára történik.

Bár ez a cikk azt mutatja be, hogy a kiépítés egyedi regisztrációval történt, beléptetési csoportokat is használhat. A regisztrációs csoportok használata néhány eltérést is igénybe vehet. Például egy származtatott eszköz kulcsát kell használnia az eszköz egyedi regisztrációs azonosítójával. Bár a szimmetrikus kulcsot használó regisztrációs csoportok nem csak örökölt eszközök esetében használhatóak, a [Szimmetrikus kulcsok használata örökölt eszközök kiépítéséhez](how-to-legacy-device-symm-key.md) című cikk példája jól szemlélteti a regisztrációs csoportok használatát. További információért lásd: [Csoportos beléptetés használata szimmetrikus kulcsú igazolásnál](concepts-symmetric-key-attestation.md#group-enrollments)

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át a [kiépítés](about-iot-dps.md#provisioning-process) áttekintését. 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) vagy újabb verziója telepítve van a gépen.

* Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>A Java SDK-környezet előkészítése 

1. Győződjön meg róla, hogy a Git telepítve van a gépen, és hogy hozzá lett adva a parancsablakból elérhető környezeti változókhoz. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

2. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat az eszközszimuláció kódmintájához:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Navigáljon a gyökérkönyvtárhoz `azure-iot-sdk-java` , és hozza létre a projektet az összes szükséges csomag letöltéséhez.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza a bal oldali menüben az **összes erőforrás** gombot, és nyissa meg az eszköz kiépítési szolgáltatásának (DPS) példányát.

2. Válassza a **regisztrációk kezelése** fület, majd válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

3. A **beléptetés hozzáadása** panelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Kulcsok automatikus generálása**: jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például: **Symm-Key-Java-Device-007**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. Például: **Java-Device-007**.

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Miután mentette a regisztrációt, a rendszer létrehozza az **elsődleges kulcsot** és a **másodlagos kulcsot** , és hozzáadja a beléptetési bejegyzéshez. A szimmetrikus kulcsú eszközök beléptetése az *Egyéni* *regisztrációk lap regisztrációs azonosító* oszlopában, a **Symm-Key-Java-Device-007** néven jelenik meg. 

    Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**. Ezt a kulcs értéket és a **regisztrációs azonosítót** később fogja használni, amikor frissíti az eszközhöz tartozó Java-kódot.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Eszköz rendszerindítási sorrendjének szimulálása

Ebben a szakaszban frissíteni fogja az eszköz mintakód az eszköz rendszerindítási sorrendjének a DPS-példányba való küldéséhez. Ez a rendszerindítási folyamat azt eredményezi, hogy az eszköz felismeri, hitelesítve van, és hozzá van rendelve egy IoT hubhoz, amely a DPS-példánnyal van társítva.

1. Az eszközök kiépítési szolgáltatásának menüjében válassza az **Áttekintés** lehetőséget, és jegyezze fel az _azonosító hatókörét_ és a _kiépítési szolgáltatás globális végpontját_.

    ![Szolgáltatás adatai](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. A Java-eszköz mintakód megnyitása szerkesztéshez. Az eszköz mintakód teljes elérési útja:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Adja hozzá a DPS-példány _azonosító hatókörét_ és a _kiépítési szolgáltatás globális végpontját_ . Adja meg az elsődleges szimmetrikus kulcsot és az egyéni regisztrációhoz választott regisztrációs azonosítót is. Mentse a módosításokat. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Nyisson meg egy parancssort a létrehozáshoz. Navigáljon a Java SDK-tárház kiépítési minta projekt mappájához.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Hozza létre a mintát, majd navigáljon a `target` mappához a létrehozott. jar fájl végrehajtásához.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. A várt kimenetnek a következőhöz hasonlóan kell kinéznie:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. Az Azure Portalon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Miután sikeresen kiépítte a szimulált szimmetrikus kulcsú eszközt a központba, az eszköz azonosítója megjelenik a **Device Explorer** panelen, az *állapota* pedig **engedélyezett**.  Előfordulhat, hogy a felső **frissítés** gombra kell kattintania, ha már megnyitotta a panelt a minta eszköz futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációinak kezelése** elemet, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközöket** a központhoz, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a szimulált eszközt a Windows rendszerű gépen, és kiépíti azt az IoT hub-ba a portálon található Azure-IoT Hub Device Provisioning Service szimmetrikus kulcs használatával. Az eszköz programozott módon történő regisztrálásának megismeréséhez folytassa az X. 509 eszközök programozott regisztrálására szolgáló rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök regisztrálása az Azure-ba IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)