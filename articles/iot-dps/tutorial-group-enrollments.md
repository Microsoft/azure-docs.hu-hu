---
title: Oktatóanyag – szimulált X. 509 eszköz kiépítése az Azure IoT Hub Javával és regisztrációs csoportok használatával
description: Ebben az oktatóanyagban egy szimulált X. 509 eszköz létrehozása és kiépítése a Java-eszköz és a szolgáltatás-SDK és a regisztrációs csoportok használatával IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4cfbfe3e3e3ba620d8292767012c9bb866d8a878
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968094"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Oktatóanyag: szimulált X. 509 eszköz létrehozása és kiépítése a Java-eszköz és a Service SDK használatával, valamint csoportos regisztrációkkal IoT Hub Device Provisioning Service

Ezek a lépések bemutatják, hogyan hozhat létre szimulált X.509-eszközt egy Windows operációs rendszert futtató fejlesztői gépen, és hogyan használhat egy kódmintát, hogy ezt a szimulált eszközt regisztrációs csoportok segítségével összekösse a Device Provisioning Service-szel és az IoT Hubbal. 

A folytatás előtt végezze el az [IoT Hub Device Provisioning Service beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit.


## <a name="prerequisites"></a>Előfeltételek

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) telepítve van a gépén.

1. Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. A tesztelési tanúsítványok létrehozásához használja az alábbi [tanúsítvány-áttekintést](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) .

    > [!NOTE]
    > Ez a lépés az [OpenSSL](https://www.openssl.org/)-t igényli, amely a forrástól, illetve a külső [féltől](https://wiki.openssl.org/index.php/Binaries) származó, illetve azokból letölthető és [telepíthető.](https://sourceforge.net/projects/openssl/) Ha már létrehozta a _fő-_, _köztes_ és _eszköztanúsítványt_, kihagyhatja ezt a lépést.
    >

    1. A _fő-_ és a _köztes_ tanúsítvány létrehozásához végezze el az első két lépést.

    1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatását.

        1. Az eszközkiépítési szolgáltatás összefoglaló panelén válassza a **Tanúsítványok** lehetőséget, és kattintson a felül található **Hozzáadás** gombra.

        1. A **Tanúsítvány hozzáadása** területen adja meg a következő információkat:
            - Adjon meg egy egyedi tanúsítványnevet.
            - Válassza ki a létrehozott **_RootCA. PEM_** fájlt.
            - Ha végzett, kattintson a **Mentés** gombra.

           ![Tanúsítvány hozzárendelése](./media/tutorial-group-enrollments/add-certificate.png)

        1. Válassza ki az újonnan létrehozott tanúsítványt:
            - Kattintson az **Ellenőrző kód létrehozása** elemre. Másolja ki a kapott kódot.
            - Futtassa az ellenőrzést. Adja meg az _ellenőrző kódot_, vagy kattintson a jobb gombbal, hogy beillessze a PowerShell-ablakba.   Nyomja le az **Enter** billentyűt.
            - Válassza ki az újonnan létrehozott **_verifyCert4.pem_** fájlt az Azure Portalon. Kattintson az **Ellenőrzés** lehetőségre.

              ![Tanúsítvány ellenőrzése](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Végezetül futtassa az eszköztanúsítványok létrehozásához és a felesleges erőforrások törléséhez szükséges műveleteket.

       > [!NOTE]
       > Az eszköztanúsítványok létrehozásakor ügyeljen arra, hogy az eszköz nevében kizárólag kisbetűs alfanumerikus karaktereket, valamint kötőjeleket használjon.
       >


## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

1. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat a Java SDK-kódmintákhoz:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. A letöltött forráskódban keresse meg az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** mintamappát. Nyissa meg az **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** fájlt egy tetszőleges szövegszerkesztővel, és adja hozzá az alábbi részeket:

    1. Adja hozzá a `[Provisioning Connection String]` karakterláncot a kiépítési szolgáltatáshoz a portálról:

        1. Navigáljon a kiépítési szolgáltatáshoz a [Azure Portal](https://portal.azure.com).

        1. Nyissa meg a **Megosztott elérési szabályzatok** panelt, és válasszon ki egy szabályzatot, amely rendelkezik az *EnrollmentWrite* engedéllyel.

        1. Másolja ki az **elsődleges kulcs kapcsolati sztringjét**.

            ![A kiépítési kapcsolati sztring lekérése a portálról](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. A **_ServiceEnrollmentGroupSample.java_** mintakódfájlban cserélje le a `[Provisioning Connection String]` sztringet az **elsődleges kulcs kapcsolati sztringjére**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Nyissa meg a köztes aláíró tanúsítványfájl-fájlt egy szövegszerkesztőben. Frissítse az `PUBLIC_KEY_CERTIFICATE_STRING` értéket a köztes aláíró tanúsítvány értékével.

        Ha a bash rendszerhéjral generálta az eszköz tanúsítványait, a *./certs/Azure-IOT-test-only.Intermediate.CERT.PEM* tartalmazza a köztes tanúsítvány kulcsát. Ha a tanúsítványok a PowerShell-lel lettek létrehozva, a *./Intermediate1.PEM* lesz a köztes tanúsítványfájl.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. Az [Azure Portalon](https://portal.azure.com) lépjen a kiépítési szolgáltatáshoz csatolt IoT Hubra. Nyissa meg a hubhoz tartozó **Áttekintés** lapot, és másolja ki az **Eszköznév** értékét. Rendelje hozzá ezt az **Eszköznév** értéket az *IOTHUB_HOST_NAME* paraméterhez.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Tanulmányozza a mintakódot, A segítségével létrehozhat, frissíthet, lekérdezhet és törölhet egy csoportos X.509-eszközregisztrációt. Ha ellenőrizni szeretné a regisztráció sikerességét a portálon, ideiglenesen tegye megjegyzésbe a következő kódsorokat a _ServiceEnrollmentGroupSample.java_ fájl végén:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Mentse a _ServiceEnrollmentGroupSample.java_ fájlt.

1. Nyisson meg egy parancsablakot, és lépjen az **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** mappára.

1. Hozza létre a mintakódot a következő paranccsal:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Futtassa a mintát a következő parancsokkal a parancsablakban:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. A sikeres regisztráció érdekében kísérje figyelemmel a kimeneti ablakot.

    ![Sikeres regisztráció](./media/tutorial-group-enrollments/enrollment.png) 

1. Az Azure Portalon lépjen a kiépítési szolgáltatásra. Kattintson a **Regisztrációk kezelése** elemre. Az X.509-eszközök csoportjának a **Regisztrációs csoportok** lapon kell megjelennie egy automatikusan létrehozott *CSOPORTNÉV* alatt.

## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az eszköz kiépítési szolgáltatásának összefoglaló paneljén válassza az **Áttekintés** lehetőséget, és jegyezze fel az _azonosító hatókörét_ és a _kiépítési szolgáltatás globális végpontját_.

    ![Szolgáltatás adatai](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Nyisson meg egy parancssort. Lépjen a mintaprojekt mappájára.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Szerkessze `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` úgy, hogy tartalmazza az _azonosító hatókörét_ és a _kiépítési szolgáltatás globális végpontját_ , amelyet korábban feljegyzett.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Frissítse a `leafPublicPem` és a `leafPrivateKey` változókat a nyilvános és a privát eszköz tanúsítványával.

    Ha az eszköz tanúsítványait a PowerShell-lel generálta, a fájlok mydevice a * tartalmazza a nyilvános kulcsot, a titkos kulcsot és a PFX-t az eszközhöz.

    Ha a bash rendszerhéjral generálta az eszköz tanúsítványait, a./certs/New-Device.CERT.PEM a nyilvános kulcsot tartalmazza. Az eszköz titkos kulcsa a./Private/New-Device.key.PEM fájlban lesz.

    Nyissa meg a nyilvános kulcs fájlját, és frissítse a `leafPublicPem` változót ezzel az értékkel. Másolja ki a _-----BEGIN Private key-----_ _-----END titkos kulcs-----_.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Nyissa meg a titkos kulcsot tartalmazó fájlt, és frissítse a `leafPrivatePem` változót ezzel az értékkel. Másolja a szöveget a _-----BEGIN RSA titkos kulcs-----_ _-----End rsa titkos kulcs-----_.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Adjon hozzá egy új változót közvetlenül `leafPrivateKey` a köztes tanúsítványhoz. Nevezze el az új változót `intermediateKey` . Adja meg a köztes aláíró tanúsítvány értékét.

    Ha a bash rendszerhéjral generálta az eszköz tanúsítványait, a *./certs/Azure-IOT-test-only.Intermediate.CERT.PEM* tartalmazza a köztes tanúsítvány kulcsát. Ha a tanúsítványok a PowerShell-lel lettek létrehozva, a *./Intermediate1.PEM* lesz a köztes tanúsítványfájl.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. A `main` függvényben adja hozzá a `intermediateKey` t a `signerCertificates` gyűjteményhez az inicializálás előtt `securityProviderX509` .

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Mentse a módosításokat, és hozza létre a mintát. Lépjen a célmappára, és futtassa a létrehozott jar-fájlt.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Sikeres regisztráció](./media/tutorial-group-enrollments/registration.png)

1. A portálon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Ha sikeresen kiépíti a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik a **Device Explorer** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz. Ha már a minta eszközalkalmazás futtatása előtt megnyitotta a panelt, akkor lehet, hogy rá kell kattintania a fenti **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszközügyfél minta használatát és megismerését, akkor ne törölje a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **regisztrációk kezelése** paneljét, majd kattintson az **Egyéni regisztrációk** fülre. Válassza ki az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítóját* , majd kattintson a felül található **Törlés** gombra. 
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** paneljét, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban egy szimulált X.509-eszközt hozott létre Windows rendszerű gépén, majd kiépítette az IoT Hubon az Azure IoT Hub Device Provisioning Service-szel és regisztrációs csoportokkal. Ha további információra van szüksége az X.509-eszközzel kapcsolatban, folytassa az eszközzel kapcsolatos alapvető információkat ismertető témakörrel. 

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service fogalmak](concepts-service.md)