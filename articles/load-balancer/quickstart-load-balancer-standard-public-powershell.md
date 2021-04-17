---
title: 'Rövid útmutató: Nyilvános terheléselosztás létrehozása – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre terheléselosztást Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
ms.author: allensu
manager: KumudD
ms.date: 11/22/2020
ms.assetid: ''
ms.topic: quickstart
ms.service: load-balancer
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 0ddaf0eede59053cd8022fef24d37a37c6d7db5a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529584"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Rövid útmutató: Nyilvános terheléselosztás létrehozása a virtuális gépek terhelésének terheléselosztása Azure PowerShell

A Azure Load Balancer használatának első Azure PowerShell nyilvános terheléselosztás és három virtuális gép létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell helyileg vagy helyileg telepített Azure Cloud Shell

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a parancsot is futtatnia kell az Azure-ral `Connect-AzAccount` való kapcsolat létrehozásához.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup segítségével:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePubLBQS-rg' -Location 'eastus'

```
---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Az éles számítási feladatokhoz standard termékváltozatú terheléselosztás ajánlott. A SKUS-okkal kapcsolatos további információkért lásd: **[Azure Load Balancer SKUs](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="A rövid útmutatóhoz létrehozott standard terheléselosztási erőforrások." border="false":::

## <a name="create-a-public-ip-address---standard"></a>Nyilvános IP-cím létrehozása – Standard

Nyilvános IP-cím létrehozásához használja a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) címet.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1,2,3
}
New-AzPublicIpAddress @publicip

```

Zónaként megadott nyilvános IP-cím létrehozásához használja az alábbi parancsot az 1. zónában:

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1
}
New-AzPublicIpAddress @publicip

```

## <a name="create-standard-load-balancer"></a>Standard terheléselosztás létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

* Hozzon létre egy előtérben található IP-címet a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) segítségével az előtérben található IP-címkészlethez. Ez az IP-cím fogadja a bejövő forgalmat a terheléselosztáson

* Hozzon létre egy háttércímkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) segítségével a terheléseltöltő előtere által küldött forgalomhoz. Ebben a készletben helyezhetők üzembe a háttér virtuális gépek.

* Hozzon létre egy állapot-mintavételt az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) segítségével, amely meghatározza a háttér virtuálisgép-példányok állapotát.

* Hozzon létre egy terheléselosztási szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) segítségével, amely meghatározza, hogyan ossza el a rendszer a forgalmat a virtuális gépek között.

* Hozzon létre egy nyilvános terheléselosztást a [New-AzLoadBalancer segítségével.](/powershell/module/az.network/new-azloadbalancer)


```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset -DisableOutboundSNAT

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---standard"></a>Virtuális hálózat konfigurálása – Standard

A virtuális gépek üzembe helyezése és a terheléseltöltő tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

Hozzon létre egy virtuális hálózatot a háttér virtuális gépek számára.

Hozzon létre egy hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Virtuális hálózat, hálózati biztonsági csoport és megerősített gazdagép létrehozása

* Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork segítségével.](/powershell/module/az.network/new-azvirtualnetwork)

* Hozzon létre egy hálózati biztonságicsoport-szabályt a [New-AzNetworkSecurityRuleConfig segítségével.](/powershell/module/az.network/new-aznetworksecurityruleconfig)

* Hozzon létre Azure Bastion gazdagépet a [New-AzBastion segítségével.](/powershell/module/az.network/new-azbastion)

* Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup segítségével.](/powershell/module/az.network/new-aznetworksecuritygroup)

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---standard"></a>Virtuális gépek létrehozása – Standard

Ebben a szakaszban a terheléselosztás háttérkészletének három virtuális gépét fogja létrehozni.

* Hozzon létre három hálózati adaptert a [New-AzNetworkInterface használatával.](/powershell/module/az.network/new-aznetworkinterface)

* Állítson be egy rendszergazdai felhasználónevet és jelszót a virtuális gépekhez a [Get-Credential használatával.](/powershell/module/microsoft.powershell.security/get-credential)

* Hozza létre a virtuális gépeket a következővel:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

