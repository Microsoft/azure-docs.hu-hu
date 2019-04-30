---
title: Ismerkedés az Azure IoT Hub-modulidentitással és -modulikerrel (.NET) | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre modulidentitást és frissíthet modulikert a .NET-hez készült IoT SDK-k használatával.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 3b10ae0998acbb010e61ebb41c602c7d42bc14fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741823"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Ismerkedés az IoT Hub identitás- és modul ikermodul .NET-háttérrendszer és a .NET-eszköz használata

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Amíg az Azure IoT Hub eszközidentitása és eszközikre lehetővé teszi a háttéralkalmazás számára az eszköz konfigurálását, és rálátást nyújt az eszköz feltételeire, a modulidentitás és a moduliker az eszköz egyes összetevőihez biztosítja ezeket a lehetőségeket. A megfelelő, több összetevős eszközök, például az operációs rendszeren vagy a belső vezérlőprogramon alapuló eszközök esetében lehetővé teszi az elkülönített konfigurációk és feltételek beállítását az egyes összetevőkhöz.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* A **CreateIdentities** egy eszközidentitást, egy modulidentitást valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat az eszközhöz és a modulügyfelekhez.

* Az **UpdateModuleTwinReportedProperties** a moduliker jelentett tulajdonságainak frissítését továbbítja az IoT Hub részére.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához, kapcsolatos információkért lásd: [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2017.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Létrehozta az IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és IoT Hub kapcsolati sztringgel.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>A moduliker frissítése a .NET eszközoldali SDK-val

Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre a szimulált eszközön a moduliker jelentett tulajdonságainak frissítéséhez.

1. **A Visual Studio-projekt létrehozása:** A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus Asztalialkalmazás-projektet az a meglévő megoldás használatával a **Console App (.NET Framework)** projektsablonnal. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja. Adja a projektnek az **UpdateModuleTwinReportedProperties** nevet.

    ![Hozzon létre egy Visual Studio-projektet.](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Telepítse a legújabb Azure IoT hubhoz .NET eszközoldali SDK:** Identitás- és modul ikermodul jelenleg nyilvános előzetes verzióban. Csak akkor érhető el az IoT Hub SDK-k előzetes eszközön. A Visual Studióban válassza a Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz) elemet. Keresse meg a Microsoft.Azure.Devices.Client csomagot. Győződjön meg arról, hogy az előzetes verzió jelölőnégyzete be van jelölve. Válassza ki a legújabb verziót, és telepítse. Most már az összes modulfunkcióhoz rendelkezik hozzáféréssel. 

    ![Az Azure IoT Hub .NET szolgáltatási SDK 1.16.0-preview-005-ös verziójának telepítése](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **A modul kapcsolati sztring lekérése** – Ha bejelentkezik az [az Azure portal](https://portal.azure.com/). Keresse meg az IoT Hubot, és kattintson az IoT-eszközök elemre. Keresés myFirstDevice, nyissa meg azt, és tekintse meg a myFirstModule sikeresen létrejött. Másolja ki a modul kapcsolati sztringjét. A következő lépés során szükség lesz rá.

    ![Az Azure Portal moduladatai](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **UpdateModuleTwinReportedProperties Konzolalkalmazás létrehozása**

    Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át a modul kapcsolati sztringjére.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Adja hozzá a **Program** osztályhoz a következő, **OnDesiredPropertyChanged** metódust:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    A kódminta segítségével megtudhatja, hogyan kérheti le a modulikret és frissítheti a jelentett tulajdonságokat az AMQP-protokollal. A nyilvános előzetes verzióban az AMQP csak a moduliker-műveletek esetében támogatott.

5. A fentiek mellett **fő** módszerrel adhat hozzá alábbi kódblokkot, amellyel esemény küldése az IoT hubhoz a modulból:

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására. A Visual Studióban a Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd kattintson a **Set StartUp projects** (Indítási projektek beállítása) parancsra. Válassza a **Multiple startup projects** (Több indítási projekt) lehetőséget, majd válassza a **Start** (Indítás) elemet a konzolalkalmazás műveleteként. Ezt követően nyomja le az F5 gombot az alkalmazás elindításához.

## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az eszközfelügyelettel](iot-hub-node-node-device-management-get-started.md)
* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)