---
title: A Linux-Azure Disk Encryption titkosítási állapotának ellenőrzése
description: Ez a cikk útmutatást nyújt a titkosítási állapotnak a platformról és az operációs rendszer szintjeiről való ellenőrzéséhez.
author: kailashmsft
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 04654672cc5806465ec9f75b695772dcb2037eab
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564135"
---
# <a name="verify-encryption-status-for-linux"></a>A Linux titkosítási állapotának ellenőrzése 

Ennek a cikknek a hatóköre egy virtuális gép titkosítási állapotának ellenőrzése különböző módszerekkel: a Azure Portal, a PowerShell, az Azure CLI vagy a virtuális gép operációs rendszerének használata. 

A titkosítási állapotot a titkosítás során vagy azt követően ellenőrizheti a következők bármelyikével:

- Egy adott virtuális géphez csatlakoztatott lemezek ellenőrzése. 
- A titkosítási beállítások lekérdezése az egyes lemezeken, függetlenül attól, hogy a lemez csatlakoztatva van-e vagy le van-e kapcsolva.

Ez a forgatókönyv Azure Disk Encryption kettős Pass és Single-pass kiterjesztésekre vonatkozik. Ebben a forgatókönyvben a Linux-disztribúciók az egyetlen környezet.

>[!NOTE] 
>A cikk során változókat használunk. Cserélje le az értékeket ennek megfelelően.

## <a name="portal"></a>Portál

A Azure Portal a **bővítmények** szakaszban válassza ki a Azure Disk Encryption bővítményt a listában. Az **állapotjelző üzenet** információi az aktuális titkosítási állapotot jelzik:

