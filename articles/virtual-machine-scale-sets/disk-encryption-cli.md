---
title: Lemezek titkosítása Azure-méretezési készletekhez az Azure CLI használatával
description: Megtudhatja, hogyan titkosíthatja a virtuálisgép-példányokat és a csatlakoztatott lemezeket egy Windows rendszerű virtuálisgép-méretezési készletben az Azure CLI használatával
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d347be4e6727cdda659620befe20824678160020
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792434"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Operációs rendszer és csatlakoztatott adatlemezek titkosítása egy virtuálisgép-méretezési készletben az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre és titkosíthatja a virtuálisgép-méretezési csoportokat az Azure CLI használatával. Az Azure Disk Encryption virtuálisgép-méretezési készletre való alkalmazásával kapcsolatos további információkért lásd: Azure Disk Encryption [a Virtual Machine Scale Sets.](disk-encryption-overview.md)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.31-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely automatikusan frissül a módosítások alkalmazásakor, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének a *~/.ssh/id_rsa* helyen. Minden virtuálisgép-példányhoz 32 Gb-os adatlemez van csatolva, és az [azure-beli](../virtual-machines/extensions/custom-script-linux.md) egyéni szkriptbővítmény az az vmss extension set használatával készíti elő az [adatlemezeket:](/cli/azure/vmss/extension)

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Lemeztitkosításhoz engedélyezett Azure Key Vault létrehozása

Azure Key Vault a kulcsokat, titkos kulcsokat vagy jelszavakat, amelyek lehetővé teszik azok biztonságos implementtatását az alkalmazásokban és szolgáltatásokban. A titkosítási kulcsokat a Azure Key Vault szoftveres védelemmel tárolják, vagy a kulcsokat a FIPS 140-2 2. szintű 2. szabványnak megfelelő hardveres biztonsági modulokban (HSM-ekkel) importálhatja vagy hozhatja létre. Ezek a titkosítási kulcsok a virtuális géphez csatolt virtuális lemezek titkosítására és visszafejtéséhez használhatók. Ön továbbra is ön irányíthatja ezeket a titkosítási kulcsokat, és naplózhatja azok használatát.

Definiálja saját egyedi *keyvault_name.* Ezután hozzon létre egy KeyVault-et [az az keyvault create](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-create) használatával ugyanabban az előfizetésben és régióban, mint a méretezési csoport, és állítsa be az *--enabled-for-disk-encryption* hozzáférési szabályzatot.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Meglévő alkalmazás Key Vault

Erre a lépésre csak akkor van szükség, ha Key Vault lemeztitkosítással használni kívánt meglévő adatbázissal. Hagyja ki ezt a lépést, ha az előző Key Vault létrehozott egy újat.

Definiálja saját egyedi *keyvault_name.* Ezután frissítse a KeyVaultot [az az keyvault update](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-update) frissítéssel, és állítsa be az *--enabled-for-disk-encryption* hozzáférési szabályzatot.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

A méretezési csoportban található virtuálisgép-példányok titkosításához először szerezze be a Key Vault erőforrás-azonosítóját [az az keyvault show segítségével.](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-show) Ezek a változók indítják el a titkosítási folyamatot az [az vmss encryption enable parancs használatával:](/cli/azure/vmss/encryption#az_vmss_encryption_enable)

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

A titkosítási folyamat elindulása egy-két percet is igénybe vehet.

Mivel a méretezési készlet egy korábbi lépésben létrehozott méretezési készlet frissítési szabályzata, automatikusra van állítva, a virtuálisgép-példányok automatikusan elindítják a titkosítási folyamatot. Olyan méretezési készleteken, ahol a frissítési szabályzat manuális, indítsa el a titkosítási szabályzatot a [virtuálisgép-példányon az az vmss update-instances használatával.](/cli/azure/vmss#az_vmss_update_instances)

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Titkosítás engedélyezése kEK használatával a kulcs becsomagolása érdekében

A virtuálisgép-méretezési készlet titkosítása során kulcstitkosítási kulcsot is használhat a nagyobb biztonság érdekében.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ja, a következőként:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>A titkosítási folyamat ellenőrzése

A lemeztitkosítás állapotának ellenőrzéshez használja [az az vmss encryption show eszközt:](/cli/azure/vmss/encryption#az_vmss_encryption_show)

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Ha a virtuálisgép-példányok titkosítva vannak, az állapotkód *az EncryptionState/encrypted* állapotot jelenti, ahogy az alábbi példakimenetben látható:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Titkosítás letiltása

Ha már nem szeretne titkosított virtuálisgép-példányokat használni, letilthatja a titkosítást [az az vmss encryption disable](/cli/azure/vmss/encryption#az_vmss_encryption_disable) használatával az alábbiak szerint:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Következő lépések

- Ebben a cikkben az Azure CLI használatával titkosított egy virtuálisgép-méretezési készletet. A sablonokat is [Azure PowerShell](disk-encryption-powershell.md) vagy [Azure Resource Manager is használhatja.](disk-encryption-azure-resource-manager.md)
- Ha egy másik bővítmény üzembe Azure Disk Encryption után szeretné alkalmazni az alkalmazásokat, használhatja a [bővítmény-szekvenálást.](virtual-machine-scale-sets-extension-sequencing.md) 
- A Linux rendszerű méretezésihalmazok adatlemez-titkosítására vonatkozó, végpontok között lévő batch-fájlra itt [talál példát.](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat) Ebben a példában létrehozunk egy linuxos méretezési csoportot, csatlakoztatunk egy 5 GB-os adatlemezt, és titkosítjuk a virtuálisgép-méretezési csoportot.
