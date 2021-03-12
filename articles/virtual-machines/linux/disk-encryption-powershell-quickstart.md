---
title: Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és titkosíthat Linux rendszerű virtuális gépeket az Azure PowerShell használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1833b25554df53a480af9a8fc643755dbc2bbdb8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564679"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Rövid útmutató: Linux rendszerű virtuális gép létrehozása és titkosítása az Azure-ban Azure PowerShell

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használható a Azure PowerShell modul egy linuxos virtuális gép (VM) létrehozásához, a titkosítási kulcsok tárolásához Key Vault létrehozásához és a virtuális gép titkosításához. Ez a rövid útmutató az Ubuntu 16,04 LTS Marketplace rendszerképét használja a Canonicalből és egy VM-Standard_D2S_V3 méretből. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoportok olyan logikai tárolók, amelyekbe a rendszer üzembe helyezi az Azure-erőforrásokat és kezeli azokat:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy [új AzVM](/powershell/module/az.compute/new-azvm)rendelkező Azure-beli virtuális gépet, és továbbítsa azt a fent létrehozott virtuálisgép-konfigurációs objektumnak.

```powershell-interactive
$cred = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:18.04-LTS:latest -Size Standard_D2S_V3
```

A virtuális gép üzembe helyezése eltarthat néhány percig. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsokhoz konfigurált Key Vault létrehozása

Az Azure Disk Encryption egy Azure Key Vault tárolja a titkosítási kulcsát. Hozzon létre egy Key Vaultt a [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Ha engedélyezni szeretné a Key Vault számára a titkosítási kulcsok tárolását, használja a-EnabledForDiskEncryption paramétert.

> [!Important]
> Minden Key vaultnak rendelkeznie kell egy egyedi névvel az Azure-ban. Az alábbi példákban cserélje le a <saját egyedi-kulcstartó-Name> a választott névre.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gépet a [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

A Set-AzVmDiskEncryptionExtension a Key Vault objektum egyes értékeit igényli. Ezeket az értékeket a Key Vault egyedi nevének a [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault)értékre való átadásával szerezheti be.

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Néhány perc elteltével a folyamat a következőt fogja visszaadni:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

A [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)futtatásával ellenőrizheti a titkosítási folyamatot.

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Ha engedélyezve van a titkosítás, a visszaadott kimenetben a következő jelenik meg:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vault, amely engedélyezte a titkosítási kulcsokat, és titkosította a virtuális gépet.  A következő cikkből többet tudhat meg a Linux rendszerű virtuális gépek Azure Disk Encryptionéről.

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
