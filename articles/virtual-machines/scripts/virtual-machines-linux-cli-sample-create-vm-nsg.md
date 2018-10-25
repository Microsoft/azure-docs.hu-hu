---
title: Azure CLI-példaszkript – Két virtuális gép létrehozása belső és külső NSG-vel | Microsoft Docs
description: Azure CLI-példaszkript – Két virtuális gép létrehozása belső és külső NSG-vel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 85add5e360ae9f0b80cae916cde27e549dc45e1e
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467769"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>A virtuális gépek közötti hálózati adatforgalom védelme

Ez a szkript két virtuális gépet hoz létre, és biztonságossá teszi a rájuk irányuló forgalmat. Az egyik virtuális gép az interneten érhető el, és egy olyan hálózati biztonsági csoporttal (NSG) rendelkezik, amelynek konfigurációja a 22-es és a 80-as porton engedélyezi a forgalmat. A második virtuális gép nem érhető el az interneten, és egy olyan NSG-vel rendelkezik, amelynek konfigurációja csak az első virtuális gépről érkező forgalmat engedélyezi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Létrehoz egy alhálózatot. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | Egy hálózati biztonsági csoport szabályával kapcsolatos információkat ad vissza. Ebben a példában a szabály nevét eltároljuk egy változóban, hogy a szkript későbbi részében is fel lehessen használni. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Frissít egy NSG-szabályt. Ebben a példában a háttérrendszerre vonatkozó szabályt frissítjük, hogy csak az előtéri alhálózatról érkező forgalmat engedje át. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
