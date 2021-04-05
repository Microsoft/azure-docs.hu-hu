---
title: Hálózati forgalom irányítása – Azure CLI | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan irányíthatja át a hálózati forgalmat egy útválasztási táblázattal az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 043d450a1b968174ad263579d39de06a296a98e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94741485"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Hálózati forgalom irányítása útválasztási táblázattal az Azure CLI használatával

Egy adott virtuális hálózaton belül az Azure alapértelmezés szerint automatikusan elosztja a forgalmat az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Útválasztási táblázat létrehozása
* Útvonal létrehozása
* Több alhálózattal rendelkező virtuális hálózat létrehozása
* Útválasztási táblázat társítása alhálózattal
* Forgalmat irányító hálózati virtuális berendezés létrehozása
* Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
* Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Az útválasztási táblázat létrehozása előtt hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal az ebben a cikkben létrehozott összes erőforráshoz. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy útválasztási táblázatot az [az Network Route-Table Create](/cli/azure/network/route-table#az-network-route-table-create)paranccsal. A következő példa egy *myroutetablepublic elemet* nevű útválasztási táblázatot hoz létre. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat az útválasztási táblában az [az Network Route-Table Route Create](/cli/azure/network/route-table/route#az-network-route-table-route-create)paranccsal. 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Ahhoz, hogy egy útválasztási táblázatot alhálózathoz lehessen rendelni, létre kell hoznia egy virtuális hálózatot és alhálózatot. Hozzon létre egy alhálózattal rendelkező virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet)paranccsal.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre két további alhálózatot az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet)paranccsal.

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Társítsa a *myroutetablepublic elemet* útválasztási táblázatot a *nyilvános* alhálózathoz az [az Network vnet subnet Update paranccsal](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

Hozzon létre egy NVA a *DMZ* -alhálózatban az [az VM Create](/cli/azure/vm)paranccsal. Amikor létrehoz egy virtuális gépet, az Azure alapértelmezés szerint létrehoz és hozzárendel egy nyilvános IP-címet a virtuális géphez. A `--public-ip-address ""` paraméter arra utasítja az Azure-t, hogy ne hozzon létre és rendeljen nyilvános IP-címet a virtuális géphez, mivel a virtuális gépnek nem kell az internetről csatlakoznia. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépéssel, amíg az Azure befejezi a virtuális gép létrehozását, és visszaadja a virtuális gép kimenetét. 

Ahhoz, hogy egy hálózati adapter továbbíthassa a neki küldött, de nem a saját IP-címére címzett forgalmat, engedélyeznie kell az IP-továbbítást a hálózati adapteren. Engedélyezze az IP-továbbítást a hálózati adapter számára az [az Network NIC Update paranccsal](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Engedélyezze a virtuális gép operációs rendszerén belüli IP-továbbítást az [az VM Extension set](/cli/azure/vm/extension)paranccsal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

A parancs végrehajtása akár egy percet is igénybe vehet.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton, így ellenőrizheti, hogy a *nyilvános* alhálózatról érkező forgalom a NVA egy későbbi lépésben átirányítja-e a *privát* alhálózatra. 

Hozzon létre egy virtuális gépet a *nyilvános* alhálózaton az [az VM Create](/cli/azure/vm)paranccsal. A `--no-wait` paraméter lehetővé teszi, hogy az Azure végrehajtsa a parancsot a háttérben, így továbbra is folytathatja a következő parancsot. A cikk egyszerűsítése érdekében a rendszer jelszót használ. A kulcsokat jellemzően éles környezetben használják. Ha kulcsokat használ, az SSH-ügynök továbbítását is konfigurálnia kell. További információkért tekintse meg az SSH-ügyfél dokumentációját. Cserélje le a `<replace-with-your-password>` parancsot a következő parancsra a választott jelszóval.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Hozzon létre egy virtuális gépet a *privát* alhálózatban.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Jegyezze fel a **publicIpAddress** értékét. Ez a címe egy későbbi lépésben a virtuális gép internetről való elérésére szolgál.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

A következő parancs használatával hozzon létre egy SSH-munkamenetet a *myVmPrivate* virtuális géppel. Cserélje le a *\<publicIpAddress>* t a virtuális gép nyilvános IP-címére. A fenti példában az IP-cím *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Ha a rendszer jelszót kér, adja meg a [virtuális gépek létrehozásakor](#create-virtual-machines)kiválasztott jelszót.

A következő parancs használatával telepítse a nyomkövetési útvonalat a *myVmPrivate* virtuális gépre:

```bash
sudo apt-get install traceroute
```

A következő parancs használatával tesztelheti a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztását a *myVmPrivate* virtuális gépről.

```bash
traceroute myVmPublic
```

A válasz a következő példához hasonló:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy a rendszer a *myVmPrivate* virtuális gépről közvetlenül a *myVmPublic* virtuális gépre irányítja a forgalmat. Az Azure alapértelmezett útvonalai közvetlenül az alhálózatok között irányítják a forgalmat. 

Használja az alábbi parancsot az SSH-val a *myVmPublic* virtuális gépre a *myVmPrivate* virtuális gépről:

```bash
ssh azureuser@myVmPublic
```

A következő parancs használatával telepítse a nyomkövetési útvonalat a *myVmPublic* virtuális gépre:

```bash
sudo apt-get install traceroute
```

A következő parancs használatával tesztelheti a *myVmPrivate* virtuális gépre irányuló hálózati forgalom útválasztását a *myVmPublic* virtuális gépről.

```bash
traceroute myVmPrivate
```

A válasz a következő példához hasonló:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy az első ugrás a 10.0.2.4 cím, amely az NVA magánhálózati IP-címe. A második ugrás a 10.0.1.4 cím – ez a *myVmPrivate* virtuális gép magánhálózati IP-címe. A *myRouteTablePublic* útválasztási táblázathoz hozzáadott és a *Magánjellegű* alhálózathoz rendelt útvonal miatt az Azure az NVA-n keresztül továbbította a forgalmat ahelyett, hogy közvetlenül a *Privát* alhálózatra továbbította volna.

Az SSH-munkamenetek bezárásával a *myVmPublic* és a *myVmPrivate* virtuális gépek is megtalálhatók.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozta az útválasztási táblázatot, és hozzárendelte azt egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) számos előre konfigurált NVA-t helyezhet üzembe, amelyek olyan hálózati funkciókat végeznek, mint például a tűzfal és a WAN-optimalizálás. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. További információ: a [hálózati hozzáférés korlátozása a Pásti-erőforrásokhoz](tutorial-restrict-network-access-to-resources-cli.md).
