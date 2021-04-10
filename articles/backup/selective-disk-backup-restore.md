---
title: Szelektív lemezes biztonsági mentés és visszaállítás Azure-beli virtuális gépekhez
description: Ebben a cikkben megismerheti a szelektív lemezek biztonsági mentését és visszaállítását az Azure-beli virtuális gépek biztonsági mentési megoldásának használatával.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: e82c959dc63222e8565243cc9ac805283cab6617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501829"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Szelektív lemezes biztonsági mentés és visszaállítás Azure-beli virtuális gépekhez

Azure Backup támogatja a virtuális gép összes lemezének (operációs rendszerének és adattípusának) biztonsági mentését egy virtuális gépen, a virtuális gépek biztonsági mentési megoldásával együtt. Most, hogy a szelektív lemezek biztonsági mentési és visszaállítási funkcióját használja, biztonsági mentést készíthet a virtuális gépek adatlemezei egy részhalmazáról. Ez hatékony és költséghatékony megoldást kínál a biztonsági mentési és visszaállítási igények kielégítésére. Az egyes helyreállítási pontok csak a biztonsági mentési művelet részét képező lemezeket tartalmazzák. Ez lehetővé teszi, hogy a visszaállítási művelet során a megadott helyreállítási pontról visszaállított lemezek egy részhalmaza legyen. Ez a pillanatképek és a tároló visszaállítására egyaránt vonatkozik.

## <a name="scenarios"></a>Forgatókönyvek

Ez a megoldás különösen a következő helyzetekben hasznos:

1. Ha a kritikus adatokról biztonsági másolatot szeretne készíteni csak egy lemezen vagy a lemezek egy részhalmazán, és nem kíván biztonsági másolatot készíteni a virtuális géphez csatolt többi lemezről, hogy az a biztonsági másolatok tárolási költségeit csökkentse.
2. Ha más biztonsági mentési megoldásokkal rendelkezik a virtuális gép vagy az adatai egy részénél. Ha például biztonsági mentést készít az adatbázisokról vagy az adatokról egy másik munkaterhelés-biztonsági mentési megoldással, és az Azure-beli virtuális gép biztonsági mentését szeretné használni a többi adathoz vagy lemezhez, és így hatékony és robusztus rendszerre van szüksége az elérhető legjobb funkciók használatával.

A PowerShell vagy az Azure CLI használatával az Azure-beli virtuális gép szelektív lemezes biztonsági mentését is konfigurálhatja.  Egy parancsfájl használatával adatlemezeket is hozzáadhat vagy kizárhat a LUN-számuk alapján.  Jelenleg a szelektív lemezek biztonsági mentésének lehetősége a Azure Portalon keresztül a **csak a biztonsági mentés operációsrendszer-lemezére** lehetőségre korlátozódik. Így konfigurálhatja az Azure-beli virtuális gép biztonsági mentését az operációsrendszer-lemezzel, és kihagyhatja az ahhoz csatolt összes adatlemezt.

>[!NOTE]
> Alapértelmezés szerint az operációsrendszer-lemez hozzá van adva a virtuális gép biztonsági mentéséhez, és nem zárható ki.

## <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Győződjön meg arról, hogy az az CLI Version 2.0.80 vagy újabb verzióját használja. A CLI verziója a következő paranccsal kérhető le:

```azurecli
az --version
```

Jelentkezzen be az előfizetés-AZONOSÍTÓba, ahol a Recovery Services-tároló és a virtuális gép létezik:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>Az alábbi parancsokban csak a tárolóhoz tartozó **resourcegroup** neve (nem a objektum) szükséges.

### <a name="configure-backup-with-azure-cli"></a>Biztonsági mentés konfigurálása az Azure CLI-vel

