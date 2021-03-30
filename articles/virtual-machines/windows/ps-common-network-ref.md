---
title: Azure-beli virtuális hálózatok általános PowerShell-parancsai
description: Gyakori PowerShell-parancsok a virtuális hálózatok és a virtuális gépekhez kapcsolódó erőforrások létrehozásának megkezdéséhez.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b4d6b20e63c42616aad0f8776fae159a0f2aa455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87088376"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure-beli virtuális hálózatok általános PowerShell-parancsai

Ha virtuális gépet szeretne létrehozni, létre kell hoznia egy [virtuális hálózatot](../../virtual-network/virtual-networks-overview.md) , vagy tudnia kell egy olyan meglévő virtuális hálózatról, amelybe a virtuális gép hozzá tud adni. A virtuális gépek létrehozásakor általában meg kell fontolnia a cikkben ismertetett erőforrások létrehozását is.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása).

Bizonyos változók hasznosak lehetnek, ha a cikkben szereplő parancsok közül többet futtat:

- $location – a hálózati erőforrások helye. A [Get-AzLocation](/powershell/module/az.resources/get-azlocation) használatával megkeresheti az Ön számára megfelelő [földrajzi régiót](https://azure.microsoft.com/regions/) .
- $myResourceGroup – annak az erőforráscsoportnak a neve, ahol a hálózati erőforrások találhatók.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

| Feladat | Parancs |
| ---- | ------- |
| Alhálózat-konfigurációk létrehozása |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1"-AddressPrefix XX. X. X. x/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-Name "mySubnet2"-AddressPrefix XX. X. X. X/XX<BR><BR>Egy tipikus hálózat rendelkezhet alhálózattal az [internetkapcsolattal rendelkező terheléselosztó](../../load-balancer/load-balancer-overview.md) és egy különálló alhálózat számára a [belső terheléselosztó](../../load-balancer/load-balancer-overview.md)számára. |
| Virtuális hálózat létrehozása |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $MyResourceGroup-Location $Location-AddressPrefix XX. X. X. x/XX-alhálózat $subnet 1, $subnet 2 |
| Egyedi tartománynév tesztelése |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -Domainnamelabel értékkel "myDNS" – hely $Location<BR><BR>Megadhat egy DNS-tartománynevet egy [nyilvános IP-erőforráshoz](../../virtual-network/public-ip-addresses.md), amely létrehoz egy leképezést a nyilvános IP-címhez az Azure által FELÜGYELt DNS-kiszolgálók domainname.location.cloudapp.Azure.com. A név csak betűket, számokat és kötőjelet tartalmazhat. Az első és az utolsó karakternek betűnek vagy számnak kell lennie, és a tartománynévnek egyedinek kell lennie az Azure-helyen belül. Ha a függvény **igaz** értéket ad vissza, a javasolt név globálisan egyedi. |
| Hozzon létre egy nyilvános IP-címet |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp"-ResourceGroupName $MyResourceGroup-Domainnamelabel értékkel "myDNS" – location $Location – AllocationMethod Dynamic<BR><BR>A nyilvános IP-cím a korábban tesztelt tartománynevet használja, amelyet a terheléselosztó előtér-konfigurációja használ. |
| Előtér-IP-konfiguráció létrehozása |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP"-PublicIpAddress $pip<BR><BR>A előtér-konfiguráció tartalmazza azt a nyilvános IP-címet, amelyet korábban a bejövő hálózati forgalomhoz hozott létre. |
| Hátércímkészlet létrehozása |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Belső címeket biztosít a terheléselosztó olyan hátteréhez, amely hálózati adapteren keresztül érhető el. |
| Mintavétel létrehozása |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe"-RequestPath "healthProbe. aspx"-Protocol http-port 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>A háttér-címkészlet virtuálisgép-példányainak rendelkezésre állásának vizsgálatához használt állapot-mintavételt tartalmaz. |
| Terheléselosztási szabály létrehozása |$lbRule = [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -Name http-frontendipconfiguration osztály $FrontendIP-BackendAddressPool $BeAddressPool-szonda $HealthProbe-Protocol TCP-FrontendPort 80-BackendPort 80<BR><BR>Olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját hozzárendelik a háttér-címkészlet egy portjához. |
| Bejövő NAT-szabály létrehozása |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1"-frontendipconfiguration osztály $FrontendIP-Protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját hozzárendelik egy adott virtuális gép portjához a háttér-címkészlet során. |
| Terheléselosztó létrehozása |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer"-location $Location-frontendipconfiguration osztály $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $beAddressPool-mintavétel $healthProbe |
| Hálózati adapter létrehozása |$nic 1 = [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC"-location $Location-privateipaddress tulajdonságot XX. x. X. x-alhálózat $subnet 2-LoadBalancerBackendAddressPool $LoadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $LoadBalancer. InboundNatRules [0]<BR><BR>Hozzon létre egy hálózati adaptert a korábban létrehozott nyilvános IP-cím és virtuális hálózati alhálózat használatával. |

## <a name="get-information-about-network-resources"></a>Hálózati erőforrásokkal kapcsolatos információk beolvasása

| Feladat | Parancs |
| ---- | ------- |
| Virtuális hálózatok listázása |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes virtuális hálózatát listázza. |
| Virtuális hálózattal kapcsolatos információk beolvasása |Get-AzVirtualNetwork neve: "myVNet" – ResourceGroupName $myResourceGroup |
| Virtuális hálózatban lévő alhálózatok listázása |Get-AzVirtualNetwork-Name "myVNet" – ResourceGroupName $myResourceGroup &#124; alhálózatok kiválasztása |
| Alhálózat adatainak beolvasása |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" – VirtualNetwork $vnet<BR><BR>Beolvassa a megadott virtuális hálózat alhálózatának adatait. A $vnet érték a Get-AzVirtualNetwork által visszaadott objektumot jelöli. |
| IP-címek listázása |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Felsorolja az erőforráscsoport nyilvános IP-címeit. |
| Load balancerek listázása |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes terheléselosztó listáját listázza. |
| Hálózati adapterek listázása |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Az erőforráscsoport összes hálózati adapterét listázza. |
| Hálózati csatolóval kapcsolatos információk beolvasása |Get-AzNetworkInterface neve: "myNIC" – ResourceGroupName $myResourceGroup<BR><BR>Egy adott hálózati adapter információinak beolvasása. |
| Hálózati adapter IP-konfigurációjának beolvasása |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" – hálózati adaptere $NIC<BR><BR>A megadott hálózati adapter IP-konfigurációjával kapcsolatos információk beolvasása. A $nic érték a Get-AzNetworkInterface által visszaadott objektumot jelöli. |

## <a name="manage-network-resources"></a>Hálózati erőforrások kezelése

| Feladat | Parancs |
| ---- | ------- |
| Alhálózat hozzáadása virtuális hálózathoz |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X. X. x/XX – név: "mySubnet1" – VirtualNetwork $vnet<BR><BR>Alhálózatot rendel egy meglévő virtuális hálózathoz. A $vnet érték a Get-AzVirtualNetwork által visszaadott objektumot jelöli. |
| Virtuális hálózat törlése |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott virtuális hálózatot az erőforráscsoporthoz. |
| Hálózati adapter törlése |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott hálózati adaptert az erőforráscsoporthoz. |
| Terheléselosztó törlése |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott terheléselosztó-csoportot az erőforráscsoporthoz. |
| Nyilvános IP-cím törlése |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" – ResourceGroupName $myResourceGroup<BR><BR>Eltávolítja a megadott nyilvános IP-címet az erőforráscsoporthoz. |

## <a name="next-steps"></a>Következő lépések
Használja az imént létrehozott hálózati adaptert a [virtuális gép létrehozásakor](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
