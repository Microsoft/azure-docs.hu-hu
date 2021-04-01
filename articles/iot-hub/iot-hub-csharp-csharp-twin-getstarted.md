---
title: Ismerkedés az Azure IoT Hub Device ikrek szolgáltatással (.NET/.NET) | Microsoft Docs
description: Az Azure IoT Hub-eszközök ikrek használata címkék hozzáadásához és IoT Hub-lekérdezés használatához. A .NET-hez készült Azure IoT eszközoldali SDK segítségével megvalósíthatja a szimulált eszköz alkalmazást és a .NET-hez készült Azure IoT Service SDK-t egy olyan szolgáltatásalkalmazás megvalósításához, amely hozzáadja a címkéket és futtatja a IoT Hub lekérdezést.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 267a69486dc91ef95c0de704346eeb1d1780ef48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89013758"
---
# <a name="get-started-with-device-twins-net"></a>Ismerkedés az eszközökhöz készült ikrek (.NET) használatával

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ebben az oktatóanyagban a következő .NET-konzolos alkalmazásokat hozza létre:

* **AddTagsAndQuery**. Ez a háttérbeli alkalmazás címkéket hoz létre, és lekérdezi az eszközökhöz tartozó ikreket.

* **ReportConnectivity**. Ez az eszköz egy olyan eszközt szimulál, amely a korábban létrehozott IoT-hubhoz csatlakozik, és a kapcsolati feltételét jelzi.

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k cikke olyan Azure IoT SDK-kat tartalmaz, amelyek segítségével mind az eszközök, mind a háttérbeli alkalmazások készíthetők.
>

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studióval.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy .NET-konzol alkalmazást hoz létre a C# használatával, amely a hely metaadatainak hozzáadását a **myDeviceId** társított eszközökhöz. Ezután lekérdezi az IoT hub-ban tárolt, az USA-ban található eszközöket, majd a mobil kapcsolatról jelentést tartalmazó eszközt.

1. A Visual Studióban válassza az **új projekt létrehozása** lehetőséget. Az **új projekt létrehozása** területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább** gombra.

