---
title: Oktatóanyag egyéni kiosztási szabályzatok Azure IoT Hub Device Provisioning Service (DPS) való használatához
description: Oktatóanyag egyéni kiosztási szabályzatok Azure IoT Hub Device Provisioning Service (DPS) való használatához
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f19f43b89cd2527a67827d7434f2e054ee40001e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227381"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Oktatóanyag: egyéni foglalási szabályzatok használata az eszközök kiépítési szolgáltatásával (DPS)

Az egyéni foglalási szabályzatok részletesebben szabályozzák, hogy az eszközök hogyan legyenek hozzárendelve az IoT hubhoz. Ez egy olyan [Azure-függvény](../azure-functions/functions-overview.md) egyéni kódjának használatával valósul meg, amely az eszközök IoT hubhoz való hozzárendeléséhez fut a kiépítés során. Az eszköz kiépítési szolgáltatása meghívja az Azure-függvény kódját, amely az eszközre és a regisztrációra vonatkozó összes releváns információt megadja. A rendszer végrehajtja a függvény kódját, és az eszköz kiépítési IoT hub-információit adja vissza.

Egyéni kiosztási szabályzatok használatával saját kiosztási szabályzatokat határozhat meg, ha az eszköz kiépítési szolgáltatása által biztosított szabályzatok nem felelnek meg a forgatókönyv követelményeinek.

Tegyük fel például, hogy meg szeretné vizsgálni az eszköz által a kiépítés során használt tanúsítványt, és hozzárendeli az eszközt egy IoT-hubhoz a Certificate tulajdonság alapján. Vagy előfordulhat, hogy az eszközök adatbázisában tárolt adatokkal rendelkezik, és le kell kérdezni az adatbázist annak meghatározásához, hogy melyik IoT hub-eszközt kell hozzárendelni.

Ez a cikk egy olyan egyéni foglalási házirenddel rendelkező beléptetési csoportot mutat be, amely egy C# nyelven írt Azure-függvényt használ a kenyérpirítók szimmetrikus kulcsokkal történő kiépítéséhez. Minden olyan eszköz, amely nem ismerhető fel kenyérpirítóként, nem lesz kiépítve egy IoT hubhoz.

Az eszközök az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-ban foglalt kiépítési mintakód használatával igényelnek kiépítési mintát.


Ebben az oktatóanyagban az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * Új Azure-függvényalkalmazás létrehozása egyéni foglalási funkció támogatásához
> * Új csoportos regisztráció létrehozása Azure-függvény használatával az egyéni kiosztási szabályzathoz
> * Kulcsok létrehozása két eszközhöz
> * A fejlesztési környezet beállítása például az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -ból származó eszköz kódja
> * Futtassa az eszközöket, és ellenőrizze, hogy az egyéni kiosztási szabályzatnak megfelelően van-e kiépítve


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk azt feltételezi, hogy elvégezte az IoT Hub és a DPS-példány létrehozásához szükséges [Azure Portal IoT hub Device Provisioning Service beállításának](./quick-setup-auto-provision.md) lépéseit.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

