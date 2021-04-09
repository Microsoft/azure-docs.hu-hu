---
title: Virtuális hálózatok összekötése a VNet-Azure PowerShell
description: Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a virtuális hálózatok virtuális hálózati kapcsolattal Azure PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d93efaedbb0596382b0601a17098311e075618b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791980"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuális hálózatok összekötése virtuális hálózattal a PowerShell használatával

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Két virtuális gép létrehozása
* Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
* Virtuális gép üzembe helyezése mindkét virtuális hálózaton
* Virtuális gépek közötti kommunikáció

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz, és az ebben a cikkben létrehozott összes többi erőforrást. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy *myVirtualNetwork1* nevű virtuális hálózatot a *10.0.0.0/16* előtaggal.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Az alábbi példa egy alhálózati konfigurációt hoz létre egy 10.0.0.0/24 előtaggal:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Írja be az alhálózat konfigurációját a virtuális hálózatba a [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), amely létrehozza az alhálózatot:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Hozzon létre egy 10.1.0.0/16 előtaggal és egy alhálózattal rendelkező virtuális hálózatot:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

Hozzon létre egy társ-létrehozást a [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). A következő példában szereplő *myVirtualNetwork1* a *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **PeeringState** *inicializálva* van. A társítás a *kezdeményezett* állapotban marad, amíg létre nem hozza a *myVirtualNetwork2* -ből a *myVirtualNetwork1*-be. Hozzon létre egy társat a *myVirtualNetwork2* -ből a *myVirtualNetwork1*-be.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **PeeringState** *csatlakoztatva* van. Az Azure a *myVirtualNetwork1-myVirtualNetwork2* peering társítási állapotát is megváltoztatta a *csatlakozáshoz*. Győződjön meg arról, hogy a *myVirtualNetwork1-myVirtualNetwork2-* társítás társítási állapota a [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering)-vel való *csatlakozásra* módosult.

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Az egyik virtuális hálózat erőforrásai nem tudnak kommunikálni a másik virtuális hálózatban lévő erőforrásokkal, amíg a **PeeringState** mindkét virtuális hálózatban *csatlakoztatva* van.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). Az alábbi példa egy *myVm1* nevű virtuális gépet hoz létre a *myVirtualNetwork1* virtuális hálózaton. A `-AsJob` lehetőség a háttérben hozza létre a virtuális gépet, így folytathatja a következő lépéssel. Ha a rendszer kéri, adja meg azt a felhasználónevet és jelszót, amelyhez be szeretné jelentkezni a virtuális gépre.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a későbbi lépéseket, amíg az Azure létrehozza a virtuális gépet, és visszaadja a kimenetet a PowerShellnek.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A virtuális gép nyilvános IP-címéhez is csatlakozhat az internetről. A [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) használatával visszaállíthatja egy virtuális gép nyilvános IP-címét. Az alábbi példa a *myVm1* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A következő parancs használatával hozzon létre egy távoli asztali munkamenetet a *myVm1* virtuális géppel a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Létrejön egy RDP protokoll (. rdp) fájl, amely le van töltve a számítógépre, és meg van nyitva. Adja meg a felhasználónevet és a jelszót (lehetséges, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** kell választania, majd **másik fiókot kell használnia**), majd kattintson **az OK** gombra. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

A *myVm1* virtuális gépen engedélyezze a Internet Control Message Protocol (ICMP) szolgáltatást a Windows tűzfalon keresztül, hogy egy későbbi lépésben Pingelje a virtuális gépet a *MyVm2* , a PowerShell használatával:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Bár a ping a jelen cikkben található virtuális gépek közötti kommunikációra szolgál, nem ajánlott az ICMP-t a Windows tűzfalon keresztül éles környezetekben használni.

A *myVm2* virtuális géphez való csatlakozáshoz adja meg a következő parancsot egy parancssorban a *myVm1* virtuális gépen:

```
mstsc /v:10.1.0.4
```

Mivel engedélyezte a ping szolgáltatást a *myVm1*-on, mostantól az IP-cím alapján pingelheti azt egy parancssorból a *myVm2* virtuális gépen:

```
ping 10.0.0.4
```

Négy választ kap. Válassza le az RDP-munkameneteket a *myVm1* és *myVm2* virtuális gépről egyaránt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan csatlakoztathatók a két hálózat ugyanabban az Azure-régióban, virtuális hálózattal. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#powershell) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

A [saját számítógépét VPN-kapcsolaton keresztül is összekapcsolhatjuk egy virtuális hálózattal](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , és a virtuális hálózatban lévő erőforrásokkal és a társ virtuális hálózatokkal is kezelhetik. A virtuális hálózati cikkekben tárgyalt feladatok számos feladatának elvégzéséhez újrafelhasználható parancsfájlok esetén lásd: [parancsfájl-minták](powershell-samples.md).
