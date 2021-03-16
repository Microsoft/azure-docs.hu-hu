---
title: PowerShell-parancsfájlok Azure IoT Edge Linux rendszerhez Windows rendszeren | Microsoft Docs
description: Információk a Linux rendszerhez készült Azure IoT Edge Windows PowerShell-parancsfájlokban a Windows rendszerű virtuális gépeken történő üzembe helyezéshez, kiépítéshez és állapothoz IoT Edge.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: fe04615e234eaf5b006a80e6f584905626c637ba
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489485"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>PowerShell-parancsfájlok IoT Edge Linux rendszerhez Windows rendszeren

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ismerje meg azokat a PowerShell-parancsfájlokat, amelyek a Windows rendszerű virtuális gépeken a Linux IoT Edge telepítését, üzembe helyezését és állapotát kérik.

A cikkben ismertetett parancsok a `AzureEFLOW.psm1` fájlból származnak, amely a címtárában található a rendszerben `WindowsPowerShell` `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

Az **Deploy-eFlow** parancs a fő telepítési módszer. Az üzembe helyezési parancs létrehozza a virtuális gépet, kiépíti a fájlokat és telepíti a IoT Edge Agent modult. Míg a paraméterek egyike sem szükséges, a rendszer a telepítés során felhasználhatja IoT Edge eszközét, és módosíthatja a virtuális gép beállításait a létrehozás során. A teljes listát az paranccsal használhatja `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>A kiépítési típushoz a **X509** jelenleg kizárólag az [Azure IoT hub Device Provisioning Service](../iot-dps/about-iot-dps.md)használatával történő kiépítés X509 vonatkozik. A manuális X509 létesítési módszer jelenleg nem támogatott.

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| eflowVhdxDir | Könyvtár elérési útja | Az a könyvtár, amelyben az üzembe helyezés a virtuális gép VHDX-fájlját tárolja. |
| provisioningType | **manuális**, **TPM**, **X509** vagy **szimmetrikus** |  Meghatározza a IoT Edge eszközhöz használni kívánt kiépítés típusát. |
| devConnString | Egy meglévő IoT Edge eszköz eszköz-kapcsolatainak karakterlánca | Eszköz-csatlakoztatási karakterlánc egy IoT Edge eszköz manuális kiépítési (**manuális**) létrehozásához. |
| symmKey | Egy meglévő DPS-regisztráció vagy egy meglévő IoT Edge eszköz elsődleges kulcsa szimmetrikus kulccsal regisztrálva | IoT Edge-eszköz (**X509** vagy **szimmetrikus**) kiépítési szimmetrikus kulcsa. |
| Hatókör | Egy meglévő DPS-példány hatókör-azonosítója. | IoT Edge eszköz (**X509** vagy **szimmetrikus**) kiépítési hatókör-azonosítója. |
| regisztrációban | Meglévő IoT Edge eszköz regisztrációs azonosítója | IoT Edge-eszköz (**X509** vagy **szimmetrikus**) kiépítési regisztrációs azonosítója. |
| identityCertLocVm | Könyvtár elérési útja; olyan mappában kell lennie, amely a szolgáltatás tulajdonában van `iotedge` | Az IoT Edge eszköz (**X509** vagy **szimmetrikus**) üzembe helyezéséhez használt virtuális gépen található Identity tanúsítvány abszolút célhelyének elérési útja. |
| identityCertLocWin | Könyvtár elérési útja | Az identitási tanúsítvány abszolút forrásának elérési útja a Windowsban IoT Edge eszköz (**X509** vagy **szimmetrikus**) kiépítés céljából. |
| identityPkLocVm |  | Könyvtár elérési útja; olyan mappában kell lennie, amely a szolgáltatás tulajdonában van `iotedge` | A IoT Edge eszköz (**X509** vagy **szimmetrikus**) üzembe helyezéséhez használt virtuális gépen található Identity Private-kulcs abszolút célhelyének elérési útja. |
| identityPkLocWin | Könyvtár elérési útja | Az identitás titkos kulcsának abszolút forrásának elérési útja a Windowsban IoT Edge eszköz (**X509** vagy **szimmetrikus**) kiépítési állapotához. |
| vmSizeDefintion | Legfeljebb 30 karakter hosszú lehet | A virtuális gép magok számának és a rendelkezésre álló RAM-nak a meghatározása. **Alapértelmezett érték**: Standard_K8S_v1. |
| vmDiskSize | 8 GB és 256 GB között | A dinamikusan bővülő virtuális merevlemez maximális mérete. **Alapértelmezett érték**: 16 GB. |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |
| vnetType | **Transzparens** vagy **ICS** | A virtuális kapcsoló típusa. **Alapértelmezett érték**: transzparens. |
| vnetName | Nem 64 karakternél hosszabb | A virtuális kapcsoló neve. **Alapértelmezett érték**: External. |
| enableVtpm | Nincs | **Kapcsoló paraméter**. Hozza létre a virtuális gépet, amelyen engedélyezve van a TPM, vagy le van tiltva. |
| mobyPackageVersion | Legfeljebb 30 karakter hosszú lehet |  A virtuális gépen ellenőrizni vagy telepíteni kívánt Moby-csomag verziója.  **Alapértelmezett érték:** 19.03.11. |
| iotedgePackageVersion | Legfeljebb 30 karakter hosszú lehet | A virtuális gépen ellenőrizni vagy telepíteni kívánt IoT Edge-csomag verziója. **Alapértelmezett érték:** 1.1.0. |
| installPackages | Nincs | **Kapcsoló paraméter**. Ha be van jelölve, a parancsfájl megkísérli telepíteni a Moby és IoT Edge csomagokat ahelyett, hogy csak a csomagok ellenőrzése megtörtént. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

