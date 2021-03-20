---
title: 'Oktatóanyag: több Azure-beli virtuális gép biztonsági mentése a PowerShell-lel'
description: Ez az oktatóanyag részletesen ismerteti a több Azure-beli virtuális gép egy Recovery Services-tárolóba való biztonsági mentését Azure PowerShell használatával.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 53a5df430a3ea57201ecb19bac68ef9f073beb14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980970"
---
# <a name="back-up-azure-vms-with-powershell"></a>Azure-beli virtuális gépek biztonsági mentése PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag azt ismerteti, hogyan helyezhet üzembe egy [Azure Backup](backup-overview.md) Recovery Services-tárolót több Azure-beli virtuális gép biztonsági mentéséhez a PowerShell használatával.  

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Hozzon létre egy Recovery Services-tárolót, és állítsa be a tár környezetét.
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * Egy igény szerinti biztonsági mentési feladatot indít el a védett virtuális gépek számára, mielőtt biztonsági másolatot készít (vagy véd) egy virtuális gépet, el kell végeznie az [előfeltételeket](backup-azure-arm-vms-prepare.md) , hogy előkészítse a környezetet a virtuális gépek védelmére.

> [!IMPORTANT]
> Ez az oktatóanyag feltételezi, hogy már létrehozott egy erőforráscsoportot és egy Azure-beli virtuális gépet.

## <a name="sign-in-and-register"></a>Bejelentkezés és regisztrálás

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

    ```powershell
    Connect-AzAccount
    ```

2. Amikor első alkalommal használja a Azure Backup, regisztrálnia kell az Azure Recovery Service providert az előfizetésében a [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Ha már regisztrált, ugorja át ezt a lépést.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tár egy logikai tároló, amely a védett erőforrások, például az Azure-beli virtuális gépek biztonsági mentési szolgáltatásait tárolja. A biztonsági mentési feladatok futtatásakor egy helyreállítási pontot hoz létre a Recovery Services-tárolón belül. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

* Ebben az oktatóanyagban a tárolót ugyanabban az erőforráscsoportban és helyen hozza létre, mint azt a virtuális gépet, amelyről biztonsági másolatot szeretne készíteni.
* A Azure Backup automatikusan kezeli a tárterületet a biztonsági másolatok tárolására. Alapértelmezés szerint a [tár geo-redundáns tárolást (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)használ. A Geo-redundancia biztosítja, hogy a biztonsági másolatba mentett adatok egy másodlagos Azure-régióba replikálódnak, amely több száz kilométerre van az elsődleges régiótól.

Hozza létre a tárolót a következőképpen:

1. A  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)használatával hozza létre a tárolót. Adja meg annak a virtuális gépnek az erőforráscsoport-nevét és helyét, amelyről biztonsági másolatot szeretne készíteni.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ezért célszerű a Backup Recovery Services Vault-objektumot tárolni egy változóban.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Állítsa be a tár környezetét a [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * A tárolási környezet a tár által védett adatok típusa.
   * A környezet beállítása után az összes további parancsmagra vonatkozik

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése

A biztonsági mentések a biztonsági mentési szabályzatban megadott ütemezés szerint futnak. Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik.

* Az alapértelmezett védelmi házirend egy adott időpontban naponta egyszer elindítja a biztonsági mentési feladatot.
* Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat.

A jelen oktatóanyagban az Azure-beli virtuális gép engedélyezéséhez és biztonsági mentéséhez a következőket tesszük:

1. Adja meg azt a tárolót, amely a biztonsági mentési adatait a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)tárolja.
2. Minden biztonsági mentéshez használt virtuális gép egy tétel. A biztonsági mentési feladatok elindításához a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)használatával szerezheti be a virtuális gép adatait.
3. Futtasson egy igény szerinti biztonsági mentést a[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * Az első kezdeti biztonsági mentési feladathoz teljes helyreállítási pont jön létre.
    * A kezdeti biztonsági mentés után az egyes biztonsági mentési feladatok növekményes helyreállítási pontokat hoznak létre.
    * A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

A következőképpen engedélyezheti és futtathatja a biztonsági mentést:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a virtuális gép biztonsági mentése során problémákba ütközik, tekintse át ezt a [hibaelhárítási cikket](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ha törölni kell egy tárolót, először törölnie kell a helyreállítási pontokat a tárolóban, majd meg kell szüntetnie a tároló regisztrációját a következőképpen:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Következő lépések

* [Tekintse át](backup-azure-vms-automation.md) az Azure-beli virtuális gépek PowerShell-lel történő biztonsági mentésének és visszaállításának részletes ismertetését.
* [Azure-beli virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
* [Azure-beli virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
