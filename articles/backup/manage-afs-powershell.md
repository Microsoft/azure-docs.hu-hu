---
title: Azure-fájlmegosztás biztonsági másolatainak kezelése a PowerShell-lel
description: Megtudhatja, hogyan kezelheti és figyelheti a PowerShell használatával a Azure Backup szolgáltatás által biztonsági mentés alatt lévő Azure-fájlmegosztást.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89000226"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Azure-fájlmegosztás biztonsági másolatainak kezelése a PowerShell-lel

Ez a cikk azt ismerteti, hogyan használható a Azure PowerShell az Azure Backup szolgáltatás által biztonsági mentés alatt álló Azure-fájlmegosztás felügyeletéhez és figyeléséhez.

> [!WARNING]
> Győződjön meg arról, hogy a PowerShell-verzió az AFS biztonsági mentések esetében az "az. Recoveryservices szolgáltatónál 2.6.0" minimális verziójára van frissítve. További részletekért tekintse meg a módosítás követelményét [ismertető szakaszt](backup-azure-afs-automation.md#important-notice-backup-item-identification) .

## <a name="modify-the-protection-policy"></a>A védelmi szabályzat módosítása

Az Azure-fájlmegosztás biztonsági mentésére használt szabályzat módosításához használja az [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). A megfelelő biztonsági mentési elem és az új biztonsági mentési szabályzat meghatározása.

A következő példa a **testAzureFS** védelmi szabályzatot a **dailyafs** -ről a **monthlyafs**-re módosítja.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Biztonsági mentési és visszaállítási feladatok nyomon követése

Az igény szerinti biztonsági mentési és visszaállítási műveletek a feladatokat egy AZONOSÍTÓval együtt visszaadják, ahogyan az [igény szerinti biztonsági mentés futtatásakor](backup-azure-afs-automation.md#trigger-an-on-demand-backup)is látható. A [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsmag használatával követheti nyomon a feladatok előrehaladását és részleteit.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Az összes jövőbeli biztonsági mentési feladat leállítása és az összes helyreállítási pont *törlése*
* Az összes jövőbeli biztonsági mentési feladat leállítása, de a helyreállítási pontok *elhagyása*

Előfordulhat, hogy a tárolóban lévő helyreállítási pontok elhagyása után a rendszer megőrzi a Azure Backup által létrehozott mögöttes pillanatképeket. Azonban a helyreállítási pontok elhagyása után a fájlmegosztás később is visszaállítható, ha szükséges. A helyreállítási pontok elhagyása költségével kapcsolatos további információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/storage/files/). Ha úgy dönt, hogy törli az összes helyreállítási pontot, a fájlmegosztás nem állítható vissza.

## <a name="stop-protection-and-retain-recovery-points"></a>Védelem leállítása és helyreállítási pontok megőrzése

A védelem leállításához az adat megőrzése mellett használja a [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) parancsmagot.

A következő példa leállítja a *afsfileshare* -fájlmegosztás védelmét, de megőrzi az összes helyreállítási pontot:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

A kimenetben lévő Job ID attribútum megfelel a Backup szolgáltatás által a "védelem leállítása" művelethez létrehozott feladatok AZONOSÍTÓjának. A feladatok állapotának nyomon követéséhez használja a [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsmagot.

## <a name="stop-protection-without-retaining-recovery-points"></a>A védelem leállítása a helyreállítási pontok megőrzése nélkül

A védelem leállításához a helyreállítási pontok megőrzése nélkül használja a [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) parancsmagot, és adja hozzá a **-RemoveRecoveryPoints** paramétert.

A következő példa leállítja a *afsfileshare* -fájlmegosztás védelmét a helyreállítási pontok megőrzése nélkül:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Következő lépések

[Ismerje meg](manage-afs-backup.md) , hogyan kezelheti az Azure-fájlmegosztás biztonsági másolatait a Azure Portalban.