Az **ellenőrzés-EflowVm** parancs egy elérhető funkció annak ellenőrzéséhez, hogy a Windows rendszerű virtuális gépen a Linux IoT Edge lett-e létrehozva. Csak általános paramétereket vesz igénybe, és **igaz** értéket ad vissza, ha a virtuális gép létrejött és **hamis** , ha nem. További információkért használja az parancsot `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

A **kiépítés-EflowVm** parancs hozzáadja a IoT Edge eszköz kiépítési adatait a virtuális gép IoT Edge `config.yaml` fájljához. A kiépítés az üzembe helyezési fázisban is elvégezhető a **Deploy-eFlow** parancs paramétereinek megadásával. További információkért használja az parancsot `Get-Help Provision-EflowVm -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |
| provisioningType | **manuális**, **TPM**, **X509** vagy **szimmetrikus** |  Meghatározza a IoT Edge eszközhöz használni kívánt kiépítés típusát. |
| devConnString | Egy meglévő IoT Edge eszköz eszköz-kapcsolatainak karakterlánca | Eszköz-csatlakoztatási karakterlánc egy IoT Edge eszköz manuális kiépítési (**manuális**) létrehozásához. |
| symmKey | Egy meglévő DPS-regisztráció vagy egy meglévő IoT Edge eszköz elsődleges kulcsa szimmetrikus kulccsal regisztrálva | Egy IoT Edge eszköz kiépítési szimmetrikus kulcsa (**DPS**, **szimmetrikus**). |
| Hatókör | Egy meglévő DPS-példány hatókör-azonosítója. | IoT Edge eszköz (**DPS**) kiépítési hatókör-azonosítója. |
| regisztrációban | Meglévő IoT Edge eszköz regisztrációs azonosítója | IoT Edge eszköz (**DPS**) kiépítési regisztrációs azonosítója. |
| identityCertLocVm | Könyvtár elérési útja; olyan mappában kell lennie, amely a szolgáltatás tulajdonában van `iotedge` | A virtuális gépen lévő Identity tanúsítvány abszolút célhelyének elérési útja egy IoT Edge eszköz kiépítési céljára (**DPS**, **X509**). |
| identityCertLocWin | Könyvtár elérési útja | A Windows rendszerű identitás-tanúsítvány abszolút forrásának elérési útja IoT Edge eszköz kiépítési céljára (**DPS**, **X509**). |
| identityPkLocVm |  | Könyvtár elérési útja; olyan mappában kell lennie, amely a szolgáltatás tulajdonában van `iotedge` | A virtuális gépen lévő Identity Private-kulcs abszolút célhelyének elérési útja egy IoT Edge eszköz kiépítési céljára (**DPS**, **X509**). |
| identityPkLocWin | Könyvtár elérési útja | Az identitás titkos kulcsának abszolút forrásának elérési útja a Windowsban egy IoT Edge eszköz kiépítési (**DPS**, **X509**) létesítéséhez. |

