---
title: 'Rövid útmutató – X.509-eszköz regisztrálása az Azure Device Provisioning Service-be a C használatával #'
description: Ez a rövid útmutató csoportos regisztrációkat használ. Ebben a rövid útmutatóban X.509-eszközöket regisztrál a Azure IoT Hub Device Provisioning Service (DPS) szolgáltatásba a C# használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b6b9331007ec633fe8b8368bd493ce18b4ddde24
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862092"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Rövid útmutató: X.509-eszközök regisztrációja a Device Provisioning Service-be a C# használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre a C# segítségével programozott módon egy [regisztrációs csoportot](concepts-service.md#enrollment-group), amely köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítványokat használ. A regisztrációs csoport a [.NET-hez készült Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-csharp) és egy C# .NET Core-mintaalkalmazás használatával jön létre. Egy regisztrációs csoport a tanúsítványláncukban ugyanazon aláíró tanúsítvánnyal rendelkező eszközök kiépítési szolgáltatáshoz való hozzáférését szabályozza. További tudnivalókért lásd: [Eszközök kiépítési szolgáltatáshoz való hozzáférésének szabályozása X.509-tanúsítványokkal](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). További információ az X.509-tanúsítványon alapuló nyilvánoskulcs-infrastruktúra (PKI) az Azure IoT Hubbal és a Device Provisioning Service-szel való használatáról: [X.509 hitelesítésszolgáltatói tanúsítványok biztonsági áttekintése](../iot-hub/iot-hub-x509ca-overview.md). 

Ez a rövid útmutató azt várja, hogy már létrehozott egy IoT Hubot és egy Device Provisioning Service-példányt. Ha még nem hozta létre ezeket [](./quick-setup-auto-provision.md) az erőforrásokat, a cikk folytatása előtt IoT Hub Device Provisioning Service a Azure Portal rövid útmutatót.

