---
title: Azure Backup helyreállítható törlés
description: Megtudhatja, hogyan teheti biztonságosabbá a biztonsági mentéseket a Azure Backup biztonsági funkciói segítségével.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95254049"
---
# <a name="soft-delete-for-azure-backup"></a>Azure Backup helyreállítható törlés

A biztonsági problémákkal, például a kártevővel, a ransomware és a behatolással kapcsolatos problémák egyre nagyobbak. Ezek a biztonsági problémák költségesek lehetnek a pénz és az adatmennyiség tekintetében. Az ilyen támadások elleni védelem érdekében Azure Backup mostantól biztonsági funkciókat biztosít a biztonsági mentési adatainak a törlés után még a védelme érdekében.

Az egyik ilyen funkció a Soft delete. A helyreállítható törléssel akkor is, ha egy rosszindulatú színész töröl egy biztonsági másolatot (vagy véletlenül törli a biztonsági mentési adatmennyiséget), a biztonsági mentési adat 14 további napig tart, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. A "Soft Delete" állapotban lévő biztonsági mentési adatok megőrzésének további 14 napja nem jár semmilyen költséggel.

A következő szolgáltatások esetében érhető el a Soft delete Protection:

- [Azure-beli virtuális gépekhez készült Soft delete](soft-delete-virtual-machines.md)
- [Az Azure-beli virtuális gépen futó SQL Server helyreállítható törlését és az Azure-beli virtuális gépek számítási feladataiban SAP HANA](soft-delete-sql-saphana-in-azure-vm.md)

Ez a folyamatábra a biztonsági mentési elemek különböző lépéseit és állapotát jeleníti meg, ha a Soft delete engedélyezve van:

