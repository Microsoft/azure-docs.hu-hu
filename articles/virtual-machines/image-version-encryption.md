---
title: Előnézet – a saját kulcsaival titkosított rendszerkép-verzió létrehozása
description: Hozzon létre egy rendszerkép-verziót egy megosztott rendszerkép-gyűjteményben az ügyfél által felügyelt titkosítási kulcsok használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 258d8ab6ab23a95d73b8ed0c2549f373cf097674
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554088"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Előzetes verzió: az ügyfél által felügyelt kulcsok használata a rendszerképek titkosításához

A megosztott képtárban lévő rendszerképeket pillanatképként tárolja a rendszer, így azok automatikusan titkosítva lesznek a kiszolgálóoldali titkosítással. A kiszolgálóoldali titkosítás 256 bites [AES-titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ, amely az egyik legerősebb blokk titkosítási algoritmus. A kiszolgálóoldali titkosítás is FIPS 140-2-kompatibilis. Az Azure Managed Disks mögöttes titkosítási modulokkal kapcsolatos további információkért lásd: a [következő generációs kriptográfiai API](/windows/desktop/seccng/cng-portal)-k.

Használhatja a platform által felügyelt kulcsokat a lemezképek titkosításához, vagy használhatja a saját kulcsait. A kettős titkosításhoz mindkettőt használhatja együtt is. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a lemezképek összes lemezének titkosítására és visszafejtésére használhat. 

Az ügyfél által felügyelt kulcsok használatával történő kiszolgálóoldali titkosítás Azure Key Vault. Importálhatja [az RSA-kulcsokat](../key-vault/keys/hsm-protected-keys.md) a kulcstartóba, vagy LÉTREHOZHAT új RSA-kulcsokat a Azure Key Vault.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk megköveteli, hogy az egyes régiókban, amelyeken replikálni szeretné a lemezképet, már rendelkezik egy lemezes titkosítási készlettel:

- Ha csak az ügyfél által felügyelt kulcsot szeretné használni, tekintse meg az ügyfél által felügyelt kulcsok kiszolgálóoldali titkosítással való engedélyezését ismertető cikket a [Azure Portal](./disks-enable-customer-managed-keys-portal.md) vagy a [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation)használatával.

- Ha a platform által felügyelt és az ügyfél által felügyelt kulcsokat is szeretné használni (kettős titkosításhoz), tekintse meg a kettős titkosítás engedélyezése a [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) vagy a [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md)használatával című cikket.

   > [!IMPORTANT]
   > A Azure Portal eléréséhez a hivatkozást kell használnia [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) . A kettős titkosítás jelenleg nem látható a nyilvános Azure Portalban, hacsak nem használja ezt a hivatkozást.

## <a name="limitations"></a>Korlátozások

Ha ügyfél által felügyelt kulcsokat használ a lemezképek megosztott képtárban való titkosításához, a következő korlátozások érvényesek:   

- A titkosítási kulcsokhoz tartozó készleteknek a rendszerképével megegyező előfizetésben kell lenniük.

- A titkosítási kulcsok helyi erőforrások, így minden régióhoz egy másik titkosítási kulcs van beállítva.

- Az ügyfél által felügyelt kulcsokat használó rendszerképek nem másolhatók és nem oszthatók meg. 

- Miután használta a saját kulcsait egy lemez vagy kép titkosításához, nem mehet vissza a platform által felügyelt kulcsok használatára a lemezek és a lemezképek titkosításához.


> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok titkosítása jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

A nyilvános előzetes verzióhoz először regisztrálnia kell a szolgáltatást:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

A regisztráció befejezéséhez néhány percet is igénybe vehet. A `Get-AzProviderFeature` szolgáltatás regisztrációjának állapotát a következő paranccsal ellenőrizhető:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Ha `RegistrationState` visszatér `Registered` , a következő lépésre léphet.

Győződjön meg arról, hogy a szolgáltató regisztrálva van. Győződjön meg róla, hogy visszaadja `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Ha nem ad vissza értéket `Registered` , a következő kód használatával regisztrálja a szolgáltatókat:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Lemezkép-verzióhoz tartozó lemezes titkosítási csoport megadásához használja a  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) `-TargetRegion` paramétert a következő paraméterrel: 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Létrehozhat egy virtuális gépet (VM) egy megosztott rendszerkép-gyűjteményből, és az ügyfél által felügyelt kulcsokkal titkosíthatja a lemezeket. A szintaxis megegyezik egy [általánosított](vm-generalized-image-version-powershell.md) vagy [speciális](vm-specialized-image-version-powershell.md) virtuális gép rendszerképből való létrehozásával. Használja a kiterjesztett paramétert, és adja hozzá `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` a virtuális gép konfigurációját.

Adatlemezek esetén adja hozzá a `-DiskEncryptionSetId $setID` paramétert az [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)használatakor.


## <a name="cli"></a>parancssori felület 

A nyilvános előzetes verzióhoz először regisztrálnia kell a szolgáltatást. A regisztráció körülbelül 30 percet vesz igénybe.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

A szolgáltatás regisztrálási állapotának ellenõrzése:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Ha ezt a kódot adja vissza `"state": "Registered"` , a következő lépésre léphet.

A regisztráció megkeresése:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Ha a regisztráció nem jelent meg, futtassa a következő parancsot:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


A lemezkép verziójának lemezes titkosítási készletének megadásához használja az [az rendszerkép-katalógus Create-lemezkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create) `--target-region-encryption` paramétert. A formátuma az `--target-region-encryption` operációs rendszer és az adatlemezek titkosítására szolgáló kulcsok vesszővel tagolt listája. A következőhöz hasonlóan kell kinéznie: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Ha az operációsrendszer-lemez forrása felügyelt lemez vagy virtuális gép, a használatával `--managed-image` adhatja meg a lemezkép verziójának forrását. Ebben a példában a forrás egy olyan felügyelt lemezkép, amely operációsrendszer-lemezzel és egy adatlemezzel rendelkezik a 0. logikai egységben. Az operációsrendszer-lemez titkosítva lesz a DiskEncryptionSet1, és az adatlemez titkosítva lesz a DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Ha az operációsrendszer-lemez forrása pillanatkép, `--os-snapshot` az operációsrendszer-lemez megadásához használja a következőt:. Ha vannak olyan adatlemez-Pillanatképek, amelyeknek a lemezkép verziója is részét kell képeznie, adja hozzá ezeket. A használatával `--data-snapshot-luns` adhatja meg a LUN-t, és `--data-snapshots` a használatával adhatja meg a pillanatképeket.

Ebben a példában a források lemezes Pillanatképek. Van egy operációsrendszer-lemez és egy adatlemez a LUN 0-on. Az operációsrendszer-lemez titkosítva lesz a DiskEncryptionSet1, és az adatlemez titkosítva lesz a DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>A virtuális gép létrehozása

Létrehozhat egy virtuális gépet egy megosztott rendszerkép-gyűjteményből, és az ügyfél által felügyelt kulcsokkal titkosíthatja a lemezeket. A szintaxis megegyezik egy [általánosított](vm-generalized-image-version-cli.md) vagy [speciális](vm-specialized-image-version-cli.md) virtuális gép rendszerképből való létrehozásával. Csak adja hozzá a `--os-disk-encryption-set` paramétert a titkosítási készlet azonosítójával. Adatlemezek esetén adja hozzá az `--data-disk-encryption-sets` adatlemezek lemezes titkosítási csoportjainak szóközzel tagolt listáját.


## <a name="portal"></a>Portál

Amikor létrehozza a rendszerkép verzióját a portálon, a **titkosítás** lapon alkalmazhatja a tároló titkosítási készleteit.

> [!IMPORTANT]
> A kettős titkosítás használatához a hivatkozást kell használnia [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) a Azure Portal eléréséhez. A kettős titkosítás jelenleg nem látható a nyilvános Azure Portalban, hacsak nem használja ezt a hivatkozást.


1. A **rendszerkép verziójának létrehozása** lapon válassza a **titkosítás** lapot.
2. A **titkosítás típusa** beállításnál válassza ki a **REST-alapú titkosítást egy ügyfél által felügyelt kulccsal** , vagy a **platform által felügyelt és az ügyfél által felügyelt kulcsokkal rendelkező dupla titkosítással**. 
3. A rendszerkép mindegyik lemezén válasszon ki egy titkosítási készletet a **lemez titkosítási készlete** legördülő listából. 

### <a name="create-the-vm"></a>A virtuális gép létrehozása

Létrehozhat egy virtuális gépet a rendszerkép verziójának használatával, és az ügyfél által felügyelt kulcsokkal titkosíthatja a lemezeket. Amikor létrehozza a virtuális gépet a portálon, a **lemezek** lapon válassza a **titkosítás az ügyfelek által felügyelt kulcsokkal** vagy a **kettős titkosítás lehetőséget a platform által felügyelt és az ügyfél által felügyelt kulcsokkal** a **titkosítási típushoz**. Ezután kiválaszthatja a titkosítási készletet a legördülő listából.

## <a name="next-steps"></a>Következő lépések

További információ a [kiszolgálóoldali lemezek titkosításáról](./disk-encryption.md).

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).