---
title: Hálózati forgalom átirányítása Azure PowerShell | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan irányíthatja a hálózati forgalmat útválasztási táblával a PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5bd52e8865bb704497740851f6a0e3c886ed9d6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790204"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Hálózati forgalom irányítása útválasztási táblázattal a PowerShell használatával

Egy adott virtuális hálózaton belül az Azure alapértelmezés szerint automatikusan elosztja a forgalmat az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Útválasztási táblázat létrehozása
* Útvonal létrehozása
* Több alhálózattal rendelkező virtuális hálózat létrehozása
* Útválasztási táblázat társítása alhálózattal
* Forgalmat irányító hálózati virtuális berendezés létrehozása
* Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
* Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Az útválasztási táblázat létrehozása előtt hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a cikkben létrehozott összes erőforráshoz.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy útválasztási táblázatot a [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). A következő példa egy *myroutetablepublic elemet* nevű útválasztási táblázatot hoz létre.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat az útválasztási táblázat objektum [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)való lekérésével, hozzon létre egy útvonalat az [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)paranccsal, majd írja be az útvonal-konfigurációt az útválasztási táblázatba a [set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Ahhoz, hogy egy útválasztási táblázatot alhálózathoz lehessen rendelni, létre kell hoznia egy virtuális hálózatot és alhálózatot. Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy *myVirtualNetwork* nevű virtuális hálózatot a *10.0.0.0/16* előtaggal.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre három alhálózatot három alhálózat konfigurációjának létrehozásával a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Az alábbi példa három alhálózati konfigurációt hoz létre a *nyilvános*, a *privát* és a *DMZ* -alhálózatokhoz:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Írja be az alhálózati konfigurációkat a virtuális hálózatba a [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), amely létrehozza az alhálózatokat a virtuális hálózatban:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Társítsa a *myroutetablepublic elemet* útválasztási táblázatot a *nyilvános* alhálózathoz a [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) , majd írja be az alhálózati konfigurációt a virtuális hálózatra a [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)használatával.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $myRouteTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

A virtuális gép létrehozása előtt hozzon létre egy hálózati adaptert.

### <a name="create-a-network-interface"></a>Hálózati adapter létrehozása

Hálózati adapter létrehozása előtt le kell kérnie a virtuális hálózati azonosítót a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), majd az alhálózati azonosítót a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)paranccsal. Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) a *DMZ* -alhálózatban, AMELYEN engedélyezve van az IP-továbbítás:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Egy virtuális gép létrehozásához és egy meglévő hálózati adapter csatlakoztatásához először létre kell hoznia egy VM-konfigurációt a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). A konfiguráció tartalmazza az előző lépésben létrehozott hálózati adaptert. Ha a rendszer a Felhasználónév és a jelszó megadását kéri, válassza ki azt a felhasználónevet és jelszót, amellyel be szeretné jelentkezni a virtuális gépre.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Hozza létre a virtuális gépet a VM-konfigurációval a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. A következő példa egy *myVmNva* nevű virtuális gépet hoz létre.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A `-AsJob` lehetőség a háttérben hozza létre a virtuális gépet, így folytathatja a következő lépéssel.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton, hogy ellenőrizni tudja, hogy a *nyilvános* alhálózatról érkező forgalmat a rendszer egy későbbi lépésben a hálózati virtuális berendezésen keresztül irányítja-e a *privát* alhálózatra.

Hozzon létre egy virtuális gépet a *nyilvános* alhálózatban a [New-AzVM](/powershell/module/az.compute/new-azvm). Az alábbi példa egy *myVmPublic* nevű virtuális gépet hoz létre a *MyVirtualNetwork* virtuális hálózat *nyilvános* alhálózatában.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Hozzon létre egy virtuális gépet a *privát* alhálózatban.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépéssel, amíg létre nem jön a virtuális gép, és az Azure kimenetet ad vissza a PowerShellnek.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

A [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) használatával visszaállíthatja a *myVmPrivate* virtuális gép nyilvános IP-címét. A következő példa a *myVmPrivate* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancs használatával hozzon létre egy távoli asztali munkamenetet a *myVmPrivate* virtuális géppel a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.

Egy későbbi lépésben a parancs az `tracert.exe` Útválasztás tesztelésére szolgál. A tracert a Internet Control Message Protocol (ICMP) protokollt használja, amely a Windows tűzfalon keresztül megtagadva. Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPrivate* virtuális gépen a következő PowerShell-parancsot:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Bár a nyomkövetési útvonal a jelen cikkben található Útválasztás tesztelésére szolgál, a Windows tűzfalon az éles környezetekben való üzembe helyezéshez nem ajánlott az ICMP engedélyezése.

Az Azure-ban a virtuális gép hálózati adapterében, az IP-továbbítás engedélyezése szakaszban engedélyezte az IP-továbbítást. A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Engedélyezze az IP-továbbítást a *myVmNva* operációs rendszerén belül.

A *myVmPrivate* virtuális gépen egy parancssorból a Távoli asztalról a *myVmNva*:

``` 
mstsc /v:myvmnva
```

Az operációs rendszeren belüli IP-továbbítás engedélyezéséhez adja meg a következő parancsot a PowerShellben a *myVmNva* virtuális gépről:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Indítsa újra a *myVmNva* virtuális gépet – ez megszakítja a távoli asztali munkamenetet is.

Miközben továbbra is csatlakozik a *myVmPrivate* virtuális géphez, hozzon létre távoli asztali kapcsolatot a *myVmPublic* virtuális géppel, miután a *myVmNva* virtuális gép újraindult:

``` 
mstsc /v:myVmPublic
```

Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPublic* virtuális gépen a következő PowerShell-parancsot:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

A *myVmPublic* virtuális gépről a *myVmPrivate* virtuális gépre irányuló hálózati forgalom útválasztásának teszteléséhez adja meg a következő parancsot a PowerShellben a *myVmPublic* virtuális gépen:

```
tracert myVmPrivate
```

A válasz a következő példához hasonló:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

Láthatja, hogy az első ugrás a 10.0.2.4 cím, amely az NVA magánhálózati IP-címe. A második ugrás a 10.0.1.4 cím – ez a *myVmPrivate* virtuális gép magánhálózati IP-címe. A *myRouteTablePublic* útválasztási táblázathoz hozzáadott és a *Magánjellegű* alhálózathoz rendelt útvonal miatt az Azure az NVA-n keresztül továbbította a forgalmat ahelyett, hogy közvetlenül a *Privát* alhálózatra továbbította volna.

Zárja be a *myVmPublic* virtuális gépre irányuló távoli asztali munkamenetet. Ez nem bontja a *myVmPrivate* virtuális géppel való kapcsolatot.

A *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztásának teszteléséhez adja meg a következő parancsot egy parancssorban a *myVmPrivate* virtuális gépen:

```
tracert myVmPublic
```

A válasz a következő példához hasonló:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Láthatja, hogy a rendszer a *myVmPrivate* virtuális gépről közvetlenül a *myVmPublic* virtuális gépre irányítja a forgalmat. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozta az útválasztási táblázatot, és hozzárendelte azt egy alhálózathoz. Létrehozott egy egyszerű hálózati virtuális készüléket, amely egy nyilvános alhálózatról egy privát alhálózatra irányítja a forgalmat. Helyezzen üzembe számos előre konfigurált hálózati virtuális készüléket, amelyek olyan hálózati funkciókat végeznek, mint például a tűzfal és a WAN-optimalizálás az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. További információ: a [hálózati hozzáférés korlátozása a Pásti-erőforrásokhoz](tutorial-restrict-network-access-to-resources-powershell.md).
