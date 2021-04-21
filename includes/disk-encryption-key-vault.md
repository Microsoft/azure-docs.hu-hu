---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 98922829e83f84078c3d8cadae15844dba194c93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800360"
---
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

*Ha már rendelkezik erőforráscsoporttal, ugorjon [a Kulcstartó létrehozása részhez.](#create-a-key-vault)*

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

Hozzon létre egy erőforráscsoportot [az az group create](/cli/azure/group#az_group_create) Azure CLI-paranccsal, a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell paranccsal vagy a [Azure Portal.](https://portal.azure.com)

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

*Ha már rendelkezik kulcstartóval, ugorjon a Kulcstartó speciális hozzáférési [szabályzatok beállítása részhez.](#set-key-vault-advanced-access-policies)*

Hozzon létre egy kulcstartót [az az keyvault create](/cli/azure/keyvault#az_keyvault_create) Azure CLI-paranccsal, a [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure PowerShell-paranccsal, a [Azure Portal](https://portal.azure.com)paranccsal vagy egy Resource Manager [sablonnal.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

>[!WARNING]
> A kulcstartónak és a virtuális gépeknek ugyanabban az előfizetésben kell lennie. Annak biztosítása érdekében, hogy a titkosítási titkos kulcsok ne lépik át a régiók határait, Azure Disk Encryption a Key Vault és a virtuális gépeknek ugyanabban a régióban kell lennie. Hozzon létre és Key Vault, amely ugyanabban az előfizetésben és régióban található, mint a titkosítani szükséges virtuális gépek. 

Minden Key Vault egyedi névvel kell rendelkezik. Cserélje <a your-unique-keyvault-name> a kulcstartó nevére a következő példákban.

### <a name="azure-cli"></a>Azure CLI

Amikor az Azure CLI használatával hoz létre kulcstartót, adja hozzá az "--enabled-for-disk-encryption" jelzőt.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Amikor kulcstartót hoz létre a Azure PowerShell, adja hozzá az "-EnabledForDiskEncryption" jelzőt.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-sablon

Kulcstartót a következő sablonnal [is Resource Manager:](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Az Azure gyorsindítási sablonban kattintson az Üzembe **helyezés az Azure-ban elemre.**
2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, Key Vault nevét, az objektumazonosítót, a jogi feltételeket és a szerződést, majd kattintson a **Vásárlás gombra.** 


##  <a name="set-key-vault-advanced-access-policies"></a>A kulcstartó speciális hozzáférési szabályzatainak beállítása

Az Azure platformnak hozzá kell férni a kulcstartóban található titkosítási kulcsokhoz vagy titkos kulcsokhoz, hogy elérhetővé tegye őket a virtuális gép számára a kötetek indításához és visszafejtéséhez. 

Ha a létrehozáskor nem engedélyezték a kulcstartót a lemeztitkosításhoz, az üzembe helyezéshez vagy a sablonok üzembe helyezéséhez (az előző lépésben bemutatva), frissítenie kell annak speciális hozzáférési szabályzatait.  

### <a name="azure-cli"></a>Azure CLI

A [kulcstartó lemeztitkosításának](/cli/azure/keyvault#az_keyvault_update) engedélyezéséhez használja az az keyvault update eszközt. 

 - **A Key Vault engedélyezése lemeztitkosításhoz:** A lemeztitkosítás engedélyezése kötelező. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Engedélyezze Key Vault központi telepítéshez, ha szükséges:** Engedélyezi a Microsoft.Compute erőforrás-szolgáltatónak, hogy titkos kulcsokat lekérje ebből a kulcstartóból, amikor az erőforrás létrehozásakor erre a kulcstartóra hivatkozik, például egy virtuális gép létrehozásakor.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Engedélyezze Key Vault sablontelepítéshez, ha szükséges:** Engedélyezze Resource Manager számára a titkos kulcsok lekérését a tárolóból.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 A Key Vault [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) PowerShell-parancsmagját használva engedélyezze a lemeztitkosítást a kulcstartóhoz.

  - **A Key Vault engedélyezése lemeztitkosításhoz:** Az Azure Disk Encryptionhez enabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Engedélyezze Key Vault központi telepítéshez, ha szükséges:** Engedélyezi a Microsoft.Compute erőforrás-szolgáltatónak, hogy titkos kulcsokat lekérje ebből a kulcstartóból, amikor az erőforrás létrehozásakor erre a kulcstartóra hivatkozik, például egy virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Engedélyezze Key Vault sablontelepítéshez, ha szükséges:** Engedélyezi Azure Resource Manager, hogy titkos kulcsokat lekért erről a kulcstartóról, amikor erre a kulcstartóra hivatkozik egy sablon üzembe helyezése során.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Válassza ki a kulcstartót, válassza a Hozzáférési **szabályzatok** lehetőséget, majd kattintson ide **a speciális hozzáférési szabályzatok kiválasztásához.**
2. Jelölje be az Enable access to Azure Disk Encryption for volume encryption (Hozzáférés engedélyezése Azure Disk Encryption **kötettitkosításhoz) feliratú jelölőnégyzetet.**
3. Jelölje **be az Azure-Virtual Machines** hozzáférésének engedélyezése az üzembe helyezéshez és/vagy a Hozzáférés engedélyezése Azure Resource Manager a sablonok üzembe helyezéséhez lehetőséget, ha szükséges.  
4. Kattintson a **Mentés** gombra.

    ![Azure Key Vault – speciális hozzáférési szabályzatok](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Kulcstitkosítási kulcs (KEK) beállítása

Ha kulcstitkosítási kulcsot (KEK) szeretne használni a titkosítási kulcsok további biztonsági rétegeként, adjon hozzá egy KEK-et a kulcstartóhoz. Ha kulcstitkosítási kulcs van megadva, a Azure Disk Encryption ezzel a kulccsal burk szóval burköli a titkosítási titkos kódokat, mielőtt Key Vault.

Létrehozhat egy új KEK-et az Azure CLI [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) parancs, az Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag vagy a [Azure Portal.](https://portal.azure.com/) Létre kell hoznia egy RSA-kulcstípust; Azure Disk Encryption még nem támogatja az elliptikus görbét használó kulcsokat.

Ehelyett importálhat egy KEK-et a helyszíni kulcskezelő HSM-ről. További információt a [dokumentációban Key Vault talál.](../articles/key-vault/keys/hsm-protected-keys.md)

A Kulcstartó KEK URL-címeit verziószámozva kell lennie. Az Azure kikényszeríteni a verziószámozás ezen korlátozását. Az érvényes titkos kódért és a KEK URL-címekért tekintse meg az alábbi példákat:

* Példa egy érvényes titkos URL-címre: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Példa érvényes KEK URL-címre: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption nem támogatja a portszámok kulcstartókulcsok és KEK URL-címek részeként való megadását. A nem támogatott és támogatott kulcstartó-URL-címekre az alábbi példákban talál példákat:

  * Elfogadható kulcstartó URL-címe: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * A Key Vault elfogadhatatlan URL-címe: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) parancsával hozzon létre egy új KEK-et, és tárolja a kulcstartóban.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Ehelyett importálhat egy titkos kulcsot az Azure CLI [az keyvault key import parancsával:](/cli/azure/keyvault/key#az_keyvault_key_import)

Mindkét esetben a KEK nevét kell megadva az Azure CLI [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) --key-encryption-key paraméterben. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Az Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag használatával hozzon létre egy új KEK-et, és tárolja a kulcstartóban.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Ehelyett importálhat egy titkos kulcsot a Azure PowerShell [az keyvault key import paranccsal.](/cli/azure/keyvault/key#az_keyvault_key_import)

Mindkét esetben meg kell adni a KEK-kulcstartó azonosítóját és a KEK URL-címét a Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) -KeyEncryptionKeyVaultId és -KeyEncryptionKeyUrl paraméterekhez. Vegye figyelembe, hogy ez a példa feltételezi, hogy ugyanazt a kulcstartót használja a lemeztitkosítási kulcshoz és a KEK-hez is.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```