A védelem konfigurálása művelet során meg kell adnia a lemez lista beállítást egy **belefoglalási**  /  **kizárási** paraméterrel, amely megadja, hogy a rendszer a biztonsági mentésbe felvenni vagy kizárni kívánt lemezek LUN-számát adja meg.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Ha a virtuális gép nem ugyanabban az erőforráscsoporthoz van, mint a tároló, akkor a **ResourceGroup** arra az erőforráscsoporthoz utal, amelyben a tárolót létrehozták. A virtuális gép neve helyett adja meg a virtuális gép AZONOSÍTÓját az alább jelzett módon.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>A már biztonsági másolattal rendelkező virtuális gépek védelmének módosítása az Azure CLI-vel

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Csak az operációsrendszer-lemez biztonsági mentése az Azure CLI-vel

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Csak az operációsrendszer-lemez biztonsági mentése az Azure parancssori felületének módosításakor

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Lemezek visszaállítása az Azure CLI-vel

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Csak az operációsrendszer-lemez visszaállítása az Azure CLI-vel

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Védett elemek beolvasása a lemezek kizárási részleteinek beolvasása az Azure CLI-vel

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

A védett elemmel kapcsolatban további **diskExclusionProperties** paraméter látható az alábbiak szerint:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Biztonsági mentési feladatok beolvasása az Azure CLI-vel

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Ezzel a paranccsal a következő ábrán látható módon kérheti le a mentett lemezek és kizárt lemezek részleteit:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Helyreállítási pontok listázása az Azure CLI-vel

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Ez megadja a virtuális gépen csatolt és biztonsági mentés alatt álló lemezek számát.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Helyreállítási pont beolvasása az Azure CLI-vel

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Mindegyik helyreállítási pont a tartalmazott és kizárt lemezek információit tartalmazza:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Lemezek kizárási beállításainak eltávolítása és védett elemek beolvasása az Azure CLI-vel

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

