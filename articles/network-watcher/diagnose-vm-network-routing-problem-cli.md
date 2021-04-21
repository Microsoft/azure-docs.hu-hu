---
title: Virtuális gép hálózati útválasztási problémájának diagnosztizálása – Azure CLI
titleSuffix: Azure Network Watcher
description: Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja a virtuális gépek hálózati útválasztási problémáját az Azure CLI használatával az Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 2ca7a3b25b1355e21782c1d9f736d20a14cbd4ac
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785450"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Virtuális gép hálózati útválasztási problémájának diagnosztizálása – Azure CLI

Ebben a cikkben egy virtuális gépet helyez üzembe, majd ellenőrzi az IP-címre és URL-címre való kommunikációt. Meghatározza a kommunikációs hiba okát és feloldásának módját.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

- A cikkben az Azure CLI parancsai Bash-felületen való futtatásra vannak formázva.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az alábbi példa egy *myVm* nevű virtuális gépet hoz létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépésekkel, amíg létre nem jön a virtuális gép, és az Azure CLI vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A Network Watcher-val való hálózati kommunikáció tesztelése érdekében először engedélyeznie kell egy Network Watchert abban a régióban, ahol a tesztelni kívánt virtuális gép található, majd a Network Watcher következő ugrás funkcióját kell használnia a kommunikáció tesztelésére.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezve van egy Network Watcher az USA keleti régiójában, ugorjon a Következő ugrás [használata részhez.](#use-next-hop) Az [az network watcher configure paranccsal](/cli/azure/network/watcher#az_network_watcher_configure) hozzon létre egy Network Watchert az USA keleti régiójában:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. A virtuális gépről való útválasztás teszteléshez használja az [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) gombra a következő útválasztási ugrás meghatározásához, ha a forgalom egy adott címhez van rendelve.

Tesztelje a virtuális gép kimenő kommunikációját a www.bing.com IP-címeinek egyikén:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Néhány másodperc elteltével a kimenet tájékoztatja, hogy a **nextHopType** az **Internet**, a **routeTableId** pedig **a rendszerútvonal.** Ez az eredmény azt jelenti, hogy érvényes útvonal vezet a célhoz.

Tesztelje a virtuális gép kimenő kommunikációját a 172.31.0.100 címen:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

A visszaadott kimenet tájékoztatja, hogy a **None** a **nextHopType**, és hogy a **routeTableId** szintén **rendszerútvonal.** Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Az útválasztás további elemzéséhez tekintse át a hálózati adapter hatályos útvonalait az [az network nic show-effective-route-table paranccsal:](/cli/azure/network/nic#az_network_nic_show_effective_route_table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

A visszaadott kimenet a következő szöveget tartalmazza:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Amikor a parancs használatával teszteli a `az network watcher show-next-hop` 13.107.21.200 címre kimenő kommunikációt  a [Következő](#use-next-hop)ugrás használatacím alatt, a rendszer a 0.0.0.0/0** címelőtagú útvonalat használta a forgalom címhez való útválasztásához, mivel a kimenetben egyetlen másik útvonal sem tartalmazza a címet. Alapértelmezés szerint minden olyan cím, amely nincs egy másik útvonal címelőtagjában meghatározva, az internetre lesz irányítva.

Amikor azonban a parancsot használta a 172.31.0.100 felé irányuló kimenő kommunikáció tesztelésére, az eredményből kiderült, hogy nem volt következő `az network watcher show-next-hop` ugrási típus. A visszaadott kimenetben a következő szöveg is látható:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Ahogy a parancs kimenetében látható, bár van egy alapértelmezett útvonal `az network watcher nic show-effective-route-table` a 172.16.0.0/12 előtaghoz, amely tartalmazza a 172.31.0.100 címet, a **nextHopType** a **Nincs.** Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például hozzáadta a 172.16.0.0/12 címtartományt a virtuális hálózat címtartományához, az  Azure a **nextHopType** típust Virtuális hálózatra módosítja az útvonalhoz. Az ellenőrzés ekkor a **következőhoptípusként** mutatja a **virtuális hálózatot:**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozott egy virtuális gépet, és diagnosztizálta a virtuális gépről származó hálózati útválasztást. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Kimenő virtuálisgép-kapcsolatok esetén a virtuális gép és a végpont közötti késést, valamint a virtuális gép és a végpont közötti engedélyezett és tiltott hálózati forgalmat az Network Watcher kapcsolati [hibaelhárítási képességével állapíthatja](network-watcher-connectivity-cli.md) meg. A virtuális gépek és a végpontok közötti kommunikációt ( például IP-címet vagy URL-címet) a kapcsolatmonitor funkcióval Network Watcher figyelheti. További információ: [Hálózati kapcsolat figyelése.](connection-monitor.md)
