---
title: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure CLI-t Windows rendszerű virtuális gépek létrehozására és titkosítására
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e81d29922bee53ba9021c9c26816258f7922a59c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759704"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Rövid útmutató: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure CLI-t Windows Server 2016 rendszerű virtuális gép (VM) létrehozására és titkosítására.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.30-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hoz létre. Ez a példa az *azureuser* rendszergazdanevet és a *myPassword12* jelszót használja.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő kimeneti példa azt mutatja be, hogy a virtuális gép létrehozási művelete sikeres volt.

```console
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

Az Azure Disk Encryption titkosítási kulcsát egy Azure Key Vault. Hozzon létre egy Key Vault [az az keyvault create segítségével.](/cli/azure/keyvault#az_keyvault_create) A titkosítási kulcsok Key Vault engedélyezéséhez használja az --enabled-for-disk-encryption paramétert.
> [!Important]
> Minden Key Vault névnek egyedinek kell lennie. Az alábbi példa létrehoz egy Key Vault *myKV* néven, de a saját nevet valami másnak kell neveznie.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gépet [az az vm encryption](/cli/azure/vm/encryption)használatával, és Key Vault egyedi nevét a --disk-encryption-keyvault paraméternek.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Az az vm show használatával ellenőrizheti, hogy a titkosítás engedélyezve [van-e a virtuális gépen](/cli/azure/vm/encryption#az_vm_encryption_show)

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

A visszaadott kimenet a következőt fogja látni:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és a Key Vault.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, Key Vault a titkosítási kulcsokhoz, és titkosította a virtuális gépet.  Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Disk Encryption előfeltétel-konfigurációs szkriptjeiről az IaaS virtuális gépek esetében.

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintés](disk-encryption-overview.md)
