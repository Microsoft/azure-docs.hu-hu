---
title: Lemezek titkosítása az Azure-méretezési csoportokhoz az Azure CLI-vel
description: Ismerje meg, hogyan titkosíthatja a virtuálisgép-példányokat és a csatlakoztatott lemezeket az Azure CLI használatával egy Windows rendszerű virtuálisgép-méretezési csoportokban
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 302f53bd218a2e01a039be4780a0e2ff5974e7b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215952"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Operációs rendszer és csatlakoztatott adatlemezek titkosítása virtuálisgép-méretezési csoportokban az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre és titkosíthat virtuálisgép-méretezési csoportokat az Azure CLI használatával. Az Azure Disk Encryption virtuálisgép-méretezési csoportokra való alkalmazásával kapcsolatos további információkért lásd: [Azure Disk Encryption Virtual Machine Scale sets](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.31 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely automatikusan frissül a módosítások alkalmazásakor, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének a *~/.ssh/id_rsa* helyen. Minden virtuálisgép-példányhoz 32Gb-adatlemez van csatolva, az Azure [Custom script bővítmény](../virtual-machines/extensions/custom-script-linux.md) pedig az adatlemezek előkészítésére szolgál az az [vmss Extension set](/cli/azure/vmss/extension):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Lemezes titkosításhoz engedélyezett Azure Key Vault létrehozása

A Azure Key Vault képes tárolni a kulcsokat, titkokat vagy jelszavakat, amelyek lehetővé teszik az alkalmazások és szolgáltatások biztonságos megvalósítását. A titkosítási kulcsokat a szoftveres védelem Azure Key Vault tárolja, vagy importálhatja vagy létrehozhatja a kulcsokat az FIPS 140-2 2. szintű standard minősítésű hardveres biztonsági modulokban (HSM). Ezek a titkosítási kulcsok a virtuális GÉPHEZ csatolt virtuális lemezek titkosítására és visszafejtésére szolgálnak. Megőrzi a titkosítási kulcsok felügyeletét, és naplózhatja a használatukat.

Saját egyedi *keyvault_name* definiálása. Ezután hozzon létre egy kulcstartót az az Key [Vault Create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) paranccsal ugyanabban az előfizetésben és régióban, mint a méretezési csoport, és állítsa be a *-enabled-for-Disk-Encryption* hozzáférési házirendet.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Meglévő Key Vault használata

Ez a lépés csak akkor szükséges, ha rendelkezik egy meglévő Key Vault, amelyet lemezes titkosítással szeretne használni. Hagyja ki ezt a lépést, ha az előző szakaszban létrehozott egy Key Vault.

Saját egyedi *keyvault_name* definiálása. Ezután frissítse a kulcstartót az [az Key Vault Update paranccsal](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) , és állítsa be az *--enabled-a-Disk-Encryption* hozzáférési szabályzatot.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Titkosítás engedélyezése

A méretezési csoportokban lévő virtuálisgép-példányok titkosításához először szerezzen be néhány információt az Key Vault erőforrás-AZONOSÍTÓval az [az kulcstartó show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show)paranccsal. A rendszer ezeket a változókat használja a titkosítási folyamat elindításához az [az vmss encryption Enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

A titkosítási folyamat elindításához egy-két percet is igénybe vehet.

Mivel a méretezési csoport egy korábbi lépésben létrehozott méretezési csoport frissítési szabályzata *automatikus* értékre van állítva, a virtuálisgép-példányok automatikusan elindítják a titkosítási folyamatot. Azokon a méretezési csoportokon, amelyeken a frissítési szabályzat manuális, a virtuálisgép-példányok titkosítási szabályzatát az [az vmss Update-instances paranccsal](/cli/azure/vmss#az-vmss-update-instances)indíthatja el.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Titkosítás engedélyezése a KEK használatával a kulcs becsomagolásához

A virtuálisgép-méretezési csoport titkosításakor a titkosítási kulcs is használható a további biztonsághoz.

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
>  A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító sztring:</br>
/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[erőforrás-csoport-név]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br></br>
> A kulcs-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja, a következőhöz hasonlóan:</br>
https://[kulcstartó-név]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id]

## <a name="check-encryption-progress"></a>Titkosítási folyamat ellenőrzésének állapota

A lemezes titkosítás állapotának megtekintéséhez használja az [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Ha a virtuálisgép-példányok titkosítva vannak, az állapotkód *EncryptionState/titkosítva* jelenik meg, ahogy az a következő példában látható:

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

Ha már nem kíván titkosított virtuálisgép-példányokat használni, letilthatja a titkosítást az [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable) paranccsal a következőképpen:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Következő lépések

- Ebben a cikkben az Azure CLI használatával titkosít egy virtuálisgép-méretezési készletet. Használhat [Azure PowerShell](disk-encryption-powershell.md) vagy [Azure Resource Manager sablonokat](disk-encryption-azure-resource-manager.md)is.
- Ha azt szeretné, hogy a Azure Disk Encryption egy másik bővítmény kiépítés után is alkalmazza, használhatja a [bővítmények sorrendjét](virtual-machine-scale-sets-extension-sequencing.md). 
- A Linux-méretezési csoport adatlemez-titkosításának végpontok közötti batch-fájlja [itt](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)található. Ez a példa létrehoz egy erőforráscsoportot, egy Linux-méretezési csoportot, csatlakoztat egy 5 GB-nyi adatlemezt, és titkosítja a virtuálisgép-méretezési csoportot.
