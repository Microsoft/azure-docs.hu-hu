---
title: Áltörlés virtuális gépekhez
description: Megtudhatja, hogyan teszi biztonságosabbá a biztonsági mentéseket a Virtual Machines szolgáltatásban.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89022241"
---
# <a name="soft-delete-for-virtual-machines"></a>Áltörlés virtuális gépekhez

A virtuális gépek Soft törlésével megvédheti a virtuális gépek biztonsági másolatait a nem kívánt törléstől. Még a biztonsági másolatok törlése után is megmaradnak a további 14 napig.

> [!NOTE]
> A helyreállítható törlés csak a törölt biztonsági mentési adatvédelmet védi. Ha egy virtuális gépet biztonsági mentés nélkül töröl, a törlési funkció nem őrzi meg az adattárolást. A teljes rugalmasság biztosítása érdekében minden erőforrást Azure Backup védelemmel kell ellátni.
>

## <a name="supported-regions"></a>Támogatott régiók

A Soft delete jelenleg az USA nyugati középső régiójában támogatott Kelet-Ázsia, Közép-Kanada, Kelet-Kanada, Közép-Franciaország, Dél-Brazília, Korea középső régiója, Dél-Korea, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Kelet-Ausztrália, Kelet-Ausztrália, Észak-Európa, USA nyugati régiója, Nyugat-RÉGIÓJA, USA középső régiója, Dél-Kelet-Ázsia, északi középső régió, USA 2. keleti régiója, Kelet-Japán, Nyugat-India , Észak-Svájc, Nyugat-Svájc, Norvégia nyugati régiója, Kelet-Norvégia és minden nemzeti régió.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Soft Delete a virtuális gépekhez Azure Portal használatával

