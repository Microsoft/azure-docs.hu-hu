---
title: Oktatóanyag – eszköz beállítása az Azure IoT Hub Device Provisioning Service
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az eszközt a IoT Hub Device Provisioning Service (DPS) használatával az eszköz gyártási folyamata során.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: dd82b8ac3a510d1b16b0d2f42d3e50803162a119
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94959798"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Oktatóanyag: eszköz üzembe helyezése az Azure IoT Hub Device Provisioning Service használatával

Az előző oktatóanyagban bemutattuk, hogyan állíthatja be, hogy az Azure IoT Hub Device Provisioning Service automatikusan regisztrálja az eszközöket az IoT Hubban. Ez az oktatóanyag azt mutatja be, hogyan állíthatók be az eszközök a gyártási folyamat során, ami lehetővé teszi az IoT Hub általi automatikus regisztrációt. Az eszköz regisztrálása az [igazolási eljárás](concepts-service.md#attestation-mechanism), az első indítás és a regisztrálási szolgáltatással létesített kapcsolat alapján történik. Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Platformspecifikus Device Provisioning Services ügyfél-SDK kiépítése
> * Biztonsági összetevők kicsomagolása
> * Az eszközregisztrációs szoftver létrehozása

Ehhez az oktatóanyag feltételezi, hogy már létrehozta a Device Provisioning Service-példányt és az IoT hubot az előző, [Felhőerőforrások beállítása](tutorial-set-up-cloud.md) című oktatóanyag alapján.

Ez az oktatóanyag az [Azure IoT SDKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c) (Azure IoT SDK-k és tárak – C) adattárt használja, amely a C nyelvhez készült Device Provisioning Service ügyfél-SDK-t tartalmazza. Az SDK jelenleg Windows- és Ubuntu-implementációkon futó rendszerek számára kínál TPM- és X.509-támogatást. Ez az oktatóanyag egy Windows-fejlesztői ügyfél használatán alapul, amely a Visual Studióval való alapszintű jártasságot is feltételezi. 

Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](about-iot-dps.md#provisioning-process) áttekintését. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztési környezetéhez szükségesek. Linux vagy macOS esetén tekintse meg a [fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) az SDK-ban című dokumentáció megfelelő szakaszát.

* A [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019-es verziójában engedélyezve van az ["asztali fejlesztés C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) -ban" számítási feladattal. A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Az SDK platformspecifikus verziójának kiépítése

A Device Provisioning Service ügyfél-SDK az eszközregisztrációs szoftver implementálását segíti. Az SDK használata előtt azonban ki kell építenie annak fejlesztésiügyfél-platformjához tartozó verzióját, illetve az igazolási eljárást. Ebben az oktatóanyagban egy olyan SDK-t hoz létre, amely a Visual studiót használja egy Windows fejlesztési platformon, amely támogatott típusú igazolást biztosít:

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

5. Hozza létre a fejlesztési platform SDK-ját a használni kívánt igazoló mechanizmusok alapján. Használja az alábbi parancsok egyikét (az egyes parancsokhoz tartozó két záró pontot se felejtse el). Ha elkészült, a CMake kiépíti az eszközspecifikus tartalommal rendelkező `/cmake` alkönyvtárat:
 
    - Az igazoláshoz TPM-szimulátort használó eszközök esetében:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Bármely más eszköz esetében (fizikai TPM-/HSM-/X.509- vagy szimulált X.509-tanúsítvány):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Most már készen áll arra, hogy az SDK segítségével létrehozza az eszközregisztrációs kódot. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Biztonsági összetevők kicsomagolása 

A következő lépés az eszköz által használt igazolási eljárás biztonsági összetevőinek kicsomagolása. 

### <a name="physical-devices"></a>Fizikai eszközök 

Attól függően, hogy az SDK-t fizikai TPM-/HSM-igazolás vagy X.509-tanúsítványok használatával hozta létre, a rendszer az alábbi módon gyűjti össze a biztonsági összetevőket:

- TPM-eszköz esetén meg kell határozni a TPM-lapka gyártója által hozzá társított **Ellenőrzőkulcsot**. A TPM-eszköz egyedi **Regisztrációs azonosítójának** származtatását az ellenőrzőkulcs kivonatolásával végezheti el.  

- X.509-eszköz esetén be kell szereznie az eszköz(ök)höz kiadott tanúsítványokat. A kiépítési szolgáltatás kétféle regisztrációs bejegyzést tesz közzé, amelyek az X.509-igazolás mechanizmust használó eszközök hozzáférését szabályozzák. A szükséges tanúsítványok a használni kívánt regisztrációtípusoktól függnek.

    - Egyéni regisztrációk: megadott különálló eszköz regisztrációja. Az ilyen típusú regisztrációs bejegyzéshez [végfelhasználói, vagy levéltanúsítványok](concepts-x509-attestation.md#end-entity-leaf-certificate) szükségesek.
    
    - Regisztrációs csoportok: Az ilyen típusú regisztrációs bejegyzéshez köztes vagy főtanúsítványok szükségesek. További információért lásd: [Eszközök kiépítési szolgáltatáshoz való hozzáférésének szabályozása X.509-tanúsítványokkal](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Szimulált eszközök

Attól függően, hogy az SDK-t TPM- vagy x.509-tanúsítványokat használó szimulált eszköz igazolása céljából hozta létre, a rendszer az alábbi módon gyűjti össze a biztonsági összetevőket:

- Szimulált TPM-eszköz:

   1. Nyisson meg egy Windows-parancssort, majd keresse meg az `azure-iot-sdk-c` alkönyvtárat, és futtassa a TPM-szimulátort. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárja be ezt a parancsablakot. A szimulátornak a következő rövid útmutató végéig futnia kell. 

      Az `azure-iot-sdk-c` alkönyvtárból futtassa a következő parancsot a szimulátor indításához:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Ha a Git bash parancssort használja ehhez a lépéshez, változtassa a fordított perjeleket perjelekre, például így: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. A Visual Studióval nyissa meg a *cmake* mappában létrehozott `azure_iot_sdks.sln` nevű megoldást, és hozza létre a „Build” menü „Megoldás fordítása” parancsával.

   1. A Visual Studio *Solution Explorer* (Megoldáskezelő) panelén lépjen a **Provision\_Tools** mappára. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget. 

   1. Futtassa a megoldást a Hibakeresés menü valamelyik „Indítás” parancsa segítségével. A kimeneti ablak megjeleníti a TPM-szimulátor eszközök beléptetéséhez és regisztrálásához szükséges **_regisztrációs azonosítóját_** és az **_ellenőrzőkulcsot_**. Másolja ki ezeket az értékeket későbbi felhasználás céljára. Bezárhatja a regisztrációs azonosítót és az ellenőrzőkulcsot tartalmazó ablakot, de hagyja futni a TPM-szimulátor ablakát, amelyet az 1. lépésben nyitott meg.

- Szimulált X.509-eszköz:

  1. A Visual Studióval nyissa meg a *cmake* mappában létrehozott `azure_iot_sdks.sln` nevű megoldást, és hozza létre a „Build” menü „Megoldás fordítása” parancsával.

  1. A Visual Studio *Solution Explorer* (Megoldáskezelő) panelén lépjen a **Provision\_Tools** mappára. Kattintson a jobb gombbal a **dice\_device\_enrollment** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 
  
  1. Futtassa a megoldást a Hibakeresés menü valamelyik „Indítás” parancsa segítségével. A kimeneti ablakba írja be a következőt az egyéni regisztrációhoz, amikor a rendszer erre kéri: **i**. A kimeneti ablakban megjelenik egy helyileg létrehozott X.509-tanúsítvány a szimulált eszközhöz. Másolja a vágólapra a kimenetet a *-----BEGIN CERTIFICATE-----* sortól az első *-----END CERTIFICATE-----* sorig, és ügyeljen arra, hogy a kijelölésben ez a két sor is benne legyen. Csak az első tanúsítványra van szüksége a kimeneti ablakból.
 
  1. Hozzon létre egy **_X509testcert.pem_** nevű fájlt, nyissa meg egy szövegszerkesztőben, és másolja bele a vágólapra kimásolt szöveget. Mentse a fájlt, mivel később még szüksége lesz rá az eszköz regisztrálásához. A regisztrációs szoftver működés közben ugyanazt a tanúsítványt használja, mint az automatikus regisztrálás során.    

Ezek a biztonsági összetevők az eszköz Device Provisioning Service szolgáltatásba történő regisztrálásához szükségesek. A regisztrációs szolgáltatás a későbbiekben mindig megvárja, amíg az eszköz elindul és létrejön a kapcsolat. Az eszköz első indításakor az ügyfél-SDK-logika a lapkával (vagy szimulátorral) együttműködve kicsomagolja az eszközön lévő biztonsági összetevőket, és ellenőrzi a Device Provisioning Service szolgáltatással meglévő regisztrációt. 

## <a name="create-the-device-registration-software"></a>Az eszközregisztrációs szoftver létrehozása

Végső lépésként egy olyan regisztrációs alkalmazást kell megírni, amely a Device Provisioning Service ügyfél-SDK segítségével regisztrálja az eszközt az IoT Hub szolgáltatással. 

> [!NOTE]
> Ennek a lépésnek a során a szimulált eszközt fogjuk használni, amihez a munkaállomáson lévő SDK-mintaregisztrációs alkalmazást futtatjuk. Ugyanezek az alapelvek érvényesülnek azonban, ha a regisztrációs alkalmazást egy fizikai eszközre történő regisztrációhoz készíti. 

1. Az Azure Portalon válassza ki az eszközregisztrációs szolgáltatás **Áttekintés** panelét, és másolja ki az **_Azonosító hatóköre_** értéket. A minden esetben egyedi *Azonosító hatóköre* értéket a szolgáltatás hozza létre. Az érték nem módosítható, és a regisztrációazonosítók egyedi azonosítására szolgál.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. A számítógépén a Visual Studio *Solution Explorer* (Megoldáskezelő) panelén lépjen a **Provision\_Samples** mappára. Válassza ki a **prov\_dev\_client\_sample** nevű mintaprojektet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

1. Rendelje az 1. lépésben létrehozott _Azonosító hatóköre_ értéket az `id_scope` változóhoz (a bal/`[` és jobb/`]` oldali szögletes zárójeleket eltávolítva): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    A `global_prov_uri` változó teszi lehetővé az IoT Hub ügyfél-regisztrációs API-nak (`IoTHubClient_LL_CreateFromDeviceAuth`), hogy kapcsolódjon a hozzá kijelölt Device Provisioning Service-példányhoz.

1. Ugyanebben a fájlban, a **main()** függvényben az eszköz regisztrációs szoftvere (TPM vagy X.509) által használt igazolási eljáráshoz illő `hsm_type` változó mellett adjon hozzá/távolítson el megjegyzésjelölőt: 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Mentse a módosításokat, és a „Build” menü „Megoldás létrehozása” parancsát választva hozza létre újból a **prov\_dev\_client\_sample** mintát. 

1. A **Provision\_Samples** mappában kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. MÉG NE futtassa a mintaalkalmazást!

> [!IMPORTANT]
> Még ne futtassa/indítsa el az eszközt! Először fejezze be a folyamatot, és regisztrálja az eszközt a Device Provisioning Service szolgáltatással. A Következő lépések szakasz a következő cikkre irányítja.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>A regisztráció során használt SDK API-k (csak referenciaként)

Az SDK a következő, regisztrációhoz használható API-kat biztosítja az alkalmazásához. Ezek az API-k az indításkor segítik az eszköz csatlakoztatását és regisztrálását a Device Provisioning Service szolgáltatással. Cserébe az eszköz megkapja az IoT Hub-példánnyal létrehozandó kapcsolathoz szükséges adatokat:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Előfordulhat, hogy először egy szimulált eszköz, majd egy tesztszolgáltatás beállítása segítségével finomítania kell a Device Provisioning Service ügyfél-regisztrációs alkalmazást. Ha az alkalmazás megfelelően működik a tesztkörnyezetben, a valós eszközhöz is létrehozhatja, és az eszközképre másolhatja a végrehajtható fájlokat. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Előfordulhat, hogy ekkor fut a portálon a Device Provisioning Service és az IoT Hub szolgáltatás. Ha félbe szeretné hagyni az eszközregisztráció beállítását, és/vagy később fejezné be ezt az oktatóanyagot, javasoljuk, hogy a felesleges költségek elkerülése érdekében kapcsolja ki az eszközt.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Platformspecifikus Device Provisioning Service ügyfél-SDK kiépítése
> * Biztonsági összetevők kicsomagolása
> * Az eszközregisztrációs szoftver létrehozása

A következő oktatóanyagra lépve megtudhatja, hogyan regisztrálhatja az eszközt az IoT Hubban az automatikus regisztrációt elvégző Azure IoT Hub Device Provisioning Service szolgáltatásba való regisztrálással.

> [!div class="nextstepaction"]
> [Az eszköz regisztrálása az IoT Hubban](tutorial-provision-device-to-hub.md)