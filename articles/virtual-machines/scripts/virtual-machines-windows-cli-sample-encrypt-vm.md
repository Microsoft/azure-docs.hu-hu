---
title: Azure CLI-példaszkript – Windows rendszerű virtuális gép titkosítása
description: Azure CLI-példaszkript – Windows rendszerű virtuális gép titkosítása
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 857bddaad32ea98635f5fbd2f8202975fb033b70
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459440"
---
# <a name="encrypt-a-windows-virtual-machine-in-azure"></a>Windows rendszerű virtuális gép titkosítása az Azure-ban

Ez a szkript egy biztonságos Azure Key Vault-tárolót, titkosító kulcsokat, egy Azure Active Directory-szolgáltatásnevet és egy Windows rendszerű virtuális gépet (VM) hoz létre. A virtuális gépet ezután a Key Vaultból származó titkosítási kulccsal és a szolgáltatásnév hitelesítő adataival titkosítjuk.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_windows_vm.sh "Encrypt VM disks")]

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

További virtuális gép CLI-példaszkripteket az [Azure Windows virtuális gépekre vonatkozó dokumentációban](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%windows%2ftoc.json) találhat.
