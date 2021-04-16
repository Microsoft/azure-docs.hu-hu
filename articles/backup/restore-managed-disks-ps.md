---
title: Azure-Managed Disks visszaállítása Azure PowerShell
description: Megtudhatja, hogyan állítható vissza az Azure Managed Disks a Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520037"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure-Managed Disks visszaállítása Azure PowerShell

Ez a cikk azt ismerteti, hogyan állíthatók [Managed Disks](../virtual-machines/managed-disks-overview.md) Azure-beli virtuális Azure Backup.

Jelenleg a Original-Location (OLR) lehetőség nem támogatott a visszaállításhoz a meglévő forráslemez cseréjével, ahonnan a biztonsági másolatok készültek. Helyreállítási pontról visszaállítással új lemezt hozhat létre ugyanazon az erőforráscsoporton belül, mint annak a forráslemeznek a lemezét, amelyről a biztonsági másolatok készültek, vagy bármely más erőforráscsoportban. Ez az úgynevezett Alternate-Location Recovery (ALR), és így a forráslemez és a visszaállított (új) lemez is megtartható.

Ebből a cikkből megtudhatja, hogyan:

- Visszaállítás új lemez létrehozásához

- A visszaállítási művelet állapotának nyomon követése

A példákban a "testBkpVaultRG" erőforráscsoportban lévő "TestBkpVaultRG" biztonsági mentési tárolóra hivatkozunk

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Visszaállítás új lemez létrehozásához

### <a name="setting-up-permissions"></a>Engedélyek beállítása

A Backup Vault felügyelt identitással fér hozzá más Azure-erőforrásokhoz. A biztonsági másolatból való visszaállításhoz a Backup-tároló felügyelt identitásának engedélykészletre van szüksége arra az erőforráscsoportra, ahol a lemezt vissza kívánja állítani.

A Backup-tároló egy rendszer által hozzárendelt felügyelt identitást használ, amely erőforrásonként egy-egyre van korlátozva, és az erőforrás életciklusához van kötve. A felügyelt identitáshoz az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) adhat engedélyeket. A felügyelt identitás egy speciális típusú szolgáltatásnév, amely csak Azure-erőforrásokkal használható. További információ a [felügyelt identitásokkal kapcsolatban:](../active-directory/managed-identities-azure-resources/overview.md).

Rendelje hozzá a tároló rendszer által hozzárendelt felügyelt identitásához kapcsolódó engedélyeket arra a cél erőforráscsoportra, ahol a lemezek vissza lesznek állítani/létre lesznek hozva az [itt említettek szerint.](restore-managed-disks.md#restore-to-create-a-new-disk)

### <a name="fetching-the-relevant-recovery-point"></a>A megfelelő helyreállítási pont beolvasása

A [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) paranccsal hívja le az összes példányt, és azonosítsa a megfelelő példányt.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Az **Az.Resourcegraph** és a [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) paranccsal számos tárolóban és előfizetésben lévő példány között kereshet.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

A példány azonosítása után hívja le a megfelelő helyreállítási pontot.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>A visszaállítási kérelem előkészítése

Készítse el annak az új lemeznek az ARM-azonosítóját, amelyet a [](#setting-up-permissions)célként kijelölt erőforráscsoporttal kell létrehozni, amelyhez a fent részletezett módon engedélyeket rendelt, valamint a szükséges lemeznevet. Egy lemez neve lehet például **PSTestDisk2** egy másik előfizetéssel egy erőforráscsoport **célcsoportja** alatt.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Az [Initialize-AzDataProtectionRestoreRequest paranccsal](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) készítse elő a visszaállítási kérést az összes releváns részlettel.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>A visszaállítás aktiválása

A [Start-AzDataProtectionBackupInstanceRestore paranccsal](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) indítsa el a visszaállítást a fent előkészített kéréssel.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Feladat nyomon követése

Az összes feladat nyomon követése a [Get-AzDataProtectionJob paranccsal.](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) Felsorolhatja az összes feladatot, és lekérhet egy adott feladat részleteit.

Az **Az.ResourceGraph** használatával az összes backup-tároló összes feladatát nyomon követheti. A [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) paranccsal le tudja kérni a megfelelő feladatot, amely bármely Backup-tárolóban lehet.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Backup – gyakori kérdések](disk-backup-faq.yml)
