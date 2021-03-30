---
title: 'Rövid útmutató – szimmetrikus kulcs használata eszköz Azure-IoT Hub való kiépítéséhez C használatával #'
description: Ebben a rövid útmutatóban a C# eszközoldali SDK-t fogja használni az eszköz kiépítési szolgáltatásához (DPS) egy szimmetrikus kulcsú eszköz IoT-hubhoz való kiépítéséhez.
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: f97840a05115bf5659a6f7579b72786e890051a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92429369"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Gyors útmutató: szimmetrikus kulcsú eszköz kiépítése C használatával #

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy Windows-fejlesztő gépet eszközként egy IoT hub-ben a C# használatával. Ez az eszköz egy szimmetrikus kulcsot és egy egyéni regisztrációt használ egy eszköz-kiépítési szolgáltatás (DPS) példányának hitelesítéséhez ahhoz, hogy IoT hubhoz rendeljen hozzá. Az eszköz kiépítéséhez a C#-hoz készült [Azure IoT-mintákból](https://github.com/Azure-Samples/azure-iot-samples-csharp) származó mintakód lesz használva. 

Bár ez a cikk azt mutatja be, hogy a kiépítés egyedi regisztrációval történt, a beléptetési csoportokat is használhatja. A regisztrációs csoportok használata néhány eltérést is igénybe vehet. Például egy származtatott eszköz kulcsát kell használnia az eszköz egyedi regisztrációs azonosítójával. [A szimmetrikus kulccsal rendelkező eszközök kiépítése](how-to-legacy-device-symm-key.md) beléptetési csoportra vonatkozó példát tartalmaz. A beléptetési csoportokkal kapcsolatos további információkért lásd: a [szimmetrikus kulcsú tanúsítványok igénylésének csoportosítása](concepts-symmetric-key-attestation.md#group-enrollments).

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át a [kiépítés](about-iot-dps.md#provisioning-process) áttekintését. 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-Példákért lásd: [bérlős létesítése](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a [.net Core 2,1 SDK](https://www.microsoft.com/net/download/windows) vagy újabb verziója telepítve van a Windows-alapú gépen.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza a bal oldali menüben az **összes erőforrás** gombot, és nyissa meg az eszköz kiépítési szolgáltatásának (DPS) példányát.

2. Válassza a **regisztrációk kezelése** fület, majd válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

3. A **beléptetés hozzáadása** panelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Kulcsok automatikus generálása**: jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például: **Symm-Key-csharp-Device-01**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. Például: **csharp-Device-01**.

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. Miután mentette a regisztrációt, a rendszer létrehozza az **elsődleges kulcsot** és a **másodlagos kulcsot** , és hozzáadja a beléptetési bejegyzéshez. A szimmetrikus kulcsú eszközök beléptetése **Symm-Key-csharp-Device-01** néven jelenik meg az *Egyéni* regisztrációk lap *regisztrációs azonosító* oszlopában. 

5. Nyissa meg a beléptetést, és másolja ki a generált **elsődleges kulcs** és **másodlagos kulcs** értékét. Ezt a kulcs értéket és a **regisztrációs azonosítót** később fogja használni, amikor környezeti változókat ad hozzá az eszköz kiépítési mintakód használatával történő használathoz.



## <a name="prepare-the-c-environment"></a>A C#-környezet előkészítése 

1. Nyisson meg egy git CMD vagy git bash parancssori környezetet. A következő parancs használatával klónozott [Azure IoT-mintákat a C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-adattárhoz:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Az eszköz üzembe helyezési kódjának előkészítése

Ebben a szakaszban a következő négy környezeti változót adja hozzá, amelyek paraméterként lesznek felhasználva az eszköz kiépítési mintakód számára a szimmetrikus kulcsos eszköz kiépítéséhez. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

A kiépítési kód az eszköz hitelesítéséhez a következő változók alapján felveszi a kapcsolatot a DPS-példánnyal. Az eszköz ezután hozzá lesz rendelve egy olyan IoT hubhoz, amely az egyéni regisztrációs konfiguráció alapján már össze van kapcsolva a DPS-példánnyal. A kiépítés után a mintakód egy teszt telemetria küld az IoT hub-nak.

1. A [Azure Portal](https://portal.azure.com)eszköz kiépítési szolgáltatás menüjében válassza az **Áttekintés** lehetőséget, és másolja a _szolgáltatási végpontot_ és az _azonosító hatókörét_. Ezeket az értékeket fogja használni a `PROVISIONING_HOST` és `DPS_IDSCOPE` környezeti változókhoz.

    ![Szolgáltatás adatai](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Nyisson meg egy parancssort, és navigáljon a klónozott minták tárházában található *SymmetricKeySample* :

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. A *SymmetricKeySample* mappában Nyissa meg a *program. cs* szöveget egy szövegszerkesztőben, és keresse meg a `individualEnrollmentPrimaryKey` és karakterláncokat beállító kód sorait `individualEnrollmentSecondaryKey` . A következő módon frissítse a kód sorait, hogy a rendszer a kulcsok kódolása helyett a környezeti változókat használja.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "";
        //private const string individualEnrollmentSecondaryKey = "";

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable("PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Továbbá keresse meg a karakterláncot megadó kódot, `registrationId` és a következőképpen frissítse azt a környezeti változó használatára is a következő módon:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    Mentse a *program. cs* fájl módosításait.

3. A parancssorban adja hozzá az azonosító hatókör, a regisztrációs azonosító, az elsődleges és a másodlagos szimmetrikus kulcsok környezeti változóit, amelyeket az előző szakaszban lévő egyéni regisztrációból másolt.  

    A következő parancsok példák a parancs szintaxisának megjelenítésére. Ügyeljen arra, hogy a megfelelő értékeket használja.

    ```console
    set DPS_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-csharp-device-01
    ```

    ```console
    set PRIMARY_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

    ```console
    set SECONDARY_SYMMETRIC_KEY=Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
    ```


4. Hozza létre és futtassa a mintakód használatát a következő paranccsal.

    ```console
    dotnet run
    ```

5. A várt kimenetnek az alábbihoz hasonlóan kell kinéznie, amely megjeleníti a csatolt IoT hubot, amelyet az eszköz az egyéni regisztrációs beállítások alapján rendelt hozzá. Egy példa "TestMessage" karakterláncot kap a Központ számára tesztként:

    ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run
    RegistrationID = symm-key-csharp-device-01
    ProvisioningClient RegisterAsync . . . Assigned
    ProvisioningClient AssignedHub: docs-test-iot-hub.azure-devices.net; DeviceID: csharp-device-01
    Creating Symmetric Key DeviceClient authentication
    DeviceClient OpenAsync.
    DeviceClient SendEventAsync.
    DeviceClient CloseAsync.
    Enter any key to exit
    ```
    
6. A Azure Portal navigáljon a kiépítési szolgáltatáshoz társított IoT hubhoz, és nyissa meg a **IoT-eszközök** panelt. Miután sikeresen kiépítte a szimmetrikus kulcs eszközét a központba, az eszköz azonosítója **engedélyezett** *állapottal* jelenik meg. Előfordulhat, hogy a felül lévő **frissítés** gombra kell kattintania, ha már megnyitotta a panelt az eszköz mintakód futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációinak kezelése** elemet, majd válassza az **Egyéni regisztrációk** lapot. Jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközöket** a központhoz, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Windows-alapú szimmetrikus kulcsú eszközt telepített a IoT hub-hoz a IoT Hub Device Provisioning Service használatával. Az x. 509 tanúsítványok C# használatával történő kiépítésének megismeréséhez folytassa az alábbi rövid útmutatót X. 509 eszközök esetén. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök kiépítése a DPS és a C használatával #](quick-create-simulated-device-x509-csharp.md)
