---
title: 'Rövid útmutató – Szimmetrikus kulcs használata eszköz üzembe helyezéséhez Azure IoT Hub C használatával #'
description: Ebben a rövid útmutatóban a Device Provisioning Service (DPS) C# eszközoldali SDK-ját fogja használni egy szimmetrikus kulcsos eszköz üzembe helyezéséhez egy IoT Hubon
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: a38d58eecd200ec312e7677a05531e27bf4ba6b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868698"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Rövid útmutató: Szimmetrikus kulcsos eszköz kiépítése a C használatával #

Ebből a rövid útmutatóból megtudhatja, hogyan lehet kiépítni egy Windows rendszerű fejlesztői gépet eszközként egy IoT Hubon a C# használatával. Ez az eszköz szimmetrikus kulcsot és egyéni regisztrációt használ a Device Provisioning Service- (DPS-) példányokkal való hitelesítéshez az IoT Hubhoz való hozzárendeléshez. A [C#-hoz használt Azure IoT-minták](https://github.com/Azure-Samples/azure-iot-samples-csharp) mintakódja lesz használva az eszköz üzembe építéséhez. 

Bár ez a cikk az egyéni regisztrációval való kiépítést mutatja be, regisztrációs csoportokat is használhat. A regisztrációs csoportok használata között van néhány különbség. Például egy származtatott eszközkulcsot kell használnia az eszköz egyedi regisztrációs azonosítójával. [Az eszközök szimmetrikus kulcsokkal való kiépítése](how-to-legacy-device-symm-key.md) egy regisztrációs csoportra mutat példát. A regisztrációs csoportokkal kapcsolatos további információkért lásd: [Group Enrollments for Symmetric Key Attestation](concepts-symmetric-key-attestation.md#group-enrollments)( Csoportos regisztrációk szimmetrikus kulcsú igazoláshoz).

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át a [kiépítés áttekintését.](about-iot-dps.md#provisioning-process) 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Egy Linux-példáért lásd: [Kiépítés több-bérlős környezethez.](how-to-provision-multitenant.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a Windows-alapú gépen telepítve van a [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download) vagy újabb verzió.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Eszközregisztrálás létrehozása

1. Jelentkezzen be a Azure Portal [a](https://portal.azure.com)  bal oldali menüben válassza az Összes erőforrás gombot, és nyissa meg a Device Provisioning Service-példányt (DPS).

2. Válassza a **Regisztrációk kezelése** lapot, majd kattintson felül az **Egyéni** regisztráció hozzáadása gombra. 

3. A Regisztráció **hozzáadása panelen** adja meg a következő adatokat, majd kattintson a **Mentés gombra.**

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Kulcsok automatikus létrehozása:** Jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például: **symm-key-csharp-device-01**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. Például: **csharp-device-01**.

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. A regisztráció mentése után létrejön  az  elsődleges kulcs és a másodlagos kulcs, és hozzáadódik a regisztrációs bejegyzéshez. A szimmetrikus kulcsú eszközregisztráció **symm-key-csharp-device-01** néven jelenik meg az Egyéni regisztrációk lap Regisztrációs azonosító *oszlopában.*  

5. Nyissa meg a regisztrációt, és  másolja ki a létrehozott elsődleges és másodlagos kulcs **értékét.** Ezt a kulcsértéket  és a regisztrációs azonosítót később fogja használni, amikor környezeti változókat ad hozzá az eszközregisztrációs mintakódhoz.



## <a name="prepare-the-c-environment"></a>A C#-környezet előkészítése 

1. Nyisson meg egy Git CMD- vagy Git Bash parancssori környezetet. Klónozza [az Azure IoT-mintákat a C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-adattárhoz a következő paranccsal:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Az eszköz kiépítési kódának előkészítése

Ebben a szakaszban a következő négy környezeti változót fogja hozzáadni, amelyek paraméterekként lesznek használva az eszközátépítési mintakódhoz a szimmetrikus kulcsos eszköz üzembe hozása érdekében. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

A kiépítési kód ezen változók alapján kapcsolatba lép a DPS-példánytal az eszköz hitelesítése érdekében. Az eszköz ezután hozzá lesz rendelve egy, a DPS-példányhoz már társított IoT Hubhoz az egyéni regisztrációs konfiguráció alapján. A kiépítés után a mintakód teszt-telemetriát küld az IoT Hubnak.

1. A [Azure Portal](https://portal.azure.com)Device Provisioning Service menüjében válassza az **Áttekintés** lehetőséget, és másolja ki a _szolgáltatásvégpontot_ és az _azonosító hatókörét._ Ezeket az értékeket fogja használni a `PROVISIONING_HOST` és `DPS_IDSCOPE` a környezeti változókhoz.

    ![Szolgáltatás adatai](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Nyisson meg egy parancssort, és navigáljon a *SymmetricKeySample* fájlhoz a klónozott mintatárban:

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. A *SymmetricKeySample mappában* nyissa meg a *Program.cs* fájlt egy szövegszerkesztőben, és keresse meg a és a sztringet beállító `individualEnrollmentPrimaryKey` `individualEnrollmentSecondaryKey` kódsorokat. Frissítse ezeket a kódsorokat az alábbiak szerint, hogy a rendszer a környezeti változókat használja a kulcsok szoftveres kódolása helyett.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "&quot;;
        //private const string individualEnrollmentSecondaryKey = &quot;&quot;;

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable(&quot;PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Keresse meg azt a kódsort is, amely beállítja a sztringet, és frissítse a következőképpen, hogy környezeti változót is használjon `registrationId` az alábbiak szerint:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    Mentse a *Program.cs fájl módosításait.*

3. A parancssorban adja hozzá az azonosító hatóköre, a regisztrációs azonosító, az elsődleges és a másodlagos szimmetrikus kulcsok környezeti változóit, amelyek az előző szakaszban az egyéni regisztrációból másoltak ki.  

    Az alábbi parancsok például bemutatják a parancs szintaxisát. Ügyeljen arra, hogy a megfelelő értékeket használja.

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


4. Az alábbi paranccsal készítse el és futtassa a mintakódot.

    ```console
    dotnet run
    ```

5. A várt kimenetnek az alábbihoz hasonlóan kell kinéznie, amely azt a társított IoT Hubot mutatja, amelyhez az eszköz hozzá lett rendelve az egyéni regisztrációs beállítások alapján. Tesztként egy "TestMessage" sztringet küld a központnak:

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
    
6. A Azure Portal keresse meg a kiépítési szolgáltatáshoz csatolt IoT Hubot, és nyissa meg az **IoT-eszközök panelt.** Miután sikeresen kiépíti a szimmetrikus kulcsos eszközt a hubon, az eszköz azonosítója a *STATUS* (ÁLLAPOT) beállítással engedélyezve **lesz.** Előfordulhat, hogy az  eszköz mintakód futtatása előtt meg kell nyitnia a panel tetején található Frissítés gombot. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszköz ügyfélmintán való munkát és annak felfedezését, akkor ne tisztítsa meg az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást.

1. A bal oldali menüben válassza Azure Portal **Összes** erőforrás lehetőséget, majd a Device Provisioning Service-t. Nyissa **meg a szolgáltatás Regisztrációk** kezelése lapját, majd válassza az Egyéni **regisztrációk** lapot. Jelölje be a rövid útmutatóban regisztrált eszköz *REGISZTRÁCIÓS AZONOSÍTÓJA* melletti jelölőnégyzetet, majd kattintson a panel tetején található Törlés gombra.  
1. A bal oldali menüben válassza a Azure Portal az Összes erőforrás **lehetőséget,** majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközeit,**  jelölje be a rövid útmutatóban regisztrált eszköz eszközazonosítója melletti jelölőnégyzetet, majd kattintson a panel tetején található Törlés gombra. 

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Windows-alapú szimmetrikus kulcsos eszközt létesített az IoT Hubon a IoT Hub Device Provisioning Service. Ha meg szeretne ismerkedni az X.509-tanúsítványeszközök C# használatával való üzembe helyezéssel, folytassa az alábbi, X.509-eszközökre vonatkozó rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X.509-eszközök kiépítése DPS és C használatával #](quick-create-simulated-device-x509-csharp.md)