* Windowsos fejlesztési környezetben a [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019-es verziójának használatakor engedélyezve van az ["asztali fejlesztés C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) -ban" beállítás. A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* Linux vagy macOS esetén tekintse meg a [fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) dokumentációjának megfelelő szakaszát.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Az egyéni foglalási függvény létrehozása

Ebben a szakaszban egy Azure-függvényt hoz létre, amely megvalósítja az egyéni foglalási szabályzatot. Ez a függvény eldönti, hogy az eszköz regisztrálva van-e a IoT Hub, attól függően, hogy a regisztrációs azonosítója tartalmazza-e a **contoso-kenyérpirító karakterlánc-** előtagot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A kezdőlapon válassza az **+ erőforrás létrehozása** lehetőséget.

2. A *Keresés a piactéren* mezőbe írja be a "függvényalkalmazás" kifejezést. A legördülő listában válassza a **függvényalkalmazás** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

3. **Függvényalkalmazás** Létrehozás lap **alapok** lapján adja meg az új Function alkalmazás következő beállításait, majd válassza a **felülvizsgálat + létrehozás** lehetőséget:

    **Előfizetés**: Ha több előfizetéssel rendelkezik, és a kívánt előfizetés nincs kiválasztva, válassza ki a használni kívánt előfizetést.

    **Erőforráscsoport**: ebben a mezőben létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt, amely tartalmazza a Function alkalmazást. Válassza ki ugyanazt az erőforráscsoportot, amely a korábban teszteléshez létrehozott IOT hubot tartalmazza, például **TestResources**. Ha az összes kapcsolódó erőforrást egy csoportban helyezi el, akkor mindet együtt kezelheti.

    **Függvényalkalmazás neve**: adjon meg egy egyedi Function app-nevet. Ez a példa a **contoso-Function-app-** t használja.

    **Közzététel**: Ellenőrizze, hogy a **kód** ki van-e választva.

    **Futásidejű verem**: válassza a **.net Core** elemet a legördülő menüből.

    **Régió**: válassza ki ugyanazt a régiót, mint az erőforráscsoportot. Ez a példa az **USA nyugati** régióját használja.

    > [!NOTE]
    > Alapértelmezés szerint a Application Insights engedélyezve van. A Application Insights nem szükséges ehhez a cikkhez, de segíthet megérteni és megvizsgálni az egyéni foglalással kapcsolatban felmerülő problémákat. Ha szeretné, letilthatja Application Insights a **figyelés** lapon, majd a **nem** lehetőséget választva **engedélyezheti a Application Insights**.

    ![Azure-függvényalkalmazás létrehozása az egyéni foglalási funkció üzemeltetéséhez](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget a Function alkalmazás létrehozásához. Az üzembe helyezés eltarthat néhány percig. Ha befejeződik, válassza **az Ugrás erőforráshoz** lehetőséget.

5. A függvény bal oldali ablaktábláján **kattintson a** **függvények** elemre, majd a **+ Hozzáadás** lehetőségre egy új függvény hozzáadásához.

6. A sablonok lapon válassza a http- **trigger** csempét, majd kattintson a **create Function (függvény létrehozása**) elemre. Létrejön egy **HttpTrigger1** nevű függvény, és a portál megjeleníti a függvény áttekintés lapját.

7. Kattintson az új függvény **Code + test (kód + tesztelés** ) elemére. A portál megjeleníti a **Run. CSX** fájl tartalmát. 

8. Cserélje le a **HttpTrigger1** függvény kódját a következő kódra, majd válassza a **Mentés** lehetőséget. Az egyéni foglalási kód készen áll a használatra.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. A **Run. CSX** fájl alján kattintson a **naplók** elemre az egyéni foglalási függvény naplózásának figyeléséhez. 


## <a name="create-the-enrollment"></a>A regisztráció létrehozása

Ebben a szakaszban egy új beléptetési csoportot fog létrehozni, amely az egyéni kiosztási szabályzatot használja. Az egyszerűség kedvéért ez a cikk [szimmetrikus kulcsú tanúsítványokat](concepts-symmetric-key-attestation.md) használ a beléptetéshez. A biztonságosabb megoldás érdekében érdemes lehet az [X. 509 tanúsítvány-igazolást](concepts-x509-attestation.md) használni egy megbízhatósági lánc használatával.

1. Még mindig a [Azure Portalon](https://portal.azure.com)nyissa meg a kiépítési szolgáltatást.

2. Válassza a **regisztrációk kezelése** lehetőséget a bal oldali ablaktáblán, majd kattintson a **regisztrációs csoport hozzáadása** gombra az oldal tetején.

3. A **regisztrációs csoport hozzáadása** oldalon adja meg az alábbi táblázatban szereplő információkat, majd kattintson a **Save (Mentés** ) gombra.

    | Mező | Leírás és/vagy javasolt érték |
    | :---- | :----------------------------- |
    | **Csoport neve** | Adja meg a **contoso-Custom-lefoglalt-Devices** |
    | **Igazolás típusa** | **Szimmetrikus kulcs** kiválasztása |
    | **Kulcsok automatikus generálása** | Ezt a jelölőnégyzetet már be kell jelölni. |
    | **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a hubokhoz** | Válassza **az egyéni (Azure-függvény használata) lehetőséget.** |
    | **Válassza ki azokat a IoT hubokat, amelyekre ez a csoport hozzá lehet rendelni** | Válassza ki a korábban létrehozott IoT hubot a gyors üzembe helyezés befejezése után. |
    | **Azure-függvény kiválasztása** | Válassza ki azt az előfizetést, amely a létrehozott Function alkalmazást tartalmazza. Ezután válassza ki a **contoso-Function-app** és a **HttpTrigger1** a függvényhez. |

    ![Egyéni foglalási beléptetési csoport hozzáadása a szimmetrikus kulcs igazolásához](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Miután mentette a beléptetést, nyissa meg újra, és jegyezze fel az **elsődleges kulcsot**. Először mentenie kell a beléptetést, hogy a kulcsok létrejöttek legyenek. Ezt az elsődleges szimmetrikus kulcsot fogja használni a rendszer a később üzembe helyezést megkísérlő eszközök egyedi eszköz-kulcsainak létrehozására. 

## <a name="derive-unique-device-keys"></a>Egyedi eszköz kulcsának származtatása

Az eszközök nem használják közvetlenül az elsődleges szimmetrikus kulcsot. Ehelyett az elsődleges kulcsot kell használnia az egyes eszközökhöz tartozó eszköz kulcsának származtatása érdekében. Ebben a szakaszban két egyedi eszköz kulcsát hozza létre. Egy szimulált kenyérpirító eszközhöz egy kulcs lesz használatban. A rendszer egy szimulált hőszivattyús eszközhöz használja a másik kulcsot. A generált kulcsok lehetővé teszik mindkét eszköz számára a regisztrálást. Csak egy eszköz regisztrációs azonosítója érvényes utótaggal fog rendelkezni az egyéni foglalási szabályzat (például kód) számára. Ennek eredményeképpen az egyik el lesz fogadva, a másik pedig elutasítva

Az eszköz kulcsának kiszámításához a korábban feljegyzett szimmetrikus kulcsot kell használnia, hogy kiszámítsa az eszköz regisztrációs AZONOSÍTÓjának [HMAC](https://wikipedia.org/wiki/HMAC) az egyes eszközökön, és az eredményt Base64 formátumra alakítsa át. A származtatott eszközök regisztrációs csoportokkal való létrehozásával kapcsolatos további információkért tekintse meg a [szimmetrikus kulcs igazolásának](concepts-symmetric-key-attestation.md)csoportos regisztrációk című szakaszát.

A cikkben szereplő példában a következő két eszköz regisztrációs azonosítóját használja az alábbi kóddal, hogy mindkét eszközhöz kiszámítsa az eszköz kulcsát:

* **contoso – kenyérpirító – 007**
* **contoso-heatpump-088**

Cserélje le a **Key** változó értékét a regisztrációs csoport létrehozása után korábban feljegyzett **elsődleges kulcsra** . Az alábbi kóddal megjelenített kulcs értéke és kimenete csak példa.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha Windows-alapú munkaállomást használ, a PowerShell használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Ha Linux-munkaállomást használ, az OpenSSL használatával hozhatja elő a származtatott eszköz kulcsait, ahogy az a következő bash-példában is látható.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Az eszközök az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-ban foglalt kiépítési mintakód használatával igényelnek kiépítési mintát.

Ebben a szakaszban az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)létrehozásához használt fejlesztési környezetet készíti elő. Az SDK tartalmazza a szimulált eszközhöz tartozó mintakód kódját. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

Ez a szakasz a Windows-alapú munkaállomás irányába mutat. Linux-példaként tekintse meg a virtuális gépek beállítása a [bérlős](how-to-provision-multitenant.md)való kiépítésével foglalkozó témakört.

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

5. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Ha `cmake` nem találja a C++ fordítót, előfordulhat, hogy a parancs futtatásakor hibákat fog kiépíteni. Ha ez történik, próbálja meg futtatni a parancsot a [Visual Studio-parancssorban](/dotnet/framework/tools/developer-command-prompt-for-vs).

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Az eszközök szimulálása

Ebben a szakaszban egy, a korábban beállított Azure IoT C SDK-ban található **prov \_ dev \_ Client \_ minta** nevű kiépítési mintát frissít.

Ez a mintakód szimulál egy eszköz rendszerindítási sorozatot, amely elküldi a kiépítési kérést az eszköz kiépítési szolgáltatásának példányára. A rendszerindítási folyamat azt eredményezi, hogy a kenyérpirító eszköz fel lesz ismerve, és hozzá lesz rendelve az IoT hub-hoz az egyéni kiosztási házirend használatával.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg azt a **azure_iot_sdks. SLN** -megoldást, amelyet korábban a CMAK futtatása hozott létre. A megoldásfájlnak a következő helyen kell lennie:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg róla, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, mint az alább is látható:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. A `main()` függvényben keresse meg a hívást a következőhöz: `Prov_Device_Register_Device()` . Közvetlenül a hívás előtt adja hozzá a következő sornyi kódot, amely használatával [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) Egyéni JSON-adattartalmat adhat át a kiépítés során. Ezzel további információkat adhat meg az egyéni foglalási funkciókhoz. Ez az eszköz típusának átadására is használható a regisztrációs azonosító vizsgálata helyett. További információ az egyéni adattartalomnak a DPS használatával történő elküldéséről és fogadásáról: [adattartalom átvitele az eszközök és a DPS között](how-to-send-additional-data.md).

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget.

### <a name="simulate-the-contoso-toaster-device"></a>A contoso kenyérpirító eszközének szimulálása

1. A kenyérpirító eszköz szimulálásához keresse meg a " `prov_dev_set_symmetric_key_info()` **prov \_ dev \_ Client \_ sample. c** " hívását, amely kommentálva van.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Adja meg a függvény hívását, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket is) a kenyérpirító regisztrációs azonosítójával és a korábban létrehozott származtatott eszköz kulccsal. A **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** alább látható kulcs értéke csak példaként van megadva.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésének megadásához válassza az **Igen** lehetőséget, ha a Futtatás előtt szeretné újraépíteni a projektet.

    A következő szöveg például a Toaster-eszközön futó egyéni foglalási függvény kódjának naplózási kimenetét jeleníti meg. Figyelje meg, hogy egy központi központ sikeresen ki van választva egy kenyérpirító eszköz számára. Figyelje meg azt a `payload` tagot is, amely a kódhoz hozzáadott egyéni JSON-tartalmat tartalmazza. Ez elérhető a kód számára a alkalmazásban `deviceRuntimeContext` .

    Ez a naplózás a portálon a függvények kódja alatt található **naplók** elemre kattintva érhető el:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    A következő példában az eszköz kimenete azt mutatja, hogy a szimulált kenyérpirító eszköz sikeresen elindult, és csatlakozik a kiépítési szolgáltatáshoz, amelyet az egyéni kiosztási szabályzat rendel hozzá a kenyérpirítók IoT hubhoz:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>A contoso hőszivattyú eszköz szimulálása

1. A hőszivattyús eszköz szimulálásához frissítse a `prov_dev_set_symmetric_key_info()` **prov \_ dev \_ Client \_ sample. c** -ben megjelenő HÍVÁST, és a hőszivattyú regisztrációs azonosítóját és a korábban létrehozott származtatott eszköz kulcsát. A **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** alább látható kulcs értéke szintén csak példaként van megadva.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésének megadásához válassza az **Igen** lehetőséget a projekt újraépítéséhez a futtatása előtt.

    Az alábbi szöveg a hőszivattyú-eszközön futó egyéni foglalási funkció kódjából történő naplózást szemlélteti. Az egyéni kiosztási szabályzat elutasítja ezt a regisztrációt a 400-es HTTP-hiba esetén. Figyelje `payload` meg, hogy melyik tag tartalmazza a kódhoz hozzáadott egyéni JSON-tartalmat. Ez elérhető a kód számára a alkalmazásban `deviceRuntimeContext` .

    Ez a naplózás a portálon a függvények kódja alatt található **naplók** elemre kattintva érhető el:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    A következő példában az eszköz kimenete mutatja a szimulált hőszivattyús eszközt, és csatlakozik a kiépítési szolgáltatási példányhoz, hogy megpróbáljon regisztrálni egy IoT hub-ra az egyéni kiosztási házirend használatával. Ez a hiba () miatt meghiúsul, `Custom allocation failed with status code: 400` mivel az egyéni kiosztási házirend csak a Toaster-eszközöket engedélyezi:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a jelen cikkben létrehozott erőforrásokkal való munkát, meghagyhatja őket. Ha nem tervezi tovább használni az erőforrásokat, a következő lépésekkel törölheti az ebben a cikkben létrehozott összes erőforrást a szükségtelen költségek elkerülése érdekében.

Az itt leírt lépések azt feltételezik, hogy a cikkben szereplő összes erőforrást a **contoso-US-Resource-Group** nevű erőforráscsoport utasításai szerint hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport törlése név szerint:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **szűrés név szerint...** szövegmezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét, a **contoso-US-Resource-Group** nevet. 

3. Az erőforráscsoport jobb oldalán, az eredmények listájában válassza a **...** , majd az **erőforráscsoport törlése** elemet.

4. A rendszer kérni fogja, hogy erősítse meg az erőforráscsoport törlését. A megerősítéshez írja be ismét az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Részletesebb egyéni kiosztási szabályzatot például a következő témakörben talál: 

> [!div class="nextstepaction"]
> [Egyéni kiosztási szabályzatok használata](how-to-use-custom-allocation-policies.md)

* További információ:

> [!div class="nextstepaction"]
> [A IoT Hub eszköz újraépítésével kapcsolatos fogalmak](concepts-device-reprovision.md)

* További részletekért lásd:

> [!div class="nextstepaction"]
> [Korábban kiépített eszközök megszüntetése](how-to-unprovision-devices.md)