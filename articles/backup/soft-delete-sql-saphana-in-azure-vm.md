---
title: Az Azure-beli virtuális gépeken futó SQL Server-kiszolgáló és a SAP HANA Azure-beli virtuális gépek számítási feladatainak törlése
description: Ismerje meg, hogy az Azure-beli virtuális gépen futó SQL Server és az Azure-beli virtuális gépek számítási feladatainak SAP HANAa hogyan teszi biztonságosabbá a biztonsági mentéseket
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91254295"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Az Azure-beli virtuális gépeken futó SQL Server-kiszolgáló és a SAP HANA Azure-beli virtuális gépek számítási feladatainak törlése

A Azure Backup mostantól az Azure-beli virtuális gépen futó SQL Serverhez és az Azure-beli virtuális gépek számítási feladataihoz SAP HANA. Ez a már támogatott Azure-beli [virtuális gépekre vonatkozó Soft delete forgatókönyvön](soft-delete-virtual-machines.md)kívül.

A [Soft delete](backup-azure-security-feature-cloud.md) egy biztonsági funkció, amely a törlés után is védelmet nyújt a biztonsági mentési adatai számára. A helyreállítható törléssel akkor is, ha egy rosszindulatú szereplő töröl egy adatbázis biztonsági másolatát (vagy a biztonsági mentési adat véletlenül törlődik), a biztonsági mentési adat 14 további napig megmarad. Ez lehetővé teszi, hogy a biztonsági mentési tétel adatvesztés nélkül legyen helyreállítva. Ez a "Soft Delete" állapotban lévő biztonsági mentési adatoknak a 14 napos további megőrzésével kapcsolatos költségek nem merülnek fel az ügyfél számára.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Az Azure-beli virtuális gépen futó SQL Server-kiszolgáló Soft delete Azure Portal használatával

>[!NOTE]
>Ezek az utasítások az Azure virtuális gépen SAP HANAra is érvényesek.

1. Egy SQL Server-adatbázis biztonsági mentési adatfájljainak törléséhez le kell állítani a biztonsági mentést. A Azure Portal nyissa meg a Recovery Services-tárolót, nyissa meg a biztonsági mentési elemet, és válassza a **biztonsági mentés leállítása** lehetőséget.

   ![Biztonsági mentés leállítása](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. A következő ablakban lehetősége lesz törölni vagy megőrizni a biztonsági mentési adatait. Ha a **biztonsági másolati elemek törlése** lehetőséget választja, az adatbázis biztonsági másolata nem lesz véglegesen törölve. Ehelyett a rendszer 14 napig őrzi meg a biztonsági mentési adatait a helyreállítható törölt állapotban. A törlés a 15. napon, a normál riasztási e-mailekkel, az első, a 12. és a 15. napon értesíti a felhasználót az adatbázis biztonsági mentési állapotáról.

   ![Biztonsági mentési adatok törlése](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. A 14 nap során a Recovery Services-tárolóban megjelenik a Soft Deleted elem, amely mellett vörös "Soft-Delete" ikon jelenik meg.

   ![Soft törölt elemek](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. A puha törlésű adatbázis visszaállításához előbb törölni kell a szolgáltatást. A törlés visszavonásához válassza a Soft-Deleted DB elemet, majd válassza a **Törlés** visszavonása lehetőséget.

   ![Adatbázis törlésének visszavonása](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Megjelenik egy ablak, amely figyelmeztetést jelenít meg, ha a törlés lehetőség van kiválasztva, az adatbázis összes visszaállítási pontja törölve lesz, és a visszaállítási művelet elvégezhető. A biztonsági mentési elem a "védelem leállítása az adatmegőrzéshez" állapotú, a biztonsági mentések felfüggesztésével, valamint a biztonsági mentési szabályzat hatályba léptetése után örökre megőrizve.

   ![Figyelmeztetés törlésének visszavonása](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. Ezen a ponton visszaállíthatja az adathalmazt úgy, hogy kijelöli a visszaállítás lehetőséget a kiválasztott **helyreállított** törölt biztonsági mentési elemhez.

   ![Virtuális gép visszaállítása](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. A törlési folyamat befejezése után az állapot vissza fog térni a "biztonsági mentés leállítása az adatmegőrzéshez" értékre, majd a **biztonsági mentés folytatása** lehetőségre kattint. A **biztonsági mentés folytatása** művelet visszaadja az aktív állapotú biztonsági mentési elemet, amely a felhasználó által a biztonsági mentési és adatmegőrzési ütemtervet definiáló biztonsági mentési házirendhez van társítva.

   ![Biztonsági mentés folytatása](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Az SQL Server rendszerhez készült Soft delete Azure PowerShell használatával

>[!NOTE]
>Az az. Recoveryservices szolgáltatónál verziója szükséges ahhoz, hogy a Azure PowerShell a Soft delete használatával a minimális 2.2.0-t használja. `Install-Module -Name Az.RecoveryServices -Force`A legújabb verziójának beszerzéséhez használja a következőt:.

A Azure PowerShell használatára vonatkozó lépések sorrendjének megegyeznek az Azure Portalban, a fentiekben ismertetett módon.

### <a name="delete-the-backup-item-using-azure-powershell"></a>A biztonsági mentési tétel törlése Azure PowerShell használatával

Törölje a biztonsági mentési tételt a [disable-AzRecoveryServicesBackupProtection PowerShell-](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) parancsmag használatával.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

A biztonsági mentési elem **DeleteState** a **NotDeleted** -ről a **törlendőként**-re változik. A biztonsági mentési adat 14 napig őrzi meg a rendszer. Ha vissza szeretné állítani a törlési műveletet, hajtsa végre a visszavonás – törlés műveletet.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>A törlési művelet visszavonása a Azure PowerShell használatával

Először olvassa be a megfelelő biztonsági mentést, amely a törlésre kész állapotú.

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Ezután hajtsa végre a visszavonás-törlés műveletet a [Visszavonás-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell-parancsmag használatával.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

A biztonsági mentési elemek **DeleteState** visszaállnak a **NotDeleted**. A védelem azonban továbbra is leáll. A védelem újbóli engedélyezéséhez folytassa a biztonsági mentést.

## <a name="how-to-disable-soft-delete"></a>A Soft delete letiltása

A funkció letiltása nem ajánlott. Ha a védett elemek új tárolóba való áthelyezését tervezi, és a törlés és az ismételt védelem (például egy tesztkörnyezetben) esetében nem várhatja el a szükséges 14 napot, az egyetlen olyan körülmény, amelyben érdemes megfontolni a Soft delete letiltását. A Soft delete letiltásával kapcsolatos utasításokért lásd: a [Soft delete engedélyezése és letiltása](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Következő lépések

- A Soft delete [gyakori kérdéseinek](backup-azure-security-feature-cloud.md#frequently-asked-questions) áttekintése
- További információ a [Azure Backup összes biztonsági szolgáltatásáról](security-overview.md)
