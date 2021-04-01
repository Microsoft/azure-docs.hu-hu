---
title: Első lépések az Azure IoT Hub modul Identity & modul twin (.NET)
description: Megtudhatja, hogyan hozhat létre modulidentitást és frissíthet modulikert a .NET-hez készült IoT SDK-k használatával.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: daba400b9daadf464c4c125ad266745237e71367
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142508"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Ismerkedés a IoT Hub modul identitásával és moduljával (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Noha az Azure IoT Hub eszköz identitása és az eszköz kettős változata lehetővé teszi a háttér-alkalmazás számára az eszköz konfigurálását, valamint az eszköz feltételeinek láthatóságát, a modul identitása és modulja külön biztosítja ezeket a képességeket az eszközök egyes összetevőihez. A több összetevővel rendelkező kompatibilis eszközökön, például az operációs rendszer-alapú eszközökön vagy a belső vezérlőprogram-eszközökön a modul-identitások és az ikrek modul lehetővé teszi az elkülönített konfigurációt és feltételeket az egyes összetevőkhöz.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* **CreateIdentities**. Ez az alkalmazás létrehoz egy eszköz-identitást, egy modul-identitást és egy társított biztonsági kulcsot az eszköz és a modul ügyfeleinek csatlakoztatásához.

* **UpdateModuleTwinReportedProperties**. Ez az alkalmazás a frissített modul két jelentett tulajdonságát küldi el az IoT hubhoz.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studióval.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

## <a name="create-a-hub"></a>Elosztó létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>A moduliker frissítése a .NET eszközoldali SDK-val

Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre a szimulált eszközön a moduliker jelentett tulajdonságainak frissítéséhez.

Mielőtt elkezdené, szerezze be a modulhoz tartozó kapcsolási karakterláncot. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Navigáljon a hubhoz, és válassza a **IoT-eszközök** elemet. **MyFirstDevice** keresése. Válassza a **myFirstDevice** lehetőséget a megnyitásához, majd kattintson a **myFirstModule** elemre a megnyitásához. A **modul identitásának részleteiben** másolja a **kapcsolati karakterláncot (elsődleges kulcs)** , ha szükséges az alábbi eljárásban.

   ![Az Azure Portal moduladatai](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. A Visual Studióban vegyen fel egy új projektet a megoldásba a **fájl**  >  **új**  >  **projekt** lehetőség kiválasztásával. Az új projekt létrehozása területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, és kattintson a **Tovább gombra**.

1. Adja a projektnek az *UpdateModuleTwinReportedProperties* nevet. A **megoldás** mezőben válassza a **Hozzáadás a megoldáshoz** lehetőséget. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja.

    ![Visual Studio-projekt létrehozása](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. A projekt létrehozásához válassza a **Létrehozás** elemet.

1. A Visual Studióban nyissa meg az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-csomagok kezelése megoldást**. Válassza a **Tallózás** lapot.

1. Keresse meg és válassza ki a **Microsoft. Azure. Devices. Client** elemet, majd válassza a **telepítés** lehetőséget.

    ![Képernyőkép, amely megjeleníti a "Microsoft. Azure. Devices. Client" elemet, és a "telepítés" gomb kiemelve.](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át a modul kapcsolati sztringjére.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Adja hozzá a **Program** osztályhoz a következő, **OnDesiredPropertyChanged** metódust:

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

1. Adja hozzá a következő sorokat a **Main** metódushoz:

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

1. Ha szeretné, ezeket az utasításokat a **Main** metódushoz is hozzáadhatja, ha egy eseményt szeretne elküldeni IoT hub a modulból. Helyezze el ezeket a sorokat a `try catch` blokk alá.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már futtathatja az alkalmazásokat.

1. A Visual Studióban **megoldáskezelő** kattintson a jobb gombbal a megoldásra, majd válassza az **indítási projektek beállítása** lehetőséget.

1. Az **Általános tulajdonságok** területen válassza az **indítási projekt elemet.**

1. Válasszon **több indítási projektet**, majd válassza a **Start** lehetőséget az alkalmazások művelete elemnél, és az **OK gombra** kattintva fogadja el a módosításokat.

1. Az alkalmazások elindításához nyomja le az **F5** billentyűt.

## <a name="next-steps"></a>Következő lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)