---
title: Gyors útmutató – szimulált X. 509 eszköz kiépítése az Azure-IoT Hub Javával
description: Azure rövid útmutató – szimulált X. 509 eszköz létrehozása és kiépítése a IoT Hub Device Provisioning Service Javához készült Java eszközoldali SDK-val (DPS). Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 2ed458ee0267a65bb7551940fc522509dc310311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966615"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: szimulált X. 509 eszköz létrehozása és kiépítése a IoT Hub Device Provisioning Service Javához készült Java eszközoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ebben a rövid útmutatóban egy szimulált X. 509 eszközt hoz létre egy Windows rendszerű számítógépen. A szimulált eszköz és az IoT hub között a Device kiépítési szolgáltatással (DPS) való egyéni regisztráció használatával csatlakoztathatja a szimulált eszközt az eszköz minta Java-kódjához.

## <a name="prerequisites"></a>Előfeltételek

- Ismerje meg a [kiépítési](about-iot-dps.md#provisioning-process) fogalmakat.
- [A IoT hub Device Provisioning Service beállításának befejezése a Azure Portal](./quick-setup-auto-provision.md).
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) telepítve van a gépén.

2. Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

3. Győződjön meg róla, hogy a Git telepítve van a gépen, és hogy hozzá lett adva a parancsablakból elérhető környezeti változókhoz. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

4. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat az eszközszimuláció kódmintájához:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Lépjen az `azure-iot-sdk-`java` gyökérmappájába, és futtasson buildelést a projekten az összes szükséges csomag letöltéséhez.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Lépjen a tanúsítványt létrehozó projekthez, és építse ki a projektet. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Önaláírt X.509-eszköztanúsítvány és egyéni regisztrációs bejegyzés létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fogunk használni. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

Az [Azure IoT SDK for Java](https://github.com/Azure/azure-iot-sdk-java.git) mintakódját használja majd a szimulált eszköz egyéni regisztrációs bejegyzéséhez használt tanúsítvány létrehozásához.

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): egyetlen eszköz regisztrálására szolgál.

Ez a cikk az egyéni regisztrációkat mutatja be.

1. Az előző lépések parancssorának használatával lépjen a `target` mappába, majd futtassa az előző lépésben létrehozott. jar fájlt.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. A **Do you want to input common name** (Szeretne megadni köznapi nevet?) kérdésnél adja meg az _N_ értéket. Másolja a vágólapra a `Client Cert` kimenetét a *-----BEGIN CERTIFICATE-----* sortól kezdődően az *-----END CERTIFICATE-----* sorig.

   ![Egyénitanúsítvány-készítő](./media/java-quick-create-simulated-device-x509/individual.png)

3. Hozzon létre egy **_X509individual.pem_** nevű fájlt a Windows rendszerű gépén, nyissa meg egy tetszőleges szövegszerkesztőben, és másolja bele a vágólapra kimásolt szöveget. Mentse a fájlt, és zárja be a szerkesztőt.

4. A parancssorba írja be az **N** értéket az _ellenőrző kód_ beírásához, és a program kimenetét a rövid útmutató későbbi részében megnyitva hagyja meg. Később másolnia kell a `Client Cert` és a `Client Cert Private Key` értéket, melyeket a következő szakaszban fog használni.

5. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza a bal oldali menüben az **összes erőforrás** gombot, és nyissa meg az eszköz kiépítési szolgáltatásának példányát.

6. Az eszközök kiépítési szolgáltatásának menüjében válassza a **regisztrációk kezelése** lehetőséget. Válassza az **Egyéni regisztrációk** fület, és válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

7. A **beléptetés hozzáadása** panelen adja meg a következő adatokat:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Az *elsődleges tanúsítvány. PEM vagy. cer fájlban* válassza a *fájl kiválasztása* lehetőséget az előző lépésekben létrehozott **X509individual. PEM** fájl kiválasztásához.  
   - Ha kívánja, megadhatja az alábbi információkat is:
     - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
     - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor. 
     - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
     - Ha elkészült, kattintson a **Save (Mentés** ) gombra. 

     [![Egyéni regisztráció hozzáadása X. 509 tanúsítványhoz a portálon](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Sikeres regisztrációt követően az X.509-eszköz **microsoftriotcore** azonosítóval megjelenik a *Regisztrációs azonosító* oszlopban, az *Egyéni regisztrációk* lapon. 



## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az eszközök kiépítési szolgáltatásának menüjében válassza az **Áttekintés** lehetőséget, és jegyezze fel az _azonosító hatókörét_ és a _kiépítési szolgáltatás globális végpontját_.

    ![Szolgáltatás adatai](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Nyisson meg egy parancssort. Lépjen a Java SDK adattárban a mintaprojekt mappájára.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Adja meg a kiépítési szolgáltatás és az X.509-identitás adatait a kódban. Ezt a rendszer a szimulált eszköz igazolásához használja az automatikus kiépítés során, mielőtt regisztrálná az eszközt:

   - Szerkessze a fájlt `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` , hogy tartalmazza az _azonosító hatókörét_ és a _kiépítési szolgáltatás globális végpontját_ a korábban feljegyzett módon. Adja hozzá a _Client Cert_ és a _Client Cert Private Key_ értéket is az előző szakaszban feljegyzett adatok alapján.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - A tanúsítvány és a titkos kulcs másolásakor és beillesztésekor használja a következő formátumot:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Hozza létre a mintát. Navigáljon a `target` mappához, és hajtsa végre a létrehozott. jar fájlt.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Az Azure Portalon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Ha sikeresen kiépítette a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik a **Device Explorer** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz.  Előfordulhat, hogy a felső **frissítés** gombra kell kattintania, ha már megnyitotta a panelt a minta eszköz futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációk kezelése** paneljét, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
3. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközök** panelt, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítója* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy szimulált X. 509 eszközt a Windows rendszerű gépen. Ezután konfigurálta az eszköz beléptetését az Azure IoT Hub Device Provisioning szolgáltatásban, majd automatikusan kiépítette az eszközt saját IoT-központján. Az X. 509 eszköz programozott módon történő regisztrálásának megismeréséhez folytassa az X. 509 eszközök programozott regisztrálására szolgáló rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök regisztrálása az Azure-ba IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)