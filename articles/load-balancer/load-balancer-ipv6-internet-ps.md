---
title: Internetre irányuló terheléselosztó létrehozása IPv6-Azure PowerShell
titleSuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó az IPv6 protokollal a Resource Managerhez készült PowerShell használatával
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, Dual stack, nyilvános IP-cím, natív IPv6, mobil, IOT
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 43203a756bcb42c7d00de9c11e9223f1d8b9e2a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001587"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Bevezetés az internetkapcsolattal rendelkező Load Balancer IPv6-tal való létrehozásához a PowerShell használatával a Resource Managerben

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Sablon](load-balancer-ipv6-internet-template.md)

>[!NOTE] 
>Ez a cikk egy bevezető IPv6-szolgáltatást ismertet, amely lehetővé teszi, hogy az alapszintű Load Balancer IPv4-és IPv6-kapcsolatot is biztosítson. A teljes körű IPv6-kapcsolat mostantól elérhető az [IPv6 for Azure virtuális hálózatok](../virtual-network/ipv6-overview.md) , amely integrálja az IPv6-kapcsolatot a virtuális hálózatokkal, és olyan kulcsfontosságú funkciókat tartalmaz, mint például az IPv6 hálózati biztonsági csoportra vonatkozó szabályok, az IPv6 felhasználói útválasztás, az IPv6 alapszintű és a standard szintű terheléselosztás, valamint egyebek.  Az IPv6 az Azure virtuális hálózatok az Azure-beli IPv6-alkalmazások esetében ajánlott szabvány. Lásd: [IPv6 az Azure VNET PowerShell üzembe helyezéséhez](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Példa telepítési forgatókönyv

A következő ábra szemlélteti a cikkben üzembe helyezett terheléselosztási megoldást.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

Ebben a forgatókönyvben a következő Azure-erőforrásokat fogja létrehozni:

* egy internetre irányuló Load Balancer IPv4-és IPv6-alapú nyilvános IP-címmel
* két terheléselosztási szabály a nyilvános VIP-címek a privát végpontokra való leképezéséhez
* a két virtuális gépet tartalmazó rendelkezésre állási csoport
* két virtuális gép (VM)
* virtuális hálózati adapter minden virtuális GÉPHEZ, amelyhez IPv4-és IPv6-címek vannak hozzárendelve

## <a name="deploying-the-solution-using-the-azure-powershell"></a>A megoldás üzembe helyezése a Azure PowerShell használatával

Az alábbi lépések bemutatják, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a Azure Resource Manager és a PowerShell használatával. A Azure Resource Manager az egyes erőforrások létrehozása és konfigurálása egyenként történik, majd egy erőforrás létrehozásával együtt.

Terheléselosztó üzembe helyezéséhez a következő objektumokat kell létrehoznia és konfigurálnia:

* Előtér-IP-konfiguráció – nyilvános IP-címeket tartalmaz a bejövő hálózati forgalomhoz.
* Háttérbeli címkészlet – hálózati adaptereket (NIC) tartalmaz a virtuális gépek számára a terheléselosztó hálózati forgalmának fogadásához.
* Terheléselosztási szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá háttércímkészlet portjaihoz.
* Bejövő NAT-szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá egy adott virtuális gép portjához a háttércímkészletben.
* Mintavételezők – állapotfigyelő mintavételezőket tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állását ellenőrzik a háttércímkészletben.

További információ: Azure Load Balancer- [összetevők](./components.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>A PowerShell beállítása a Resource Manager használatához

Győződjön meg arról, hogy rendelkezik a PowerShell Azure Resource Manager moduljának legújabb üzemi verziójával.

1. Bejelentkezés az Azure-ba

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    Amikor a rendszer erre kéri, adja meg a hitelesítő adatait.

2. Keresse meg a fiókot az előfizetésekben

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Válassza ki, hogy melyek Azure-előfizetését használja.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ)

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Virtuális hálózat és nyilvános IP-cím létrehozása az előtér-IP-címkészlethez

1. Hozzon létre egy alhálózattal rendelkező virtuális hálózatot.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Azure nyilvános IP-cím (PIP) erőforrások létrehozása az előtér-IP-címkészlet számára. Ügyeljen arra, hogy a `-DomainNameLabel` következő parancsok futtatása előtt módosítsa az értéket. Az értéknek egyedinek kell lennie az Azure-régión belül.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > A terheléselosztó a nyilvános IP-cím tartományának címkéjét használja a teljes tartománynévhez előtagként. Ebben a példában a teljes tartománynevek a következők: *lbnrpipv4.westus.cloudapp.Azure.com* és *lbnrpipv6.westus.cloudapp.Azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Front-End IP-konfiguráció és egy Back-End-címkészlet létrehozása

1. A létrehozott nyilvános IP-címeket használó előtér-cím konfigurációjának létrehozása.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Háttérbeli címkészlet létrehozása

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>LB-szabályok, NAT-szabályok, mintavétel és terheléselosztó létrehozása

Ez a példa a következő elemeket hozza létre:

* NAT-szabály, amely az 443-es porton bejövő összes forgalmat lefordítja a 4443-es portra
* egy terheléselosztó-szabályt, amely elosztja a 80-as porton bejövő összes forgalmat a háttér-címkészletben szereplő címekhez tartozó 80-as porton.
* egy terheléselosztó-szabályt, amely engedélyezi az RDP-kapcsolat használatát a 3389-es porton lévő virtuális gépeken.
* a *HealthProbe. aspx* nevű vagy a 8080-es porton található szolgáltatás állapotának ellenőrzését szolgáló mintavételi szabály.
* egy terheléselosztó, amely az összes ilyen objektumot használja

1. Hozza létre a NAT-szabályokat.

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Hozzon létre egy állapotmintát. Két módszer van a mintavétel konfigurálására:

    HTTP-mintavétel

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    vagy TCP-mintavétel

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Ebben a példában a TCP-próbákat fogjuk használni.

3. Hozzon létre egy terheléselosztó-szabályt.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Hozza létre a terheléselosztó-t a korábban létrehozott objektumok használatával.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Hálózati adapterek létrehozása a háttérbeli virtuális gépekhez

1. Szerezze be a Virtual Network és Virtual Network alhálózatot, ahol a hálózati adaptereket létre kell hozni.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Hozzon létre IP-konfigurációkat és hálózati adaptereket a virtuális gépek számára.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Hozzon létre virtuális gépeket, és rendelje hozzá az újonnan létrehozott hálózati adaptereket

A virtuális gépek létrehozásával kapcsolatos további információkért lásd: [Windows rendszerű virtuális gép létrehozása és előre konfigurálása Resource Managerrel és Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Rendelkezésre állási csoport és Storage-fiók létrehozása

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Hozzon létre minden virtuális gépet, és rendelje hozzá az előző létrehozott hálózati adaptereket

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```


