---
title: Oktatóanyag – eszköz kiépítése az Azure IoT Hub Device Provisioning Service (.NET) használatával
description: Ez az oktatóanyag bemutatja, hogyan építheti ki az eszközt egyetlen IoT hubhoz az Azure IoT Hub Device Provisioning Service (DPS) használatával a .NET használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f9a14ee6ee3e10b36d64ec11fc23807efe2bfaf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966564"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Oktatóanyag: az eszköz regisztrálása egy IoT hubhoz az Azure IoT Hub-létesítési szolgáltatás ügyfelének használatával (.NET)

Az előző oktatóanyagban bemutattuk, hogyan állíthat be egy eszközt úgy, hogy az a Device Provisioning Service-hez csatlakozzon. Ebben az oktatóanyagban megtudhatja, hogyan építheti ki az eszközt ezzel a szolgáltatással egyetlen IoT Hubra, **_egyéni regisztráció_** és **_regisztrációs csoportok_** használatával. Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Az eszköz regisztrálása
> * Az eszköz elindítása
> * Az eszköz sikeres regisztrálásának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy [a kiépítendő eszköz az Azure IoT Hub Device Provisioning Service használatával történő beállítását](./tutorial-set-up-device.md) ismertető oktatóanyagban leírt módon konfigurálta az eszközt és annak *hardverbiztonsági modulját*.

* Visual Studio

