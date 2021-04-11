---
title: Azure-Managed Disks biztonsági mentése Azure PowerShell használatával
description: Ismerje meg, hogyan készíthet biztonsági mentést az Azure Managed Disksról Azure PowerShell használatával.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630401"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Azure-Managed Disks biztonsági mentése Azure PowerShell használatával

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni az [Azure felügyelt lemezről](../virtual-machines/managed-disks-overview.md) a Azure PowerShell használatával.

Ebből a cikkből megtudhatja, hogyan:

- Backup-tároló létrehozása

- Biztonsági mentési szabályzat létrehozása

- Azure-lemez biztonsági mentésének konfigurálása

- Igény szerinti biztonsági mentési feladatok futtatása

Az Azure Disk Backup-régiók rendelkezésre állásával, a támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos információkért tekintse meg a [támogatási mátrixot](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Backup-tároló létrehozása

A Backup-tároló egy Azure-beli tárolási entitás, amely a Azure Backup által támogatott különböző újabb számítási feladatok, például Azure Database for PostgreSQL-kiszolgálók és Azure-lemezek biztonsági mentési adataival rendelkezik. A Backup-tárolók megkönnyítik a biztonsági másolatok rendszerezését, miközben minimalizálják a felügyeleti terhelést. A Backup-tárolók az Azure Azure Resource Manager modelljén alapulnak, amely továbbfejlesztett funkciókat biztosít a biztonsági mentési adatmennyiség biztosításához.

A mentési tár létrehozása előtt válassza ki a tárolóban lévő adattárolási redundanciát. Ezután folytassa a mentési tároló létrehozásával a tárolási redundanciával és a helyével. Ebben a cikkben egy "TestBkpVault" nevű Backup-tárolót hozunk létre a "westus" régióban a "testBkpVaultRG" erőforráscsoport alatt. A [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) parancs használatával hozzon létre egy backup-tárolót. További információ a [Backup-tároló létrehozásáról](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

A tár létrehozása után hozzon létre egy biztonsági mentési szabályzatot az Azure-lemezek védelme érdekében.

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

Az Azure Disk Backup biztonsági mentési szabályzatának belső összetevőinek megismeréséhez a [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true)paranccsal kérje le a szabályzat sablonját. Ez a parancs egy alapértelmezett házirend-sablont ad vissza egy adott adatforrás-típushoz. Új szabályzat létrehozásához használja ezt a házirend-sablont.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

A házirend-sablon egy eseményindítóból áll (amely eldönti, hogy mi indítja el a biztonsági mentést) és egy életciklust (amely meghatározza, hogy mikor kell törölni/másolni/áthelyezni a biztonsági másolatot). Az Azure Disk Backup szolgáltatásban az aktiválás alapértelmezett értéke egy ütemezett óránkénti aktiválás minden 4 órában (PT4H), valamint az egyes biztonsági másolatok 7 napig való megőrzése.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Az Azure Disk Backup naponta több biztonsági mentést is kínál. Ha több gyakori biztonsági mentésre van szüksége, válassza az **óránkénti** biztonsági mentés gyakoriságát, hogy a biztonsági másolatokat 4, 6, 8 vagy 12 óra intervallummal végezze. A biztonsági mentéseket a kiválasztott **időintervallum alapján ütemezi a rendszer** . Ha például **4 óránként** választja ki, akkor a biztonsági másolatok körülbelül 4 óránként lesznek elérhetők, így a biztonsági másolatok egyenlően oszlanak meg a nap folyamán. Ha a napi biztonsági mentés elegendő, válassza ki a **napi** biztonsági mentés gyakoriságát. A napi biztonsági mentési gyakoriságban megadhatja a biztonsági másolatok készítésének napját. Fontos megjegyezni, hogy a nap időpontja a biztonsági mentés kezdési idejét jelzi, nem pedig a biztonsági mentés befejeződésének időpontját. A biztonsági mentési művelet befejezéséhez szükséges idő számos tényezőtől függ, többek között a lemez méretétől és az egymást követő biztonsági mentések közötti adatforgalomtól. Az Azure Disk Backup azonban olyan ügynök nélküli biztonsági másolat, amely [növekményes pillanatképeket](../virtual-machines/disks-incremental-snapshots.md)használ, ami nem befolyásolja az éles alkalmazások teljesítményét.

   >[!NOTE]
   > Bár a kiválasztott tár a globális redundancia beállítással rendelkezhet, jelenleg az Azure Disk Backup csak a pillanatkép-adattár használatát támogatja. Az összes biztonsági mentést egy erőforráscsoport tárolja az előfizetésben, és a rendszer nem másolja a Backup tárolóba.

A szabályzatok létrehozásával kapcsolatos további részletekért tekintse meg az [Azure Disk Backup-szabályzat](backup-managed-disks.md#create-backup-policy) dokumentumát.

Ha szerkeszteni szeretné az óradíjat vagy a megőrzési időtartamot, használja az [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) és/vagy a [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) parancsokat. Ha a házirend-objektum minden kívánt értékkel rendelkezik, folytassa az új szabályzat létrehozását a házirend-objektumból a [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true)használatával.

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

A tár és a szabályzat létrehozása után 3 kritikus ponttal kell rendelkeznie ahhoz, hogy a felhasználónak meg kell fontolnia az Azure-lemezek biztosítását.

### <a name="key-entities-involved"></a>Érintett kulcsfontosságú entitások

#### <a name="disk-to-be-protected"></a>Védendő lemez

A védeni kívánt lemez ARM-AZONOSÍTÓjának beolvasása. Ez a lemez azonosítóját fogja szolgálni. Egy másik előfizetéshez tartozó "diskrg" erőforráscsoport alatt egy "PSTestDisk" nevű lemezt fogunk használni.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Pillanatkép-erőforráscsoport

A lemez-pillanatképeket az előfizetésében található erőforráscsoport tárolja. Útmutatóként javasoljuk, hogy hozzon létre egy dedikált erőforráscsoportot a Azure Backup szolgáltatás által használandó pillanatkép-adattárként. A dedikált erőforráscsoport lehetővé teszi a hozzáférési engedélyek korlátozását az erőforráscsoport számára, biztonságot nyújtva és megkönnyíti a biztonsági mentési adat kezelését. Jegyezze fel annak az erőforrásnak az ARM-AZONOSÍTÓját, amelyen a lemez-pillanatképeket el szeretné helyezni

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Biztonsági mentési tár

A Backup-tárolók a lemezre és a pillanatkép-erőforráscsoporthoz vonatkozó engedélyeket igényelnek a pillanatképek elindításához és életciklusuk kezeléséhez. A tároló rendszerhez rendelt felügyelt identitása az ilyen engedélyek hozzárendelésére szolgál. Az [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) parancs használatával engedélyezheti a rendszerhez rendelt felügyelt identitást a Recovery Services-tárolóhoz.

### <a name="assign-permissions"></a>Engedélyek hozzárendelése

A felhasználónak néhány engedélyt kell kiosztania a RBAC-on keresztül a tár (a tár MSI által jelölt) és a megfelelő lemez és/vagy RG alapján. Ezek a portálon vagy a PowerShellen keresztül végezhetők el. Az összes kapcsolódó engedély részletesen szerepel ebben a [szakaszban](backup-managed-disks.md#configure-backup)az 1., 2. és 3. pontban.

### <a name="prepare-the-request"></a>A kérelem előkészítése

Ha az összes vonatkozó engedély be van állítva, a biztonsági mentés konfigurálása két lépésben történik. Először készítse elő a vonatkozó kérést a megfelelő tár, házirend, lemez és pillanatkép-erőforráscsoport használatával az [inicializálás-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) paranccsal. Ezt követően a [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) parancs használatával beküldjük a lemez védelemmel való ellátására vonatkozó kérést.

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

A megfelelő biztonsági mentési példány beolvasása, amelyen a felhasználó a [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) használatával szeretné elindítani a biztonsági mentést

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Megadhat egy megőrzési szabályt a biztonsági mentés elindítása közben. A házirend megőrzési szabályainak megtekintéséhez navigáljon az adatmegőrzési szabályokhoz tartozó házirend-objektumon. Az alábbi példában a "default" nevű szabály jelenik meg, és ezt a szabályt fogjuk használni az igény szerinti biztonsági mentéshez.

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Igény szerinti biztonsági mentés indítása a [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) parancs használatával.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Feladatok nyomon követése

Az összes feladat követése a [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) parancs használatával. Listázhatja az összes feladatot, és beolvashatja az adott feladat részleteit.

Az az. ResourceGraph használatával is nyomon követheti az összes feladatot az összes Backup-tárolóban. A [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) paranccsal kérheti le a megfelelő feladatot, amely bármely Backup-tárolóban felhasználható.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Következő lépések

- [Azure-Managed Disks visszaállítása Azure PowerShell használatával](restore-managed-disks-ps.md)
