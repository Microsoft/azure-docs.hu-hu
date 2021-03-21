---
title: Rövid útmutató – virtuális gép biztonsági mentése a PowerShell-lel
description: Ebből a rövid útmutatóból megtudhatja, hogyan készíthet biztonsági mentést Azure-beli virtuális gépekről a Azure PowerShell modullal.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 81005a3032355f566aef2a6794cf2ec5038b0dd8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737116"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Virtuális gép biztonsági mentése az Azure-ban a PowerShell használatával

A [Azure PowerShell](/powershell/azure/new-azureps-module-az) az modul az Azure-erőforrások parancssorból vagy parancsfájlokból való létrehozására és kezelésére szolgál.

[Azure Backup](backup-overview.md) biztonsági mentést készít a helyszíni gépekről és alkalmazásokról, valamint az Azure-beli virtuális gépekről. Ez a cikk bemutatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépről az az modullal. Azt is megteheti, hogy biztonsági másolatot készít egy virtuális gépről az [Azure CLI](quick-backup-vm-cli.md)használatával vagy a [Azure Portal](quick-backup-vm-portal.md).

Ez a rövid útmutató a meglévő Azure-beli virtuális gépek biztonsági mentését mutatja be. Ha létre kell hoznia egy virtuális gépet, [létrehozhatja azt az Azure PowerShell használatával](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json).

Ehhez a rövid útmutatóhoz a Azure PowerShell az modul Version 1.0.0 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Bejelentkezés és regisztrálás

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

    ```powershell
    Connect-AzAccount
    ```

2. A Azure Backup első használatakor regisztrálnia kell az Azure Recovery-szolgáltatót az előfizetésben a [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), az alábbiak szerint:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tár egy logikai tároló, amely a védett erőforrások, például az Azure-beli virtuális gépek biztonsági mentési szolgáltatásait tárolja. A biztonsági mentési feladatok futtatásakor egy helyreállítási pontot hoz létre a Recovery Services-tárolón belül. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

A tároló létrehozásakor:

- Az erőforráscsoport és a hely mezőben válassza ki azt az erőforráscsoportot és helyet, amelyről biztonsági másolatot szeretne készíteni.
- Ha ezt a [parancsfájlt](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json) használta a virtuális gép létrehozásához, az erőforráscsoport **myResourceGroup**, a virtuális gép **_myVM_*, és az erőforrások a **WestEurope** régióban találhatók.
- A Azure Backup automatikusan kezeli a tárterületet a biztonsági másolatok tárolására. Alapértelmezés szerint a [tár geo-redundáns tárolást (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)használ. A Geo-redundancia biztosítja, hogy a biztonsági másolatba mentett adatok egy másodlagos Azure-régióba replikálódnak, amely több száz kilométerre van az elsődleges régiótól.

Most hozzon létre egy tárolót:

1. A  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) használatával hozza létre a tárolót:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Állítsa be a tár környezetét a [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), a következő módon:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Módosítsa a tár tárolási redundancia-konfigurációját (LRS/GRS) a [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty), a következő módon:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > A tárterület-redundancia csak akkor módosítható, ha nincsenek védett biztonsági mentési elemek a tárolóban.

## <a name="enable-backup-for-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gépeken

Engedélyezheti egy Azure-beli virtuális gép biztonsági mentését, és megadhat egy biztonsági mentési szabályzatot.

- A szabályzat meghatározza a biztonsági mentések futását, valamint azt, hogy a biztonsági másolatok által létrehozott helyreállítási pontokat mennyi ideig kell megőrizni.
- Az alapértelmezett védelmi házirend naponta egyszer futtat biztonsági mentést a virtuális gép számára, és 30 napig megőrzi a létrehozott helyreállítási pontokat. Ezt az alapértelmezett házirendet használhatja a virtuális gép gyors biztosításához.

A biztonsági mentést a következőképpen engedélyezheti:

1. Először állítsa be az alapértelmezett szabályzatot a [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Engedélyezze a virtuális gép biztonsági mentését az [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Adja meg a házirendet, az erőforráscsoportot és a virtuális gép nevét.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

A biztonsági mentések a biztonsági mentési szabályzatban megadott ütemezés szerint futnak. Az igény szerinti biztonsági mentést is futtathatja:

- Az első kezdeti biztonsági mentési feladathoz teljes helyreállítási pont jön létre.
- A kezdeti biztonsági mentés után az egyes biztonsági mentési feladatok növekményes helyreállítási pontokat hoznak létre.
- A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

Igény szerinti biztonsági mentés futtatásához használja a [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Megad egy tárolót a tárolóban, amely a biztonsági mentési adatait a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)tárolja.
- A rendszer minden olyan virtuális gépet, amelyről biztonsági másolat készül, egy elemként kezeli. A biztonsági mentési feladatok elindításához a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)használatával szerezheti be a virtuális gép adatait.

Futtasson egy igény szerinti biztonsági mentési feladatot a következőképpen:

1. Adja meg a tárolót, szerezze be a virtuális gép adatait, és futtassa a biztonsági mentést.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Előfordulhat, hogy akár 20 percet is várnia kell, mivel az első biztonsági mentési feladatok teljes helyreállítási pontot hoznak létre. Figyelje meg a feladatot a következő eljárásban leírtak szerint.

## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

1. Futtassa a [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsot a feladatok állapotának figyeléséhez.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    A kimenet a következő példához hasonlóan jelenik meg, amely a feladatot **a következőképpen mutatja:**

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. A feladatok állapotának **befejeződése után** a virtuális gép védett, és teljes helyreállítási pontja van tárolva.

## <a name="clean-up-the-deployment"></a>Az üzemelő példány tisztítása

Ha már nincs szüksége a virtuális gép biztonsági mentésére, törölheti azt.

- Ha ki szeretné próbálni a virtuális gép visszaállítását, ugorja át a tisztítást.
- Ha meglévő virtuális gépet használt, akkor kihagyhatja a végleges [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot, hogy az erőforráscsoport és a virtuális gép ne legyen a helyén.

Tiltsa le a védelmet, távolítsa el a visszaállítási pontokat és a tárolót. Ezután törölje az erőforráscsoportot és a kapcsolódó virtuálisgép-erőforrásokat az alábbiak szerint:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot.

- [Megtudhatja, hogyan](tutorial-backup-vm-at-scale.md) készíthet biztonsági mentést a virtuális gépekről a Azure Portalban.
- [Megtudhatja, hogyan](tutorial-restore-disk.md) állíthatja be gyorsan a virtuális gépeket