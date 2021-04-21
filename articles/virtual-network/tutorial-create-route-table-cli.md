---
title: Hálózati forgalom útválasztása – Azure CLI-| Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan irányítható a hálózati forgalom egy útvonaltáblával az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ff643c39820fa529c8678c7a36881dd25da354c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762496"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Hálózati forgalom útválasztási táblázattal való útválasztása az Azure CLI használatával

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

- Ehhez a cikkhez az Azure CLI 2.0.28-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Mielőtt létrehoz egy útvonaltáblát, hozzon létre egy erőforráscsoportot [az az group create](/cli/azure/group) segítségével a cikkben létrehozott összes erőforráshoz. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy útvonaltáblát [az az network route-table create gombra.](/cli/azure/network/route-table#az_network_route_table_create) A következő példában létrehozunk egy *myRouteTablePublic nevű útválasztási táblázatot.* 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat az útvonaltáblában [az az network route-table route create gombra.](/cli/azure/network/route-table/route#az_network_route_table_route_create) 

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

Mielőtt alhálózathoz társíthat egy útválasztási táblázatot, létre kell hoznia egy virtuális hálózatot és egy alhálózatot. Hozzon létre egy virtuális hálózatot egy alhálózattal [az az network vnet create gombra.](/cli/azure/network/vnet)

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre két további alhálózatot [az az network vnet subnet create segítségével.](/cli/azure/network/vnet/subnet)

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

Társítsa a *myRouteTablePublic* útválasztási táblázatot a *Nyilvános* alhálózathoz [az az network vnet subnet update frissítéssel.](/cli/azure/network/vnet/subnet)

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

Hozzon létre egy NVA-t a *DMZ* alhálózatban [az az vm create segítségével.](/cli/azure/vm) Virtuális gép létrehozásakor az Azure alapértelmezés szerint létrehoz és hozzárendel egy nyilvános IP-címet a virtuális géphez. A paraméter arra utasítja az Azure-t, hogy ne hozzon létre és rendeljen hozzá nyilvános IP-címet a virtuális géphez, mivel a virtuális gépnek nem kell az `--public-ip-address ""` internetről csatlakozva lennie. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépéssel, amíg az Azure be nem fejezi a virtuális gép létrehozását, és vissza nem adja a virtuális gép kimenetét. 

Ahhoz, hogy egy hálózati adapter továbbíthassa a neki küldött, de nem a saját IP-címére címzett forgalmat, engedélyeznie kell az IP-továbbítást a hálózati adapteren. Engedélyezze az IP-továbbítást a hálózati adapter számára [az az network nic update parancs használatával.](/cli/azure/network/nic)

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Engedélyezze az IP-továbbítást a virtuális gép operációs rendszerén [az az vm extension set beállítással:](/cli/azure/vm/extension)

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

Hozzon létre két virtuális gépet a virtuális hálózatban, így egy későbbi  lépésben ellenőrizheti, hogy a nyilvános alhálózatról származó forgalom a Privát alhálózatra lesz-e irányítva az NVA-n keresztül.  

Hozzon létre egy virtuális gépet a *Nyilvános* alhálózaton [az az vm create segítségével.](/cli/azure/vm) A paraméter lehetővé teszi, hogy az Azure végrehajtsa a parancsot a `--no-wait` háttérben, így folytathatja a következő paranccsal. A cikk leegyszerűsítése érdekében a rendszer jelszót használ. A kulcsokat általában éles környezetben használják. Kulcsok használata esetén konfigurálnia kell az SSH-ügynökátirányítást is. További információért tekintse meg az SSH-ügyfél dokumentációját. A `<replace-with-your-password>` következő parancsban cserélje le a helyére a választott jelszót.

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

Hozzon létre egy virtuális gépet a *Privát* alhálózaton.

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jeleníti meg: 

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

Jegyezze fel a **publicIpAddress** értékét. Ez a cím a virtuális gép internetről való elérésére használható egy későbbi lépésben.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

A következő paranccsal hozzon létre SSH-munkamenetet a *myVmPrivate virtuális géphez.* Cserélje *\<publicIpAddress>* le a helyére a virtuális gép nyilvános IP-címét. A fenti példában az IP-cím *a következő: 13.90.242.231.*

```bash
ssh azureuser@<publicIpAddress>
```

Amikor a rendszer jelszót kér, adja meg a Virtuális gépek [létrehozása mezőben kiválasztott jelszót.](#create-virtual-machines)

A következő paranccsal telepítse a nyomkövetési útvonalat a *myVmPrivate virtuális gépre:*

```bash
sudo apt-get install traceroute
```

A következő paranccsal tesztelje a *myVmPublic* virtuális gépre kimenő hálózati forgalom útválasztását a *myVmPrivate virtuális gépről.*

```bash
traceroute myVmPublic
```

A válasz a következő példához hasonló:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy a rendszer a *myVmPrivate* virtuális gépről közvetlenül a *myVmPublic* virtuális gépre irányítja a forgalmat. Az Azure alapértelmezett útvonalai közvetlenül az alhálózatok között irányítják a forgalmat. 

Használja a következő parancsot a *myVmPrivate virtuális gépről a myVmPublic virtuális* géphez való *SSH-hoz:*

```bash
ssh azureuser@myVmPublic
```

A következő paranccsal telepítse a nyomkövetési útvonalat a *myVmPublic virtuális* gépen:

```bash
sudo apt-get install traceroute
```

A következő paranccsal tesztelje a *myVmPublic* virtuális gépről a *myVmPrivate* virtuális gépre kimenő hálózati forgalom útválasztását.

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

Zárja be az SSH-munkameneteket a *myVmPublic* és *a myVmPrivate virtuális* gépeken.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [az az group delete](/cli/azure/group) parancs használatával távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozott egy útvonaltáblát, és társította azt egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) számos előre konfigurált NVA-t helyezhet üzembe, amelyek olyan hálózati funkciókat végeznek, mint például a tűzfal és a WAN-optimalizálás. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. További információ: [A PaaS-erőforrásokhoz](tutorial-restrict-network-access-to-resources-cli.md)való hálózati hozzáférés korlátozása.