## <a name="get-eflowvmname"></a>Get-EflowVmName

A **Get-EflowVmName** parancs a virtuális gép aktuális állomásnévének lekérdezésére szolgál. Ez a parancs azért van, mert az a tény, hogy a Windows-állomásnév idővel változhat. Csak általános paramétereket vesz igénybe, és a virtuális gép aktuális állomásnevét fogja visszaadni. További információkért használja az parancsot `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

A **Get-EflowLogs** parancs a Windows-telepítésben a Linux rendszerhez készült IoT Edge naplók összegyűjtésére és becsomagolására szolgál. A kötegelt naplókat mappa formájában adja vissza `.zip` . További információkért használja az parancsot `Get-Help Get-EflowLogs -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

A **Get-EflowVmTpmProvisioningInfo** parancs a virtuális gép vTPM kiépítési adatainak gyűjtésére és megjelenítésére szolgál. Ha a virtuális gép vTPM nélkül lett létrehozva, a parancs visszaküldi azt, hogy nem találta meg a TPM kiépítési információit. További információkért használja az parancsot `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

A **Get-EflowVmAddr** parancs a virtuális gép IP-és Mac-címeinek megkeresésére és megjelenítésére szolgál. Csak a gyakori paramétereket veszi igénybe. További információkért használja az parancsot `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

A **Get-EflowVmSystemInformation** parancs a rendszerinformációk gyűjtésére és megjelenítésére szolgál a virtuális gépről, például a memória és a tárterület használatáról. További információkért használja az parancsot `Get-Help Get-EflowVmSystemInformation -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

A **Get-EflowVmEdgeInformation** parancs a virtuális gép IoT Edge adatainak gyűjtésére és megjelenítésére szolgál, például a virtuális gép IoT Edge verzióját. További információkért használja az parancsot `Get-Help Get-EflowVmEdgeInformation -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

A **Get-EflowVmEdgeModuleList** parancs a virtuális gépen futó IoT Edge modulok listájának lekérdezésére és megjelenítésére szolgál. További információkért használja az parancsot `Get-Help Get-EflowVmEdgeModuleList -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

A **Get-EflowVmEdgeStatus** parancs a virtuális gépen IoT Edge futtatókörnyezet állapotának lekérdezésére és megjelenítésére szolgál. További információkért használja az parancsot `Get-Help Get-EflowVmEdgeStatus -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

A **Get-EflowVmUserName** parancs a virtuális gép beállításjegyzékből való létrehozásához használt virtuális gép felhasználónevének lekérdezésére és megjelenítésére szolgál. Csak a gyakori paramétereket veszi igénybe. További információkért használja az parancsot `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

A **Get-EflowVmSshKey** parancs a virtuális gép által használt SSH-kulcs lekérdezésére és megjelenítésére szolgál. Csak a gyakori paramétereket veszi igénybe. További információkért használja az parancsot `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

Az **SSH-EflowVm** parancs a virtuális GÉPRE való SSH-t használja. További információkért használja az parancsot `Get-Help Ssh-EflowVm -full` .

| Paraméter | Elfogadott értékek | Megjegyzések |
| --------- | --------------- | -------- |
| vmUser | Legfeljebb 30 karakter hosszú lehet | A virtuális gépre való bejelentkezéshez használt Felhasználónév. |

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan használhatja ezeket a parancsokat:

* [A Linux Azure IoT Edge telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)

* Tekintse [meg a Linux rendszerhez készült IoT Edge](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) a PowerShell-en keresztül elérhető összes parancshoz a Windows PowerShell parancsfájl-referenciáját.
