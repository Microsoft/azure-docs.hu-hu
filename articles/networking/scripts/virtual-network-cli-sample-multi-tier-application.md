---
title: Azure CLI parancsfájl-minta – hálózat létrehozása többrétegű alkalmazásokhoz
description: Azure CLI-példaszkript – Virtuális hálózat létrehozása többrétegű alkalmazásokhoz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: bcfe82dedcac01c60e93788b78fec779f0da7dbc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530252"
---
# <a name="use-an-azure-cli-script-sample-to-create-a-network-for-multi-tier-applications"></a>A többrétegű alkalmazások hálózatának létrehozása Azure CLI parancsfájl-minta használatával

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. A szkript futtatása után két virtuális géppel fog rendelkezni, eggyel-eggyel mindegyik olyan alhálózatban, amelyben üzembe helyezhet webkiszolgálót és MySQL-szoftvert.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Példaszkript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Háttérbeli alhálózatot hoz létre. |
| [az network public-ip create](/cli/azure/network/public-ip) | Létrehoz egy nyilvános IP-címet a virtuális gép internetről való eléréséhez. |
| [az network nic create](/cli/azure/network/nic) | Virtuális hálózati adaptereket hoz létre, és a virtuális hálózat előtérbeli és háttérbeli alhálózataihoz csatolja őket. |
| [az network nsg create](/cli/azure/network/nsg) | Az előtérbeli és a háttérbeli alhálózatokhoz társított hálózati biztonsági csoportokat (NSG) hoz létre. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |NSG-szabályokat hoz létre, amelyek engedélyeznek vagy blokkolnak adott alhálózatokra mutató bizonyos portokat. |
| [az vm create](/cli/azure/vm) | Virtuális gépeket hoz létre, és minden virtuális géphez csatol egy NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További hálózati CLI-szkriptek az [Azure Networking áttekintés dokumentációjában](../cli-samples.md) találhatók.
