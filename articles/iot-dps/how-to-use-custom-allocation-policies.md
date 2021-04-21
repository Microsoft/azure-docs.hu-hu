---
title: Egyéni kiosztási szabályzatok Azure IoT Hub Device Provisioning Service-sel
description: Egyéni kiosztási szabályzatok használata a Azure IoT Hub Device Provisioning Service (DPS) szolgáltatással
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1432aee341509d8a5bdc9fffe89dd9bad33fc7de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766478"
---
# <a name="how-to-use-custom-allocation-policies"></a>Egyéni kiosztási szabályzatok használata

Az egyéni kiosztási szabályzatokkal jobban szabályozható, hogyan vannak hozzárendelve az eszközök az IoT Hubhoz. Ez úgy valósítható meg, hogy egyéni kódot használ egy [Azure-függvényben](../azure-functions/functions-overview.md) az eszközök IoT Hubhoz való hozzárendeléséhez. Az eszközregisztrációs szolgáltatás az Azure-függvény kódját hívja meg, és minden releváns információt biztosít az eszközről és a regisztrációról. A rendszer végrehajtja a függvénykódot, és visszaadja az eszköz üzembe építéséhez használt IoT Hub-információkat.

Egyéni kiosztási szabályzatok használatával saját kiosztási szabályzatokat határozhat meg, ha a Device Provisioning Service által biztosított szabályzatok nem biztosítanak megfelelőt a forgatókönyv követelményeinek.

Előfordulhat például, hogy meg szeretné vizsgálni az eszköz által a kiépítés során használt tanúsítványt, és egy tanúsítványtulajdonság alapján hozzá szeretné rendelni az eszközt egy IoT Hubhoz. Az is lehetséges, hogy az eszközök adatbázisában tárolt adatok alapján le kellkérdezni az adatbázist, hogy megállapítsa, melyik IoT Hubhoz kell hozzárendelni az eszközt.

Ez a cikk egy C# nyelven írt Azure-függvényt használó egyéni kiosztási szabályzatot mutat be. Két új IoT-központ jön létre, amelyek a *Contoso Toasters részleget* és a *Contoso Heat Pump Részleget képviselik.* A kiépítést kérő eszközöknek rendelkezniük kell egy regisztrációs azonosítóval, amely tartalmazza az alábbi utótagok egyikét a kiépítéshez:

* **-contoso-tstrsd-007:** Contoso Toasters részleg
* **-contoso-hpsd-088:** Contoso Heat Pump Division

