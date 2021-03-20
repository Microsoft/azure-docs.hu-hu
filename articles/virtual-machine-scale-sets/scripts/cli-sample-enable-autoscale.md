---
title: Azure CLI-minták – gazdagép-alapú autoskálázás engedélyezése
description: Ez a szkript egy Ubuntu rendszert futtató virtuálisgép-méretezési csoportot hoz létre, illetve gazdagépalapú mérőszámok használatával automatikusan elvégzi a méretezést a CPU terhelésének változása alapján.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8e8118a32da296d090ba22122b5188f941419e94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87502137"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Virtuálisgép-méretezési csoport automatikus méretezése az Azure CLI használatával
Ez a szkript egy Ubuntu rendszert futtató virtuálisgép-méretezési csoportot hoz létre, illetve gazdagépalapú mérőszámok használatával automatikusan elvégzi a méretezést a CPU terhelésének változása alapján.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.sh "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuálisgép-méretezési csoport és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/ad/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vmss create](/cli/azure/vmss) | Létrehozza a virtuálisgép-méretezési csoportot, és csatlakoztatja a virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. Továbbá egy terheléselosztót is létrehoz, amely elosztja a forgalmat a virtuálisgép-példányok között. A parancs megadja emellett a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings) | Automatikus méretezési szabályokat hoz létre, és alkalmazza azokat egy virtuálisgép-méretezési csoporton. |
| [az group delete](/cli/azure/ad/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure/overview).
