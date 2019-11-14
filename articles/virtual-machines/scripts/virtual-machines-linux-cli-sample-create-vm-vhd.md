---
title: Azure CLI parancsfájl-minta – virtuális gép létrehozása VHD-vel
description: Azure CLI-példaszkript – Virtuális gép létrehozása virtuális merevlemezzel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 16f4e3e153cbc02f8626199d168d069add48e4b6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037668"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Virtuális gép létrehozása virtuális merevlemezzel

Ez a példa virtuális merevlemezzel (VHD) hoz létre egy virtuális gépet.
Létrehoz egy erőforráscsoportot, egy tárfiókot és egy tárolót, majd feltölti a virtuális merevlemezt a tárolóba egy virtuális gép létrehozásához.
Lecseréli az SSH nyilvános kulcsát az Ön nyilvános kulcsára, hogy Ön hozzá tudjon férni a virtuális géphez.

Szüksége lesz egy rendszerindító virtuális merevlemezre. A szkript a `~/sample.vhd` kifejezést keresi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account) | Felsorolja a tárfiókokat. |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account) | Ellenőrzi a tárfiók nevének érvényességét és azt, hogy valahol már használatban van-e. |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys) | Felsorolja a tárfiókok kulcsait. |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob) | Ellenőrzi, hogy a blob létezik-e. |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container) | Létrehoz egy tárolót egy tárfiókban. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob) | Létrehoz egy blobot a tárolóban a VHD feltöltésével. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm) | A `--query` elemmel együtt használva ellenőrzi, hogy a virtuális gép neve már használatban van-e. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Létrehozza a virtuális gépeket. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az-vm-list-ip-addresses) | Lekéri a létrehozott virtuális gép IP-címét. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
