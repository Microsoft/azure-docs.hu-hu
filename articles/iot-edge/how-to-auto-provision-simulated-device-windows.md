---
title: Windows-eszközök automatikus kiépítése DPS-sel – Azure IoT Edge | Microsoft Docs
description: Szimulált eszköz használata Windows rendszerű gépén a Device Provisioning Service-Azure IoT Edge eszköz automatikus eszközátépítésének teszteléséhez
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bd027773a5759caee19228f56ba4b3dfe8c2cf
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482007"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Szimulált virtuális TPM IoT Edge létrehozása és kiépítése Windows rendszeren

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge a [Device Provisioning](../iot-dps/index.yml) Service-sel automatikusan kiépítheti az eszközöket, csakúgy, mint a peremhálózati eszközök esetében. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

A DPS támogatja a szimmetrikus kulcsú igazolást a IoT Edge eszközök esetében mind az egyéni regisztrációban, mind a csoportos regisztrációban. Csoportregisztrálás esetén, ha IoT Edge szimmetrikus kulcsú igazolásban a "is IoT Edge device" beállítás true (igaz) értékkel van bejelölve, akkor IoT Edge regisztrációs csoportban regisztrált összes eszköz IoT Edge lesz.

Ez a cikk bemutatja, hogyan tesztelje az automatikus kiépítést egy szimulált IoT Edge eszközön a következő lépésekkel:

* Hozza létre a IoT Hub Device Provisioning Service (DPS) egy példányát.
* Szimulált eszköz létrehozása Windows rendszerű gépén szimulált platformmegbízhatósági modul (TPM) a hardverbiztonság érdekében.
* Egyéni regisztráció létrehozása az eszközhöz.
* Telepítse a IoT Edge, és csatlakoztassa az eszközt a IoT Hub.

> [!TIP]
> Ez a cikk az automatikus kiépítés virtuális eszközökön TPM-igazolással való tesztelését ismerteti, de sok esetben fizikai TPM-hardverek használata esetén is érvényes.

## <a name="prerequisites"></a>Előfeltételek

* Egy Windows rendszerű fejlesztői gép. Ez a cikk a következő Windows 10.
* Aktív IoT Hub.

> [!NOTE]
> A TPM 2.0-ra akkor van szükség, ha TPM-igazolást használ a DPS-sel, és csak egyéni, nem csoportos regisztrációk létrehozására használható.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service