1. Egy virtuális gép biztonsági mentési adatmennyiségének törléséhez le kell állítani a biztonsági mentést. A Azure Portal nyissa meg a Recovery Services-tárolót, kattintson a jobb gombbal a biztonsági másolat elemre, és válassza a **biztonsági mentés leállítása** lehetőséget.

   ![Azure Portal biztonsági másolati elemek képernyőképe](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. A következő ablakban lehetősége lesz törölni vagy megőrizni a biztonsági mentési adatait. Ha a biztonsági másolati elemek **törlése** és a **biztonsági mentés leállítása** lehetőséget választja, a virtuális gép biztonsági mentése nem lesz véglegesen törölve. Ehelyett a rendszer 14 napig őrzi meg a biztonsági mentési adatait a helyreállítható törölt állapotban. Ha a **biztonsági mentési adatok törlése** lehetőség van kiválasztva, a rendszer az e-mailek törlésére vonatkozó riasztást küldi el a felhasználó számára, hogy 14 nap maradjon a biztonsági mentési adatok megőrzéséhez. Emellett e-mailben értesítést küldünk a 12. napon arról, hogy a törölt adat feltámasztása még két nappal megmaradt. A törlés Elhalasztva marad a 15. napon, amikor végleges törlés történik, és a rendszer a végleges e-mail riasztást küldi el az adat végleges törléséről.

   ![Képernyőkép a Azure Portalről, a biztonsági mentési képernyő leállítása](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. A 14 nap során a Recovery Services-tárolóban a helyreállított virtuális gép megjelenik egy piros "Soft-Delete" ikon mellett.

   ![Képernyőkép a Azure Portalről, a virtuális gép a Soft delete állapotában](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Ha a tárolóban nem találhatók meg a helyreállított biztonsági mentési elemek, a tár ekkor nem törölhető. A biztonsági másolati elemek végleges törlését követően próbálja meg törölni a tárolót, és nincsenek olyan elemek, amelyek nem törlődnek a tárolóban.

4. A helyreállított virtuális gép visszaállításához először törölni kell a szolgáltatást. A törlés visszavonásához válassza a Soft-Deleted VM elemet, majd válassza a **Törlés** visszavonása lehetőséget.

   ![Azure Portal képernyőkép a virtuális gép törléséről](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Megjelenik egy ablak, amely figyelmeztetést jelenít meg, ha a törlés lehetőség van kiválasztva, a virtuális gép összes visszaállítási pontja törölve lesz, és a visszaállítási művelet elvégezhető. A virtuális gép "megőrzött adatmegőrzés" állapotban marad, a biztonsági mentések felfüggesztésével és a biztonsági mentési szabályzat hatályba lépésével megőrizve.

   ![Azure Portal képernyőképe – virtuális gép törlésének megerősítése](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Ezen a ponton a virtuális GÉPET a kiválasztott visszaállítási pontról a **virtuális gép visszaállítása** lehetőség kiválasztásával is visszaállíthatja.  

   ![Képernyőkép a Azure Portalről, virtuális gép visszaállítása lehetőség](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > A rendszer csak akkor futtatja a szemét-gyűjtőt, amikor a felhasználó elvégezte a **biztonsági mentési művelet folytatását** .

5. A törlési folyamat befejezése után az állapot vissza fog térni a "biztonsági mentés leállítása az adatmegőrzéshez" értékre, majd a **biztonsági mentés folytatása** lehetőségre kattint. A **biztonsági mentés folytatása** művelet visszaadja az aktív állapotú biztonsági mentési elemet, amely a felhasználó által a biztonsági mentési és adatmegőrzési ütemtervet definiáló biztonsági mentési házirendhez van társítva.

   ![Képernyőkép a Azure Portalről, biztonsági mentés folytatása lehetőség](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Soft Delete a virtuális gépekhez Azure PowerShell használatával

> [!IMPORTANT]
> Az az. Recoveryservices szolgáltatónál verziója szükséges ahhoz, hogy a Azure PowerShell a Soft delete használatával a minimális 2.2.0-t használja. ```Install-Module -Name Az.RecoveryServices -Force```A legújabb verziójának beszerzéséhez használja a következőt:.

A fentiekben leírtak szerint a lépések sorrendjének Azure Portal a Azure PowerShell is.

### <a name="delete-the-backup-item-using-azure-powershell"></a>A biztonsági mentési tétel törlése Azure PowerShell használatával

Törölje a biztonsági mentési tételt a [disable-AzRecoveryServicesBackupProtection PowerShell-](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) parancsmag használatával.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

A biztonsági mentési elem "DeleteState" változása "NotDeleted" értékről "törlendőként" értékre változik. A biztonsági mentési adat 14 napig őrzi meg a rendszer. Ha vissza szeretné állítani a törlési műveletet, hajtsa végre a visszavonás – törlés műveletet.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>A törlési művelet visszavonása a Azure PowerShell használatával

Először olvassa be a megfelelő biztonsági mentést, amely a törlésre kész állapotú.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ezután hajtsa végre a visszavonás-törlés műveletet a [Visszavonás-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell-parancsmag használatával.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

A biztonsági mentési elemek "DeleteState" állapota "NotDeleted" lesz. A védelem azonban továbbra is leáll. A védelem újbóli engedélyezéséhez [folytassa a biztonsági mentést](./backup-azure-vms-automation.md#change-policy-for-backup-items) .

## <a name="soft-delete-for-vms-using-rest-api"></a>Soft Delete a virtuális gépekhez REST API használatával

- Törölje a biztonsági másolatokat a REST API az [itt](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)említett módon.
- Ha vissza szeretné vonni ezeket a törlési műveleteket, tekintse meg az [itt](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)említett lépéseket.

## <a name="how-to-disable-soft-delete"></a>A Soft delete letiltása

A funkció letiltása nem ajánlott. Ha a védett elemek új tárolóba való áthelyezését tervezi, és a törlés és az ismételt védelem (például egy tesztkörnyezetben) esetében nem várhatja el a szükséges 14 napot, az egyetlen olyan körülmény, amelyben érdemes megfontolni a Soft delete letiltását. A Soft delete letiltásával kapcsolatos utasításokért lásd: a [Soft delete engedélyezése és letiltása](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Következő lépések

- A Soft delete [gyakori kérdéseinek](backup-azure-security-feature-cloud.md#frequently-asked-questions) áttekintése
- További információ a [Azure Backup összes biztonsági szolgáltatásáról](security-overview.md)
