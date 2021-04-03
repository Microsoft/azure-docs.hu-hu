---
title: Ismerkedés az Azure IoT Hub-eszközök felügyeletével (.NET/.NET) | Microsoft Docs
description: Az Azure IoT Hub-eszközök felügyeletének használata távoli eszköz újraindításának elindításához. A .NET-hez készült Azure IoT Device SDK használatával olyan szimulált eszköz alkalmazást alkalmazhat, amely közvetlen metódust és a .NET-hez készült Azure IoT Service SDK-t használja a közvetlen metódust meghívó szolgáltatásalkalmazás megvalósításához.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89018399"
---
# <a name="get-started-with-device-management-net"></a>Ismerkedés az eszközkezelés (.NET) szolgáltatással

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A Azure Portal használatával hozzon létre egy IoT hubot, és hozzon létre egy eszköz-identitást az IoT hub-ban.

* Hozzon létre egy szimulált eszköz alkalmazást, amely egy közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy olyan .NET-konzol alkalmazást, amely a szimulált eszközön a IoT hub használatával meghívja a közvetlen újraindítási módszert.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* **SimulateManagedDevice**. Ez az alkalmazás csatlakozik az IoT hubhoz a korábban létrehozott eszköz-identitással, a rendszer újraindítási közvetlen módszert kap, szimulálja a fizikai újraindítást, és jelentést készít az utolsó újraindítás idejéről.

* **TriggerReboot**. Ez az alkalmazás egy közvetlen metódust hív meg a szimulált eszköz alkalmazásban, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studióval.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás indítása az eszközön közvetlen módszer használatával

Ebben a szakaszban egy .NET-konzol alkalmazást hoz létre a C# használatával, amely egy távoli újraindítást kezdeményez egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz kettős lekérdezéseit használva észleli az adott eszköz utolsó újraindításának idejét.

1. A Visual Studióban válassza az **új projekt létrehozása** lehetőséget.

1. A **create a New Project (új projekt létrehozása**) területen keresse meg és válassza ki a **Console app (.NET-keretrendszer)** projekt sablonját, majd válassza a **tovább** lehetőséget.

1. Az **új projekt konfigurálása** lapon nevezze el a projekt *TriggerReboot*, majd válassza a .NET-keretrendszer 4.5.1-es vagy újabb verzióját. Válassza a **Létrehozás** lehetőséget.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. **Megoldáskezelő** kattintson a jobb gombbal a **TriggerReboot** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.

1. Válassza a **Tallózás** lehetőséget, majd keresse meg és válassza ki a **Microsoft. Azure. Devices** elemet. A **Microsoft. Azure. Devices** csomag telepítéséhez válassza a **telepítés** lehetőséget.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagra és annak függőségeire mutató hivatkozást.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A `{iot hub connection string}` helyőrző értékét cserélje le a korábban a [IoT hub-beli kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)során másolt IoT hubi kapcsolatok karakterlánccá.

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Adja hozzá a következő metódust a **program** osztályhoz.  Ez a kód beolvassa az eszközhöz tartozó Twin eszközt az újraindításhoz, és kiírja a jelentett tulajdonságokat.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Adja hozzá a következő metódust a **program** osztályhoz.  Ez a kód közvetlen módszer használatával indítja el az újraindítást az eszközön.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Végül adja a következő sorokat a **Main** metódushoz:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Válassza a **Build**  >  **Build megoldás** lehetőséget.

> [!NOTE]
> Ez az oktatóanyag csak egyetlen lekérdezést hajt végre az eszköz jelentett tulajdonságaihoz. Az éles kódban javasolt a lekérdezés a jelentett tulajdonságok változásainak észlelésére.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Hozzon létre egy .NET-konzol alkalmazást, amely válaszol a felhő által meghívott közvetlen metódusra.

* Szimulált eszköz újraindításának indítása.

* A jelentett tulajdonságok használatával engedélyezheti az eszköz kettős lekérdezéseit az eszközök azonosításához és az utolsó újraindításkor.

A szimulált eszköz alkalmazás létrehozásához kövesse az alábbi lépéseket:

1. A Visual Studióban, a már létrehozott TriggerReboot-megoldásban válassza a **fájl**  >  **új**  >  **projekt** lehetőséget. A **create a New Project (új projekt létrehozása**) területen keresse meg és válassza ki a **Console app (.NET-keretrendszer)** projekt sablonját, majd válassza a **tovább** lehetőséget.

1. Az **új projekt konfigurálása** lapon nevezze el a projekt *SimulateManagedDevice*, és a **megoldáshoz** válassza a **Hozzáadás a megoldáshoz** lehetőséget. Válassza a **Létrehozás** lehetőséget.

    ![A projekt neve és hozzáadása a megoldáshoz](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. Megoldáskezelőban kattintson a jobb gombbal az új **SimulateManagedDevice** -projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.

1. Válassza a **Tallózás** elemet, majd keresse meg és válassza ki a **Microsoft. Azure. Devices. Client** elemet. Válassza a **Telepítés** gombot.

    ![NuGet csomagkezelő ablak ügyfélalkalmazás](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomagra és annak függőségeire mutató hivatkozást.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a `{device connection string}` helyőrző értékét a korábban feljegyzett eszköz-összekapcsolási sztringre az [IoT hub új eszköz regisztrálása](#register-a-new-device-in-the-iot-hub)részében.

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Adja hozzá a következőt a közvetlen metódus megvalósításához az eszközön:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Végül adja hozzá a következő kódot a **Main** metódushoz a IoT hub-hoz való kapcsolódás megnyitásához és a metódus-figyelő inicializálásához:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. A Megoldáskezelő kattintson a jobb gombbal a megoldásra, majd válassza az **indítási projektek beállítása** lehetőséget.

1. A **gyakori tulajdonságok**  >  **indítási projekt** esetében válassza az **egyetlen indítási projekt** lehetőséget, majd válassza ki a **SimulateManagedDevice** projektet. A módosítások mentéséhez kattintson az **OK** gombra.

1. Válassza a **Build**  >  **Build megoldás** lehetőséget.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. A termelési kódban az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults)szolgáló újrapróbálkozási házirendeket (például exponenciális leállítási) kell végrehajtania.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A .NET-eszköz alkalmazás **SimulateManagedDevice** futtatásához megoldáskezelő kattintson a jobb gombbal a **SimulateManagedDevice** projektre, válassza a **hibakeresés** lehetőséget, majd válassza az **új példány indítása** lehetőséget. Az alkalmazásnak meg kell kezdenie az IoT hub metódus-hívásainak figyelését.

1. Miután az eszköz csatlakoztatva van, és várakozik a metódus meghívására, kattintson a jobb gombbal a **TriggerReboot** projektre, válassza a **hibakeresés** lehetőséget, majd válassza az **új példány indítása** lehetőséget.

   A "újraindítás!" üzenetnek kell megjelennie. a **SimulatedManagedDevice** -konzolban és az eszköz jelentett tulajdonságaiban szerepel, amely tartalmazza a **TriggerReboot** -konzolon írt utolsó újraindítási időt.

    ![Szolgáltatás-és eszköz-alkalmazás futtatása](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
