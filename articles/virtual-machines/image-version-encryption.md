---
title: Előzetes verzió – Saját kulcsokkal titkosított rendszerképverzió létrehozása
description: Hozzon létre egy rendszerképverziót egy megosztott rendszerkép-katalógusban az ügyfél által felügyelt titkosítási kulcsok használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 601b8236ca413dd510585bdfffddc3e892caa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759668"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Előzetes verzió: Felhasználó által kezelt kulcsok használata képek titkosításához

A megosztott rendszerkép-katalógusban található rendszerképek pillanatképekként vannak tárolva, így a rendszer automatikusan kiszolgálóoldali titkosítással titkosítja őket. A kiszolgálóoldali titkosítás 256 bites [AES-titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ, amely az egyik legerősebb elérhető blokktitkosítás. A kiszolgálóoldali titkosítás a FIPS 140-2 szabványnak is megfelel. További információ az Azure felügyelt lemezei mögötti titkosítási modulokról: [Titkosítási API: Következő generáció.](/windows/desktop/seccng/cng-portal)

A rendszerképek titkosítása platform által felügyelt kulcsokat is használhat, vagy használhatja a saját kulcsait. Mindkettőt együtt is használhatja a dupla titkosításhoz. Ha úgy dönt, hogy a titkosítást a saját kulcsokkal kezeli, megadhat egy felhasználó által kezelt kulcsot, amely *a* lemezképek összes lemezének titkosításához és visszafejtéséhez használható. 

Az ügyfél által kezelt kulcsokkal való kiszolgálóoldali titkosítás Azure Key Vault. Importálhatja [RSA-kulcsait](../key-vault/keys/hsm-protected-keys.md) a kulcstartóba, vagy új RSA-kulcsokat hozhat létre a Azure Key Vault.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk megköveteli, hogy már legyen egy lemeztitkosítási készlet minden olyan régióban, ahol replikálni szeretné a rendszerképet:

- Ha csak ügyfél által felügyelt kulcsot használ, tekintse meg az ügyfél által kezelt [](./disks-enable-customer-managed-keys-portal.md) kulcsok kiszolgálóoldali titkosítással, az Azure Portal Vagy a PowerShell használatával való engedélyezésével kapcsolatos [cikkeket.](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation)

- A platform által felügyelt és az ügyfél által felügyelt kulcsok (kettős titkosításhoz) a következő cikkekben talál további cikkeket: Dupla titkosítás engedélyezése az Azure Portal [vagy](./disks-enable-double-encryption-at-rest-portal.md) [a PowerShell használatával.](./windows/disks-enable-double-encryption-at-rest-powershell.md)

   > [!IMPORTANT]
   > A hivatkozást kell használnia [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) a Azure Portal. Az aktív adatok dupla titkosítása jelenleg nem látható a nyilvános Azure Portal, hacsak nem használja ezt a hivatkozást.

## <a name="limitations"></a>Korlátozások

Ha felhasználó által kezelt kulcsokat használ a képek titkosításához egy megosztott rendszerkép-katalógusban, a következő korlátozások érvényesek:   

- A titkosítási kulcskészletnek és a rendszerképnek ugyanabban az előfizetésben kell lennie.

- A titkosítási kulcskészletek regionális erőforrások, így minden régióhoz más titkosítási kulcskészletre van szükség.

- Az ügyfél által kezelt kulcsokat felhasználó által felügyelt rendszerképeket nem másolhat vagy oszthat meg. 

- Miután a saját kulcsait használta egy lemez vagy rendszerkép titkosításához, nem használhatja a platform által kezelt kulcsokat a lemezek vagy lemezképek titkosításához.


> [!IMPORTANT]
> Az ügyfél által kezelt kulcsokkal való titkosítás jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előzetes verzió nem biztosít szolgáltatásiszint-szerződést, és nem javasoljuk éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

A nyilvános előzetes verzióhoz először regisztrálnia kell a funkciót:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

A regisztráció befejezése eltarthat néhány percig. A `Get-AzProviderFeature` funkcióregisztráció állapotának ellenőrzéshez használja a következőt:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Ha `RegistrationState` a értéket adja `Registered` vissza, továbbléphet a következő lépésre.

Ellenőrizze a szolgáltató regisztrációját. Győződjön meg arról, hogy a értéket adja `Registered` vissza.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Ha nem ad vissza `Registered` kódot, a következő kóddal regisztrálhatja a szolgáltatókat:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Ha lemeztitkosítási készletet ad meg egy rendszerképverzióhoz, használja a  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) `-TargetRegion` paramétert a következő paraméterrel: 

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

Létrehozhat egy virtuális gépet (VM) egy megosztott rendszerkép-katalógusból, és ügyfél által felügyelt kulcsokkal titkosíthatja a lemezeket. A szintaxis megegyezik egy rendszerképből egy [általánosított vagy](vm-generalized-image-version-powershell.md) [specializált](vm-specialized-image-version-powershell.md) virtuális gép létrehozásával. Használja a kiterjesztett paraméterkészletet, és adja `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` hozzá a virtuálisgép-konfigurációhoz.