A parancsok végrehajtásakor látni fogja a következőt: `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>A PowerShell használata

Győződjön meg arról, hogy Azure PowerShell 3.7.0 vagy újabb verziót használ.

A védelem konfigurálása művelet során meg kell adnia a lemez lista beállítást egy befoglalási/kizárási paraméterrel, amely megadja a biztonsági mentésbe bevonni vagy kizárni kívánt lemezek LUN-számát.

### <a name="enable-backup-with-powershell"></a>Biztonsági mentés engedélyezése a PowerShell-lel

Például:

```azurepowershell
$disks = ("0","1")
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "rg-p-recovery_vaults" -Name "rsv-p-servers"
Get-AzRecoveryServicesBackupProtectionPolicy
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "P-Servers"
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList $disks -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList $disks -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Csak az operációsrendszer-lemez biztonsági mentése a Backup konfigurálása a PowerShell-lel

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>A védelem módosítása a PowerShell-lel

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $targetVault.ID -FriendlyName "V2VM"
```

A fenti beszerzett **$Item** objektumot a következő parancsmagok **– Item** paraméteréhez kell átadnia.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Meglévő virtuális gépek védelmének módosítása a PowerShell-lel

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Csak az operációsrendszer-lemez biztonsági mentése a védelem módosítása a PowerShell használatával

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Lemezterület-kizárási beállítás visszaállítása a PowerShell-lel

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Szelektív lemezek visszaállítása a PowerShell-lel

```azurepowershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $item -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [$disks]
```

### <a name="restore-only-os-disk-with-powershell"></a>Csak az operációsrendszer-lemez visszaállítása a PowerShell-lel

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

A Azure Portal használatával megtekintheti a befoglalt és kizárt lemezeket a virtuális gép biztonsági mentése részletei panelen és a biztonsági mentési feladatok ablaktáblán.  A visszaállítás során, amikor kiválasztja a helyreállítási pontot a visszaállításhoz, a helyreállítási pontban megtekintheti a biztonsági másolatban szereplő lemezeket.

Itt megtekintheti a virtuális gépek befoglalt és kizárt lemezeit a portálon a virtuális gép biztonsági mentése részletei panelen:

![A befoglalt és kizárt lemezek megtekintése a biztonsági másolat részletei panelen](./media/selective-disk-backup-restore/backup-details.png)

Itt megtekintheti a tartalmazott és kizárt lemezeket egy biztonsági másolatból a feladatok részletei panelen:

![Befoglalt és kizárt lemezek megtekintése a feladatok részletei panelről](./media/selective-disk-backup-restore/job-details.png)

Itt megtekintheti a biztonsági másolatban szereplő lemezeket a visszaállítás során, amikor kiválasztja a visszaállítani kívánt helyreállítási pontot:

![Mentett lemezek megtekintése a visszaállítás során](./media/selective-disk-backup-restore/during-restore.png)

A virtuális gépek szelektív lemezes biztonsági mentési élményének konfigurálása a Azure Portalon keresztül **csak a biztonsági mentés operációsrendszer-lemezre** lehetőségre korlátozódik. Ha a szelektív lemezek biztonsági mentését már egy biztonsági másolattal rendelkező virtuális gépen szeretné használni, vagy egy virtuális gép adott adatlemezének speciális felvételére vagy kizárására van lehetősége, használja a PowerShellt vagy az Azure CLI-t.

>[!NOTE]
>Ha az adatlemezek átnyúlnak, győződjön meg arról, hogy az összes függő lemez szerepel a biztonsági mentésben. Ha nem készít biztonsági másolatot az összes függő lemezről egy köteten, akkor a visszaállítás során a rendszer nem biztonsági másolattal rendelkező lemezeket hoz létre.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Az operációsrendszer-lemez biztonsági mentése csak a Azure Portal

Ha Azure Portal használatával engedélyezi a biztonsági mentést, kiválaszthatja az **operációs rendszer lemezének biztonsági mentése** lehetőséget. Így konfigurálhatja az Azure-beli virtuális gép biztonsági mentését az operációsrendszer-lemezzel, és kizárhatja a hozzá csatolt összes adatlemezt.

![Csak az operációsrendszer-lemez biztonsági mentésének konfigurálása](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Az Azure REST API használata

Az Azure-beli virtuális gépek biztonsági mentését néhány kiválasztott lemezzel is konfigurálhatja, vagy módosíthatja a meglévő virtuális gépek védelmét úgy, hogy az [itt](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)leírtak szerint belefoglalja vagy kizárja a néhány lemezt.

## <a name="selective-disk-restore"></a>Szelektív lemez visszaállítása

A szelektív lemez-visszaállítás a szelektív lemezek biztonsági mentési funkciójának engedélyezésekor hozzáadott funkciók. Ezzel a funkcióval visszaállíthatja a szelektív lemezeket az összes olyan lemezről, amelyekről biztonsági mentés készül egy helyreállítási pontban. Ez hatékonyabb, és segít időt takaríthat meg olyan helyzetekben, amikor tudja, hogy mely lemezeket kell visszaállítani.

- Alapértelmezés szerint az operációsrendszer-lemezt a virtuális gép biztonsági mentése és visszaállítása tartalmazza, és nem zárható ki.
- A szelektív lemezek visszaállítása csak a lemez kizárási képességének engedélyezése után létrehozott helyreállítási pontok esetében támogatott.
- A lemezek kizárása beállítással végzett biztonsági mentések **csak a** **lemez-visszaállítási** lehetőséget támogatják. Ebben az esetben a **virtuális gép visszaállítása** vagy a meglévő visszaállítási beállítások **cseréje** nem támogatott.

![A virtuális gép visszaállításának lehetősége és a meglévő cseréje nem érhető el a visszaállítási művelet során.](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Korlátozások

A szelektív lemezek biztonsági mentési funkciója nem támogatott a klasszikus virtuális gépek és a titkosított virtuális gépek esetében. Így az Azure-beli virtuális gépek, amelyeket Azure Disk Encryption (ADE) titkosítanak a BitLocker használatával a Windows rendszerű virtuális gép titkosításához, és a Linux rendszerű virtuális gépekhez készült dm-crypt funkció nem támogatott.

Az **új virtuális gép létrehozásához** és a **meglévő cseréjéhez** szükséges visszaállítási beállítások nem támogatottak a virtuális gép számára, amelyhez engedélyezve van a szelektív lemezek biztonsági mentése funkció.

Az Azure-beli virtuális gép biztonsági mentése jelenleg nem támogatja az olyan virtuális gépeket, amelyek a csatlakoztatott lemezekkel vagy megosztott lemezzel rendelkeznek. A szelektív lemezes biztonsági mentés nem használható olyan esetekben, amelyek kizárják a lemezt, és biztonsági másolatot készítenek a virtuális gépről.

## <a name="billing"></a>Számlázás

Az Azure-beli virtuális gépek biztonsági mentése a meglévő díjszabási modellt követi, amely részletesen ismerteti [itt](https://azure.microsoft.com/pricing/details/backup/).

A **védett példány (PI)** díját csak akkor számítjuk ki az operációsrendszer-lemezre, ha úgy dönt, hogy **csak az operációs rendszer lemezének** használatával készít biztonsági mentést.  Ha a biztonsági mentést konfigurálja, és legalább egy adatlemezt választ ki, a PI-költségeket a rendszer a virtuális géphez csatolt összes lemez esetében kiszámítja. A **biztonsági mentési tárolási költségeket** a rendszer csak a tartalmazott lemezek alapján számítja ki, így a tárolási költségeket is megtakaríthatja. A rendszer mindig kiszámítja a **Pillanatképek árát** a virtuális gép összes lemezéhez (a tartalmazott és kizárt lemezekkel együtt).

Ha a régiók közötti visszaállítás (CRR) funkciót választotta, akkor a [CRR díjszabása](https://azure.microsoft.com/pricing/details/backup/) a lemez kizárása után a biztonsági mentési tárterületre vonatkozik.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Hogyan számítják ki a Protected instance (PI) díjait a Windows és a Linux operációs rendszer lemezes biztonsági mentéséhez?

A PI-költségeket a virtuális gép tényleges (felhasznált) mérete alapján számítjuk ki.

- Windows esetén: a felhasznált terület kiszámítása az operációs rendszert tároló meghajtón alapul (ez általában a C:).
- Linux esetén: a felhasznált terület kiszámítása azon az eszközön alapul, ahol a gyökér fájlrendszer (/) csatlakoztatva van.

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Csak az operációsrendszer-lemez biztonsági mentését konfiguráltam, miért történik a pillanatkép az összes lemezen?

A szelektív lemezes biztonsági mentési funkciók lehetővé teszik, hogy a biztonsági mentés részét képező befoglalt lemezeket megerősítve mentse a biztonságimásolat-tároló tárolási költségeit. A rendszer azonban a pillanatképet a virtuális géphez csatolt összes lemez esetében elvégzi. Így a rendszer mindig kiszámítja a pillanatkép költségeit a virtuális gép összes lemeze számára (a tartalmazott és kizárt lemezekkel együtt). További információ: [számlázás](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Nem tudom konfigurálni az Azure-beli virtuális gép biztonsági mentését a virtuális géphez csatlakoztatott Ultra Disk vagy Shared Disks kizárásával

A szelektív lemezes biztonsági mentési funkció az Azure-beli virtuális gépek biztonsági mentési megoldásán felüli képesség. Az Azure-beli virtuális gép biztonsági mentése jelenleg nem támogatja az olyan virtuális gépeket, amelyekhez a lemez csatlakoztatva van.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa](backup-support-matrix-iaas.md)
- [Gyakori kérdések – Azure-beli virtuális gépek biztonsági mentése](backup-azure-vm-backup-faq.yml)
