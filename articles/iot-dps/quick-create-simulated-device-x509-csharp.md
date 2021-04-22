---
title: 'Rövid útmutató – Szimulált X.509-eszköz üzembe Azure IoT Hub C használatával #'
description: Rövid útmutató – X.509-eszköz létrehozása és kiépítése a Device Provisioning Service Azure IoT Hub (DPS) szolgáltatáshoz készült C# eszközoldali SDK-val. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 7aca75d1abed5470d51de22f9285459381f684bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868590"
---
# <a name="quickstart-create-and-provision-an-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: X.509-eszköz létrehozása és kiépítése az IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ezek a lépések azt mutatják be, hogyan használhatók az X.509-eszközök az [Azure IoT-minták C#-hoz](https://github.com/Azure-Samples/azure-iot-samples-csharp) való használatával. Ebben a cikkben eszköz mintakódot fog futtatni a fejlesztői gépen, hogy egy virtuális géphez csatlakozzon IoT Hub Device Provisioning Service használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át a [kiépítés áttekintését.](about-iot-dps.md#provisioning-process) Emellett a folytatás előtt végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit.

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése 

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. Nyisson meg egy parancssort vagy a Git Basht. Klónozza az Azure IoT-mintákat a C# GitHub-adattárhoz:
    
    ```bash
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Győződjön meg arról, hogy [a .NET Core 3.1 SDK vagy](https://dotnet.microsoft.com/download) újabb telepítve van a gépen. Az alábbi paranccsal ellenőrizheti a verzióját.

    ```bash
    dotnet --info
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Önaláírt X.509-eszköztanúsítvány létrehozása

Ebben a szakaszban létre fog hozni egy önaírt X.509 teszt tanúsítványt a tulajdonos `iothubx509device1` közneveként. Fontos szem előtt tartani az alábbi szempontokat:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.
* Az IoT-eszköz eszközazonosítója lesz a tulajdonos közneve a tanúsítványon. Ügyeljen arra, hogy olyan tulajdonosnevet használjon, amely megfelel az Eszközazonosító sztring [követelményeinek.](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)

Az [X509Sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) mintakódját fogja használni az eszköz egyéni regisztrációs bejegyzéséhez használni szükséges tanúsítvány létrehozásához.


1. Egy PowerShell-parancssorban váltsa át a könyvtárakat az X.509 eszközátépítési minta projektkönyvtárára.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. A mintakód úgy lett beállítva, hogy egy jelszóval védett PKCS12 formázott fájlban (certificate.pfx) tárolt X.509-tanúsítványokat használja. Emellett szüksége lesz egy nyilvános kulcsú tanúsítványfájlra (certificate.cer), amely a rövid útmutató későbbi, egyéni regisztrációját hozza létre. Az önaírt tanúsítvány és a hozzá tartozó .cer és .pfx fájlok létrehozásához futtassa a következő parancsot:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. A szkript kér egy PFX-jelszót. Jegyezzük meg ezt a jelszót, mert később ismét használnia kell a minta futtatásakor. A futtatásával `certutil` kiírással kiírást futtathat a tanúsítványon, és ellenőrizheti a tulajdonos nevét.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>Egyéni regisztrációs bejegyzés létrehozása az eszközhöz


1. Jelentkezzen be a Azure Portal, válassza  a bal oldali menü Minden erőforrás gombját, és nyissa meg a kiépítési szolgáltatást.

2. A Device Provisioning Service menüjében válassza a **Regisztrációk kezelése lehetőséget.** Válassza **az Egyéni regisztrációk** lapot, és válassza a felül található **Egyéni regisztráció** hozzáadása gombot. 

3. A Regisztráció **hozzáadása panelen** adja meg a következő adatokat:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Az Elsődleges *tanúsítványfájl (.pem vagy .cer)* alatt válassza *a Fájl* kiválasztása lehetőséget az előző lépésekben létrehozott **certificate.cer** tanúsítványfájl kiválasztásához.
   - Az **Eszközazonosító** mezőt hagyja üresen. Az eszköz kiépítésekor a rendszer az eszközazonosítót az X.509-tanúsítványban lévő **iothubx509device1** köznapi névre állítja be. Ez a köznév lesz az egyéni regisztrációs bejegyzés regisztrációs azonosítójának neve is. 
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Ha elkészült, kattintson a **Mentés gombra.** 

     [![Egyéni regisztráció hozzáadása X.509-igazoláshoz a portálon](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Sikeres regisztráció esetén az X.509 regisztrációs bejegyzés **iothubx509device1** azonosítóval jelenik meg a *Regisztrációs azonosító* oszlopban az *Egyéni regisztrációk* lapon. 



## <a name="provision-the-device"></a>Az eszköz kiépítése

1. Jegyezze **fel** az Azonosító hatóköre értéket a kiépítési szolgáltatás **_Áttekintés paneljéhez._**

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Írja be az alábbi parancsot az X.509-eszköz kiépítési mintájának összeállításához és futtatásához. Az `<IDScope>` értéket cserélje le a kiépítési szolgáltatás Azonosító hatóköre értékére. 

    A tanúsítványfájl alapértelmezés szerint *./certificate.pfx* lesz, és a rendszer kéri a .pfx jelszót.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Ha mindent paraméterként szeretne átadni, használja az alábbi példaformátumot.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. Az eszköz csatlakozik a DPS-hez, és hozzárendeli egy IoT Hub. Az eszköz telemetriai üzenetet is küld a hubnak.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

4. Ellenőrizze, hogy a rendszer kiépítette-e az eszközt. Ha sikeresen kiépíti az eszközt a kiépítési szolgáltatáshoz csatolt IoT Hubon, az eszköz azonosítója megjelenik a hub **IoT-eszközök panelén.** 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Az ikereszköz-használat](../iot-hub/iot-hub-devguide-device-twins.md) és az ikereszköz-használat IoT Hub


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszköz ügyfélmintán való munkát és annak felfedezését, akkor ne tisztítsa meg az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. A bal oldali menüben válassza Azure Portal **Összes** erőforrás lehetőséget, majd a Device Provisioning Service-t. Az Áttekintés panel **tetején** kattintson a **Törlés gombra** a panel tetején.  
1. A bal oldali menüben válassza a Azure Portal az Összes erőforrás **lehetőséget,** majd válassza ki az IoT Hubot. Az Áttekintés panel **tetején** kattintson a **törlés gombra** a panel tetején.  

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy X.509-eszközt létesített az IoT Hubon az Azure IoT Hub Device Provisioning Service használatával. Ha meg szeretne ismerkedni az X.509-eszköz programozott regisztrálásának menetével, folytassa az X.509-eszközök programozott regisztrációjának rövid útmutatójában. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X.509-eszközök regisztrálása Azure IoT Hub Device Provisioning Service-be](quick-enroll-device-x509-csharp.md)
