---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179578"
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

    
