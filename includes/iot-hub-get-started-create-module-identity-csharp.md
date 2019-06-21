---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179772"
---
## <a name="create-a-module-identity"></a>Modulidentitás létrehozása

Ebben a szakaszban egy .NET-konzolalkalmazást fog létrehozni, amely egy eszközidentitást és egy modulidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz vagy modul csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd: a [Eszközidentitás-jegyzék szakaszában az IoT Hub fejlesztői útmutatójának](../articles/iot-hub/iot-hub-devguide-identity-registry.md). A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Ezekkel az értékekkel az eszköz és a modul azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.


1. **Visual Studio-projekt létrehozása** – A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az új megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja. Adja a projektnek a **CreateIdentities** nevet, a megoldásnak pedig az **IoTHubGetStarted** nevet.

    ![Hozzon létre egy Visual Studio-megoldást.](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Telepítse az Azure IoT Hub .NET szolgáltatási SDK 1.16.0-preview-001-es verzióját** – A modulidentitás és a moduliker nyilvános előzetes verzióban érhető el. Csak akkor érhető el az IoT Hub előzetes szolgáltatás SDK-k. A Visual Studióban válassza a Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz) elemet. Keresse meg a Microsoft.Azure.Devices csomagot. Győződjön meg arról, hogy az előzetes verzió jelölőnégyzete be van jelölve. Válassza ki az 1.16.0-preview-001-es verziót, és telepítse azt. Most már az összes modulfunkcióhoz rendelkezik hozzáféréssel. 

    ![Az Azure IoT Hub .NET szolgáltatási SDK 1.16.0-preview-001-es verziójának telepítése](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati sztringre.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Adja hozzá a következő kódot a **fő** osztály.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Adja hozzá a **Program** osztályhoz a következő metódusokat:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Az AddDeviceAsync() metódus egy eszközidentitást hoz létre a **myFirstDevice** azonosítóval. (Ha ez az eszköz már létezik az identitásjegyzékben, a kód egyszerűen lekéri a meglévő eszközinformációkat.) Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált eszközalkalmazásban használja az IoT Hubhoz való csatlakozáshoz.

    Az AddModuleAsync() metódus egy modulidentitást hoz létre a **myFirstModule** azonosítóval a **myFirstDevice** alatt. (Ha ez a modulazonosító már létezik az identitásjegyzékben, a kód egyszerűen lekéri a meglévő modulinformációkat.) Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált modulalkalmazásban használja az IoT Hubhoz való csatlakozáshoz.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Futtassa az alkalmazást, és jegyezze fel az eszköz és a modul kulcsát.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszköz- és modulidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információkért lásd: [IoT Hub fejlesztői útmutatójának](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
