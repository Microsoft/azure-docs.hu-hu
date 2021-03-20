---
title: Virtuális hálózatok összekötése VNet-társítással – Azure CLI
description: Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a virtuális hálózatok virtuális hálózati kapcsolattal az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c6399e437fa314aa82e0b41cbf8a170ea3ab72e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94741773"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Virtuális hálózatok összekötése virtuális hálózati kapcsolattal az Azure CLI használatával

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Két virtuális gép létrehozása
* Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
* Virtuális gép üzembe helyezése mindkét virtuális hálózaton
* Virtuális gépek közötti kommunikáció

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz, és az ebben a cikkben létrehozott összes többi erőforrást. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. A következő példában létrehozunk egy *myVirtualNetwork1* nevű virtuális hálózatot a *10.0.0.0/16* előtaggal.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre egy *myVirtualNetwork2* nevű virtuális hálózatot a *10.1.0.0/16* előtaggal:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

A virtuális hálózati azonosítók között létrejöttek a társítások, ezért először le kell kérnie az egyes virtuális hálózatok AZONOSÍTÓját az [az Network vnet show](/cli/azure/network/vnet) paranccsal, és TÁROLJA az azonosítót egy változóban.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Hozzon létre egy társat a *myVirtualNetwork1* -ből a *myVirtualNetwork2* az [az Network vnet peering Create](/cli/azure/network/vnet/peering)paranccsal. Ha a `--allow-vnet-access` paraméter nincs megadva, a rendszer létrehoz egy társítást, de a kommunikáció nem végezhető el rajta.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **PeeringState** *inicializálva* van. A társítás a *kezdeményezett* állapotban marad, amíg létre nem hozza a *myVirtualNetwork2* -ből a *myVirtualNetwork1*-be. Hozzon létre egy társat a *myVirtualNetwork2* -ből a *myVirtualNetwork1*-be. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **PeeringState** *csatlakoztatva* van. Az Azure a *myVirtualNetwork1-myVirtualNetwork2* peering társítási állapotát is megváltoztatta a *csatlakozáshoz*. Győződjön meg arról, hogy a *myVirtualNetwork1-myVirtualNetwork2-* társítás társítási állapota az [az Network vnet peering show](/cli/azure/network/vnet/peering)paranccsal *kapcsolódott* .

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Az egyik virtuális hálózat erőforrásai nem tudnak kommunikálni a másik virtuális hálózatban lévő erőforrásokkal, amíg a **peeringState** mindkét virtuális hálózatban *csatlakoztatva* van. 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa egy *myVm1* nevű virtuális gépet hoz létre a *myVirtualNetwork1* virtuális hálózaton. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A `--no-wait` lehetőség a háttérben hozza létre a virtuális gépet, így folytathatja a következő lépéssel.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Hozzon létre egy virtuális GÉPET a *myVirtualNetwork2* virtuális hálózaton.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Jegyezze fel a **publicIpAddress** értékét. Ez a címe egy későbbi lépésben a virtuális gép internetről való elérésére szolgál.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A következő parancs használatával hozzon létre egy SSH-munkamenetet a *myVm2* virtuális géppel. Cserélje le a `<publicIpAddress>` t a virtuális gép nyilvános IP-címére. Az előző példában a nyilvános IP-cím *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Pingelje a virtuális gépet a *myVirtualNetwork1*-ben.

```bash
ping 10.0.0.4 -c 4
```

Négy választ kap. 

Zárd be az SSH-munkamenetet a *myVm2* virtuális géphez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan csatlakoztathatók a két hálózat ugyanabban az Azure-régióban, virtuális hálózattal. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#cli) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

A [saját számítógépét VPN-kapcsolaton keresztül is összekapcsolhatjuk egy virtuális hálózattal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , és a virtuális hálózatban lévő erőforrásokkal és a társ virtuális hálózatokkal is kezelhetik. A virtuális hálózati cikkekben tárgyalt feladatok számos feladatának elvégzéséhez újrafelhasználható parancsfájlok esetén lásd: [parancsfájl-minták](cli-samples.md).
