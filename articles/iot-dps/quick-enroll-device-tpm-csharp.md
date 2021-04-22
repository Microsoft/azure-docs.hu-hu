---
title: 'Rövid útmutató – TPM-eszköz regisztrálása az Azure Device Provisioning Service-be a C használatával #'
description: Rövid útmutató – TPM-eszköz regisztrálása Azure IoT Hub Device Provisioning Service (DPS) szolgáltatásba a C# szolgáltatásoldali SDK-val. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 73a9b27c7b9119ee49fc451ca0a1c03d8db3db0e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868626"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Rövid útmutató: TPM-eszköz regisztrálása IoT Hub Device Provisioning Service C# szolgáltatásoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ez a cikk bemutatja, hogyan hozhat létre egyéni regisztrációt TPM-eszközhöz programozott módon a Azure IoT Hub Device Provisioning Service-ben a [C#](https://github.com/Azure/azure-iot-sdk-csharp) szolgáltatásoldali SDK és egy C# .NET Core-mintaalkalmazás használatával. Ha szükséges, ezzel az egyéni regisztrációs bejegyzéssel regisztrálhat szimulált TPM-eszközt a kiépítési szolgáltatásba. Bár ezek a lépések Windows és Linux rendszerű számítógépeken is működnek, ez a cikk windowsos fejlesztési számítógépet használ.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

