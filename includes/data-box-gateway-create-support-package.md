---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582177"
---
Ha bármilyen eszközzel kapcsolatos probléma merül fel, létrehozhat egy támogatási csomagot a rendszernaplókból. Microsoft ügyfélszolgálata ezt a csomagot használja a problémák elhárításához. Támogatási csomag létrehozásához kövesse az alábbi lépéseket:

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](#connect-to-the-powershell-interface).
2. `Get-HcsNodeSupportPackage`Támogatási csomag létrehozásához használja a parancsot. A parancsmag használata a következő:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    A parancsmag gyűjti a naplókat az eszközről, és átmásolja a naplókat egy megadott hálózati vagy helyi megosztásra.

    A használt paraméterek a következők:

    - `-Path` -Válassza a hálózatot vagy a helyi elérési utat a támogatási csomag másolásához. szükséges
    - `-Credential` -A védett útvonal eléréséhez használandó hitelesítő adatok megadása.
    - `-Zip` -A zip-fájl létrehozásához meg kell adni.
    - `-Include` -Adja meg a támogatási csomagban szerepeltetni kívánt összetevőket. Ha nincs megadva, `Default` a rendszer feltételezi.
    - `-IncludeArchived` -Adja meg, hogy az archivált naplók szerepeljenek a támogatási csomagban.
    - `-IncludePeriodicStats` -Adja meg, hogy az időszakos stat naplókat tartalmazza a támogatási csomagban.

    
