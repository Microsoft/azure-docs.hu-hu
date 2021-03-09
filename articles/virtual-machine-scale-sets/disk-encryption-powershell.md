---
title: Lemezek titkosítása Azure-beli méretezési csoportokhoz Azure PowerShell
description: Ismerje meg, hogyan titkosíthatja a virtuálisgép-példányokat és a csatlakoztatott lemezeket a Azure PowerShell használatával a Windowsos virtuálisgép-méretezési csoportokban
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519571"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Operációs rendszer és csatlakoztatott adatlemezek titkosítása virtuálisgép-méretezési csoportokban Azure PowerShell

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.  Ez a cikk bemutatja, hogyan hozhat létre és titkosíthat virtuálisgép-méretezési csoportokat a Azure PowerShell használatával. A virtuálisgép-méretezési csoportokra Azure Disk Encryption alkalmazásával kapcsolatos további információkért lásd: [Azure Disk Encryption Virtual Machine Scale sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Lemezes titkosításhoz engedélyezett Azure Key Vault létrehozása

A Azure Key Vault képes tárolni a kulcsokat, titkokat vagy jelszavakat, amelyek lehetővé teszik az alkalmazások és szolgáltatások biztonságos megvalósítását. A titkosítási kulcsokat a szoftveres védelem Azure Key Vault tárolja, vagy importálhatja vagy létrehozhatja a kulcsokat az FIPS 140-2 2. szintű standard minősítésű hardveres biztonsági modulokban (HSM). Ezek a titkosítási kulcsok a virtuális GÉPHEZ csatolt virtuális lemezek titkosítására és visszafejtésére szolgálnak. Megőrzi a titkosítási kulcsok felügyeletét, és naplózhatja a használatukat.

Hozzon létre egy Key Vaultt a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Ha engedélyezni szeretné a Key Vault a lemezes titkosításhoz, állítsa be a *EnabledForDiskEncryption* paramétert. Az alábbi példa az erőforráscsoport neve, a Key Vault neve és a hely változóit is meghatározza. Adja meg a saját egyedi Key Vault nevét:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Meglévő Key Vault használata

Ez a lépés csak akkor szükséges, ha rendelkezik egy meglévő Key Vault, amelyet lemezes titkosítással szeretne használni. Hagyja ki ezt a lépést, ha az előző szakaszban létrehozott egy Key Vault.

Engedélyezheti egy meglévő Key Vault ugyanabban az előfizetésben és régióban, mint a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)használatával történő lemezes titkosítás méretezési csoportját. Adja meg a meglévő Key Vault nevét a *$vaultName* változóban a következőképpen:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Először a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuálisgép-példányok rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre egy virtuálisgép-méretezési készletet a [New-AzVmss](/powershell/module/az.compute/new-azvmss). A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó olyan szabályokat tartalmaz, amelyek elosztják a 80-as TCP-porton beérkező forgalmat, valamint lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-porton:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

A méretezési csoportokban lévő virtuálisgép-példányok titkosításához először szerezzen be néhány információt a [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault)Key Vault URI és erőforrás-azonosítóval kapcsolatban. A rendszer ezeket a változókat használja a titkosítási folyamat elindításához a [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Ha a rendszer kéri, írja be az *y karaktert* a méretezési csoport virtuálisgép-példányain a lemez titkosítási folyamat folytatásához.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Titkosítás engedélyezése a KEK használatával a kulcs becsomagolásához

A virtuálisgép-méretezési csoport titkosításakor a titkosítási kulcs is használható a további biztonsághoz.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító sztring:</br>
/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[erőforrás-csoport-név]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br></br>
> A kulcs-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja, a következőhöz hasonlóan:</br>
https://[kulcstartó-név]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id]

## <a name="check-encryption-progress"></a>Titkosítási folyamat ellenőrzésének állapota

A lemezes titkosítás állapotának vizsgálatához használja a [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Ha a virtuálisgép-példányok titkosítva vannak, a *EncryptionSummary* -kód a következő példában látható módon *ProvisioningState/sikeresnek* tekinti a jelentést:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Titkosítás letiltása

Ha már nem kíván titkosított virtuálisgép-példányokat használni, letilthatja a titkosítást a [disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) használatával, a következőképpen:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Következő lépések

- Ebben a cikkben egy virtuálisgép-méretezési csoport titkosításához Azure PowerShell használt. Használhatja az [Azure CLI](disk-encryption-cli.md) -t vagy [Azure Resource Manager-sablonokat](disk-encryption-azure-resource-manager.md)is.
- Ha azt szeretné, hogy a Azure Disk Encryption egy másik bővítmény kiépítés után is alkalmazza, használhatja a [bővítmények sorrendjét](virtual-machine-scale-sets-extension-sequencing.md).
