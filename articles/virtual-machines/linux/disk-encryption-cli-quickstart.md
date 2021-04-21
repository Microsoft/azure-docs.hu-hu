---
title: Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure CLI-t Linux rendszerű virtuális gép létrehozására és titkosítására
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5b98fde5e15a3c57b56ecc8aea60023ffb8c22a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774304"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Rövid útmutató: Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure CLI-t Linux rendszerű virtuális gép (VM) létrehozására és titkosítására.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hoz létre.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő kimeneti példa azt mutatja be, hogy a virtuális gép létrehozási művelete sikeres volt.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási Key Vault konfigurált tanúsítvány létrehozása

Az Azure Disk Encryption titkosítási kulcsát egy Azure Key Vault. Hozzon létre egy Key Vault [az az keyvault create segítségével.](/cli/azure/keyvault#az_keyvault_create) Ha engedélyezni szeretné Key Vault titkosítási kulcsok tárolására, használja az --enabled-for-disk-encryption paramétert.

> [!Important]
> Minden kulcstartónak egyedi névvel kell lennie az Azure-ban. Az alábbi példákban cserélje <your-unique-keyvault-name> a választott névre.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gépet [az az vm encryption használatával,](/cli/azure/vm/encryption)Key Vault egyedi nevét a --disk-encryption-keyvault paraméternek megfelelően.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

A folyamat rövid idő után visszatér a következőre: "A titkosítási kérelem elfogadva. A folyamat figyelése érdekében használja a "show" parancsot. A "show" parancs az [az vm show.](/cli/azure/vm/encryption#az_vm_encryption_show)

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Ha a titkosítás engedélyezve van, a következőt fogja látni a visszaadott kimenetben:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépet és a Key Vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vault, amely számára engedélyezték a titkosítási kulcsokat, és titkosította a virtuális gépet.  Ha többet szeretne megtudni a Linux rendszerű virtuális gépek Azure Disk Encryption kapcsolatban, olvassa el a következő cikket.

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintés](disk-encryption-overview.md)