1. Ellenőrizze, hogy [Visual Studio 2019](https://www.visualstudio.com/vs/) telepítve van-e a számítógépen.

1. Ellenőrizze, hogy telepítve [.NET Core SDK-e](https://dotnet.microsoft.com/download) a számítógépen.

1. A folytatás előtt kövesse az [IoT Hub Device Provisioning Service](./quick-setup-auto-provision.md) beállítása a Azure Portal lépéseit.

1. (Nem kötelező) Ha a rövid útmutató végén regisztrálni szeretne egy szimulált eszközt, kövesse a Szimulált [TPM-eszköz létrehozása](quick-create-simulated-device-tpm-csharp.md) és kiépítése C#-eszközoldali SDK-val lépésben leírtakat az ellenőrzőkulcs lekért lépésig. Mentse az ellenőrzőkulcsot, a regisztrációs azonosítót és igény szerint az eszközazonosítót, mert a rövid útmutató későbbi lépésében szüksége lesz rájuk.

   > [!NOTE]
   > Ne kövesse az egyéni regisztrációk egyéni regisztrációk létrehozásához szükséges lépéseket a Azure Portal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>A kiépítési szolgáltatás kapcsolati sztringjének lekérése

A rövid útmutatóban lévő mintához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére.

1. Jelentkezzen be a Azure Portal, válassza a **Minden** erőforrás lehetőséget, majd a Device Provisioning Service-t.

1. Válassza **a Megosztott hozzáférési szabályzatok** lehetőséget, majd válassza ki a tulajdonságok megnyitásához használni kívánt hozzáférési szabályzatot. A **Hozzáférési szabályzat elemben** másolja ki és mentse az elsődleges kulcs kapcsolati sztringet.

    ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre olyan .NET Core-konzolalkalmazást, amely egyéni regisztrációt ad hozzá egy TPM-eszközhöz a kiépítési szolgáltatáshoz. Néhány módosítással ezeket a lépéseket követve létrehozhat egy [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konzolalkalmazást is az egyéni regisztráció hozzáadásához. Az IoT Core-beli fejlesztéssel kapcsolatos további információkért lásd a [Windows IoT Core fejlesztői dokumentációját.](/windows/iot-core/)

1. Nyissa Visual Studio, és válassza **az Új projekt létrehozása lehetőséget.** A **Create a new project (Új projekt létrehozása) mezőben** válassza ki a Console App (.NET Core) project template for C# **(Konzolalkalmazás (.NET Core)** projektsablont a C# számára, majd válassza a Next (Tovább) **lehetőséget.**

1. A projektnek nevezze el a *CreateTpmEnrollment nevet,* majd nyomja le a **Create (Létrehozás) gombot.**

    ![Visual C# Windows klasszikus asztali projekt konfigurálása](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Amikor a megoldás megnyílik a Visual Studio panel Megoldáskezelő **kattintson** a jobb gombbal a **CreateTpmEnrollment projektre.** Válassza **a Manage NuGet Packages (NuGet-csomagok kezelése) lehetőséget.**

1. A **NuGet-Csomagkezelő** válassza a **Tallózás** lehetőséget, keresse meg és válassza a **Microsoft.Azure.Devices.Provisioning.Service** lehetőséget, majd nyomja le az **Install (Telepítés) gombot.**

   ![NuGet Package Manager (NuGet-csomagkezelő) ablak](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Ez a lépés letölti és telepíti az [Azure IoT Provisioning Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) ügyféloldali SDK NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá vonatkozó hivatkozást is.

1. Adja hozzá a következő `using` utasításokat a többi utasítás után a `using` `Program.cs` tetején:
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adja hozzá a következő mezőket a `Program` osztályhoz, és kövesse az alábbi módosításokat.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Cserélje le a helyőrző értékét annak a kiépítési szolgáltatásnak a kapcsolati sztringjével, amely számára a `ProvisioningServiceConnectionString` regisztrációt létre szeretné hozni.

   * A regisztrációs azonosító, az ellenőrzőkulcs, az eszközazonosító és a regisztrációs állapot igény szerint módosítható.

   * Ha ezt a rövid útmutatót a Szimulált [TPM-eszköz](quick-create-simulated-device-tpm-csharp.md) létrehozása és kiépítése C# eszközoldali SDK-val rövid útmutatóval együtt használja egy szimulált eszköz üzembe helyezéséhez, cserélje le az ellenőrzőkulcsot és a regisztrációs azonosítót az ebben a rövid útmutatóban feljegyzett értékekre. Az eszközazonosítót lecserélheti a rövid útmutatóban javasolt értékre, használhatja a saját értékét, vagy használhatja a mintában az alapértelmezett értéket.

1. Adja hozzá a következő metódust a `Program` osztályhoz.  Ez a kód egyéni regisztrációs bejegyzést hoz létre, majd a metódust hívja meg a metódusával, hogy hozzáadja az egyéni regisztrációt a `CreateOrUpdateIndividualEnrollmentAsync` `ProvisioningServiceClient` kiépítési szolgáltatáshoz.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Végül cserélje le a `Main` metódust a következő sorokra:

   ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
   ```

1. Hozza létre a megoldást.

## <a name="run-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta futtatása
  
Futtassa a mintát a Visual Studióban a TPM-eszköz egyéni regisztrációjának létrehozásához.

Megjelenik egy parancssori ablak, és elkezdi a megerősítést kérő üzeneteket. Sikeres létrehozás esetén a parancssori ablakban megjelenik az új egyéni regisztráció tulajdonságai.

Ellenőrizheti, hogy létrejött-e az egyéni regisztráció. Kattintson a Device Provisioning Service összegzésére, és válassza a **Regisztrációk kezelése** lehetőséget, majd válassza az **Egyéni regisztrációk lehetőséget.** Meg kell jelennie egy új regisztrációs bejegyzésnek, amely megfelel a mintában használt regisztrációs azonosítónak is.

![A regisztráció tulajdonságai a portálon](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Válassza ki a bejegyzést az ellenőrzőkulcs és a bejegyzés egyéb tulajdonságainak ellenőrzéséhez.

Ha követte a Szimulált [TPM-eszköz](quick-create-simulated-device-tpm-csharp.md) létrehozása és kiépítése C# eszközoldali SDK-val rövid útmutató lépéseit, folytathatja a rövid útmutató további lépéseit a szimulált eszköz regisztrálásához. Hagyja ki azon lépéseket, amelyek az egyéni regisztrációk Azure Portallal való létrehozására vonatkoznak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy megvizsgálja a C#-szolgáltatásmintát, ne az ebben a rövid útmutatóban létrehozott erőforrásokat takarítsa meg. Ellenkező esetben a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be a C#-minta kimeneti ablakát a számítógépen.

1. Lépjen a Device Provisioning Service-hez a Azure Portal válassza a **Regisztrációk** kezelése lehetőséget, majd válassza az Egyéni **regisztrációk** lapot. Jelölje be a rövid  útmutatóval létrehozott regisztrációs bejegyzés regisztrációs azonosítója melletti jelölőnégyzetet, majd kattintson a panel tetején található Törlés gombra. 

1. Ha követte a Szimulált [TPM-eszköz](quick-create-simulated-device-tpm-csharp.md) létrehozása és kiépítése C# eszközoldali SDK-val egy szimulált TPM-eszköz létrehozásához lépéseit, tegye a következőket:

    1. Zárja be a TPM-szimulátor ablakát és a szimulált eszköz mintakódkimeneti ablakát.

    1. Az Azure Portalon lépjen ahhoz az IoT Hubhoz, ahol regisztrálta az eszközt. Az **Explorerek** menüben válassza az **IoT-eszközök** lehetőséget,  jelölje be a rövid útmutatóban regisztrált eszköz  eszközazonosítója melletti jelölőnégyzetet, majd kattintson a panel tetején található Törlés gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban programozott módon létrehozott egy egyéni regisztrációs bejegyzést egy TPM-eszközhöz. Szükség esetén létrehozott egy szimulált TPM-eszközt a számítógépén, és kiépítette azt az IoT Hubon az Azure IoT Hub Device Provisioning Service használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)