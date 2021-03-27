---
title: Az Azure Managed Disks visszaállítása Azure PowerShell használatával
description: Ismerje meg, hogyan állíthatja vissza az Azure Managed Diskst a Azure PowerShell használatával.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630380"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure-Managed Disks visszaállítása Azure PowerShell használatával

Ez a cikk azt ismerteti, hogyan állíthatja vissza az [Azure Managed Diskst](../virtual-machines/managed-disks-overview.md) egy Azure Backup által létrehozott visszaállítási pontról.

Jelenleg a visszaállítási Original-Location helyreállítási (OLR) lehetőség a meglévő forrás lemezének lecserélésekor, ahonnan a biztonsági másolatok készültek, nem támogatottak. A helyreállítási pontról visszaállíthatók úgy, hogy egy új lemezt hozzanak létre ugyanabba az erőforráscsoporthoz, amelyből a biztonsági mentéseket vagy más erőforráscsoportot is létrehozták. Ez a Alternate-Location Recovery (ALR) néven ismert, amely segít megőrizni a forrás-és a visszaállított (új) lemezt is.

Ebből a cikkből megtudhatja, hogyan:

- Visszaállítás új lemez létrehozásához

- A visszaállítási művelet állapotának nyomon követése

A példákban a "testBkpVaultRG" erőforráscsoport alatt található "TestBkpVault" biztonsági mentési tárolóra fogunk hivatkozni.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Visszaállítás új lemez létrehozásához

### <a name="setting-up-permissions"></a>Engedélyek beállítása

A Backup-tároló felügyelt identitás használatával fér hozzá más Azure-erőforrásokhoz. A biztonsági másolatból történő visszaállításhoz a Backup-tár felügyelt identitásához meg kell adni azon erőforráscsoport engedélyeit, amelyben a lemezt vissza kell állítani.

A Backup-tároló egy rendszerhez rendelt felügyelt identitást használ, amely egy erőforrásra korlátozódik, és az erőforrás életciklusához van kötve. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával engedélyeket adhat a felügyelt identitásnak. A felügyelt identitás olyan speciális típusú szolgáltatásnév, amelyet csak az Azure-erőforrásokkal lehet használni. További információ a [felügyelt identitásokról](../active-directory/managed-identities-azure-resources/overview.md).

Rendelje hozzá a tár rendszerhez rendelt felügyelt identitásához tartozó megfelelő engedélyeket a cél erőforráscsoporthoz, ahol a lemezek visszaállíthatók/létrejönnek az [itt](restore-managed-disks.md#restore-to-create-a-new-disk)említettek szerint.

### <a name="fetching-the-relevant-recovery-point"></a>A megfelelő helyreállítási pont beolvasása

Az összes példány beolvasása a [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) paranccsal, és az érintett példány azonosítása.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Az **az. Resourcegraph** és a [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) parancs használatával számos tárolóban és előfizetésben is kereshet a különböző példányok között.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

A példány azonosítása után hívja le a megfelelő helyreállítási pontot.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>A visszaállítási kérelem előkészítése

Hozza létre a cél erőforráscsoporthoz létrehozandó új lemez ARM-AZONOSÍTÓját, hogy mely engedélyek legyenek hozzárendelve a [fentiekben](#setting-up-permissions)részletezett módon, valamint a szükséges lemez neve. Például egy lemez elnevezhető egy másik előfizetéssel rendelkező erőforráscsoport **Targetrg** **PSTestDisk2** .

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

A Restore [-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) parancs használatával készítse elő a visszaállítási kérést az összes vonatkozó részletkel.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>A visszaállítás elindítása

A [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) parancs használatával aktiválja a visszaállítást a fent előkészített kérelemmel.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Feladatok nyomon követése

Az összes feladat követése a [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) parancs használatával. Listázhatja az összes feladatot, és beolvashatja az adott feladat részleteit.

Az **az. ResourceGraph** használatával is nyomon követheti az összes feladatot az összes Backup-tárolóban. A [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) parancs használatával szerezze be a megfelelő feladatot, amely bármely Backup-tárolóban lehet.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Backup – gyakori kérdések](disk-backup-faq.md)