Az eszközök a regisztrációs azonosító egyik kötelező utótagja alapján lesznek kiépítve. Ezek az eszközök az Azure IoT C SDK-ban található kiépítési mintával [lesznek szimulálva.](https://github.com/Azure/azure-iot-sdk-c)

A cikkben a következő lépéseket kell végrehajtania:

* Az Azure CLI használatával hozzon létre két Részleg IoT Hubot **(Contoso Toasters részleg** és **Contoso Heat Pump-részleg)**
* Új csoportregisztráció létrehozása egy Azure-függvény használatával az egyéni kiosztási szabályzathoz
* Eszközkulcsok létrehozása két eszközszimulációhoz.
* Az Azure IoT C SDK fejlesztési környezetének beállítása
* Szimulálja az eszközöket, és ellenőrizze, hogy az egyéni kiosztási szabályzatban példakód alapján vannak-e kiépítve

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A windowsos fejlesztési környezetek előfeltételei a következők. Linux vagy macOS esetén tekintse [](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) meg az SDK dokumentációjának A fejlesztési környezet előkészítése című szakaszát.

- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019-es verzióban az ["Asztali fejlesztés C++ alkalmazással"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) számítási feladat engedélyezve van. Visual Studio 2015 és Visual Studio 2017 is támogatott.

- A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>A kiépítési szolgáltatás és két osztásos IoT-központ létrehozása

Ebben a szakaszban a Azure Cloud Shell egy kiépítési szolgáltatást és két IoT Hubot hoz létre, amelyek a **Contoso Toasters** részleget és a **Contoso Heat Pump részleget képviselik.**

> [!TIP]
> A cikkben használt parancsok létrehoznak egy kiépítési szolgáltatást és más erőforrásokat az USA nyugati régiója helyen. Javasoljuk, hogy a Device Provisioning Service-t támogató régióban hozza létre az erőforrásait. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. A parancsokban a helyek egyszavas vagy többszavas formátumban is megadva vannak; például: usa nyugati régiója, USA nyugati régiója, USA nyugati régiója stb. Az érték nem megkülönbözteti a kis- és nagybetűket. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `-- location "West US"`.
>

1. A Azure Cloud Shell hozzon létre egy erőforráscsoportot [az az group create paranccsal.](/cli/azure/group#az_group_create) Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

    Az alábbi példa egy *contoso-us-resource-group* nevű erőforráscsoportot hoz létre a *westus régióban.* Javasoljuk, hogy ezt a csoportot használja a cikkben létrehozott összes erőforráshoz. Ez a módszer megkönnyíti a tisztítást a munka befejezése után.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. A Azure Cloud Shell az [iot dps create](/cli/azure/iot/dps#az_iot_dps_create) paranccsal hozhat létre eszköz-kiépítési szolgáltatást (DPS). A kiépítési szolgáltatás a *contoso-us-resource-group csoporthoz lesz hozzáadva.*

    Az alábbi példa egy *contoso-provisioning-service-1098* nevű kiépítési szolgáltatást hoz létre a *westus* helyen. Egyedi szolgáltatásnevet kell használnia. A 1098 helyén a saját utótagját kell a **szolgáltatásnévben kiírni.**

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    A parancs befejezése eltarthat néhány percig.

3. Az paranccsal Azure Cloud Shell a **Contoso Toasters osztás** IoT Hubot [az az iot hub create paranccsal.](/cli/azure/iot/hub#az_iot_hub_create) Az IoT Hub a *contoso-us-resource-group csoporthoz lesz hozzáadva.*

    Az alábbi példa egy *contoso-toasters-hub-1098* nevű IoT Hubot hoz létre a *westus* helyen. Egyedi központnevet kell használnia. Az 1098 helyén a saját utótagját kell a központ **nevéből kiírni.** 

    > [!CAUTION]
    > Az egyéni kiosztási szabályzat azure-függvénykódja megköveteli a központ `-toasters-` nevének részsztringet. Ügyeljen arra, hogy a kötelező bejelentési karakterláncot tartalmazó nevet használjon.
    
    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs befejezése eltarthat néhány percig.

4. A Azure Cloud Shell az [iot hub create](/cli/azure/iot/hub#az_iot_hub_create) paranccsal hozza létre a **Contoso Heat Pump Division** IoT hubot. Ez az IoT Hub a *contoso-us-resource-group csoporthoz is hozzá lesz adva.*

    Az alábbi példa egy *contoso-heatpumps-hub-1098* nevű IoT-központot hoz létre a *westus helyen.* Egyedi központnevet kell használnia. Az 1098 helyén a saját utótagját kell a központ **nevéből kiírni.** 

    > [!CAUTION]
    > Az egyéni kiosztási szabályzat azure-függvénykódja megköveteli a központ `-heatpumps-` nevének részsztringet. Ügyeljen arra, hogy olyan nevet használjon, amely tartalmazza a szükséges heatpumps alsztringet.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs befejezése eltarthat néhány percig.

5. Az IoT Hubokat a DPS-erőforráshoz kell kapcsolni. 

    Futtassa az alábbi két parancsot az újonnan létrehozott központok kapcsolati sztringjének lekért értékével. Cserélje le a hub erőforrásneveket az egyes parancsokban kiválasztott nevekre:

    ```azurecli-interactive 
    hubToastersConnectionString=$(az iot hub connection-string show --hub-name contoso-toasters-hub-1098 --key primary --query connectionString -o tsv)
    hubHeatpumpsConnectionString=$(az iot hub connection-string show --hub-name contoso-heatpumps-hub-1098 --key primary --query connectionString -o tsv)
    ```

    Futtassa az alábbi parancsokat a központok DPS-erőforráshoz való csatolásához. Cserélje le a DPS-erőforrás nevét az egyes parancsokban választott névre:

    ```azurecli-interactive 
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubToastersConnectionString --location westus
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubHeatpumpsConnectionString --location westus
    ```




## <a name="create-the-custom-allocation-function"></a>Az egyéni kiosztási függvény létrehozása

Ebben a szakaszban létrehoz egy Azure-függvényt, amely megvalósítja az egyéni kiosztási szabályzatot. Ez a függvény dönti el, hogy melyik osztási IoT Hubon regisztrálja az eszközt annak alapján, hogy a regisztrációs azonosítója tartalmazza-e a **-contoso-tstrsd-007** vagy **a -contoso-hpsd-088 sztringet.** Az ikereszköz kezdeti állapotát is beállítja az alapján, hogy az eszköz egy bejelentési eszköz vagy hőkút.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A kezdőlapon válassza az **+ Erőforrás létrehozása lehetőséget.**

2. A Keresés *a Marketplace-en keresőmezőbe* írja be a "Függvényalkalmazás" kifejezéseket. A legördülő listában válassza a **Függvényalkalmazás** lehetőséget, majd a **Létrehozás lehetőséget.**

3. A **Függvényalkalmazás** létrehozása lap Alapvető **beállítások** lapján adja meg az alábbi beállításokat az új függvényalkalmazáshoz, majd válassza a Felülvizsgálat + **létrehozás lehetőséget:**

    **Erőforráscsoport:** Válassza ki a **contoso-us-resource-group** erőforráscsoportot, hogy a cikkben létrehozott összes erőforrás együtt maradjon.

    **Függvényalkalmazás neve:** Adjon meg egy egyedi függvényalkalmazás-nevet. Ez a példa a **contoso-function-app-1098 függvényt használja.**

    **Közzététel:** Ellenőrizze, hogy a **Kód van-e** kiválasztva.

    **Futásidejű verem:** Válassza a **.NET Core** lehetőséget a legördülő menüből.

    **Verzió:** Válassza a **3.1** lehetőséget a legördülő menüből.

    **Régió:** Válassza ki ugyanazt a régiót, mint az erőforráscsoport. Ez a példa az USA **nyugati régióját használja.**

    > [!NOTE]
    > Alapértelmezés szerint a Application Insights engedélyezve van. Application Insights nem szükséges ehhez a cikkhez, de segíthet az egyéni kiosztással kapcsolatos problémák felderítésében és kivizsgálásában. Ha szeretné, letilthatja a Application Insights a  Figyelés lapon, majd a Nem lehetőség kiválasztásával **a** **Application Insights.**

    ![Azure-függvényalkalmazás létrehozása az egyéni kiosztási függvényhez](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Az Összefoglalás **lapon** válassza a **Létrehozás lehetőséget** a függvényalkalmazás létrehozásához. Az üzembe helyezés eltarthat néhány percig. Ha befejeződött, válassza az **Erőforráshoz ugrás lehetőséget.**

5. A függvényalkalmazás Áttekintés oldalának bal  oldali panelen **kattintson** a Függvények, majd a **+** Hozzáadás elemre egy új függvény hozzáadásához.

6. A Függvény **hozzáadása lapon** kattintson a **HTTP-eseményindító** elemre, majd a **Hozzáadás gombra.**

7. A következő oldalon kattintson a **Kód + Teszt elemre.** Ez lehetővé teszi, hogy szerkessze a **HttpTrigger1** nevű függvény kódját. A **run.csx kódfájlt** meg kell nyitni szerkesztésre.

8. Hivatkozhat a szükséges NuGet-csomagokra. A kezdeti ikereszköz létrehozásához az egyéni kiosztási függvény olyan osztályokat használ, amelyek két NuGet-csomagban vannak meghatározva, és ezeket be kell tölteni az üzemeltetési környezetbe. A Azure Functions a NuGet-csomagokra egy *function.proj fájl* használatával hivatkozik. Ebben a lépésben menti és feltölti a *function.proj* fájlt a szükséges szerelvények számára.  További információ: [NuGet-csomagok használata a Azure Functions.](../azure-functions/functions-reference-csharp.md#using-nuget-packages)

    1. Másolja a következő sorokat a kedvenc szerkesztőjéhez, és mentse a fájlt a számítógépre *function.proj formátumban.*

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.16.3" />
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.27.0" />
            </ItemGroup>  
        </Project>
        ```

    2. Kattintson **a kódszerkesztő** fölött található Feltöltés gombra a *function.proj fájl feltöltéshez.* A feltöltés után a tartalom ellenőrzéséhez válassza ki a fájlt a kódszerkesztőben a legördülő listában.

9. Győződjön meg arról, hogy a *run.csx* for **HttpTrigger1** van kiválasztva a kódszerkesztőben. Cserélje le a **HttpTrigger1** függvény kódját a következő kódra, majd válassza a **Mentés lehetőséget:**

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

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
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
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
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>A regisztráció létrehozása

Ebben a szakaszban egy új regisztrációs csoportot fog létrehozni, amely az egyéni kiosztási szabályzatot használja. Az egyszerűség kedvéért ez a cikk szimmetrikus kulcsú [igazolást használ a](concepts-symmetric-key-attestation.md) regisztrációval. A biztonságosabb megoldás érdekében érdemes [lehet X.509-tanúsítvány igazolást használni](concepts-x509-attestation.md) megbízhatósági lánccal.

1. Továbbra is a [Azure Portal](https://portal.azure.com)nyissa meg a kiépítési szolgáltatást.

2. A **bal oldali panelen** válassza a  Regisztrációk kezelése lehetőséget, majd az oldal tetején található Regisztrációs csoport hozzáadása gombot.

3. A **Regisztrációs csoport hozzáadása oldalon adja** meg a következő adatokat, majd kattintson a Mentés **gombra.**

    **Csoport neve:** Adja meg **a contoso-custom-allocated-devices nevet.**

    **Igazolás típusa:** Válassza a **Szimmetrikus kulcs lehetőséget.**

    **Kulcsok automatikus létrehozása:** Ez a jelölőnégyzet már be van jelölve.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a központokhoz:** Válassza az **Egyéni (Azure-függvény használata) lehetőséget.**

    **Előfizetés:** Válassza ki azt az előfizetést, amelyben létrehozta az Azure-függvényt.

    **Függvényalkalmazás:** Válassza ki a függvényalkalmazást név alapján. **Ebben a példában a contoso-function-app-1098** volt használva.

    **Függvény:** Válassza ki a **HttpTrigger1** függvényt.

    ![Egyéni foglalási regisztrációs csoport hozzáadása szimmetrikus kulcsú igazoláshoz](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. A regisztráció mentése után nyissa meg újra, és jegyezze fel az **elsődleges kulcsot.** A kulcsok generálása előtt mentenie kell a regisztrációt. Ezzel a kulccsal később egyedi eszközkulcsokat hozhat létre a szimulált eszközökhöz.

## <a name="derive-unique-device-keys"></a>Egyedi eszközkulcsok származtatása

Ebben a szakaszban két egyedi eszközkulcsot hoz létre. A rendszer egy kulcsot fog használni egy szimulált bejelentési eszközhöz. A másik kulcs egy szimulált hőpumpa-eszközhöz lesz használva.

Az eszközkulcs létrehozásához használja  a korábban feljegyzett elsődleges kulcsot az egyes eszközök eszközregisztrációs azonosítójának [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) kiszámításához és az eredmény Base64 formátumba konvertálására. A származtatott eszközkulcsok regisztrációs csoportokkal való létrehozásával kapcsolatos további információkért lásd a szimmetrikus kulcsú igazolás csoportregisztrációs [szakaszát.](concepts-symmetric-key-attestation.md)

A cikkben található példában használja az alábbi két eszközregisztrációs kulcsot, és számítsa ki az eszközkulcsot mindkét eszközhöz. Mindkét regisztrációs adat rendelkezik egy érvényes utótaggal, amely használható az egyéni kiosztási szabályzat példakódjaként:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**


# <a name="windows"></a>[Windows](#tab/windows)

Ha Windows-alapú munkaállomást használ, a PowerShell használatával létrehozhatja a származtatott eszközkulcsot az alábbi példában látható módon.

Cserélje le a **KEY** értékét **a** korábban feljegyzett elsődleges kulcsra.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='breakroom499-contoso-tstrsd-007'
$REG_ID2='mainbuilding167-contoso-hpsd-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

# <a name="linux"></a>[Linux](#tab/linux)

Ha Linux-munkaállomást használ, az openssl használatával létrehozhatja a származtatott eszközkulcsokat az alábbi példában látható módon.

Cserélje le a **KEY** értékét **a** korábban feljegyzett elsődleges kulcsra.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=breakroom499-contoso-tstrsd-007
REG_ID2=mainbuilding167-contoso-hpsd-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---

A szimulált eszközök a származtatott eszközkulcsokat minden regisztrációs azonosítóval együtt használják a szimmetrikus kulcsú igazolás végrehajtásához.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészíti az Azure IoT C SDK felépítéséhez [használt fejlesztőkörnyezetet.](https://github.com/Azure/azure-iot-sdk-c) Az SDK tartalmazza a szimulált eszköz mintakódját. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

Ez a szakasz egy Windows-alapú munkaállomásra vonatkozik. Linux-példáért tekintse meg a virtuális gépek beállítását a [How to provision for multitenancy (Több-bérlős](how-to-provision-multitenant.md)rendszer kiépítése) fejezetben.

1. Töltse le a [CMake buildrendszert.](https://cmake.org/download/)

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattár legújabb kiadásának klónozásához. Használja az előző lépésben talált címkét a paraméter `-b` értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a következő parancsokat a `azure-iot-sdk-c` könyvtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Ha `cmake` nem találja a C++-fordítót, fordítási hibákat fog kapni a parancs futtatása közben. Ha ez történik, próbálja meg a parancsot a [parancssorban Visual Studio parancs futtatásával.](/dotnet/framework/tools/developer-command-prompt-for-vs)

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

Ebben a szakaszban egy **prov dev \_ \_ ügyfélminta \_** nevű kiépítési mintát fog frissíteni, amely a korábban beállított Azure IoT C SDK-ban található.

Ez a mintakód egy olyan eszköz rendszerindítási sorozatát szimulálja, amely elküldi a kiépítési kérelmet a Device Provisioning Service-példánynak. A rendszerindítási művelet a bejelentési eszköz felismerését és az IoT Hubhoz való hozzárendelését az egyéni kiosztási szabályzat használatával fogja elhozni.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studio nyissa meg a CMake azure_iot_sdks futtatásával létrehozott **azure_iot_sdks.sln** megoldásfájlt. A megoldásfájlnak a következő helyen kell lennie:

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

6. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget.

### <a name="simulate-the-contoso-toaster-device"></a>A Contoso toaster eszköz szimulálása

1. A bejelentési eszköz szimulálására keresse meg a hívását a `prov_dev_set_symmetric_key_info()` **prov \_ dev client \_ \_ sample.c** fájlban, amely megjegyzésként van megírva.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Tegye vissza a függvényhívást, és cserélje le a helyőrző értékeket (a szögletes zárójeleket is beleértve) a korábban létrehozott bejelentési regisztrációs azonosítóra és származtatott eszközkulcsra. A **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** kulcsérték csak példaként van megadva.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Mentse a fájlt.

2. A Visual Studio válassza a **Hibakeresés**– Indítás hibakeresés hibakeresés nélkül lehetőséget a  >   megoldás futtatásához. A projekt újraépítésére vonatkozó parancssorban válassza az **Igen** lehetőséget a projekt újraépítéséhez a futtatás előtt.

    Az alábbi kimenet egy példa arra, hogy a szimulált bejelentési eszköz sikeresen elindul, és csatlakozik a kiépítési szolgáltatáspéldányhoz, hogy az egyéni kiosztási szabályzat hozzárendelje a bejelentési eszközök IoT Hubhoz:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>A Contoso hőkezelő szivattyúeszközének szimulálása

1. A hőpumpa eszköz szimulálásaként frissítse ismét a hívását a `prov_dev_set_symmetric_key_info()` **prov \_ dev client \_ \_ sample.c** fájlban a hőpumpa regisztrációs azonosítójával és a korábban létrehozott származtatott eszközk kulccsal. Az alább **látható 6uejA9PfkQgmYypf8Zerp3kcbeVrGZ172YLa7VSnJzg=** kulcsérték szintén csak példaként van megadva.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Mentse a fájlt.

2. A Visual Studio válassza a **Hibakeresés**– Indítás hibakeresés hibakeresés nélkül lehetőséget a  >   megoldás futtatásához. A projekt újraépítésére vonatkozó kérdésben válassza az **Igen** lehetőséget a projekt újraépítéséhez a futtatás előtt.

    Az alábbi kimenet egy példa arra, hogy a szimulált hőpumpa-eszköz sikeresen elindul, és csatlakozik a kiépítési szolgáltatáspéldányhoz, hogy az egyéni kiosztási szabályzat alapján hozzá legyen rendelve a Contoso hőkitöltő IoT Hubhoz:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Egyéni kiosztási szabályzatok hibaelhárítása

Az alábbi táblázat a várt forgatókönyveket és az eredményként kapott hibakódokat mutatja be. A táblázat segítségével elháríthatja az egyéni foglalási szabályzat hibáit a Azure Functions.

| Eset | Regisztrációs eredmény a kiépítési szolgáltatásból | SDK-eredmények kiépítése |
| -------- | --------------------------------------------- | ------------------------ |
| A webhook 200 OK értéket ad vissza, ha az iotHubHostName érvényes IoT Hub-állomásnévre van beállítva | Eredmény állapota: Hozzárendelve  | Az SDK a PROV_DEVICE_RESULT_OK információkat is visszaad |
| A webhook 200 OK értéket ad vissza, a válaszban az "iotHubHostName" értékkel, de üres sztringre vagy nullértékre van állítva | Eredmény állapota: Sikertelen<br><br> Hibakód: CustomAllocationIotHubNotSpecified (400208) | Az SDK visszaadja PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| A webhook a 401 Unauthorized (Nem engedélyezett) értéket adja vissza | Eredmény állapota: Sikertelen<br><br>Hibakód: CustomAllocationAccessuthorizedAccess (400209) | Az SDK visszaadja a PROV_DEVICE_RESULT_UNAUTHORIZED |
| Létrejött egy egyéni regisztráció az eszköz letiltásához | Eredmény állapota: Letiltva | Az SDK visszaadja PROV_DEVICE_RESULT_DISABLED |
| A webhook a következő hibakódot adja vissza: >= 429 | A DPS vezénylése többször próbálkozik újra. Az újrapróbálkozási szabályzat jelenleg a következő:<br><br>&nbsp;&nbsp;– Újrapróbálkozási szám: 10<br>&nbsp;&nbsp;- Kezdeti időköz: 1s<br>&nbsp;&nbsp;- Növekmény: 9-es | Az SDK figyelmen kívül hagyja a hibát, és egy másik állapotüzenetet küld a megadott időpontban |
| A webhook bármilyen más állapotkódot ad vissza | Eredmény állapota: Sikertelen<br><br>Hibakód: CustomAllocationFailed (400207) | Az SDK visszaadja PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát a cikkben létrehozott erőforrásokkal, elhagyhatja őket. Ha nem tervezi tovább használni az erőforrásokat, a következő lépésekkel törölheti a cikkben létrehozott összes erőforrást a szükségtelen költségek elkerülése érdekében.

Az itt található lépések feltételezik, hogy a cikkben található összes erőforrást a **contoso-us-resource-group** nevű erőforráscsoportban utasítás szerint hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport név alapján való törléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **Szűrés név alapján...** szövegmezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét(contoso-us-resource-group).  

3. Az eredménylistában az erőforráscsoporttól jobbra válassza a **...** , majd az **Erőforráscsoport törlése lehetőséget.**

4. Meg kell erősítenie az erőforráscsoport törlését. A megerősítéshez írja be ismét az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.** A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

* További információ az újrabontásról: IoT Hub [eszköz-újrabeépítés fogalmai](concepts-device-reprovision.md) 
* További információ a megszüntetésről: Korábban automatikusan kiépített eszközök [megszüntetése](how-to-unprovision-devices.md)