> [!NOTE]
> Visual Studio nem szükséges. Elegendő a [.NET](https://www.microsoft.com/net) telepítése, és a fejlesztők az általuk előnyben részesített, Windowson vagy Linuxon futó szerkesztőt használhatják.  

Ez az oktatóanyag a hardvergyártási folyamat közbeni vagy az azutáni időszakot szimulálja, amikor az eszközinformációt hozzáadják a regisztrációs szolgáltatáshoz. Ez a kód általában egy számítógépen vagy egy .NET-kód futtatására képes gyári eszközön fut, és nem szabad hozzáadni magukhoz az eszközökhöz.


## <a name="enroll-the-device"></a>Az eszköz regisztrálása

Ennek a lépésnek a részét képezi az eszköz egyedi biztonsági összetevőinek a Device Provisioning Service-hez adása. Ezek a biztonsági összetevők a következők:

- TPM-alapú eszközök esetén:
    - Az egyes TPM-lapkákhoz vagy szimulációkhoz tartozó egyedi *ellenőrzőkulcs*. További információkért olvassa el a [TPM-ellenőrzőkulcsot ismertető](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) szakaszt.
    - A névtérben/hatókörben lévő eszköz egyedi azonosítására használt *regisztrációs azonosító*. Ez nem feltétlenül egyezik meg az eszköz azonosítójával. Az azonosító minden eszközhöz kötelező. TPM-alapú eszközök esetén a regisztrációs azonosító magából a TPM-ből származhat, például a TPM-ellenőrzőkulcs SHA-256 kivonata lehet.

- X.509-alapú eszközök esetén:
    - Az [eszközhöz kiadott X.509-tanúsítvány](/windows/win32/seccertenroll/about-x-509-public-key-certificates)*.pem* vagy *.cer* fájlformátumban. Egyéni regisztrációhoz az X.509 rendszer *levéltanúsítványát*, míg regisztrációs csoportok esetén a *főtanúsítványt vagy egy egyenértékű* aláíró tanúsítványt *kell használni*.
    - A névtérben/hatókörben lévő eszköz egyedi azonosítására használt *regisztrációs azonosító*. Ez nem feltétlenül egyezik meg az eszköz azonosítójával. Az azonosító minden eszközhöz kötelező. Az X.509-alapú eszközök esetén a regisztrációs azonosító a tanúsítvány köznapi nevéből (CN) származik. A követelményekkel kapcsolatos további információkért tekintse meg az [eszközökkel kapcsolatos alapelveket ismertető](./concepts-service.md) témakört.

A következő két módon regisztrálható az eszköz a Device Provisioning Service-ben:

- **Egyéni regisztrációk** A Device Provisioning Service-szel regisztrálható egyetlen eszközhöz tartozó bejegyzés. Az egyéni regisztrációk X.509-tanúsítványokat vagy SAS-tokeneket használhatnak (valós vagy virtuális TPM-ben) igazolási mechanizmusként. Az egyedi kezdeti konfigurációt igénylő vagy az igazolási mechanizmusként kizárólag TPM-en keresztül SAS-tokeneket használó eszközökhöz egyéni regisztrációk használatát javasoljuk. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

- **Regisztrációs csoportok** Közös igazolási mechanizmussal rendelkező eszközök csoportja. Nagy számú, azonos kívánt kezdeti konfigurációval rendelkező eszközhöz vagy azonos bérlőt célzó eszközökhöz érdemes regisztrációs csoportot használni. A regisztrációs csoportok mind X.509-alapúak, és az X.509-tanúsítványláncukban ugyanazon aláíró tanúsítvánnyal rendelkeznek.

### <a name="enroll-the-device-using-individual-enrollments"></a>Az eszköz regisztrálása Egyéni regisztrációk használatával

1. A Visual Studióban hozzon létre egy Visual C# konzolalkalmazás-projektet a **Konzolalkalmazás** projektsablonnal. Adja a projektnek a **DeviceProvisioning** (Eszközkiépítés) nevet.
    
1. A Megoldáskezelőben kattintson a jobb gombbal a **DeviceProvisioning** projektre, majd kattintson a **NuGet-csomagok kezelése...** parancsra.

1. A **NuGet-csomagkezelő** ablakban válassza a **Tallózás** lehetőséget, és keresse meg a **microsoft.azure.devices.provisioning.service** csomagot. Jelölje ki az elemet, és kattintson a **Telepítés** gombra a **Microsoft.Azure.Devices.Provisioning.Service** csomag telepítéséhez, majd fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT eszközkiépítési szolgáltatási SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban feljegyzett Device Provisioning Service-szolgáltatás kapcsolati sztringjére.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Adja hozzá a következőt az eszköz regisztrációjának megvalósításához:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Végül adja hozzá a következő kódot a **Fő** metódushoz az IoT Hub-kapcsolat megnyitásához és a regisztráció indításához:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. A Visual Studio Megoldáskezelő kattintson a jobb gombbal a megoldásra, majd kattintson az **indítási projektek beállítása.**.. elemre. Válassza az **egyetlen indítási projekt** lehetőséget, majd a legördülő menüben válassza ki a **DeviceProvisioning** projektet.  

1. Futtassa a **DeviceProvisiong** .NET-eszközalkalmazást. Az eszközalkalmazás beállítja az eszköz kiépítését: 

    ![Egyéni regisztráció futtatása](./media/tutorial-net-provision-device-to-hub/individual.png)

Sikeres regisztráció után az eszköznek a következőképpen kell megjelennie a portálon:

   ![Sikeres regisztráció a portálon](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Az eszköz regisztrálása Regisztrációs csoportok használatával

> [!NOTE]
> A regisztrációs mintacsoporthoz egy X.509-tanúsítvány szükséges.

1. A Visual Studio Megoldáskezelőjében nyissa meg a fent létrehozott **DeviceProvisioning** projektet. 

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át az X.509-tanúsítvány helyére.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. A csoport regisztrációjának megvalósításához adja hozzá a következőt a **Program.cs** fájlhoz:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Végül az IoT Hub-kapcsolat megnyitásához és a csoportos regisztráció indításához cserélje le a **Fő** metódust a következő kódra:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Futtassa a **DeviceProvisiong** .NET-eszközalkalmazást. Az eszközalkalmazás beállítja az eszköz csoportos kiépítését: 

    ![Csoportos regisztráció futtatása](./media/tutorial-net-provision-device-to-hub/group.png)

    Sikeres regisztráció után az eszközcsoportnak a következőképpen kell megjelennie a portálon:

   ![Sikeres csoportregisztráció a portálon](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Az eszköz elindítása

Ekkor a következő beállítás van készen az eszköz regisztrációjához:

1. Az eszköz vagy eszközcsoport regisztrálva van a Device Provisioning Service-ben, és 
2. Az eszköz biztonsági konfigurációja elkészült, amely a Device Provisioning Service ügyféloldali SDK-val érhető el az alkalmazáson keresztül.

Indítsa el az eszközt, hogy az ügyfélalkalmazás elindíthassa a Device Provisioning Service-szel való regisztrációt.  


## <a name="verify-the-device-is-registered"></a>Az eszköz sikeres regisztrálásának ellenőrzése

Az eszköz indítása után a következő műveleteket kell elvégezni. További részletekért tekintse meg az [eszközregisztrációs ügyfélmintát](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device). 

1. Az eszköz regisztrációs kérést küld a Device Provisioning Service-nek.
2. TPM-eszközök esetén a Device Provisioning Service visszaküld egy regisztrációs kihívást, amelyre az eszköz válaszol. 
3. Sikeres regisztráció esetén a Device Provisioning Service visszaküldi az eszköznek az IoT Hub URI-jét, az eszközazonosítót és a titkosított kulcsot. 
4. Az eszközön lévő IoT Hub-ügyfélalkalmazás ekkor csatlakozik a hubhoz. 
5. A hubhoz való sikeres csatlakozás esetén látnia kell az eszközt az IoT Hub **Device Explorer** paneljén. 

    ![Sikeres csatlakozás a hubhoz a portálon](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az eszköz regisztrálása
> * Az eszköz elindítása
> * Az eszköz sikeres regisztrálásának ellenőrzése

A következő oktatóanyag azt mutatja be, hogyan regisztrálhat több eszközt az elosztott terhelésű hubok között. 

> [!div class="nextstepaction"]
> [Eszközök regisztrálása elosztott terhelésű IoT Hubokon](./tutorial-provision-multiple-hubs.md)