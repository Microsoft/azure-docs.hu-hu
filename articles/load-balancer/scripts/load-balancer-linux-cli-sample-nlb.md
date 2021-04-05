---
title: A virtuális gépek forgalmának elosztása a HA-Azure CLI-Azure Load Balancer
description: Ez az Azure CLI-példaszkript bemutatja, hogyan lehet terheléselosztást végezni a virtuális gépekre a magas rendelkezésre álláshoz
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7041bb568aed7ec0619e57887db64f8041168ee6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696608"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure CLI-példaszkript: Forgalom terheléselosztása virtuális gépekre magas rendelkezésre álláshoz

A CLI-példaszkript minden összetevőt létrehoz, amelyre szükség van több, magas rendelkezésre állásra és terheléselosztásra konfigurált Ubuntu rendszerű virtuális gép futtatásához. A szkript futtatása után három virtuális géppel rendelkezik majd, amelyek egy Azure-beli rendelkezésre állási csoporthoz vannak csatlakoztatva, és egy Azure Load Balanceren keresztül érhetők el. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Azure-terheléselosztót hoz létre. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Terheléselosztói mintavételt hoz létre. A terheléselosztói mintavétel a terheléselosztó-készlet egyes virtuális gépeinek figyelésére használható. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Terheléselosztó-szabályt hoz létre. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom a terheléselosztó csoporthoz tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Létrehoz egy terheléselosztó hálózati címfordítási (Network Address Translation, NAT) szabályt.  A NAT-szabályok a terheléselosztó egyik portját hozzárendelik egy virtuális gép valamelyik portjához. Ebben a példában egy NAT-szabályt hozunk létre az SSH-forgalomnak a terheléselosztó csoportba tartozó egyes virtuális gépekre történő irányítására.  |
| [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) | Létrehoz egy hálózati biztonsági csoportot (NSG), amely biztonsági határként szolgál az internet és a virtuális gép között. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Létrehoz egy NSG-szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 22-es portot nyitjuk meg az SSH-forgalom számára. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz, az alhálózathoz és az NSG-hez. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Létrehoz egy rendelkezésre állási csoportot. A rendelkezésre állási csoportok az alkalmazások rendelkezésre állását biztosítják a virtuális gépek fizikai erőforrások közötti elosztásával, hogy az esetlegesen fellépő hibák ne érintsék a teljes készletet. |
| [az vm create](/cli/azure/vm#az-vm-create) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure-hálózatkezelési CLI-példaszkripteket az [Azure-hálózatkezelés dokumentációjában](../cli-samples.md) talál.