---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68883738"
---
## <a name="create-a-module-identity"></a>Modulidentitás létrehozása

Ebben a szakaszban egy olyan .NET-konzol alkalmazást hoz létre, amely létrehoz egy eszköz identitását és egy modul-identitást a központ identitás-beállításjegyzékében. Egy eszköz vagy modul nem tud csatlakozni a központhoz, hacsak nem rendelkezik bejegyzéssel az Identity registryben. További információkért tekintse meg a [IoT hub fejlesztői útmutatójának Identity Registry című szakaszát](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Az eszköz és a modul ezeknek az értékeknek a segítségével azonosítja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld IoT Hub. Az azonosítók megkülönböztetik a kis- és nagybetűket.

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.

1. Az **új projekt létrehozása** területen válassza a **Console app (.NET-keretrendszer)** lehetőséget.

1. Kattintson a **tovább** gombra az **új projekt konfigurálásának** megnyitásához. Adja a projektnek a *CreateIdentities* nevet, a megoldásnak pedig az *IoTHubGetStarted* nevet. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja.

    ![Adja meg a Visual Studio-megoldás nevét és keretrendszerét](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. A Visual Studióban nyissa meg az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-csomagok kezelése megoldást**. Válassza a **Tallózás** lapot.

1. Keressen rá a **Microsoft. Azure. Devices** kifejezésre. Jelölje ki, majd válassza a **telepítés** lehetőséget.

    ![Az Azure IoT Hub .NET Service SDK jelenlegi verziójának telepítése](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati sztringre.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Adja hozzá a következő kódot a **Main** osztályhoz.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Adja hozzá a **Program** osztályhoz a következő metódusokat:

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

    A `AddDeviceAsync` metódus létrehoz egy **myFirstDevice** azonosítóval rendelkező eszköz identitását. Ha már létezik ilyen AZONOSÍTÓJÚ eszköz az Identity registryben, a kód egyszerűen lekéri a meglévő eszköz adatait. Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált eszköz alkalmazásban használhatja a hubhoz való kapcsolódáshoz.

    A `AddModuleAsync` metódus létrehoz egy **myFirstModule** azonosítóval az eszköz **myFirstDevice** alatt. Ha a modul azonosítója már létezik az Identity registryben, a kód egyszerűen lekéri a meglévő modul információit. Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált modul alkalmazásban használhatja a hubhoz való kapcsolódáshoz.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Futtassa az alkalmazást, és jegyezze fel az eszköz kulcsát és a modul kulcsát.

> [!NOTE]
> A IoT Hub Identity Registry csak az eszköz-és modul-identitásokat tárolja a hub biztonságos elérésének engedélyezéséhez. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más, az eszközre jellemző metaadatokat is tárolnia kell, az alkalmazásnak alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információ: [IoT hub fejlesztői útmutató](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