Hozzon létre egy új azure IoT Hub Device Provisioning Service példányt, és csatolja az IoT Hubhoz. Kövesse a Következő útmutató [utasításait: Set up the IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Miután futtatta a Device Provisioning Service-t, másolja ki az Azonosító hatóköre **értéket** az áttekintő lapról. Ezt az értéket akkor használja, amikor a IoT Edge konfigurálja.

> [!TIP]
> Ha fizikai TPM-eszközt használ, meg kell határoznia a ellenőrzőkulcsot, amely minden TPM-lapka egyedi, és a hozzá társított TPM-lapkagyártótól szerezhető be. A TPM-eszköz egyedi regisztrációs azonosítóját származtathatja például az ellenőrzőkulcs SHA-256 kivonatának létrehozásával. 
>
> Kövesse az Eszközregisztrálások kezelése az [Azure Portalon](../iot-dps/how-to-manage-enrollments.md) című cikk utasításait a regisztráció DPS-ben való létrehozásához, majd folytassa a cikk Install the IoT Edge runtime (Az [IoT Edge-alapú](#install-the-iot-edge-runtime) futásidő telepítése) című szakaszával.

## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

Hozzon létre egy szimulált TPM-eszközt a Windows rendszerű fejlesztői gépen. Lekéri az  eszköz **regisztrációs azonosítóját** és ellenőrzőkulcsát, és használja őket egy egyéni regisztrációs bejegyzés létrehozásához a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van kezdeti ikerállapot **deklarálására.** Az ikereszközön beállíthatja a címkéket, hogy a megoldásban szükséges metrikák (például régió, környezet, hely vagy eszköztípus) alapján csoportosítják az eszközöket. Ezek a címkék az automatikus [központi telepítések létrehozására használhatók.](how-to-deploy-at-scale.md)

Válassza ki a szimulált eszköz létrehozásához használni kívánt SDK-nyelvet, és kövesse a lépéseket, amíg létre nem hozza az egyéni regisztrációt.

Az egyéni regisztráció létrehozásakor válassza  az Igaz lehetőséget annak deklarálására, hogy a Windows rendszerű fejlesztői gépen a szimulált TPM-eszköz **egy IoT Edge eszköz.**

> [!TIP]
> Az Azure CLI-ben létrehozhat [](/cli/azure/iot/dps/enrollment) egy regisztrációt vagy egy  regisztrációs csoportot, és a peremhálózati jelölővel megadhatja, hogy egy eszköz vagy eszközcsoport egy IoT Edge eszköz. [](/cli/azure/iot/dps/enrollment-group)

Szimulált eszközök és egyéni regisztrációs útmutatók:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Az egyéni regisztráció létrehozása után mentse a regisztrációs azonosító **értékét.** Ezt az értéket akkor használja, amikor a IoT Edge konfigurálja.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, hogy kódokat futtathat a peremhálózaton. Telepítse a IoT Edge futtatókörnyezetet a szimulált TPM-et futtató eszközön.

Kövesse az [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md)(Az eszköz telepítése) Azure IoT Edge, majd térjen vissza ehhez a cikkhez az eszköz üzembe lépéséhez.

> [!TIP]
> A telepítés és a tesztelés során tartsa nyitva a TPM-szimulátort futtató ablakot.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása kiépítési információkkal

Miután telepítette a futásidőt az eszközön, konfigurálja az eszközt a Device Provisioning Service-hez való csatlakozáshoz használt információkkal, és IoT Hub.

1. Az előző szakaszokban összegyűjtött  **DPS-azonosító hatókörének** és eszközregisztrációs azonosítójának személyazonossága.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. A PowerShell telepítésekor mindenképpen AMD64-munkamenetet használjon, IoT Edge PowerShellt (x86).

1. A **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows-gép támogatott verziójú-e, bekapcsolja a tárolók funkciót, majd letölti a moby IoT Edge futásidejűt. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton az IoT Core-eszközök automatikusan újraindulhatnak. Windows 10 windowsos vagy Windows Server-eszközökön újraindításra lehet szükség. Ha igen, indítsa újra az eszközt. Ha az eszköz készen áll, futtassa újra a PowerShellt rendszergazdaként.

1. Az **Initialize-IoTEdge** parancs a IoT Edge konfigurálja a virtuális gépet. A parancs alapértelmezett beállítása a Windows-tárolók manuális kiépítése. A `-Dps` jelölővel manuális kiépítés helyett a Device Provisioning Service-t használhatja.

   Cserélje le a és helyőrzőértékeket a korábban `{scope_id}` `{registration_id}` összegyűjtött adatokra.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>A sikeres telepítés ellenőrzése

Ha a futásidejű modul sikeresen elindult, a IoT Hub üzembe helyezheti IoT Edge modulokat az eszközén. Az alábbi parancsokkal ellenőrizze az eszközön, hogy a futásidő sikeresen telepítve és elkezdődött-e.  

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

Vizsgálja meg az elmúlt 5 perc szolgáltatásnaplóit.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Listából a futó modulokat.

```powershell
iotedge list
```

## <a name="next-steps"></a>Következő lépések

A Device Provisioning Service regisztrációs folyamata lehetővé teszi az eszközazonosító és az ikereszköz-címkék beállítását az új eszköz kiépítésekor. Ezeket az értékeket használhatja az egyes eszközök vagy eszközcsoportok automatikus eszközkezelés használatával való megcélzott kezelésére. Megtudhatja, hogyan [helyezhet üzembe és IoT Edge modulokat](how-to-deploy-at-scale.md) nagy méretekben a Azure Portal vagy az Azure CLI [használatával](how-to-deploy-cli-at-scale.md)
