---
title: Windows-tárolókkal rendelkező Azure IoT Edge parancsfájljai | Microsoft Docs
description: IoT Edge PowerShell-parancsfájlok telepítésére, eltávolítására és frissítésére vonatkozó információk Windows-eszközökön
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b7614f7ccd24d0237a043a9b5bf0ad988146d89c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615907"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>PowerShell-parancsfájlok IoT Edge Windows-tárolókkal

Ismerje meg a Windows-eszközökön a IoT Edge telepítését, frissítését vagy eltávolítását támogató PowerShell-parancsfájlokat.

A cikkben ismertetett parancsok a `IoTEdgeSecurityDaemon.ps1` minden [IoT Edge kiadással](https://github.com/Azure/azure-iotedge/releases)kiadott fájlból származnak. A szkript legújabb verziója mindig elérhető a következő címen: aka.ms/iotedge-win.

A parancsmag használatával bármelyik parancsot futtathatja `Invoke-WebRequest` a legújabb parancsfájl-verzió eléréséhez. Például:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

A parancsok futtatásához a parancsfájlt vagy egy adott kiadás egy verzióját is letöltheti. Például:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

A megadott parancsfájl a biztonság növelésére van aláírva. Az aláírást úgy ellenőrizheti, ha letölti a szkriptet az eszközre, majd a következő PowerShell-parancsot futtatja:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

A kimeneti állapot **érvényes** , ha az aláírás ellenőrzése megtörtént.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

A Deploy-IoTEdge parancs letölti és telepíti a IoT Edge biztonsági démont és annak függőségeit. Az üzembe helyezési parancs elfogadja ezeket a közös paramétereket többek között. A teljes listához használja az parancsot `Get-Help Deploy-IoTEdge -full` .  

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén a IoT Edge a telepítésben található Moby Container Engine-t használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek egy proxykiszolgálón keresztül kell átesnie az Internet eléréséhez. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ez a paraméter szerepel a rendszerben, a telepítő megkeresi a felsorolt könyvtárat a IoT Edge cab és a VC Runtime MSI-fájlokhoz, amelyek szükségesek a telepítéséhez. A címtárban nem található fájlok letöltődnek. Ha mindkét fájl szerepel a címtárban, a IoT Edge internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Nincs | Ez a jelző lehetővé teszi, hogy az üzembe helyezési parancsfájl szükség esetén anélkül újraindítsa a gépet. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

A Initialize-IoTEdge parancs a IoT Edge az eszköz kapcsolati sztringjét és működési részleteit konfigurálja. A parancs által generált információk nagy részét ezután a iotedge\config.YAML fájlban tárolja a rendszer. Az inicializálási parancs elfogadja ezeket a közös paramétereket többek között. A teljes listához használja az parancsot `Get-Help Initialize-IoTEdge -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Nincs | **Kapcsoló paraméter**. **Alapértelmezett érték**. Ha nincs megadva a létesítési típus, a manuális kiépítés a kapcsolatok karakterlánca az alapértelmezett érték.<br><br>Kijelenti, hogy az eszköz manuális kiépítéséhez meg kell adnia egy eszköz-kapcsolódási karakterláncot. |
| **ManualX509** | Nincs | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális kiépítés a kapcsolatok karakterlánca az alapértelmezett érték.<br><br>Kijelenti, hogy az eszköz manuális kiépítéséhez meg kell adnia egy identitás-tanúsítványt és egy titkos kulcsot.
| **DpsTpm** | Nincs | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális kiépítés a kapcsolatok karakterlánca az alapértelmezett érték.<br><br>Kijelenti, hogy megadja az eszközök kiépítési szolgáltatásának (DPS) hatókör-AZONOSÍTÓját és az eszköz regisztrációs AZONOSÍTÓját a DPS-n keresztül történő üzembe helyezéshez.  |
| **DpsSymmetricKey** | Nincs | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális kiépítés a kapcsolatok karakterlánca az alapértelmezett érték.<br><br>Kijelenti, hogy megadja az eszközök kiépítési szolgáltatásának (DPS) hatókör-AZONOSÍTÓját és az eszköz regisztrációs AZONOSÍTÓját a DPS-n keresztül történő üzembe helyezéshez, valamint egy szimmetrikus kulcsot az igazoláshoz. |
| **DpsX509** | Nincs | **Kapcsoló paraméter**. Ha nincs megadva a létesítési típus, a manuális kiépítés a kapcsolatok karakterlánca az alapértelmezett érték.<br><br>Kijelenti, hogy megadja az eszközök kiépítési szolgáltatásának (DPS) hatókör-AZONOSÍTÓját és az eszköz regisztrációs AZONOSÍTÓját a DPS-n keresztül történő üzembe helyezéshez, valamint egy X. 509 azonosító tanúsítványát és egy titkos kulcsot az igazoláshoz.  |
| **DeviceConnectionString** | Egy IoT Hubban, egyetlen idézőjelben regisztrált IoT Edge eszközhöz tartozó kapcsolatok karakterlánca | **A manuális** kiépítés a kapcsolatok karakterláncával. Ha nem ad meg egy kapcsolatok karakterláncot a parancsfájl paraméterei között, a rendszer kérni fogja az egyet. |
| **IotHubHostName** | Azon IoT hub állomásneve, amelyhez az eszköz csatlakozik. | X. 509 tanúsítvánnyal történő manuális kiépítés **szükséges** . A (z) *{hub Name}. Azure-Devices.net* formátumban fog megjelenni. |
| **DeviceId** | Az eszköz azonosítója a IoT Hub regisztrált eszköz-identitásával. | X. 509 tanúsítvánnyal történő manuális kiépítés **szükséges** . |
| **Hatókör** | A IoT Hubhoz társított eszköz-kiépítési szolgáltatás példányának hatókör-azonosítója. | A DPS kiépítés esetén **szükséges** . Ha nem ad meg hatókör-azonosítót a parancsfájl paraméterei között, a rendszer kérni fogja az egyiket. |
| **Regisztrációban** | Az eszköz által generált regisztrációs azonosító | A DPS kiépítés esetén **szükséges** a TPM vagy a szimmetrikus kulcsú tanúsítvány használata esetén. Nem **kötelező** , ha X. 509 tanúsítvány-igazolást használ. |
| **X509IdentityCertificate** | Az eszközön található X. 509 eszköz azonosító tanúsítványának URI-ja. | Az X. 509 tanúsítvány igazolásának használata esetén a manuális vagy a DPS-kiépítés **szükséges** . |
| **X509IdentityPrivateKey** | Az eszközön az X. 509 eszköz Identity Certificate kulcs URI-ja. | Az X. 509 tanúsítvány igazolásának használata esetén a manuális vagy a DPS-kiépítés **szükséges** . |
| **SymmetricKey** | A IoT Edge eszköz identitásának kiépítéséhez használt szimmetrikus kulcs a DPS használatakor | A DPS kiépítés esetében **szükséges** , ha szimmetrikus kulcsú igazolást használ. |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték.<br><br>Windows-tárolók esetén a IoT Edge a telepítésben található Moby Container Engine-t használja. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **DeviceCACertificate** | Az eszközön található X. 509 eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának URI-ja. | Is konfigurálható a `C:\ProgramData\iotedge\config.yaml` fájlban. További információ: [tanúsítványok kezelése IoT Edge eszközön](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | Az X. 509 eszköz HITELESÍTÉSSZOLGÁLTATÓI titkos kulcsának URI-elérési útja az eszközön. | Is konfigurálható a `C:\ProgramData\iotedge\config.yaml` fájlban. További információ: [tanúsítványok kezelése IoT Edge eszközön](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge ügynök rendszerképének URI-ja | Alapértelmezés szerint egy új IoT Edge telepítés a IoT Edge-ügynök lemezképének legújabb gördülő címkéjét használja. Ezzel a paraméterrel megadhat egy adott címkét a rendszerkép verziójához, vagy megadhatja a saját ügynökének rendszerképét. További információ: [IoT Edge címkék ismertetése](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Felhasználónév** | Tároló beállításjegyzékbeli felhasználóneve | Ezt a paramétert csak akkor használja, ha a-AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adjon meg egy felhasználónevet a beállításjegyzékhez való hozzáféréssel. |
| **Jelszó** | Biztonságos jelszó karakterlánca | Ezt a paramétert csak akkor használja, ha a-AgentImage paramétert egy privát beállításjegyzékben lévő tárolóra állítja be. Adja meg a beállításjegyzék eléréséhez szükséges jelszót. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** vagy **Linux** | Ha nincs megadva tároló operációs rendszer, a Windows az alapértelmezett érték. Windows-tárolók esetén a rendszer a tároló motort fogja tartalmazni a telepítésben. Linux-tárolók esetén a telepítés megkezdése előtt telepítenie kell egy tároló motort. |
| **Proxy** | Proxy URL-címe | Adja meg ezt a paramétert, ha az eszköznek egy proxykiszolgálón keresztül kell átesnie az Internet eléréséhez. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Paraméterek és értékek szórótábla | A telepítés során több webes kérelem készül. Ezt a mezőt használhatja a webes kérelmek paramétereinek megadásához. Ez a paraméter hasznos a proxykiszolgálók hitelesítő adatainak konfigurálásához. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Könyvtár elérési útja | Ha ez a paraméter szerepel a rendszerben, a telepítő megkeresi a felsorolt könyvtárat a IoT Edge cab és a VC Runtime MSI-fájlokhoz, amelyek szükségesek a telepítéséhez. A címtárban nem található fájlok letöltődnek. Ha mindkét fájl szerepel a címtárban, a IoT Edge internetkapcsolat nélkül is telepítheti. Ezt a paramétert egy adott verzió használatára is használhatja. |
| **RestartIfNeeded** | Nincs | Ez a jelző lehetővé teszi, hogy az üzembe helyezési parancsfájl szükség esetén anélkül újraindítsa a gépet. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| **Force** | Nincs | Ez a jelző arra az esetre kényszeríti az eltávolítást, ha az előző eltávolítási kísérlet sikertelen volt.
| **RestartIfNeeded** | Nincs | Ez a jelző lehetővé teszi az eltávolítási parancsfájl számára, hogy szükség esetén a gép újraindítását kérje. |

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan használhatja ezeket a parancsokat:

* [Windows Azure IoT Edge telepítése vagy eltávolítása](how-to-install-iot-edge-windows-on-windows.md)
