---
title: Gyors útmutató – TPM-eszköz regisztrálása az Azure Device kiépítési szolgáltatásba a Javával
description: Gyors útmutató – TPM-eszköz regisztrálása az Azure IoT Hub Device Provisioning Serviceba (DPS) a Java Service SDK-val. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0a1f4ed46ab9e467a19cfa722a2d345284fdc94a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96463049"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Gyors útmutató: TPM-eszköz regisztrálása IoT Hub Device Provisioning Service a Java Service SDK-val

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ebben a rövid útmutatóban programozott módon hozzon létre egy egyéni regisztrációt egy szimulált TPM-eszközhöz az Azure IoT Hub Device Provisioning Service a Java Service SDK-val egy minta Java-alkalmazás segítségével.

## <a name="prerequisites"></a>Előfeltételek

- A [IoT hub Device Provisioning Service beállításának befejezése a Azure Portal](./quick-setup-auto-provision.md).
- Az [olvasási titkosítási kulcsok befejezése a TPM-eszközről](quick-create-simulated-device.md#simulatetpm).
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). Ez a rövid útmutató telepíti a [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) -t alább. Windows és Linux rendszereken egyaránt működik. Ez a rövid útmutató a Windowst használja.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) telepítve van a gépén. 

2. Állítson be környezeti változókat a Java-telepítéshez. A `PATH` változónak tartalmaznia kell a *jdk1.8.x\bin* könyvtár teljes elérési útját. Ha ez az első Java-telepítés a számítógépen, hozzon létre egy új, `JAVA_HOME` nevű környezeti változót, amely a *jdk1.8.x* könyvtár teljes elérési útjára mutat. Windows rendszerű gépeken ez a könyvtár a *C:\\Program Files\\Java\\* mappában található. Környezeti változók létrehozásához és szerkesztéséhez keressen rá **A rendszer környezeti változóinak módosítása** kifejezésre a Windows rendszerű gép **Vezérlőpultján**. 

   Az alábbi parancs parancsablakban való futtatásával ellenőrizheti, hogy sikerült-e beállítani a Javát a számítógépen:

    ```cmd\sh
    java -version
    ```

3. Töltse le a [Maven 3](https://maven.apache.org/download.cgi)-at a számítógépre, és bontsa ki. 

4. Szerkessze a `PATH` környezeti változót, hogy az *apache-maven-3.x.x\\bin* mappára mutasson abban a mappában, amelybe a Mavent kibontotta. Az alábbi parancs parancsablakban való futtatásával ellenőrizheti, hogy sikeres volt-e a Maven telepítése:

    ```cmd\sh
    mvn --version
    ```

5. Győződjön meg arról, hogy a [git](https://git-scm.com/download/) telepítve van a gépen, és hozzá van adva a `PATH` környezeti változóhoz. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>A Java-mintakód letöltése és módosítása

Ez a szakasz bemutatja, hogyan adhatja hozzá a TPM-eszköz kiépítési adatait a mintakódhoz. 

1. Nyisson meg egy parancssort. A GitHub-tárház klónozása az eszköz beléptetési kódjához a [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/)használatával:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. A letöltött forráskódban lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** mintamappára. Nyissa meg a **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** fájlt egy tetszőleges szövegszerkesztőben, és adja hozzá az alábbi adatokat:

   1. Adja hozzá a `[Provisioning Connection String]` karakterláncot a kiépítési szolgáltatáshoz a portálról:
       1. Navigáljon a kiépítési szolgáltatáshoz a [Azure Portal](https://portal.azure.com). 
       2. Nyissa meg a **Megosztott elérési szabályzatok** panelt, és válasszon ki egy szabályzatot, amely rendelkezik az *EnrollmentWrite* engedéllyel.
       3. Másolja ki az **elsődleges kulcs kapcsolati sztringjét**. 

           ![A kiépítési kapcsolati sztring lekérése a portálról](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. A **_ServiceEnrollmentSample.java_** mintakódfájlban cserélje le a `[Provisioning Connection String]` sztringet az **elsődleges kulcs kapcsolati sztringjére**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Adja meg a TPM-eszköz adatait:
       1. Kérje le a *Regisztrációs azonosítót* és a *TPM ellenőrzőkulcsát* egy TPM-eszközszimulációhoz a [TPM-eszköz szimulálása](quick-create-simulated-device.md#simulatetpm) című szakaszhoz vezető lépések alapján.
       2. Cserélje le a **_ServiceEnrollmentSample.java_** mintakódfájl `[RegistrationId]` és `[TPM Endorsement Key]` értékét az előző lépés kimenetéből származó **_Regisztrációs azonosító_** és **_Ellenőrzőkulcs_** értékekre:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Ha szeretné, a kiépítési szolgáltatást a mintakódon keresztül is konfigurálhatja:
      - A konfiguráció mintához való hozzáadásához kövesse az alábbi lépéseket:
        1. Az [Azure Portalon](https://portal.azure.com) lépjen a kiépítési szolgáltatáshoz csatolt IoT Hubra. Nyissa meg a hubhoz tartozó **Áttekintés** lapot, és másolja ki az **Eszköznév** értékét. Rendelje hozzá ezt az **Eszköznév** értéket az *IOTHUB_HOST_NAME* paraméterhez.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Rendeljen egy könnyen megjegyezhető nevet a *DEVICE_ID* paraméterhez, és a *PROVISIONING_STATUS* paraméternél tartsa meg az alapértelmezett *ENABLED* értéket. 
    
      - VAGY, ha úgy dönt, hogy nem konfigurálja a kiépítési szolgáltatást, tegye megjegyzésbe vagy törölje a _ServiceEnrollmentSample.java_ fájl alábbi utasításait:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Tanulmányozza a mintakódot, Ez a kód egyéni TPM-eszközregisztrációt hoz létre, frissít, kérdez le és töröl. Ha ellenőrizni szeretné a regisztráció sikerességét a portálon, ideiglenesen tegye megjegyzésbe a _ServiceEnrollmentSample.java_ fájl végén található alábbi kódsorokat:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Mentse a _ServiceEnrollmentSample.java_ fájlt.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>A Java-mintakód létrehozása és futtatása

1. Nyisson meg egy parancsablakot, és lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** mappára.

2. Hozza létre a mintakódot a következő paranccsal:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Ez a parancs letölti a Maven-csomagot a [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) gépre. Ez a csomag tartalmazza a [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/)bináris fájljait, amelyeket a mintakód létrehozásához szükséges. 

3. Futtassa a mintát a következő parancsokkal a parancsablakban:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. A sikeres regisztráció érdekében kísérje figyelemmel a kimeneti ablakot. 

5. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Válassza a **regisztrációk kezelése** lehetőséget, és válassza az **Egyéni regisztrációk** lapot. Figyelje meg, hogy a szimulált TPM-eszköz *regisztrációs azonosítója* mostantól megjelenik. 

    ![Sikeres TPM-regisztráció ellenőrzése a portálon](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy feltárja a Java-szolgáltatás mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be a Java-minta kimeneti ablakát a gépen.
1. Zárja be a TPM-szimulátor ablakát, amely a TPM-eszköz szimulálásához jött létre.
1. Navigáljon a Azure Portal eszköz kiépítési szolgáltatásához, válassza a **regisztrációk kezelése** lehetőséget, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be a rövid útmutató segítségével létrehozott beléptetési bejegyzés *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban egy szimulált TPM-eszközt regisztrált az eszköz kiépítési szolgáltatásához. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával. 

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)