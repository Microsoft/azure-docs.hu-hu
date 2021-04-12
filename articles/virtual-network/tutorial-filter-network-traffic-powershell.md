---
title: Hálózati forgalom szűrése – Azure PowerShell | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan szűrheti a hálózati forgalmat egy alhálózatra hálózati biztonsági csoporttal a PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9dca3d95f9fe810c90c27a32250d1b8ffcf89ac8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065344"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Hálózati forgalom szűrése hálózati biztonsági csoporttal a PowerShell használatával

A virtuális hálózatok alhálózatainak bejövő vagy kimenő hálózati forgalmát hálózati biztonsági csoport segítségével szűrheti. A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak, amelyek IP-cím, port és protokoll szerint szűrik a hálózati forgalmat. A biztonsági szabályok az egyes alhálózatokban üzembe helyezett erőforrásokra vonatkoznak. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hálózati biztonsági csoport és biztonsági szabályok létrehozása
* Virtuális hálózat létrehozása és hálózati biztonsági csoport hozzárendelése egy alhálózathoz
* Virtuális gépek üzembe helyezése egy alhálózaton
* Forgalomszűrők tesztelése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak. A biztonsági szabályok egy forrást és egy célt határoznak meg. A források és célok lehetnek alkalmazásbiztonsági csoportok.

### <a name="create-application-security-groups"></a>Alkalmazásbiztonsági csoportok létrehozása

Először hozzon létre egy erőforráscsoportot az ebben a cikkben létrehozott összes erőforráshoz a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példa létrehoz egy erőforráscsoportot az *eastus* helyen:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy alkalmazás biztonsági csoportot a [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup). Az alkalmazásbiztonsági csoportok lehetővé teszik, hogy csoportokba rendezze a hasonló portszűrési követelményekkel rendelkező kiszolgálókat. Az alábbi példa két alkalmazásbiztonsági csoportot hoz létre.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Biztonsági szabályok létrehozása

Hozzon létre egy biztonsági szabályt a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Az alábbi példa egy olyan szabályt hoz létre, amely engedélyezi az internetről a *myWebServers* alkalmazásbiztonsági csoportba bejövő forgalmat a 80-as és 443-as porton keresztül.

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

Ebben a cikkben az RDP (3389-es port) elérhető az interneten a *myAsgMgmtServers* virtuális géphez. Éles környezet esetében a 3389-es port közvetlenül az internetre való csatlakoztatása helyett javasolt [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [privát](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hálózati kapcsolat használatával csatlakozni a kezelni kívánt Azure-erőforrásokhoz.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Az alábbi példa egy *myNsg* nevű hálózati biztonsági csoportot hoz létre:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa egy *myVirtualNetwork* virtuális hálózatot hoz létre:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)paranccsal, majd írja az alhálózat konfigurációját a virtuális hálózatra a [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)használatával. Az alábbi példa egy *mySubnet* nevű alhálózatot ad hozzá a virtuális hálózathoz, és hozzárendeli a *myNsg* hálózati biztonsági csoportot.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális gépek létrehozása előtt kérje le a Virtual Network objektumot a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)alhálózattal:

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Hozzon létre egy nyilvános IP-címet az egyes virtuális gépekhez a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Hozzon létre két hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface), és rendeljen hozzá egy nyilvános IP-címet a hálózati adapterhez. Az alábbi példa létrehoz egy hálózati adaptert, hozzárendeli a *myVmWeb* nyilvános IP-címet, és felveszi azt a *myAsgWebServers* alkalmazásbiztonsági csoport tagjai közé:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Az alábbi példa létrehoz egy hálózati adaptert, hozzárendeli a *myVmMgmt* nyilvános IP-címet, és felveszi azt a *myAsgMgmtServers* alkalmazásbiztonsági csoport tagjai közé:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Hozzon létre két virtuális gépet a virtuális hálózatban, hogy érvényesíthesse majd a fogalom szűrését egy később lépésben.

Hozzon létre egy új VM-konfigurációt a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig), majd hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). Az alábbi példa egy virtuális gépet hoz létre, amely webkiszolgálóként fog szolgálni. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel.

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Hozzon létre egy felügyeleti kiszolgálóként szolgáló virtuális gépet:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

A virtuális gép létrehozása néhány percet vesz igénybe. Ne ugorjon a következő lépésre, amíg az Azure be nem fejezte a virtuális gép létrehozását.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

A [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) használatával visszaállíthatja egy virtuális gép nyilvános IP-címét. Az alábbi példa a *myVmMgmt* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Az alábbi parancs használatával hozzon létre egy távoli asztali munkamenetet a *myVmMgmt* virtuális géppel a helyszíni számítógépéről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.

A kapcsolat sikeresen létrejön, mert a 3389-es porton engedélyezve van az internetről a *myAsgMgmtServers* alkalmazásbiztonsági csoportba (amelyben a *myVmMgmt* virtuális géphez csatlakoztatott hálózati adapter található) bejövő forgalom.

Az alábbi parancs PowerShellben történő használatával hozzon létre egy távoli asztali kapcsolatot a *myVmWeb* virtuális géppel a *myVmMgmt* virtuális gépről:

``` 
mstsc /v:myvmWeb
```

A kapcsolat sikeresen létrejön, mert az egyes hálózati biztonsági csoportokon belüli alapértelmezett biztonsági szabály minden porton keresztül engedélyezi a forgalmat a virtuális hálózaton belüli összes IP-cím között. Nem hozhat létre távoli asztali kapcsolatot a *myVmWeb* virtuális géppel az internetről, mert a *myAsgWebServers* biztonsági szabálya nem engedélyezi az internetről bejövő forgalmat a 3389-es porton keresztül.

Az alábbi parancs PowerShellben történő használatával telepítse a Microsoft IIS-t a *myVmWeb* virtuális gépre:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Az IIS telepítésének befejezése után csatlakozzon le a *myVmWeb* virtuális gépről. Így csak a *myVmMgmt* virtuális géppel létesített távoli asztali kapcsolat marad. Az IIS-üdvözlőképernyő megtekintéséhez nyisson meg egy webböngészőt, és keresse meg a http: \/ /myVmWeb.

Csatlakozzon le a *myVmMgmt* virtuális gépről.

A számítógépén írja be az alábbi parancsot a PowerShellben a *myVmWeb* kiszolgáló nyilvános IP-címének lekéréséhez.

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Annak megerősítéséhez, hogy eléri a *myVmWeb* webkiszolgálót az Azure-on kívülről is, nyisson meg egy webböngészőt a számítógépen, és lépjen a következő helyre: `http://<public-ip-address-from-previous-step>`. A kapcsolat sikeresen létrejön, mert a 80-as porton engedélyezve van az internetről a *myAsgWebServers* alkalmazásbiztonsági csoportba (amelyben a *myVmWeb* virtuális géphez csatlakoztatott hálózati adapter található) bejövő forgalom.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozott egy hálózati biztonsági csoportot, és hozzárendelte azt egy virtuális hálózati alhálózathoz. A hálózati biztonsági csoportokkal kapcsolatos további információ: [Hálózati biztonsági csoportok áttekintése](./network-security-groups-overview.md) és [Hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

Az Azure alapértelmezés szerint irányítja a forgalmat az alhálózatok között. Ehelyett lehetősége van arra, hogy egy virtuális gépen keresztül irányítsa a forgalmat az alhálózatok között, amely így például tűzfalként is szolgálhat. További információt az [útválasztási táblázat létrehozása](tutorial-create-route-table-powershell.md)című témakörben talál.