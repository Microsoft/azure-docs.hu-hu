---
title: Forgalom irányítása hálózati virtuális készüléken keresztül – Azure CLI parancsfájl minta
description: Azure CLI-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc539800cb9cb75ecc69f0d7ae923ed3edfd2ad0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87492292"
---
# <a name="route-traffic-through-a-network-virtual-appliance---azure-cli-script-sample"></a>Forgalom irányítása hálózati virtuális készüléken keresztül – Azure CLI parancsfájl minta

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Ezen kívül létrehoz egy virtuális gépet, amelyen az IP-továbbítás két alhálózat közötti útválasztása engedélyezve van. A szkript futtatása után hálózati szoftvereket, például tűzfal-alkalmazást telepíthet a virtuális gépre.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/bash) vagy egy helyi Azure CLI-telepítésből futtathatja. Ha a parancssori felületet helyileg használja, akkor ehhez a szkripthez az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha helyileg futtatja a parancssori felületet, akkor emellett a `az login` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Háttérbeli és DMZ-alhálózatokat hoz létre. |
| [az network public-ip create](/cli/azure/network/public-ip) | Nyilvános IP-címet hoz létre, amely lehetővé teszi a virtuális gép elérését az internetről. |
| [az network nic create](/cli/azure/network/nic) | Virtuális hálózati adatpert hoz létre, és engedélyezi hozzá az IP-továbbítást. |
| [az network nsg create](/cli/azure/network/nsg) | Egy hálózati biztonsági csoportot (NSG) hoz létre. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | NSG-szabályokat hoz létre, amelyek engedélyezik a virtuális gép bejövő HTTP- és HTTPS-portjait. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Az alhálózatokhoz rendeli az NSG-ket és az útválasztási táblázatokat. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Útválasztási táblázatot hoz létre minden útvonalhoz. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Útvonalakat hoz létre, amellyel irányíthatja a forgalmat az alhálózatok és az internet között a virtuális gépen keresztül. |
| [az vm create](/cli/azure/vm) | Létrehoz egy virtuális gépet, és csatolja hozzá a NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A [virtuális hálózatokra vonatkozó CLI-minták](../cli-samples.md) között további, a virtuális hálózatokra vonatkozó CLI-példaszkripteket talál.
