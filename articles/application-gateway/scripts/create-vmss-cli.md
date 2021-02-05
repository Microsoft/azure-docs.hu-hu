---
title: Azure CLI-példaszkript – Webes forgalom kezelése | Microsoft Docs
description: Azure CLI-példaszkript – Webes forgalom kezelése alkalmazásátjáróval és virtuálisgép-méretezési csoporttal.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 2e766bcdee2afa3ff4ed75476c619326c4c0e0c2
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591651"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Webes forgalom kezelése az Azure CLI-vel

Ez a szkript létrehoz egy alkalmazásátjárót, amely egy virtuálisgép-méretezési csoportot használ háttérkiszolgálókként. Az alkalmazásátjáró ezután konfigurálható a webes forgalom kezelésére. A szkript futtatása után az alkalmazásátjárót annak nyilvános IP-címével tesztelheti.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő paranccsal eltávolítható az erőforráscsoport, az alkalmazásátjáró és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet) | Virtuális hálózatot hoz létre. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Alhálózatot hoz létre egy virtuális hálózatban. |
| [az network public-ip create](/cli/azure/network/public-ip) | Az alkalmazásátjáró nyilvános IP-címét hozza létre. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Egy alkalmazásátjárót hoz létre. |
| [az vmss create](/cli/azure/vmss) | Létrehoz egy virtuálisgép-méretezési csoportot. |
| [az network public-ip show](/cli/azure/network/public-ip) | Beszerzi az alkalmazásátjáró nyilvános IP-címét. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure/overview).

További CLI-példaszkripteket az alkalmazásátjárókhoz az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációban](../cli-samples.md) találhat.
