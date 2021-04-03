---
title: Azure IoT Hub modul Identity & modul twin (portál és .NET)
description: Megtudhatja, hogyan hozhat létre modulidentitást és frissíthet modulikreket a portál és a .NET használatával.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 0d16d7a6dbf903ae790015c12e3d34e4a2a553d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139346"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Ismerkedés az IoT Hub-modulidentitással és -modulikerrel a portál és a .NET eszköz használata mellett

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Noha az Azure IoT Hub eszköz identitása és az eszköz kettős változata lehetővé teszi a háttér-alkalmazás számára az eszköz konfigurálását, valamint az eszköz feltételeinek láthatóságát, a modul identitása és modulja külön biztosítja ezeket a képességeket az eszközök egyes összetevőihez. A több összetevővel rendelkező kompatibilis eszközökön, például az operációs rendszer-alapú eszközökön vagy a belső vezérlőprogram-eszközökön a modul-identitások és az ikrek modul lehetővé teszi az elkülönített konfigurációt és feltételeket az egyes összetevőkhöz.
>

Az oktatóanyag során a következőket fogja elsajátítani:

* Modul identitásának létrehozása a portálon.

* A .NET-eszközök SDK-val történő frissítésével frissítheti a modult a Twin eszközről.

> [!NOTE]
> További információ az Azure IoT SDK-k használatáról az eszközökön és a megoldás hátterén futó alkalmazások létrehozásához: [Azure IoT SDK](iot-hub-devguide-sdks.md)-k.
>

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studióval.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

## <a name="create-a-hub"></a>Elosztó létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Új eszköz regisztrálása a központban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Modulidentitás létrehozása a portálon

Egy eszközidentitáson belül legfeljebb 20 modulidentitás hozható létre. Identitás hozzáadásához kövesse az alábbi lépéseket:

1. Az előző szakaszban létrehozott eszközhöz válassza a modul- **identitás hozzáadása** elemet az első modul identitásának létrehozásához.

1. Adja meg a *myFirstModule* nevet. Mentse a modul identitását.

    ![Modul identitásának hozzáadása](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Az új modul identitása megjelenik a képernyő alján. Válassza ki a modul-identitás részleteinek megtekintéséhez.

    ![Lásd a modul-identitás részleteit](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Mentse a **kapcsolati sztringet – elsődleges kulcs**. A következő szakaszban azt használhatja, hogy beállítsa a modult az eszközön.

## <a name="update-the-module-twin-using-net-device-sdk"></a>A moduliker frissítése a .NET eszközoldali SDK-val

Sikeresen létrehozta a modulidentitást az IoT Hubban. Ideje megkísérelni a felhővel való kommunikációt a szimulált eszközről. A modulidentitás létrehozása után egy moduliker is implicit módon létrejön az IoT Hubban. Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre a szimulált eszközön a moduliker jelentett tulajdonságainak frissítéséhez.

### <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Az alábbi lépéseket követve hozhat létre egy alkalmazást, amely frissíti a modul Twin jelentett tulajdonságait:

1. A Visual Studióban válassza az **új projekt létrehozása**, majd a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, és kattintson a **Tovább gombra**.

1. Az **új projekt konfigurálása** területen adja meg a *UpdateModuleTwinReportedProperties* **nevet a projekt neveként**. A folytatáshoz válassza a **Létrehozás** lehetőséget.

    ![A Visual Studio-projekt konfigurálása](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>A legújabb Azure IoT Hub .NET-eszköz SDK telepítése

A modul identitása és a Twin modul nyilvános előzetes verzióban érhető el. Ez csak a IoT Hub előzetes kiadású SDK-k esetében érhető el. A telepítéséhez kövesse az alábbi lépéseket:

1. A Visual Studióban nyissa meg az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-csomagok kezelése megoldást**.

1. Válassza a **Tallózás** lehetőséget, majd válassza az **előzetes verzió belefoglalása** lehetőséget. Keressen rá a *Microsoft. Azure. Devices. Client* kifejezésre. Válassza ki a legújabb verziót, és telepítse.

    ![Az Azure IoT Hub .NET Service SDK előzetes verziójának telepítése](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Most már az összes modulfunkcióhoz rendelkezik hozzáféréssel.

### <a name="get-your-module-connection-string"></a>A modulhoz tartozó kapcsolatok karakterláncának beolvasása

Szüksége lesz a konzolos alkalmazáshoz tartozó modul-csatlakoztatási karakterláncra. Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon az IoT hubhoz, és válassza a **IoT-eszközök** elemet. Nyissa meg a **myFirstDevice** , és láthatja, hogy a **myFirstModule** létrehozása sikeresen megtörtént.

1. Válassza ki a **myFirstModule** elemet a **modul-identitások** területen. A **modul identitásának részleteiben** másolja a **kapcsolati karakterláncot (elsődleges kulcs)**.

    ![Az Azure Portal moduladatai](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties-konzol alkalmazás létrehozása

Az alkalmazás létrehozásához kövesse az alábbi lépéseket:

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át a modul kapcsolati sztringjére.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Adja hozzá a **Program** osztályhoz a következő, **OnDesiredPropertyChanged** metódust:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

4. Végül cserélje le a **Main** metódust a következő kódra:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
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
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  Ezt az alkalmazást az **F5** használatával hozhatja létre és futtathatja.

A kódminta segítségével megtudhatja, hogyan kérheti le a modulikret és frissítheti a jelentett tulajdonságokat az AMQP-protokollal. A nyilvános előzetes verzióban az AMQP csak a moduliker-műveletek esetében támogatott.

## <a name="next-steps"></a>Következő lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az IoT Hub-modulidentitással és -modulikerrel .NET biztonsági mentés és .NET eszköz használata mellett](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)