---
title: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és titkosíthat egy Windows rendszerű virtuális gépet az Azure CLI használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: bffa155a51c4897cd1e2cc07a6d4aea9a8e080ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562742"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Gyors útmutató: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre és titkosíthat Windows Server 2016 rendszerű virtuális gépeket az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.30 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hoz létre. Ez a példa az *azureuser* rendszergazdanevet és a *myPassword12* jelszót használja.

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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Titkosítási kulcsokhoz konfigurált Key Vault létrehozása

Az Azure Disk Encryption egy Azure Key Vault tárolja a titkosítási kulcsát. Hozzon létre egy Key Vault az [az Key Vault Create](/cli/azure/keyvault#az-keyvault-create)paranccsal. Ha engedélyezni szeretné a Key Vault a titkosítási kulcsok tárolásához, használja az--enabled-for-Disk-Encryption paramétert.
> [!Important]
> Minden Key Vault egyedi névvel kell rendelkeznie. Az alábbi példa egy *myKV* nevű Key Vault hoz létre, de a tiéd nevet kell megadnia.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

Titkosítsa a virtuális gépet az [az VM encryption](/cli/azure/vm/encryption)paranccsal, és adjon egyedi Key Vault nevet a--Disk-Encryption-kulcstartó paraméternek.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Az [az VM show](/cli/azure/vm/encryption#az-vm-encryption-show) paranccsal ellenőrizheti, hogy a titkosítás engedélyezve van-e a virtuális gépen.

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

A visszaadott kimenetben a következő jelenik meg:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és a Key Vault.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, létrehozott egy Key Vault, amely engedélyezte a titkosítási kulcsokat, és titkosította a virtuális gépet.  Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Disk Encryption előfeltétel-konfigurációs szkriptjeiről az IaaS virtuális gépek esetében.

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
