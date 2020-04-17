---
title: Azure CLI-példaszkript – Linux rendszerű virtuális gép titkosítása
description: Azure CLI-példaszkript – Linux rendszerű virtuális gép titkosítása
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 585096f7b45a1d5396e7d59f9d54de90486a103a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459916"
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gép titkosítása az Azure-ban

Ez a szkript egy biztonságos Azure Key Vault-tárolót, titkosító kulcsokat, egy Azure Active Directory-szolgáltatásnevet és egy Linux rendszerű virtuális gépet (VM) hoz létre. A virtuális gépet ezután a Key Vaultból származó titkosítási kulccsal és a szolgáltatásnév hitelesítő adataival titkosítjuk.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy Azure Key Vault-tároló, egy szolgáltatásnév, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault) | Létrehoz egy Azure Key Vault-tárolót a védett adatok (például titkosítási kulcsok) tárolásához. |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key) | Létrehoz egy titkosítási kulcsot a Key Vault-tárolóban. |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp) | Létrehoz egy Azure Active Directory-szolgáltatásnevet a titkosítási kulcsok biztonságos hitelesítéséhez és elérésének szabályozásához. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault) | Beállítja a jogosultságokat a Key Vault-tárolóban, hogy a szolgáltatásnév hozzáférhessen a titkosítási kulcsokhoz. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption) | Bekapcsolja egy virtuális gép titkosítását a szolgáltatásnév hitelesítési adataival és titkosítási kulcsával. |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption) | Megmutatja a virtuális gép titkosítási folyamatának állapotát. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
