---
title: Több webhely terheléselosztása – Azure CLI – Azure Load Balancer
description: Ez az Azure CLI-mintaszkript bemutatja, hogyan lehet több webhely terheléselosztását megoldani ugyanazon a virtuális gépen
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 12d425cfbb093d19efec19dfa02af5ff264c1bae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696693"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Azure CLI-mintaszkript: Több webhely terheléselosztása

Ez az Azure CLI-mintaszkript olyan két virtuális gépből álló virtuális hálózatot hoz létre, amelyek egy rendelkezésre állási csoport tagjai. A terheléselosztó két különböző IP-cím forgalmát irányítja a két virtuális gépre. A szkript futtatása után telepíthet a webkiszolgáló szoftvert a virtuális gépekre, és több webhelyet is üzemeltethet, mindegyiket a saját IP-címével.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, virtuális hálózat, terheléselosztó és minden kapcsolódó erőforrás létrehozására. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Azure Load Balancert hoz létre. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Terheléselosztói mintavételt hoz létre. A terheléselosztói mintavétel a terheléselosztó-készlet egyes virtuális gépeinek figyelésére használható. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Terheléselosztó-szabályt hoz létre. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom a terheléselosztó csoporthoz tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) | Előtérbeli IP-címet hoz létre a terheléselosztóhoz. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) | Hátércímkészletet hoz létre. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz és alhálózathoz. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Létrehoz egy rendelkezésre állási csoportot. A rendelkezésre állási csoportok az alkalmazások rendelkezésre állását biztosítják a virtuális gépek fizikai erőforrások közötti elosztásával, hogy az esetlegesen fellépő hibák ne érintsék a teljes készletet. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) | Létrehoz egy IP-konfigurációt. Az előfizetésében engedélyezve kell lennie a Microsoft.Network/AllowMultipleIpConfigurationsPerNic funkciónak. A --make-primary jelzővel hálózati adapterenként csak egy konfigurációt lehet elsődleges IP-konfigurációként kijelölni. |
| [az vm create](/cli/azure/vm/availability-set#az-vm-availability-set-create) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További hálózatkezelési CLI-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.