![Portál-ellenőrzési állapot, verzió és állapotjelző üzenet kiemelve](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

A bővítmények listájában megjelenik a megfelelő Azure Disk Encryption-bővítmény verziója. A 0. x verzió a Azure Disk Encryption Dual pass értéknek felel meg, az 1. x verzió pedig Azure Disk Encryption Single pass értéknek felel meg.

További részleteket a bővítmény kiválasztásával, majd a **részletes állapot megtekintése** lehetőség kiválasztásával érhet el. A titkosítási folyamat részletes állapota JSON formátumban jelenik meg.

![A portálon való keresés a "részletes állapot megtekintése" hivatkozás kiemelésével](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Részletes állapot JSON formátumban](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

A titkosítási állapot érvényesítésének másik módja a **lemez beállításai** szakasz.

![Az operációsrendszer-lemez és az adatlemezek titkosítási állapota](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Ez az állapot azt jelenti, hogy a lemezeken a titkosítási beállítások vannak lepecsételve, és nem, hogy az operációs rendszer szintjén lettek titkosítva.
>
> A lemezeket a rendszer úgy végzi el, hogy először a lemezeket pecsételi, és később titkosítja. Ha a titkosítási folyamat meghiúsul, előfordulhat, hogy a lemezek lebélyegzettek, de nem titkosítottak. 
>
> Annak megerősítéséhez, hogy a lemezek valóban titkosítva vannak-e, megtekintheti az egyes lemezek titkosítását az operációs rendszer szintjén.

## <a name="powershell"></a>PowerShell

A titkosított virtuális gépek *általános* titkosítási állapotát a következő PowerShell-parancsok használatával ellenőrizheti:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Általános titkosítási állapot a PowerShellben](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Az egyes lemezek titkosítási beállításait a következő PowerShell-parancsokkal rögzítheti.

### <a name="single-pass"></a>Egyszeri továbbítás
Egyetlen menet esetén a titkosítási beállítások az egyes lemezeken (operációs rendszer és az összes) vannak lepecsételve. Az operációs rendszer lemezének titkosítási beállításait a következőképpen rögzítheti egy adott fázisban:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![OPERÁCIÓSRENDSZER-lemez titkosítási beállításai](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Ha a lemez nem rendelkezik lebélyegzett titkosítási beállításokkal, a kimenet üres lesz:

![Üres kimenet](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Az adatlemezek titkosítási beállításainak rögzítéséhez használja a következő parancsokat:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Adatlemezek titkosítási beállításai](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Kettős menet
Kettős menet esetén a titkosítási beállítások a virtuálisgép-modellbe kerülnek, nem mindegyik külön lemezen.

A következő parancsokkal ellenőrizheti, hogy a titkosítási beállítások egy kettős menetben vannak-e lepecsételve:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Titkosítási beállítások kettős menetben](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Nem csatlakoztatott lemezek

A virtuális géphez nem csatolt lemezek titkosítási beállításainak ellenőrzéséhez.

### <a name="managed-disks"></a>Felügyelt lemezek
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

A titkosított virtuális gépek *általános* titkosítási állapotát a következő Azure CLI-parancsok használatával ellenőrizheti:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Általános titkosítási állapot az Azure CLI-ből ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Egyszeri továbbítás
Az egyes lemezek titkosítási beállításait az alábbi Azure CLI-parancsokkal ellenőrizheti:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Adattitkosítási beállítások](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Ha a lemez nem rendelkezik lebélyegzett titkosítási beállításokkal, akkor a szöveges **lemez nem titkosítva** jelenik meg.

Használja az alábbi parancsokat a részletes állapot-és titkosítási beállítások megszerzéséhez.

OPERÁCIÓSRENDSZER-lemez:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Az operációsrendszer-lemez részletes állapot-és titkosítási beállításai](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Adatlemezek:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Az adatlemezek részletes állapot-és titkosítási beállításai](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Kettős menet

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Általános titkosítási beállítások kettős továbbításhoz az Azure CLI-n keresztül](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Az operációsrendszer-lemez virtuálisgép-modell tárolási profiljának titkosítási beállításait is megtekintheti:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![VM-profil kettős továbbításhoz az Azure CLI-n keresztül](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Nem csatlakoztatott lemezek

A virtuális géphez nem csatolt lemezek titkosítási beállításainak ellenőrzéséhez.

### <a name="managed-disks"></a>Felügyelt lemezek

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

A nem felügyelt lemezek az Azure Storage-fiókokban blobként tárolt VHD-fájlok.

Egy adott lemez adatainak beszerzéséhez a következőket kell megadnia:

- A lemezt tartalmazó Storage-fiók azonosítója.
- Az adott Storage-fiókhoz tartozó kapcsolatok karakterlánca.
- A lemezt tároló tároló neve.
- A lemez neve.

Ez a parancs felsorolja az összes Storage-fiók azonosítóját:

```azurecli
az storage account list --query [].[id] -o tsv
```
A Storage-fiók azonosítói a következő formában vannak felsorolva:

/Subscriptions/ \<subscription id> /ResourceGroups/ \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

Válassza ki a megfelelő azonosítót, és tárolja egy változóban:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Ez a parancs beolvassa egy adott Storage-fiókhoz tartozó kapcsolatok karakterláncát, és egy változóban tárolja azt:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

A következő parancs a Storage-fiókban lévő összes tárolót listázza:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
A lemezekhez használt tároló általában VHD-k néven szerepel.

Tárolja a tároló nevét egy változóban: 
```bash
ContainerName="name of the container"
```

Ezzel a paranccsal listázhatja egy adott tároló összes blobját:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Válassza ki a lekérdezni kívánt lemezt, és tárolja a nevét egy változóban:
```bash
DiskName="diskname.vhd"
```
A lemez titkosítási beállításainak lekérdezése:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Operációs rendszer
Ellenőrizze, hogy az adatlemez-partíciók titkosítva vannak-e (és az operációsrendszer-lemez nem).

Partíció vagy lemez titkosítása esetén a rendszer a **titkosítási** típusként jeleníti meg. Ha nincs titkosítva, a **kijelző/lemez** típusúként jelenik meg.

```bash
lsblk
```

![Partíció operációs rendszerének Crypt-rétege](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

További részletek a következő **lsblk** -változat használatával szerezhetők be. 

Ekkor megjelenik egy, a bővítmény által csatlakoztatott **Crypt** típusú réteg. Az alábbi példa bemutatja a logikai köteteket és a **titkosítási \_ LUKS FSTYPE** rendelkező normál lemezeket.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Operációs rendszer titkosítási rétege logikai kötetek és normál lemezek esetén](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

További lépésként ellenőrizheti, hogy az adatlemez rendelkezik-e kulcsok betöltésével:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

Továbbá megtekintheti, hogy mely **DM** -eszközök szerepelnek **Crypt**-ként:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)