A virtuális gépek és a megerősített gazdagép üzemelő példányai PowerShell-feladatként vannak elküldve. A feladatok állapotának megtekintéséhez használja a [Get-Job et:](/powershell/module/microsoft.powershell.core/get-job)

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-outbound-rule-configuration"></a>Kimenő szabálykonfiguráció létrehozása
A terheléselosztás kimenő szabályai konfigurálják a háttérkészletben található virtuális gépek kimenő forráshálózati címfordítását (SNAT). 

További információ a kimenő kapcsolatokról: Kimenő [kapcsolatok az Azure-ban.](load-balancer-outbound-connections.md)

### <a name="create-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy standard zónaredundáns nyilvános IP-címet **myPublicIPOutbound névvel.**

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1,2,3
}
New-AzPublicIpAddress @publicipout

```

Zónaként megadott nyilvános IP-cím létrehozásához használja az alábbi parancsot az 1. zónában:

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1
}
New-AzPublicIpAddress @publicipout

```

### <a name="create-outbound-configuration"></a>Kimenő konfiguráció létrehozása

* Hozzon létre egy új előtérben található IP-konfigurációt az [Add-AzLoadBalancerFrontendIpConfig segítségével.](/powershell/module/az.network/add-azloadbalancerfrontendipconfig)

* Hozzon létre egy új kimenő háttércímkészletet az [Add-AzLoadBalancerBackendAddressPoolConfig segítségével.](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig) 

* Alkalmazza a készletet és az előtere IP-címét a terheléselosztásra a [Set-AzLoadBalancer alkalmazásával.](/powershell/module/az.network/set-azloadbalancer)
*  Hozzon létre egy új kimenő szabályt a kimenő háttérkészlethez az [Add-AzLoadBalancerOutboundRuleConfig segítségével.](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig) 

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$pubip = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$publicIp = Get-AzPublicIpAddress @pubip

## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

## Create the frontend configuration ##
$fe = @{
    Name = 'myFrontEndOutbound'
    PublicIPAddress = $publicIP
}
$lb | Add-AzLoadBalancerFrontendIPConfig @fe | Set-AzLoadBalancer

## Create the outbound backend address pool ##
$be = @{
    Name = 'myBackEndPoolOutbound'
}
$lb | Add-AzLoadBalancerBackendAddressPoolConfig @be | Set-AzLoadBalancer

## Apply the outbound rule configuration to the load balancer. ##
$rule = @{
    Name = 'myOutboundRule'
    AllocatedOutboundPort = '10000'
    Protocol = 'All'
    IdleTimeoutInMinutes = '15'
    FrontendIPConfiguration = $lb.FrontendIpConfigurations[1]
    BackendAddressPool = $lb.BackendAddressPools[1]
}
$lb | Add-AzLoadBalancerOutBoundRuleConfig @rule | Set-AzLoadBalancer

```

### <a name="add-virtual-machines-to-outbound-pool"></a>Virtuális gépek hozzáadása kimenő készlethez

Adja hozzá a virtuális gép hálózati adapterét a terheléseltöltő kimenő készlethez az [Add-AzNetworkInterfaceIpConfig használatával:](/powershell/module/az.network/add-aznetworkinterfaceipconfig)

```azurepowershell-interactive
## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

# For loop with variable to add virtual machines to backend outbound pool. ##
for ($i=1; $i -le 3; $i++)
{
$nic = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = "myNicVM$i"
}
$nicvm = Get-AzNetworkInterface @nic

## Apply the backend to the network interface ##
$be = @{
    Name = 'ipconfig1'
    LoadBalancerBackendAddressPoolId = $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id
}
$nicvm | Set-AzNetworkInterfaceIpConfig @be | Set-AzNetworkInterface
}

```

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard termékváltozatú terheléselosztás éles számítási feladatokhoz ajánlott. A SKUS-okkal kapcsolatos további információkért lásd: **[Azure Load Balancer SKUs](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="A rövid útmutatóban létrehozott alapszintű terheléselosztási erőforrások." border="false":::

## <a name="create-a-public-ip-address---basic"></a>Nyilvános IP-cím létrehozása – Alapszintű

Nyilvános IP-cím létrehozásához használja a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) címet.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Basic'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicip

```

## <a name="create-basic-load-balancer"></a>Alapszintű terheléselosztás létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

* Hozzon létre egy előtérben található IP-címet a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) segítségével az előtérben található IP-címkészlethez. Ez az IP-cím fogadja a bejövő forgalmat a terheléselosztáson