Bár a cikkben található lépések Windows és Linux rendszerű számítógépeken is működnek, ez a cikk windowsos fejlesztési számítógépet használ.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse [Visual Studio 2019-es Visual Studio.](https://www.visualstudio.com/vs/)
* Telepítse [a .NET Core SDK.](https://dotnet.microsoft.com/download)
* Telepítse a [Gitet.](https://git-scm.com/download/)

## <a name="prepare-test-certificates"></a>Teszttanúsítványok előkészítése

A rövid útmutatóhoz szükség van egy .pem vagy .cer fájlra, amely tartalmazza egy köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítvány nyilvános részét. A tanúsítványnak a kiépítési szolgáltatásba feltöltöttnek és a szolgáltatás által ellenőrzöttnek kell lennie.

Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) olyan tesztelési eszközkészleteket tartalmaz, amelyek segítségével X.509-tanúsítványláncot hozhat létre, feltölthet egy fő- vagy köztes tanúsítványt a láncból, és tulajdonlástanúsítványt hozhat létre a szolgáltatással a tanúsítvány ellenőrzéséhez.

> [!CAUTION]
> Az SDK-eszközkészletekkel létrehozott tanúsítványokat csak fejlesztési tesztelésre használja.
> Ne használja ezeket a tanúsítványokat éles környezetben.
> Nem kódolt jelszavakat tartalmaznak ,például *1234,* amelyek 30 nap után lejárnak.
> A termelési használathoz megfelelő tanúsítványok beszerzésével kapcsolatos további információt az Azure IoT Hub dokumentációjának [X.509 hitelesítésszolgáltatói tanúsítvány beszerzése](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) című részében talál.
>

Ha ezzel a tesztelési eszközzel tanúsítványokat generál, tegye a következőket:

1. Keresse meg az [](https://github.com/Azure/azure-iot-sdk-c/releases/latest) Azure IoT C SDK legújabb kiadásának címkenevét.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet, és lépjen egy, a gépen található munkamappába. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattár legújabb kiadásának klónozásához. Használja az előző lépésben talált címkét a paraméter `-b` értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

   A teszteszköz a klónozott adattár *azure-iot-sdk-c/tools/CACertificates* mappájában található.

3. Kövesse a [mintákhoz és oktatóanyagokhoz készült hitelesítésszolgáltatói tanúsítványok kezeléséről](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) szóló cikk lépéseit.

A C SDK-ban lévő eszközök [](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) mellett a .NET-hez készült *Microsoft Azure IoT SDK* csoporttanúsítvány-ellenőrzési mintája bemutatja, hogyan lehet birtoklási igazolást C#-ban, meglévő köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítvánnyal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>A kiépítési szolgáltatás kapcsolati sztringjének lekérése

A rövid útmutatóban lévő mintához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére.

1. Jelentkezzen be a Azure Portal, válassza a **Minden erőforrás** lehetőséget, majd a Device Provisioning Service-t.

1. Válassza **a Megosztott hozzáférési szabályzatok** lehetőséget, majd válassza ki a használni kívánt hozzáférési szabályzatot a tulajdonságainak megnyitásához. A **Hozzáférési szabályzat elemben** másolja ki és mentse az elsődleges kulcs kapcsolati sztringet.

    ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>A regisztrációs csoport mintájának létrehozása 

Ez a szakasz bemutatja, hogyan hozhat létre olyan .NET Core-konzolalkalmazást, amely regisztrációs csoportot ad hozzá a regisztrációs szolgáltatáshoz. Néhány módosítással ezeket a lépéseket követve létrehozhat egy [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konzolalkalmazást is a regisztrációs csoport hozzáadásához. További információk az IoT Core használatával való fejlesztésről: [Windows IoT Core – fejlesztői dokumentáció](/windows/iot-core/).

1. Nyissa Visual Studio, és válassza **az Új projekt létrehozása lehetőséget.** Az **Új projekt létrehozása elemben válassza a** Console App (.NET Core) for C# (Konzolalkalmazás (.NET Core) a C#-projekthez) sablont, majd kattintson a Next (Tovább)  **gombra.**

1. A projektnek nevezze el *a CreateEnrollmentGroup nevet,* majd nyomja le a **Create (Létrehozás) gombot.**

    ![Visual C# Windows klasszikus asztali projekt konfigurálása](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Amikor a megoldás a Visual Studio nyílik meg, Megoldáskezelő **jobb** gombbal kattintson a **CreateEnrollmentGroup** projektre, majd válassza a **NuGet-csomagok kezelése lehetőséget.**

1. A **NuGet-Csomagkezelő** válassza a **Tallózás** lehetőséget, keresse meg és válassza ki a **Microsoft.Azure.Devices.Provisioning.Service** szolgáltatásokat, majd nyomja le az **Install (Telepítés) gombot.**

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Ez a lépés letölti és telepíti az [Azure IoT Provisioning Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) ügyféloldali SDK NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá vonatkozó hivatkozást is.

1. Adja hozzá a következő `using` utasításokat a többi utasítás után a `using` `Program.cs` tetején:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adja hozzá a következő mezőket a `Program` osztályhoz, és kövesse a felsorolt módosításokat.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Cserélje le a helyőrző értékét annak a kiépítési szolgáltatásnak a kapcsolati sztringjével, amely számára a `ProvisioningServiceConnectionString` regisztrációt létre szeretné hozni.

   * Cserélje le `X509RootCertPath` a helyőrző értékét egy .pem- vagy .cer-fájl elérési útjára. Ez a fájl egy köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítvány nyilvános részét jelöli, amelyet korábban feltöltöttek és ellenőriztek a kiépítési szolgáltatással.

   * Igény szerint módosíthatja az `EnrollmentGroupId` értéket. A sztring csak kisbetűs karaktereket és kötőjelet tartalmazhat.

   > [!IMPORTANT]
   > Az éles kódban vegye figyelembe a következő biztonsági szempontokat:
   >
   > * A kapcsolati sztring fix kódolása a kiépítési szolgáltatás rendszergazdája esetében nem felel meg az ajánlott biztonsági eljárásoknak. Ehelyett biztonságosan kell tárolni a sztringet, például egy biztonságos konfigurációs fájlban vagy a beállításjegyzékben.
   > * Ügyeljen arra, hogy az aláíró tanúsítványnak csak a nyilvános részét töltse fel. Soha ne töltse fel a kiépítési szolgáltatás titkos kulcsait tartalmazó .pfx (PKCS12) vagy .pem fájlt.

1. Adja hozzá a következő metódust a `Program` osztályhoz. Ez a kód létrehoz egy regisztrációscsoport-bejegyzést, majd a metódust hívja meg, amely hozzáadja a regisztrációs csoportot a `CreateOrUpdateEnrollmentGroupAsync` `ProvisioningServiceClient` regisztrációs szolgáltatáshoz.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
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

## <a name="run-the-enrollment-group-sample"></a>A regisztrációs mintacsoport futtatása
  
Futtassa a mintát a Visual Studióban a regisztrációs csoport létrehozásához. Megjelenik egy parancssori ablak, és elkezdi a megerősítést kérő üzeneteket. Sikeres létrehozás esetén a parancssori ablakban megjelenik az új regisztrációs csoport tulajdonságai.

Ellenőrizheti, hogy létrejött-e a regisztrációs csoport. A Device Provisioning Service összefoglalásában válassza a **Regisztrációk kezelése** lehetőséget, majd válassza a **Regisztrációs csoportok lehetőséget.** Meg kell jelennie egy új regisztrációs bejegyzésnek, amely megfelel a mintában használt regisztrációs azonosítónak is.

![A regisztráció tulajdonságai a portálon](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Válassza ki a bejegyzést a tanúsítvány ujjlenyomatának és a bejegyzés egyéb tulajdonságainak ellenőrzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy megvizsgálja a C#-szolgáltatásmintát, ne tisztítsa meg az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben a következő lépésekkel törölheti a rövid útmutató által létrehozott összes erőforrást.

1. Zárja be a C#-minta kimeneti ablakát a számítógépen.

1. Lépjen a Device Provisioning Service-hez a Azure Portal, válassza a **Regisztrációk kezelése,** majd a **Regisztrációs csoportok lehetőséget.** Válassza ki *az ebben* a rövid útmutatóban létrehozott regisztrációs bejegyzés regisztrációs azonosítóját, majd nyomja le a **Delete (Törlés) gombot.**

1. A Azure Portal eszköz kiépítési szolgáltatásában válassza a Tanúsítványok **lehetőséget,** válassza ki a  rövid útmutatóhoz feltöltött tanúsítványt, majd kattintson a Törlés gombra a Tanúsítvány **részletei lap tetején.**  

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy regisztrációs csoportot egy köztes vagy fő hitelesítésszolgáltatói X.509-tanúsítványhoz az Azure IoT Hub Device Provisioning Service használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)