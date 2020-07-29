---
title: Örökölt eszközök kiépítése szimmetrikus kulcsok használatával – Azure IoT Hub Device Provisioning Service
description: A szimmetrikus kulcsok használata a régi eszközök kiépítéséhez az eszköz kiépítési szolgáltatásának (DPS) példányával
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75434714"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Örökölt eszközök kiépítése szimmetrikus kulcsok használatával

Számos régi eszközzel kapcsolatos gyakori probléma, hogy gyakran van olyan identitásuk, amely egyetlen információból áll. Ezek az azonosító adatok általában MAC-címek vagy sorozatszámok. Az örökölt eszközökhöz nem tartozhat tanúsítvány, TPM vagy más olyan biztonsági funkció, amely az eszköz biztonságos azonosítására szolgál. Az IoT hub eszköz-kiépítési szolgáltatása magában foglalja a szimmetrikus kulcs igazolását. A szimmetrikus kulcs igazolásával azonosítható egy eszköz, például a MAC-címe vagy sorozatszáma alapján.

Ha egyszerűen telepítheti a [hardveres biztonsági modult (HSM)](concepts-security.md#hardware-security-module) és a tanúsítványt, akkor ez jobb megoldás lehet az eszközök azonosításához és üzembe helyezéséhez. Mivel ez a megközelítés lehetővé teszi, hogy megkerüljék az összes eszközre telepített kód frissítését, és nem rendelkezik az eszköz lemezképében beágyazott titkos kulccsal.

Ez a cikk azt feltételezi, hogy egyik HSM vagy tanúsítvány sem életképes megoldás. Azonban feltételezhető, hogy az eszközök üzembe helyezéséhez az eszköz kiépítési szolgáltatásával valamilyen módon frissítheti az eszköz kódját. 

A cikk azt is feltételezi, hogy az eszköz frissítése biztonságos környezetben történik, hogy megakadályozza a főcsoporti kulcs vagy a származtatott eszköz kulcsának jogosulatlan elérését.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.

> [!NOTE]
> A cikkben használt minta C nyelven íródott. Létezik egy [C# eszköz kiépítési szimmetrikus kulcsának mintája](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) is. A minta használatához töltse le vagy klónozott [Azure-IOT-Samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) adattárát, és kövesse a mintakód beépített utasításait. A cikk utasításait követve hozzon létre egy szimmetrikus kulcsú beléptetési csoportot a portál használatával, és keresse meg az azonosító hatókörét és a beléptetési csoportot, amely a minta futtatásához szükséges elsődleges és másodlagos kulcsokat tartalmaz. A minta használatával egyéni regisztrációkat is létrehozhat.

## <a name="overview"></a>Áttekintés

Minden eszközhöz egyedi regisztrációs azonosítót határozunk meg az eszközt azonosító információk alapján. Például a MAC-címe vagy sorozatszáma.

A rendszer az eszköz kiépítési szolgáltatásával hozza létre a [szimmetrikus kulcsú igazolást](concepts-symmetric-key-attestation.md) használó regisztrációs csoportot. A regisztrációs csoport tartalmazni fog egy csoport főkulcsát. Ez a főkulcs az egyes egyedi regisztrációs AZONOSÍTÓk kivonatolására szolgál az egyes eszközökhöz tartozó egyedi eszköz kulcsának létrehozásához. Az eszköz ezt a származtatott eszközt használja az egyedi regisztrációs AZONOSÍTÓval, hogy tanúsítsa az eszköz kiépítési szolgáltatását, és hozzá legyen rendelve egy IoT hubhoz.

A cikkben bemutatott kód a gyors üzembe helyezési ponttal megegyező mintázatot követ [: szimulált eszköz kiépítése szimmetrikus kulcsokkal](quick-create-simulated-device-symm-key.md). A kód az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-ból származó minta használatával szimulálja az eszközt. A szimulált eszköz egy regisztrációs csoporttal fog tanúsítani egy, a rövid útmutatóban bemutatott módon, egyéni regisztráció helyett.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* A [beállított IoT hub Device Provisioning Service befejezése a Azure Portal](./quick-setup-auto-provision.md) rövid útmutatóval.

A következő előfeltételek a Windows fejlesztési környezetéhez szükségesek. Linux vagy macOS esetén tekintse meg a [fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) az SDK-ban című dokumentáció megfelelő szakaszát.

* A [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019-es verziójában engedélyezve van az ["asztali fejlesztés C++](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) -ban" számítási feladattal. A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészítjük az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) build készítésére szolgáló fejlesztőkörnyezetet. 

Az SDK tartalmazza a szimulált eszközhöz tartozó mintakód kódját. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

1. Töltse le a [Csatlakozáskezelő felügyeleti csomag Build-szolgáltatását](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásához](https://github.com/Azure/azure-iot-sdk-c/releases/latest) tartozó címke nevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben megtalált címkét a paraméter értékeként `-b` :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a következő parancsokat a `azure-iot-sdk-c` címtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Szimmetrikus kulcsú beléptetési csoport létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg az eszköz kiépítési szolgáltatásának példányát.

2. Válassza a **regisztrációk kezelése** fület, majd kattintson a **regisztrációs csoport hozzáadása** gombra az oldal tetején. 

3. A **regisztrációs csoport hozzáadása**párbeszédpanelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

   - **Csoport neve**: adja meg a **mylegacydevices**.

   - **Igazolás típusa**: válassza a **szimmetrikus kulcs**lehetőséget.

   - **Kulcsok automatikus létrehozása**: Jelölje be ezt a jelölőnégyzetet.

   - **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a hubokhoz**: válassza a **statikus konfiguráció** lehetőséget, hogy egy adott hubhoz rendeljen hozzá.

   - **Válassza ki azokat a IoT hubokat, amelyekre ez a csoport hozzá lehet rendelni**: válassza ki az egyik hubok közül a megfelelőt.

     ![Beléptetési csoport hozzáadása a szimmetrikus kulcs igazolásához](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Ha mentette a regisztrációt, a rendszer létrehozza az **Elsődleges kulcsot** és a **Másodlagos kulcsot**, majd hozzáadja őket a regisztrációs bejegyzéshez. A szimmetrikus kulcs beléptetési csoportja **mylegacydevices** néven jelenik meg a *beléptetési csoportok* lapon, a *csoport neve* oszlopban. 

    Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**. Ez a kulcs a főcsoport kulcsa.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Válasszon egyedi regisztrációs azonosítót az eszközhöz

Az egyes eszközök azonosításához egyedi regisztrációs azonosítót kell megadni. Az eszköz MAC-címe, sorozatszáma vagy bármely egyedi adata használható. 

Ebben a példában egy MAC-címe és sorozatszáma kombinációját használjuk, amely a következő karakterláncot képezi a regisztrációs AZONOSÍTÓhoz.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Hozzon létre egy egyedi regisztrációs azonosítót az eszközhöz. Az érvényes karakterek a kisbetűs alfanumerikus és kötőjel ("-").


## <a name="derive-a-device-key"></a>Eszköz kulcsának származtatása 

Az eszköz kulcsának létrehozásához használja a csoport főkulcsát az eszköz egyedi regisztrációs AZONOSÍTÓjának [HMAC](https://wikipedia.org/wiki/HMAC) számításához, és Base64 formátumra alakítsa át az eredményt.

Ne foglalja bele a csoport főkulcsát az eszköz kódjába.


#### <a name="linux-workstations"></a>Linux-munkaállomások

Ha Linux-munkaállomást használ, az OpenSSL használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

Cserélje le a **REG_ID** értékét a regisztrációs azonosítóra.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows-alapú munkaállomások

Ha Windows-alapú munkaállomást használ, a PowerShell használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

Cserélje le a **REG_ID** értékét a regisztrációs azonosítóra.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Az eszköz a származtatott eszköz kulcsát az egyedi regisztrációs azonosítójával fogja használni a kiépítés során a beléptetési csoportba tartozó szimmetrikus kulcs igazolásának elvégzéséhez.



## <a name="create-a-device-image-to-provision"></a>Rendszerkép létrehozása a kiépíthető eszközhöz

Ebben a szakaszban a korábban beállított Azure IoT C SDK-ban található **prov \_ dev \_ Client \_ minta** nevű kiépítési mintát fogja frissíteni. 

Ez a mintakód szimulál egy eszköz rendszerindítási sorozatot, amely elküldi a kiépítési kérést az eszköz kiépítési szolgáltatásának példányára. A rendszerindítási folyamat azt eredményezi, hogy az eszköz fel lesz ismerve, és hozzá lesz rendelve a beléptetési csoportban konfigurált IoT hubhoz.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg azt a **azure_iot_sdks. SLN** -megoldást, amelyet korábban a CMAK futtatása hozott létre. A megoldásfájlnak a következő helyen kell lennie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg róla, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, mint az alább is látható:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Keresse meg a " `prov_dev_set_symmetric_key_info()` **prov \_ dev \_ Client \_ sample. c** " meghívását, amely kommentálva van.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Adja meg a függvény hívását, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket is) az eszköz egyedi regisztrációs azonosítójával és a létrehozott származtatott eszköz kulcsával.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Mentse a fájlt.

7. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

8. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    Az alábbi példakimeneten látható, hogy a szimulált eszköz sikeresen elindul és csatlakozik a regisztrációs szolgáltatáspéldányhoz, hogy az hozzárendelhesse egy IoT-központhoz:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. A portálon navigáljon a szimulált eszközhöz rendelt IoT hubhoz, és kattintson a IoT- **eszközök** fülre. Ha sikeresen kiépíti a szimulált eszközt a központba, az eszköz azonosítója megjelenik az **IoT-eszközök** panelen, amely **engedélyezve** *állapotú* . Lehet, hogy rá kell kattintania fent a **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Biztonsági okokból

Ügyeljen arra, hogy ez a rendszerkép részét képező származtatott eszköz kulcsát elhagyja, amely nem ajánlott biztonsági megoldás. Ez az egyik oka annak, hogy a biztonság és a könnyű használat Milyen kompromisszumokat jelent. 





## <a name="next-steps"></a>További lépések

* További információ: [IoT hub eszköz](concepts-device-reprovision.md) újraépítése 
* [Rövid útmutató: Szimmetrikus kulcs kiosztása szimulált eszköz számára](quick-create-simulated-device-symm-key.md)
* További részletekért lásd: [az előzőleg automatikusan kiépített eszközök](how-to-unprovision-devices.md) kiépítése. 











