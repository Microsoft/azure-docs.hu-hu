---
title: Különböző igazolási mechanizmusok használata az Azure IoT Hub Device Provisioning Service Client SDK-val
description: Azure – útmutató a különböző igazolási mechanizmusok használatáról az Azure-beli Device kiépítési szolgáltatás (DPS) ügyféloldali SDK-val
author: wesmc7777
ms.author: wesmc
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
ms.openlocfilehash: 0a32e2f055b2914fa0008e043e80092ac2da0814
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531508"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Különböző igazolási mechanizmusok használata a Device Provisioning Service C-hez készült ügyféloldali SDK-jával

Ez a cikk bemutatja, hogyan használhat különböző [igazolási mechanizmusokat](concepts-service.md#attestation-mechanism) a Device Provisioning Service C-hez készült ügyféloldali SDK-jával. Fizikai eszközt vagy szimulátort is használhat. A kiépítési szolgáltatás két igazolásimechanizmus-típus esetében támogatja a hitelesítést: az X.509 és a platformmegbízhatósági modul (TPM) esetében.

## <a name="prerequisites"></a>Előfeltételek

A [szimulált eszköz létrehozását és üzembe helyezését](./quick-create-simulated-device.md) ismertető útmutató a fejlesztési környezet előkészítéséről szóló szakaszában foglaltaknak megfelelően előkészített fejlesztési környezet.

### <a name="choose-an-attestation-mechanism"></a>Igazolási mechanizmus kiválasztása

Eszközgyártóként először az egyik támogatott típuson alapuló igazolási mechanizmust kell kiválasztania. A [Device Provisioning Service C-hez készült ügyféloldali SDK-ja](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) jelenleg a következő igazolási mechanizmusokat támogatja: 

- [Platformmegbízhatósági modul (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): A TPM bevett szabvány a legtöbb Windows-alapú eszközplatformhoz, valamint néhány Linux-/Ubuntu-alapú eszközhöz. Az eszköz gyártója akkor is kiválaszthatja ezt az igazolási mechanizmust, ha az eszközökön futó operációs rendszerek valamelyikét használja, és a rendszer egy meghatározott szabványt keres. TPM-lapkák esetén csak külön-külön regisztrálhatja az eszközöket a Device Provisioning Service-ben. A TPM-szimulátort használhatja fejlesztési célokra Windows- vagy Linux-alapú fejlesztői gépeken.

- [X.509](https://cryptography.io/en/latest/x509/): Az X.509-tanúsítványok tárolhatók a [hardveres biztonsági modul (HSM)](concepts-service.md#hardware-security-module) néven ismert, viszonylag új lapkákon. A Microsoft dolgozik a RIoT- és a DICE-lapkákon, amelyek az X.509-tanúsítványokat használják. Az X.509-lapkákkal kötegelt eszközregisztrációt végezhet a portálon. Ez egyes nem Windows operációs rendszereket is támogat, például az embedOS rendszert. A Device Provisioning Service ügyféloldali SDK-ja támogatja egy X.509-eszközszimulátor használatát fejlesztési célokra. 

További információ: IoT Hub Device Provisioning Service [igazolási mechanizmusok](concepts-service.md#attestation-mechanism).

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Támogatott igazolási mechanizmusok hitelesítésének engedélyezése

Az SDK hitelesítési módját (X.509 vagy TPM) engedélyezni kell a fizikai eszközön vagy a szimulátoron, mielőtt az regisztrálható lenne az Azure Portalon. Először lépjen az azure-iot-sdk-c gyökérmappájába. Ezután futtassa a megadott parancsot a választott hitelesítési módnak megfelelően:

### <a name="use-x509-with-simulator"></a>Az X.509 használata szimulátorral

A kiépítési szolgáltatás az eszköz Identity kompozíciós motor (DICE) emulátorával rendelkezik, amely létrehoz egy **X. 509** tanúsítványt az eszköz hitelesítéséhez. Az **X. 509** hitelesítés engedélyezéséhez futtassa a következő parancsot: 

```
cmake -Ddps_auth_type=x509 ..
```

A DICE-szal rendelkező hardverekkel kapcsolatban [itt](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) talál további információkat.

### <a name="use-x509-with-hardware"></a>Az X.509 használata hardverrel

A kiépítési szolgáltatás az **X. 509** használatával más hardvereken is használható. A kapcsolat kiépítéséhez egy interfész szükséges a hardver és az SDK között. Az interfészre vonatkozó információkkal kapcsolatban forduljon a HSM gyártójához.

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
   - **X. 509 egyéni regisztráció**: a kiépítési szolgáltatás összegzési paneljén válassza a **regisztrációk kezelése**lehetőséget. Válassza az **Egyéni regisztrációk** fület, és kattintson a felül található **Hozzáadás** gombra. Válassza az **X. 509** elemet az identitás-igazolási *mechanizmusként*, töltse fel a levél tanúsítványát a panel által megkövetelt módon. Ha végzett, kattintson a **Mentés** gombra. 
   - **X. 509 csoport beléptetése**: a kiépítési szolgáltatás összegzési paneljén válassza a **regisztrációk kezelése**lehetőséget. Válassza a **Csoportos beléptetések** lapot, és kattintson a felül lévő **Hozzáadás** gombra. Válassza az **X. 509** elemet az identitás-igazolási *mechanizmusként*, adja meg a csoport nevét és a tanúsítvány nevét, töltse fel a CA/Intermediate tanúsítványt a panel által megkövetelt módon. Ha végzett, kattintson a **Mentés** gombra. 

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

Miután az eszközt kiépítte a kiépítési szolgáltatással, ez az API a megadott hitelesítési móddal (**X. 509** vagy TPM) használja a IoT hubhoz való kapcsolódáshoz: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