![A nem törölt biztonságimásolat-elemek életciklusa](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>A Soft delete engedélyezése és letiltása

A Soft delete alapértelmezés szerint engedélyezve van az újonnan létrehozott tárolókban a biztonsági mentési adatok véletlen vagy rosszindulatú törlésből való védelme érdekében.  A funkció letiltása nem ajánlott. Ha a védett elemek új tárolóba való áthelyezését tervezi, és a törlés és az ismételt védelem (például egy tesztkörnyezetben) esetében nem várhatja el a szükséges 14 napot, az egyetlen olyan körülmény, amelyben érdemes megfontolni a Soft delete letiltását. Csak a tár tulajdonosa tilthatja le ezt a funkciót. Ha letiltja ezt a funkciót, a védett elemek minden jövőbeli törlése azonnali eltávolítást eredményez, a visszaállítási lehetőség nélkül. A szolgáltatás letiltását megelőzően a törölt állapotban lévő biztonsági mentési adatmennyiséget a rendszer 14 napig változatlanul törli. Ha véglegesen törölni kívánja ezeket a fájlokat, törölnie kell a törlést, majd újra törölnie kell őket a végleges törléshez.

Fontos megjegyezni, hogy ha a Soft delete le van tiltva, a szolgáltatás le van tiltva a számítási feladatok összes típusához. Például nem lehet letiltani a helyreállítható törlést csak az SQL Server-vagy SAP HANA-adatbázisok esetében, miközben az azonos tárolóban lévő virtuális gépek esetében engedélyezve van. A részletes szabályozáshoz külön tárolókat is létrehozhat.

### <a name="disabling-soft-delete-using-azure-portal"></a>A Soft delete letiltása a Azure Portal használatával

A Soft delete letiltásához kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a tárolót, majd lépjen a **Beállítások**  ->  **Tulajdonságok menüpontra**.
2. A Tulajdonságok ablaktáblán válassza a **biztonsági beállítások**  ->  **frissítés** lehetőséget.  
3. A biztonsági beállítások ablaktábla **Soft delete** területén válassza a **Letiltás** lehetőséget.

![Nem kötelező törlés letiltása](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>A Soft delete letiltása a Azure PowerShell használatával

> [!IMPORTANT]
> Az az. Recoveryservices szolgáltatónál verziója szükséges ahhoz, hogy a Azure PowerShell a Soft delete használatával a minimális 2.2.0-t használja. ```Install-Module -Name Az.RecoveryServices -Force```A legújabb verziójának beszerzéséhez használja a következőt:.

A letiltáshoz használja a [set-AzRecoveryServicesVaultBackupProperty PowerShell-](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) parancsmagot.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>A Soft delete letiltása a REST API használatával

Ha REST API használatával szeretné letiltani a helyreállítható törlési funkciót, tekintse át az [itt](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)említett lépéseket.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>A helyreállított törölt biztonsági másolati elemek végleges törlése

A szolgáltatás letiltását megelőzően a törölt állapotban lévő biztonsági mentési állapotok helyreállított állapotban maradnak. Ha véglegesen törölni kívánja ezeket a fájlokat, törölje a törlést, majd törölje őket a végleges törléshez.

### <a name="using-azure-portal"></a>Az Azure Portal használata

Kövesse az alábbi lépéseket:

1. A [Soft delete letiltásához](#enabling-and-disabling-soft-delete)kövesse a következő lépéseket:.

2. A Azure Portal nyissa meg a tárolót, lépjen a **biztonsági másolatok elemre**, és válassza a Soft Deleted elemet.

   ![A nem törölt elem kiválasztása](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Válassza a **Törlés** visszavonása lehetőséget.

   ![Törlés visszavonása](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Ekkor megjelenik egy ablak. Válassza a **Törlés** visszavonása lehetőséget.

   ![Törlés visszavonása](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Válassza a **biztonsági mentési adattörlés** lehetőséget a biztonsági másolati adatbázis végleges törléséhez.

   ![Válassza a biztonsági másolatok törlése lehetőséget.](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Írja be a biztonsági mentési tétel nevét annak megerősítéséhez, hogy törölni kívánja a helyreállítási pontokat.

   ![Írja be a biztonsági másolati tétel nevét](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Az elemhez tartozó biztonsági másolati elemek törléséhez válassza a **Törlés** lehetőséget. Egy értesítési üzenetből megtudhatja, hogy a biztonsági mentési információ törölve lett.

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Ha törölve lettek az elemek, mielőtt a rendszer letiltotta a törlést, akkor azt a rendszer egy helyreállított állapotban fogja törölni. Ha azonnal törölni szeretné őket, a törlési műveletnek sztornírozva kell lennie, majd újra el kell végrehajtania.

Azonosítsa a helyreállított állapotban lévő elemeket.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ezután visszafordíthatja a törlési műveletet, amelyet a rendszer a Soft delete engedélyezésekor végzett.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Mivel a rendszer letiltotta a törlést, a törlési művelet a biztonsági másolatok azonnali eltávolítását fogja eredményezni.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>A REST API használata

Ha törölve lettek az elemek, mielőtt a rendszer letiltotta a törlést, akkor azt a rendszer egy helyreállított állapotban fogja törölni. Ha azonnal törölni szeretné őket, a törlési műveletnek sztornírozva kell lennie, majd újra el kell végrehajtania.

1. Először vonja vissza a törlési műveleteket az [itt](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)leírt lépésekkel.
2. Ezután tiltsa le a Soft-delete funkciót a REST API használatával az [itt](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)említett lépések végrehajtásával.
3. Ezután törölje a biztonsági mentéseket a REST API az [itt](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)említett módon.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Engedélyezni kell a Soft-delete szolgáltatást minden tárolón?

Nem, beépített és alapértelmezés szerint engedélyezve van az összes Recovery Services-tárolóhoz.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Megadhatom, hogy a törlési művelet befejezése után a rendszer hány napig őrizze meg az adataimat a törölt állapotban?

Nem, a törlési művelet után a további 14 napos további megőrzési idő rögzített.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Fizetnem kell a további 14 napos adatmegőrzési díjat?

Nem, ez a 14 napos további megőrzés ingyenesen használható a Soft delete funkcióinak részeként.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Végezhetek visszaállítási műveletet, ha az adatok helyreállítható törlési állapotban vannak?

Nem, vissza kell állítania a nem törölt erőforrás törlését a visszaállításhoz. A törlési művelet visszaállítja az erőforrást a **stop Protection szolgáltatásba, és megőrzi az adatállapotot,** ahol bármikor visszaállíthatja azokat. Ebben az állapotban a szemetet gyűjtő szüneteltetve marad.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>A pillanatképek ugyanazt az életciklust követik, mint a tároló helyreállítási pontjai?

Igen.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hogyan indíthatom újra az ütemezett biztonsági mentéseket egy helyreállított erőforráshoz?

A törlés után a folytatási művelet ismét védi az erőforrást. A folytatási művelet társít egy biztonsági mentési szabályzatot az ütemezett biztonsági mentések elindításához a kiválasztott megőrzési időtartammal. Emellett a szemetet gyűjtő is fut, amint a folytatási művelet befejeződik. Ha olyan helyreállítási pontról szeretne visszaállítást végezni, amely lejárt a lejárati dátumnál, azt javasoljuk, hogy a folytatási művelet elindítása előtt végezze el a helyreállítást.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Törölhetem a tárolót, ha a tárolóban nem találhatók a törölt elemek?

A Recovery Services-tároló nem törölhető, ha a tárolóban található helyreállítható állapotú biztonsági másolati elemek vannak. A törölt elemek a törlési művelet után 14 nappal véglegesen törlődnek. Ha nem tud megvárni 14 napig, tiltsa le a helyreállítható [törlést](#enabling-and-disabling-soft-delete), törölje a nem törölt elemek törlését, majd törölje újra a véglegesen a törléshez. Miután meggyőződött róla, hogy nincsenek védett elemek, és nem találhatók a törölt elemek, a tároló törölhető.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Törölhetem a 14 napnál régebbi adattörlési időszakot a törlés után?

Nem. Nem lehet kényszeríteni a nem törölt elemek törlését. A rendszer 14 nap elteltével automatikusan törli őket. Ez a biztonsági funkció lehetővé teszi az adatok véletlen vagy rosszindulatú törlésből való védelmét.  Az elemre vonatkozó bármilyen egyéb művelet végrehajtása előtt várjon 14 napot.  A helyreállított elemek nem lesznek felszámítva.  Ha az új tárolóban 14 napon belül szeretné helyreállítani a törlésre kijelölt elemeket, akkor forduljon a Microsoft ügyfélszolgálatához.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Végrehajthatók a helyreállítható törlési műveletek a PowerShellben vagy a parancssori felületen?

A helyreállítható törlési műveletek a PowerShell használatával végezhetők el. A CLI jelenleg nem támogatott.

## <a name="next-steps"></a>Következő lépések

- [A Azure Backup biztonsági funkcióinak áttekintése](security-overview.md)
