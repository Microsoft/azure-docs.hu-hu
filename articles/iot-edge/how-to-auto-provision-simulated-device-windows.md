---
title: Windows-eszközök automatikus kiépítése a DPS-Azure IoT Edgeokkal | Microsoft Docs
description: Szimulált eszköz használata a Windows rendszerű gépen az eszközök kiépítési szolgáltatásával való Azure IoT Edge automatikus kiépítésének teszteléséhez
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 11279c552cb599c24b72473d6574175450ca7ab0
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200851"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Szimulált IoT Edge eszköz létrehozása és kiépítése virtuális TPM-sel Windows rendszeren

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge eszközök automatikusan kiállíthatók az [eszköz kiépítési szolgáltatásával](../iot-dps/index.yml) , ugyanúgy, mint az Edge-t nem használó eszközök. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át a [kiépítés](../iot-dps/about-iot-dps.md#provisioning-process) áttekintését.

A DPS támogatja a szimmetrikus kulcsos igazolást IoT Edge eszközökhöz az egyéni regisztráció és a csoportos regisztráció során. Csoportos regisztráció esetén, ha a "IoT Edge eszköz" beállítást szeretné megadni a szimmetrikus kulcsos igazolásban, akkor a beléptetési csoportban regisztrált összes eszköz IoT Edge eszközként lesz megjelölve.

Ez a cikk bemutatja, hogyan tesztelheti az automatikus kiépítést egy szimulált IoT Edge eszközön a következő lépésekkel:

* Hozzon létre egy IoT Hub Device Provisioning Service (DPS) egy példányát.
* Hozzon létre egy szimulált eszközt a Windows rendszerű gépen egy szimulált platformmegbízhatósági modulval (TPM) a hardveres biztonsághoz.
* Hozzon létre egy egyéni regisztrációt az eszközhöz.
* Telepítse a IoT Edge futtatókörnyezetet, és kapcsolja az eszközt a IoT Hubhoz.

> [!TIP]
> Ez a cikk azt ismerteti, hogyan lehet az automatikus kiépítés tesztelését a TPM-igazolás használatával virtuális eszközökön, de a fizikai TPM-hardverek használata esetén ez nagy részét is alkalmazza.

## <a name="prerequisites"></a>Előfeltételek

* Egy Windows-fejlesztői gép. Ez a cikk a Windows 10 rendszert használja.
* Aktív IoT Hub.

> [!NOTE]
> A TPM 2,0-es kiadására akkor van szükség, ha a TPM-igazolást a DPS használatával használja, és csak egyéni, nem csoportos, regisztrációkat hozhat létre.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányt a IoT Hub Device Provisioning Service az Azure-ban, és kapcsolja össze az IoT hubhoz. A [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör útmutatását követve végezheti el.

Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

> [!TIP]
> Ha fizikai TPM-eszközt használ, meg kell határoznia a **jóváhagyás kulcsát**, amely minden TPM-lapka esetében egyedi, és a hozzá társított TPM-lapka gyártójától származik. Létrehozhat egy egyedi **regisztrációs azonosítót** a TPM-eszközhöz, például a HÁTIRAT kulcs SHA-256 kivonatának létrehozásával.
>
> Kövesse a cikk utasításait az [eszközök regisztrálásának az Azure Portalon való kezeléséhez](../iot-dps/how-to-manage-enrollments.md) a DPS-ben való regisztráció létrehozásához, majd folytassa a jelen cikk [IoT Edge futtatókörnyezet telepítése](#install-the-iot-edge-runtime) című szakaszával a folytatáshoz.

## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

Hozzon létre egy szimulált TPM-eszközt a Windows fejlesztői gépen. Kérje le az eszköz **regisztrációs azonosítóját** és **záradékának kulcsát** , és hozzon létre egy egyéni beléptetési bejegyzést a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszközök Twin eszközben beállíthatja a címkéket, hogy a megoldásban szükséges mérőszámok alapján csoportosítsa az eszközöket (például régió, környezet, hely vagy eszköz típusa). Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

Válassza ki a szimulált eszköz létrehozásához használni kívánt SDK-nyelvet, és kövesse a lépéseket, amíg létre nem hozza az egyéni regisztrációt.

Az egyéni regisztráció létrehozásakor a True ( **igaz** ) érték kiválasztásával deklarálhatja, hogy a SZIMULÁLt TPM-eszköz a Windows fejlesztői gépen **IoT Edge eszköz**.

> [!TIP]
> Az Azure CLI-ben létrehozhat egy [beléptetést](/cli/azure/ext/azure-iot/iot/dps/enrollment) vagy egy [beléptetési csoportot](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , és az **Edge-kompatibilis** jelző használatával meghatározhatja, hogy egy eszköz vagy egy csoport IoT Edge eszköz-e.

Szimulált eszköz és egyéni beléptetési útmutatók:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Az egyéni regisztráció létrehozása után mentse a **regisztrációs azonosító** értékét. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, így a kód a peremhálózat szélén is futtatható. Telepítse a IoT Edge futtatókörnyezetet a szimulált TPM-t futtató eszközre.

Az eszköz kiépítéséhez kövesse az [Azure IoT Edge futtatókörnyezet telepítése](how-to-install-iot-edge.md)című témakör lépéseit, majd térjen vissza ehhez a cikkhez.

> [!TIP]
> Tartsa meg a TPM-szimulátort futtató ablakot a telepítés és a tesztelés során.

## <a name="configure-the-device-with-provisioning-information"></a>Az eszköz konfigurálása az üzembe helyezési adatokkal

Miután telepítette a futtatókörnyezetet az eszközre, konfigurálja az eszközt az általa használt információval az eszköz kiépítési szolgáltatásához való kapcsolódáshoz és a IoT Hubához.

1. Ismerje meg a DPS- **azonosító hatókörét** és az eszköz **regisztrációs azonosítóját** , amely az előző fejezetekben lett összegyűjtve.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Ügyeljen arra, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, nem a PowerShell (x86) rendszerre.

1. Az **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e, bekapcsolja a tárolók szolgáltatást, majd letölti a Moby Runtime és a IoT Edge futtatókörnyezetet. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítást kérik. Ha igen, indítsa újra az eszközt. Ha az eszköz elkészült, futtassa újra a PowerShellt rendszergazdaként.

1. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés. A `-Dps` jelző használatával a manuális kiépítés helyett a Device kiépítési szolgáltatást használhatja.

   Cserélje le a és a helyőrző értékét a `{scope_id}` `{registration_id}` korábban összegyűjtött adatokra.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futtatókörnyezet sikeresen elindult, beléphet a IoT Hubba, és megkezdheti a IoT Edge modulok üzembe helyezését az eszközön. Az eszközön az alábbi parancsokkal ellenőrizheti, hogy a futtatókörnyezet telepítése és elindítása sikeresen megtörtént-e.  

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

A szolgáltatási naplók vizsgálata az elmúlt 5 percben.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listázása.

```powershell
iotedge list
```

## <a name="next-steps"></a>Következő lépések

Az eszköz kiépítési szolgáltatásának beléptetési folyamata lehetővé teszi, hogy az eszköz AZONOSÍTÓját és az eszköz Twin címkéit az új eszköz kiépítésekor egy időben állítsa be. Ezeket az értékeket használhatja az egyes eszközök vagy eszközök automatikus eszközkezelés használatával történő megcélzásához. Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-at-scale.md) [Azure CLI használatával](how-to-deploy-cli-at-scale.md)
