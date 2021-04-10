---
title: Virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása – Azure CLI
titleSuffix: Azure Network Watcher
description: Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja a virtuálisgép-hálózati útválasztási problémát az Azure CLI használatával a következő ugrási képességgel az Azure Network Watcher-ban.
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
ms.openlocfilehash: 415fcc72116cc36644b58b619404d96ff63b024d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065910"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása – Azure CLI

Ebben a cikkben üzembe helyez egy virtuális gépet (VM), majd megtekintheti a kommunikációt egy IP-címmel és egy URL-címmel. Meghatározza a kommunikációs hiba okát és feloldásának módját.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

- A cikkben szereplő Azure CLI-parancsok egy bash-rendszerhéjban történő futtatásra vannak formázva.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A következő példa egy *myVm* nevű virtuális gépet hoz létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása előtt ne folytassa a hátralévő lépéseket, és az Azure CLI kimenetet ad vissza.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A Network Watcherával folytatott hálózati kommunikáció teszteléséhez először engedélyeznie kell egy hálózati figyelőt abban a régióban, ahol a tesztelni kívánt virtuális gép be van kapcsolva, majd a kommunikáció teszteléséhez használja a Network Watcher következő ugrási képességét.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha az USA keleti régiójában már engedélyezve van egy hálózati figyelő, ugorjon a [következő ugrás használata](#use-next-hop)lehetőségre. Az az [Network Watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) parancs használatával hozzon létre egy Network watchert az USA keleti régiójában:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. Egy virtuális gép útválasztásának teszteléséhez használja az [az Network Watcher show-Next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) elemet a következő útválasztási ugrás meghatározásához, ha a forgalom egy adott címnek van szánva.

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

Néhány másodperc elteltével a kimenet tájékoztatja Önt arról, hogy a **NextHopType** **internetes**, és hogy a **routeTableId** a **rendszer útvonala**. Ebből az eredményből megtudhatja, hogy van-e érvényes útvonal a célhelyre.

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

A visszaadott kimenet tájékoztatja, hogy **egyik sem** a **nextHopType**, és hogy a **routeTableId** is a **rendszer útvonala**. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Az Útválasztás további elemzéséhez tekintse át a hálózati adapter érvényes útvonalait az az [Network NIC show-effektív-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) paranccsal:

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

Ha a parancs használatával `az network watcher show-next-hop` teszteli a 13.107.21.200 kimenő kommunikációját a [következő ugrás használata](#use-next-hop)során, a rendszer a 0.0.0.0/0 * * **addressPrefix** útvonal használatával irányítja át a forgalmat a címnek, mivel a kimenetben nem szerepel más útvonal a címben. Alapértelmezés szerint minden olyan cím, amely nincs egy másik útvonal címelőtagjában meghatározva, az internetre lesz irányítva.

Ha a parancsot a `az network watcher show-next-hop` 172.31.0.100 kimenő kommunikáció tesztelésére használta, az eredmény azonban arról tájékoztat, hogy nem volt következő ugrási típus. A visszaadott kimenetben a következő szöveg is látható:

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

Ahogy a parancs kimenetében látható `az network watcher nic show-effective-route-table` , de a 172.16.0.0/12 előtag alapértelmezett útvonala, amely tartalmazza a 172.31.0.100-címeket, a **NextHopType** értéke **none**. Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például hozzáadta a 172.16.0.0/12 címtartományt a virtuális hálózat címterület számára, az Azure a **NextHopType** **virtuális hálózatra** módosítja az útvonalon. Az ellenőrzések után a **virtuális hálózat** **nextHopType** jelenik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozta a virtuális gépet, és diagnosztizálta a hálózati útválasztást a virtuális gépről. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

A kimenő virtuálisgép-kapcsolatok esetében meghatározhatja a virtuális gép és a végpont közötti hálózati forgalmat a Network Watcher [kapcsolódási hibákkal kapcsolatos](network-watcher-connectivity-cli.md) funkciójának használatával is. Egy virtuális gép és egy végpont (például IP-cím vagy URL) közötti kommunikációt figyelheti az Network Watcher a kapcsolat figyelője funkciójának használatával. További információ: [hálózati kapcsolatok figyelése](connection-monitor.md).