1. Az **új projekt konfigurálása** területen nevezze el a projekt **AddTagsAndQuery**.

    ![A AddTagsAndQuery-projekt konfigurálása](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. Megoldáskezelő kattintson a jobb gombbal a **AddTagsAndQuery** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.

1. Válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices** elemet. Válassza a **Telepítés** gombot.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagra és annak függőségeire mutató hivatkozást.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyére írja `{iot hub connection string}` be az [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)során másolt IoT hubi kapcsolatok karakterláncát.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    A **RegistryManager** osztály minden olyan metódust elérhetővé tesz, amely az eszközök ikrek szolgáltatásból való interakcióhoz szükséges. Az előző kód először inicializálja a **registryManager** objektumot, majd lekéri az **myDeviceId**-t, és végül frissíti a címkéket a kívánt hely adataival.

    A frissítés után két lekérdezést hajt végre: az első kiválasztja csak a **Redmond43** -üzemben található eszközökből álló ikreket, a második pedig csak azokat az eszközöket jelöli ki, amelyek a mobil hálózaton keresztül is csatlakoztatva vannak.

    A korábbi kód a **lekérdezési** objektum létrehozásakor megadja a visszaadott dokumentumok maximális számát. A **lekérdezési** objektum egy **HasMoreResults** logikai tulajdonságot tartalmaz, amelyet a **GetNextAsTwinAsync** metódusok többszöri meghívására használhat az összes eredmény lekéréséhez. A **GetNextAsJson** nevű metódus olyan eredményekhez érhető el, amelyek nem az eszközök ikrek, például az összesítési lekérdezések eredményei.

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Az alkalmazás futtatásához kattintson a jobb gombbal a **AddTagsAndQuery** projektre, és válassza a **hibakeresés** lehetőséget, majd az **új példány elindítása** parancsot. A lekérdezés eredményei között egy eszközt kell látnia, amely a **Redmond43** -ban található összes eszközt kéri, a nem pedig a lekérdezést, amely a mobil hálózatot használó eszközökre korlátozza az eredményeket.

    ![Lekérdezés eredményei az ablakban](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

A következő szakaszban létrehoz egy olyan eszközt, amely jelentést készít a kapcsolati adatokról, és megváltoztatja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban egy olyan .NET-konzol alkalmazást hoz létre, amely a **myDeviceId**-hez csatlakozik a központhoz, majd frissíti a jelentett tulajdonságokat, hogy tartalmazza a mobil hálózaton keresztül csatlakoztatott adatokat.

1. A Visual Studióban válassza a **fájl**  >  **új**  >  **projekt** lehetőséget. Az **új projekt létrehozása** területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább** gombra.

1. Az **új projekt konfigurálása** területen nevezze el a projekt **ReportConnectivity**. A **megoldáshoz** válassza a **Hozzáadás a megoldáshoz** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

1. Megoldáskezelő kattintson a jobb gombbal a **ReportConnectivity** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.

1. Válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices. Client** elemet. Válassza a **Telepítés** gombot.

   Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomagra és annak függőségeire mutató hivatkozást.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyére írja `{device connection string}` be az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban feljegyzett eszköz-csatlakoztatási karakterláncot.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Az **ügyfél** -objektum minden olyan módszert feltesz, amely az eszközön található ikrekkel való interakcióhoz szükséges. A fenti kód inicializálja az **ügyfél** -objektumot, majd lekéri a **myDeviceId**-hez tartozó különálló eszközt.

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   A fenti kód frissíti a  **myDeviceId** jelentett tulajdonságát a kapcsolódási információkkal.

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Megoldáskezelő kattintson a jobb gombbal a megoldásra, majd válassza az **indítási projektek beállítása** lehetőséget.

1. Az **Általános tulajdonságok**  >  **indítási projekt** területen válassza a **több indítási projekt** lehetőséget. A **ReportConnectivity** területen válassza a **kezdés** **műveletet**. A módosítások mentéséhez kattintson az **OK** gombra.  

1. Az alkalmazás futtatásához kattintson a jobb gombbal a **ReportConnectivity** projektre, és válassza a **hibakeresés** lehetőséget, majd **indítsa el az új példányt**. Ekkor meg kell jelennie az alkalmazásnak, és a kapcsolat küldése **_jelentett tulajdonságnak_** kell lennie.

    ![Az eszköz alkalmazásának futtatása a kapcsolat jelentéséhez](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Miután az eszköz bejelentette a kapcsolati adatait, mindkét lekérdezésben szerepelnie kell.

1. Kattintson a jobb gombbal a **AddTagsAndQuery** projektre, és válassza a **hibakeresés**  >  **új példány elindítása** lehetőséget a lekérdezések ismételt futtatásához. Ezúttal a **myDeviceId** mindkét lekérdezési eredményben szerepelnie kell.

    ![Az eszköz kapcsolata sikeresen jelezve](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttérbeli alkalmazáshoz, és írt egy szimulált eszközt, amely az eszköz kapcsolati adatait jelenti a Twin-ben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az adatokat az SQL-Like IoT Hub lekérdezési nyelv használatával.

További információt a következő forrásokból kaphat:

* Ha szeretné megtudni, hogyan küldhet telemetria az eszközökről, tekintse meg a [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-dotnet.md) -oktatóanyagba című témakört.

* Ha meg szeretné tudni, hogyan konfigurálhatja az eszközöket a Device Twin kívánt tulajdonságaival, tekintse meg a [kívánt tulajdonságok használata az eszközök konfigurálásához](tutorial-device-twins.md) oktatóanyagot.

* Ha szeretné megtudni, hogyan vezérelheti az eszközöket interaktív módon, például egy felhasználó által vezérelt alkalmazás ventilátorának bekapcsolásával kapcsolatban, tekintse meg a [közvetlen módszerek használata](quickstart-control-device-dotnet.md) oktatóanyagot.