* Hozzon létre egy háttércímkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) segítségével a terheléseltöltő előtere által küldött forgalomhoz. Ebben a készletben helyezhetők üzembe a háttér virtuális gépek.

* Hozzon létre egy állapot-mintavételt az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) segítségével, amely meghatározza a háttér virtuálisgép-példányok állapotát.

* Hozzon létre egy terheléselosztási szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) segítségével, amely meghatározza, hogyan ossza el a rendszer a forgalmat a virtuális gépek között.

* Hozzon létre egy nyilvános terheléselosztást a [New-AzLoadBalancer segítségével.](/powershell/module/az.network/new-azloadbalancer)

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---basic"></a>Virtuális hálózat konfigurálása – Alapszintű

A virtuális gépek üzembe helyezése és a terheléseltöltő tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

Hozzon létre egy virtuális hálózatot a háttér virtuális gépek számára.

Hozzon létre egy hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Virtuális hálózat, hálózati biztonsági csoport és megerősített gazdagép létrehozása

* Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork segítségével.](/powershell/module/az.network/new-azvirtualnetwork)

* Hozzon létre egy hálózati biztonságicsoport-szabályt a [New-AzNetworkSecurityRuleConfig segítségével.](/powershell/module/az.network/new-aznetworksecurityruleconfig)

* Hozzon létre Azure Bastion gazdagépet a [New-AzBastion segítségével.](/powershell/module/az.network/new-azbastion)

* Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup segítségével.](/powershell/module/az.network/new-aznetworksecuritygroup)

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---basic"></a>Virtuális gépek létrehozása – Alapszintű

Ebben a szakaszban a terheléselosztás háttérkészletének virtuális gépeit fogja létrehozni.

* Hozzon létre három hálózati adaptert a [New-AzNetworkInterface használatával.](/powershell/module/az.network/new-aznetworkinterface)

* Állítson be rendszergazdai felhasználónevet és jelszót a virtuális gépekhez a [Get-Credential használatával.](/powershell/module/microsoft.powershell.security/get-credential)

* A [New-AzAvailabilitySet használatával hozzon](/powershell/module/az.compute/new-azvm) létre egy rendelkezésre állási készletet a virtuális gépekhez.

* Hozza létre a virtuális gépeket a következővel:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvSet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

A virtuális gépek és a megerősített gazdagép üzemelő példányai PowerShell-feladatként vannak elküldve. A feladatok állapotának megtekintéséhez használja a [Get-Job et:](/powershell/module/microsoft.powershell.core/get-job)

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

---

## <a name="install-iis"></a>Az IIS telepítése

Az egyéni szkriptbővítmény telepítéséhez használja a [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) parancsprogramot. 

A bővítmény a `PowerShell Add-WindowsFeature Web-Server` parancs futtatásával telepíti az IIS-webkiszolgálót, majd a Default.htm lapot frissítve megjeleníti a virtuális gép eszköznevét:

> [!IMPORTANT]
> Mielőtt továbblépne, győződjön meg arról, hogy a virtuális gépek üzembe helyezése az előző lépésekből befejeződött.  A `Get-Job` virtuális gép üzembe helyezési feladatának állapotának ellenőrzéséhez használja a következőt: .

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreatePubLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

A bővítmények PowerShell-feladatként vannak telepítve. A telepítési feladatok állapotának megtekintéséhez használja a [Get-Job et:](/powershell/module/microsoft.powershell.core/get-job)


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A [Get-AzPublicIpAddress használatával](/powershell/module/az.network/get-azpublicipaddress) lekérte a terheléselelosztás nyilvános IP-címét:

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```

Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha látni tudja, hogyan osztja el a terheléselosztó a forgalmat mindhárom virtuális gép között, testre szabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett lapját, majd kényszerítheti a webböngésző frissítését az ügyfélszámítógépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a terheléselosztást és a fennmaradó erőforrásokat.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePubLBQS-rg'

```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban:

* Létrehozott egy standard vagy alapszintű nyilvános terheléselosztást
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztási forgalmi szabályt és az állapot-mintavételt.
* Tesztelte a terheléseltöltőt.

Ha többet szeretne megtudni a Azure Load Balancer, folytassa a következővel:
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)
