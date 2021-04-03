---
title: AzureRM PowerShell-parancsfájlok használata a StorSimple-eszközök felügyeletéhez
description: Megtudhatja, hogyan kezelheti Azure Resource Manager SDK-alapú parancsfájlokat a StorSimple 8000 Series-eszköz kezeléséhez.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968774"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Azure Resource Manager SDK-alapú parancsfájlok használata a StorSimple-eszközök kezeléséhez

Ez a cikk azt ismerteti, hogyan használhatók a Azure Resource Manager SDK-alapú parancsfájlok a StorSimple 8000 Series-eszközök felügyeletére. A parancsfájlt a környezet ezen parancsfájlok futtatásához való konfigurálásának lépésein is megtekintheti.

Ez a cikk a StorSimple 8000 sorozatú, csak Azure Portal rendszerű eszközökre vonatkozik.

## <a name="sample-scripts"></a>Mintaparancsfájlok

A különböző StorSimple-feladatok automatizálásához a következő minta-parancsfájlok érhetők el.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Azure Resource Manager SDK-alapú minta-parancsfájlok táblázata

| Azure Resource Manager parancsfájl                    | Leírás                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Ezzel a szkripttel engedélyezheti a StorSimple-eszköz számára a szolgáltatás adattitkosítási kulcsának módosítását.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Ez a szkript 8010 vagy 8020 StorSimple Cloud Appliance hoz létre. A felhőalapú berendezés ezután konfigurálható és regisztrálható a StorSimple Data Manager szolgáltatásban.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Ez a szkript létrehozza vagy módosítja a StorSimple-köteteket.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Ez a parancsfájl felsorolja a StorSimple Eszközkezelő szolgáltatásban regisztrált eszközök összes biztonsági mentését.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Ez a szkript a StorSimple-eszközre vonatkozó összes biztonsági mentési szabályzatot.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Ez a szkript beolvassa a StorSimple Eszközkezelő szolgáltatáson futó összes StorSimple feladatot.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Ez a szkript megvizsgálja a frissítési kiszolgálót, és lehetővé teszi, hogy a frissítések elérhetők legyenek a StorSimple-eszközön való telepítéshez.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Ez a szkript telepíti a rendelkezésre álló frissítéseket a StorSimple-eszközön.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Ez a szkript elindít egy manuális Felhőbeli pillanatképet, és törli a megadott megőrzési napoknál régebbi Felhőbeli pillanatképeket.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Ez a Azure Automation Runbook PowerShell-parancsfájl az összes biztonsági mentési feladat állapotát jelenti.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Ez a parancsfájl egyetlen biztonsági mentési objektumot töröl.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Ez a szkript elindít egy manuális biztonsági mentést a StorSimple-eszközön.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Ez a szkript frissíti a szolgáltatás adattitkosítási kulcsát a StorSimple Eszközkezelő szolgáltatásban regisztrált összes 8010/8020 StorSimple felhőalapú készülékre vonatkozóan.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Ez a parancsfájl a biztonsági mentési házirendekhez kapcsolódó összes ütemezés elemzése után kiemeli a hiányzó biztonsági másolatokat. Azt is ellenőrzi, hogy a biztonsági mentési katalógus tartalmazza-e az elérhető biztonsági másolatok listáját.             |




## <a name="configure-and-run-a-sample-script"></a>Minta parancsfájl konfigurálása és futtatása

Ez a szakasz egy példaként szolgáló parancsfájlt tartalmaz, és részletezi a szkript futtatásához szükséges különféle lépéseket.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

*   Azure PowerShell telepítve. Azure PowerShell modulok telepítése:
    * Windows-környezetben kövesse a [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps)című témakör lépéseit. A StorSimple a Windows Server-gazdagépen is telepítheti a Azure PowerShell, ha az egyiket használja.
    * Linux-vagy MacOS-környezetben kövesse a [Azure PowerShell telepítése és konfigurálása MacOS vagy Linux](/powershell/azure/install-az-ps)rendszeren című témakör lépéseit.

További információ a Azure PowerShell használatáról: Ismerkedés [a Azure PowerShell használatával](/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Azure PowerShell parancsfájl futtatása

Az ebben a példában használt parancsfájl felsorolja a StorSimple-eszközön lévő összes feladatot. Ide tartoznak azok a feladatok, amelyek sikeresek, sikertelenek vagy folyamatban vannak. A szkript letöltéséhez és futtatásához hajtsa végre az alábbi lépéseket.

1. Indítsa el az Azure PowerShellt. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappába.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Töltse le az NUGET CLI](https://www.nuget.org/downloads) -t az előző lépésben létrehozott mappában. A _nuget.exe_ különböző verziói vannak. Válassza ki az SDK-nak megfelelő verziót. Minden letöltési hivatkozás közvetlenül egy _. exe_ -fájlra mutat. Győződjön meg arról, hogy a jobb gombbal rákattint, és mentse a fájlt a számítógépre ahelyett, hogy a böngészőből futtatná.

    A következő parancs futtatásával is letöltheti és tárolhatja a parancsfájlt ugyanabban a mappában, amelyet korábban hozott létre.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Töltse le a függő SDK-t.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Töltse le a szkriptet a minta GitHub-projektből.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Futtassa a szkriptet. Amikor a rendszer felszólítja a hitelesítésre, adja meg az Azure-beli hitelesítő adatait. A szkriptnek a StorSimple-eszköz összes feladatának szűrt listáját kell kiadnia.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Példakimenet

A minta parancsfájl futtatásakor a következő kimenet jelenik meg. A kimenet tartalmazza az összes olyan feladatot, amely a 2017. szeptember 25-én elindított regisztrált eszközön futott, és 2017. október 2-án fejeződött be.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Következő lépések

[A StorSimple-eszköz kezeléséhez használja a StorSimple Eszközkezelő szolgáltatást](storsimple-8000-manager-service-administration.md).