Adatlemezek esetén adja hozzá a `-DiskEncryptionSetId $setID` paramétert az [Add-AzVMDataDisk parancs használata esetén.](/powershell/module/az.compute/add-azvmdatadisk)


## <a name="cli"></a>parancssori felület 

A nyilvános előzetes verzióhoz először regisztrálnia kell a funkcióra. A regisztráció körülbelül 30 percet vesz igénybe.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Ellenőrizze a szolgáltatásregisztráció állapotát:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Amikor a kód visszaadja `"state": "Registered"` a értéket, továbbléphet a következő lépésre.

Ellenőrizze a regisztrációt:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Ha nincs regisztrálva, futtassa a következő parancsot:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Ha lemeztitkosítási készletet ad meg egy rendszerképverzióhoz, használja [az az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) `--target-region-encryption` paramétert. A formátuma az operációs rendszer és az adatlemezek titkosításához szükséges kulcsok vesszővel `--target-region-encryption` elválasztott listája. Ennek így kell kinéznie: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Ha az operációsrendszer-lemez forrása felügyelt lemez vagy virtuális gép, a használatával adhatja meg a rendszerkép `--managed-image` verziójának forrását. Ebben a példában a forrás egy felügyelt rendszerkép, amely egy operációsrendszer-lemezzel és egy 0 LUN-os adatlemezzel rendelkezik. Az operációsrendszer-lemez DiskEncryptionSet1, az adatlemez pedig DiskEncryptionSet2 titkosítással lesz titkosítva.

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

Ha az operációsrendszer-lemez forrása egy pillanatkép, a használatával `--os-snapshot` adhatja meg az operációsrendszer-lemezt. Ha vannak olyan adatlemez-pillanatképek, amelyek a rendszerkép verziójának is részét kell, adja hozzá. A `--data-snapshot-luns` LUN megadásához használja a értéket, a használatával `--data-snapshots` pedig a pillanatképeket.

Ebben a példában a források lemez-pillanatképek. Az operációsrendszer-lemez és az adatlemez a 0 LUN-ban található. Az operációsrendszer-lemez DiskEncryptionSet1, az adatlemez pedig DiskEncryptionSet2 titkosítással lesz titkosítva.

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

Virtuális gépet létrehozhat egy megosztott rendszerkép-katalógusból, és ügyfél által kezelt kulcsokkal titkosíthatja a lemezeket. A szintaxis megegyezik egy [rendszerképből egy általánosított vagy](vm-generalized-image-version-cli.md) [specializált](vm-specialized-image-version-cli.md) virtuális gép létrehozásával. Egyszerűen adja hozzá `--os-disk-encryption-set` a paramétert a titkosítási készlet azonosítójával. Adatlemezek esetén adja hozzá az adatokat az adatlemezek lemeztitkosítási készletei szóközzel tagolt `--data-disk-encryption-sets` listájával.


## <a name="portal"></a>Portál

Amikor létrehozza a rendszerkép verzióját a portálon, a Titkosítás lapon alkalmazhatja a tárolótitkosítási készleteket. 

> [!IMPORTANT]
> A dupla titkosításhoz a hivatkozást kell használnia a [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) Azure Portal. Az aktívként használt kettős titkosítás jelenleg nem látható a nyilvános Azure Portal kivéve, ha ezt a hivatkozást használja.


1. A **Rendszerképverzió létrehozása lapon** válassza a **Titkosítás** lapot.
2. A **Titkosítás típusa mezőben** válassza az Adattitkosítás az ügyfél által felügyelt kulccsal vagy **a** Dupla titkosítás platform által felügyelt és felhasználó által kezelt **kulcsokkal lehetőséget.** 
3. A rendszerkép minden lemeze esetén válasszon ki egy titkosítási készletet a **Lemeztitkosítási** készlet legördülő listából. 

### <a name="create-the-vm"></a>A virtuális gép létrehozása

Létrehozhat egy virtuális gépet egy rendszerképverzióból, és ügyfél által kezelt kulcsokkal titkosíthatja a lemezeket. Amikor a portálon hozza létre  a virtuális gépet, a Lemezek lapon válassza  az Adattitkosítás ügyfél által felügyelt kulcsokkal vagy a Titkosítási típusként használt, platform által felügyelt és felhasználó által kezelt kulcsokkal dupla **titkosítás lehetőséget.**  Ezután kiválaszthatja a titkosítási készletet a legördülő listából.

## <a name="next-steps"></a>Következő lépések

További információ a [kiszolgálóoldali lemeztitkosításról.](./disk-encryption.md)

A vásárlási terv információinak megszabadlása: Supply Azure Marketplace purchase plan information when creating images (Vásárlási terv Azure Marketplace a [rendszerképek létrehozásakor).](marketplace-images.md)