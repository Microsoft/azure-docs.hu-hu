---
title: Use different attestation mechanisms with the Azure IoT Hub Device Provisioning Service Client SDK
description: Azure útmutató – Különböző igazolási mechanizmusok használata a Device Provisioning Service ügyféloldali SDK-jával az Azure-ban
author: robinsh
ms.author: robinsh
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 0cde591d2ec8c6f2f51c83b3f263c188c8cf2605
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228273"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Különböző igazolási mechanizmusok használata a Device Provisioning Service C-hez készült ügyféloldali SDK-jával

Ez a cikk bemutatja, hogyan használhat különböző [igazolási mechanizmusokat](concepts-security.md#attestation-mechanism) a Device Provisioning Service C-hez készült ügyféloldali SDK-jával. Fizikai eszközt vagy szimulátort is használhat. The provisioning service supports authentication for two types of attestation mechanisms: X.509 and Trusted Platform Module (TPM).

## <a name="prerequisites"></a>Előfeltételek

A [szimulált eszköz létrehozását és üzembe helyezését](./quick-create-simulated-device.md) ismertető útmutató a fejlesztési környezet előkészítéséről szóló szakaszában foglaltaknak megfelelően előkészített fejlesztési környezet.

### <a name="choose-an-attestation-mechanism"></a>Igazolási mechanizmus kiválasztása

Eszközgyártóként először az egyik támogatott típuson alapuló igazolási mechanizmust kell kiválasztania. A [Device Provisioning Service C-hez készült ügyféloldali SDK-ja](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) jelenleg a következő igazolási mechanizmusokat támogatja: 

- [Platformmegbízhatósági modul (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): A TPM bevett szabvány a legtöbb Windows-alapú eszközplatformhoz, valamint néhány Linux-/Ubuntu-alapú eszközhöz. Eszközgyártóként akkor választhatja ezt az igazolási mechanizmust, ha az eszközökön ezeknek az operációs rendszereknek az egyike fut, és egy bevett szabványt kíván használni. TPM-lapkák esetén csak külön-külön regisztrálhatja az eszközöket a Device Provisioning Service-ben. A TPM-szimulátort használhatja fejlesztési célokra Windows- vagy Linux-alapú fejlesztői gépeken.

- [X.509](https://cryptography.io/en/latest/x509/): Az X.509-tanúsítványok tárolhatók a [hardveres biztonsági modul (HSM)](concepts-security.md#hardware-security-module) néven ismert, viszonylag új lapkákon. A Microsoft dolgozik a RIoT- és a DICE-lapkákon, amelyek az X.509-tanúsítványokat használják. Az X.509-lapkákkal kötegelt eszközregisztrációt végezhet a portálon. Ez egyes nem Windows operációs rendszereket is támogat, például az embedOS rendszert. A Device Provisioning Service ügyféloldali SDK-ja támogatja egy X.509-eszközszimulátor használatát fejlesztési célokra. 

További információt az IoT Hub Device Provisioning Service [biztonsági](concepts-security.md) és [automatikus regisztrációval kapcsolatos fogalmait](/azure/iot-dps/concepts-auto-provisioning) ismertető témakörben talál.

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Támogatott igazolási mechanizmusok hitelesítésének engedélyezése

The SDK authentication mode (X.509 or TPM) must be enabled for the physical device or simulator before they can be enrolled in the Azure portal. Először lépjen az azure-iot-sdk-c gyökérmappájába. Ezután futtassa a megadott parancsot a választott hitelesítési módnak megfelelően:

### <a name="use-x509-with-simulator"></a>Use X.509 with simulator

The provisioning service ships with a Device Identity Composition Engine (DICE) emulator that generates an **X.509** certificate for authenticating the device. To enable **X.509** authentication, run the following command: 

```
cmake -Ddps_auth_type=x509 ..
```

A DICE-szal rendelkező hardverekkel kapcsolatban [itt](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) talál további információkat.

### <a name="use-x509-with-hardware"></a>Use X.509 with hardware

The provisioning service can be used with **X.509** on other hardware. A kapcsolat kiépítéséhez egy interfész szükséges a hardver és az SDK között. Az interfészre vonatkozó információkkal kapcsolatban forduljon a HSM gyártójához.

### <a name="use-tpm"></a>TPM használata

A kiépítési szolgáltatás a Windows- és Linux-hardverek TPM-lapkáihoz SAS-token használatával képes csatlakozni. Futtassa a következő parancsot a TPM-hitelesítés engedélyezéséhez:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>TPM használata szimulátorral

Ha nem rendelkezik TPM-lapkát tartalmazó eszközzel, fejlesztési célokra használhat szimulátort is a Windows operációs rendszeren. Futtassa a következő parancsot a TPM-hitelesítés engedélyezéséhez és a TPM-szimulátor futtatásához:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Az SDK felépítése
Az SDK-t az eszközbeléptetés létrehozása előtt építse fel.

### <a name="linux"></a>Linux
- Az SDK felépítése Linux rendszeren:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- A Hibakeresési bináris fájlok felépítéséhez egészítse ki a projektlétrehozási parancsot a megfelelő CMake beállítással, például:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Számos [CMake konfigurációs beállítás](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) érhető el az SDK felépítéséhez. Például az elérhető protokollvermek egyikének letiltásához egészítse ki a CMake projektlétrehozási parancsot egy argumentummal:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Egységtesztet is felépíthet és futtathat:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Az SDK felépítéséhez Windows rendszeren hajtsa végre a következő lépéseket a projektfájlok létrehozásához:
  - Nyisson meg egy „VS2015 fejlesztői parancssort”.
  - Futtassa az alábbi CMake parancsokat az adattár gyökérkönyvtárából:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    Ez a parancs x86 kódtárakat épít fel. Ha x64 adattárakat szeretne felépíteni, módosítsa a cmake generátorargumentumát: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Ha a projekt létrehozása sikeresen befejeződik, egy Visual Studio megoldásfájlt (.sln) kell látnia a(z) `cmake` mappában. Az SDK felépítése:
    - Nyissa meg a **cmake\azure_iot_sdks.sln** fájlt a Visual Studióban és építse fel, **VAGY**
    - Futtassa az alábbi parancsot a parancssori ablakban, amelyet a projektfájlok létrehozásához használt:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- A Hibakeresési bináris fájlok felépítéséhez használja a megfelelő MSBuild argumentumot: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Számos CMake konfigurációs beállítás érhető el az SDK felépítéséhez. Például az elérhető protokollvermek egyikének letiltásához egészítse ki a CMake projektlétrehozási parancsot egy argumentummal:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Egységteszteket is felépíthet és futtathat:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Az alkalmazandó kódtárak
- Az alábbi kódtárakat kell belevennie az SDK-ba:
    - A kiépítési szolgáltatás: dps_http_transport, dps_client, dps_security_client
    - IoTHub Security: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Eszközregisztrációs bejegyzés létrehozása Device Provisioning Service-ben

### <a name="tpm"></a>TPM
TPM használata esetén kövesse a [szimulált eszköz az IoT Hub Device Provisioning Service használatával történő létrehozását és kiépítését ismertető](./quick-create-simulated-device.md) szakaszban leírtakat eszközregisztrációs bejegyzés létrehozásához a Device Provisioning Service-ben és az első rendszerindítás szimulálásához.

### <a name="x509"></a>X.509

1. Az eszközök a kiépítési szolgáltatásba való beléptetéséhez fel kell jegyeznie az egyes eszközök Ellenőrzőkulcsát és Regisztrációs azonosítóját, amelyek az ügyfél-SDK által biztosított Kiépítési eszközben tekinthetőek meg. Futtassa a következő parancsot a legfelső szintű hitelesítésszolgáltató tanúsítványának (regisztrációs csoportok esetén) és a levéltanúsítványának (egyéni regisztráció esetén) kinyomtatásához:
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson a **Minden erőforrás** gombra, és nyissa meg a Device Provisioning Service-t.
   - **X.509 Individual Enrollment**: On the provisioning service summary blade, select **Manage enrollments**. Válassza az **Egyéni beléptetések** fület, és kattintson a felül lévő **Hozzáadás** gombra. Select **X.509** as the identity attestation *Mechanism*, upload the leaf certificate as required by the blade. Ha végzett, kattintson a **Mentés** gombra. 
   - **X.509 Group Enrollment**: On the provisioning service  summary blade, select **Manage enrollments**. Válassza a **Csoportos beléptetések** lapot, és kattintson a felül lévő **Hozzáadás** gombra. Select **X.509** as the identity attestation *Mechanism*, enter a group name and certification name, upload the CA/Intermediate certificate as required by the blade. Ha végzett, kattintson a **Mentés** gombra. 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>Eszközök hitelesítésének engedélyezése egyéni igazolási mechanizmussal (opcionális)

> [!NOTE]
> Ez a szakasz csak olyan eszközökre vonatkozik, amelyeken olyan egyéni platform vagy igazolási mechanizmus támogatása szükséges, amelyeket a Device Provisioning Service C-hez készült ügyféloldali SDK-ja jelenleg nem támogat. Vegye továbbá figyelembe, hogy az SDK gyakran használja a „HSM” kifejezést általános értelemben, az „igazolási mechanizmus” kifejezés helyett.

Először ki kell alakítania egy adat- és kódtárat az egyéni igazolási mechanizmus számára:

1. Alakítson ki egy kódtárat, ahonnan elérhetővé teheti az igazolási mechanizmust. Ennek a projektnek statikus kódtárat kell készítenie az eszközkiépítési SDK-hoz.

2. Implementálja a következő fejlécfájlban meghatározott függvényeket a kódtárában: 

    - Egyéni TPM esetén: implementálja a [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api) alatt meghatározott függvényeket.  
    - Egyéni X.509 esetén: implementálja a [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api) alatt meghatározott függvényeket. 

Miután a kódtár sikeresen létrejött önállóan, integrálnia kell azt a Device Provisioning Service ügyféloldali SDK-jával a kódtárhoz kapcsolásával. :

1. Adja meg az egyéni GitHub-adattárat és a kódtárat a következő `cmake` parancsban:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Nyissa meg a CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) által készített Visual Studio-megoldásfájlt, és építse fel. 

    - A felépítési folyamat összeállítja az SDK-kódtárat.
    - Az SDK megpróbál a `cmake` parancsban meghatározott egyéni kódtárhoz kapcsolódni.

3. Az egyéni igazolási mechanizmus megfelelő implementálásának ellenőrzéséhez futtassa a „Provision_Samples” területen lévő „prov_dev_client_ll_sample” mintaalkalmazást (ezen a helyen: `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`).

## <a name="connecting-to-iot-hub-after-provisioning"></a>Csatlakozás az IoT Hubhoz a kiépítés után

Once the device has been provisioned with the provisioning service, this API uses the specified authentication mode (**X.509** or TPM) to connect with IoT Hub: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
