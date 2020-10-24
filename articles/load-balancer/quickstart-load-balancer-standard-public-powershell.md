---
title: 'Rövid útmutató: nyilvános Load Balancer létrehozása – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre terheléselosztó használatával Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 0dfb5a68149f4745d17581dcefed6aedcf394106
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487704"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Gyors útmutató: nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához Azure PowerShell használatával

A Azure Load Balancer használatának első lépései a Azure PowerShell használatával nyilvános terheléselosztó és három virtuális gép létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* **CreatePubLBQS-RG**névvel ellátott.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

## <a name="create-a-public-ip-address-in-the-standard-sku"></a>Nyilvános IP-cím létrehozása a standard SKU-ban

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használata a következőhöz:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP**néven.
* **CreatePubLBQS – RG**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'


$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Ha egy nyilvános IP-címet szeretne létrehozni az 1. zónában, használja a következő parancsot:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'


$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -Zone 1
```

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-frontend-ip"></a>Előtérbeli IP-cím létrehozása

Előtér-IP-cím létrehozása a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Elnevezett **myFrontEnd**.
* Nyilvános IP- **myPublicIP**csatolva.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Háttérbeli címkészlet konfigurálása

Háttérbeli címkészlet létrehozása a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Elnevezett **myBackEndPool**.
* A virtuális gépek ehhez a háttér-készlethez csatlakoznak a hátralévő lépésekben.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP**protokoll.
* A **80**-es port figyelése.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton**keresztül. 
* A Health mintavételi **myHealthProbe**használata.
* **TCP**protokoll.
* A **15 perc**üresjárati időkorlátja.
* Engedélyezze a TCP-visszaállítást.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'


## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```

### <a name="create-load-balancer-resource"></a>Terheléselosztó erőforrás létrehozása

Hozzon létre egy nyilvános Load Balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Elnevezett **myLoadBalancer**
* A **eastus**.
* Az erőforráscsoport **CreatePubLBQS – RG**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network-in-the-standard-sku"></a>A virtuális hálózat konfigurálása a standard SKU-ban

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Elnevezett **myVNet**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* **MyBackendSubnet**nevű alhálózat.
* Virtuális hálózat **10.0.0.0/16**.
* Alhálózat **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Elnevezett **myNSGRuleHTTP**.
* A **http engedélyezésének**leírása.
* Hozzáférés **engedélyezése**.
* Protokoll **(*)**.
* Irány **bejövő**.
* **2000**prioritás.
* Az **Internet**forrása.
* A **(*)** forrásport tartománya.
* A **(*)** célcím előtagja.
* **80**-as célport.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Elnevezett **myNSG**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* Egy változóban tárolt előző lépésekben létrehozott biztonsági szabályokkal.

```azurepowershell
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

### <a name="create-network-interfaces"></a>Hálózati adapterek létrehozása

Hozzon létre három hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Elnevezett **myNicVM1**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Elnevezett **myNicVM2**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>3. VIRTUÁLIS GÉP

* Elnevezett **myNicVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = Get-Credential
```

Hozza létre a virtuális gépeket az alábbiakkal:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Elnevezett **myVM1**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM1**.
* A terheléselosztó **myLoadBalancer**csatolva.
* **1. zóna**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Elnevezett **myVM2**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM2**.
* A terheléselosztó **myLoadBalancer**csatolva.
* **2. zóna**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* Elnevezett **myVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM3**.
* A terheléselosztó **myLoadBalancer**csatolva.
* **3. zóna**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

## <a name="create-outbound-rule-configuration"></a>Kimenő szabály konfigurációjának létrehozása
A terheléselosztó kimenő szabályai a háttér-készletben lévő virtuális gépek kimenő forrásának hálózati címfordítását (SNAT) konfigurálja. 

A kimenő kapcsolatokról a [Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)című témakörben olvashat bővebben.

### <a name="create-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használata a következőhöz:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIPOutbound**néven.
* **CreatePubLBQS – RG**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Ha egy nyilvános IP-címet szeretne létrehozni az 1. zónában, használja a következő parancsot:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```
### <a name="create-outbound-frontend-ip-configuration"></a>Kimenő előtéri IP-konfiguráció létrehozása

Új előtér-IP-konfiguráció létrehozása az [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig):

* Elnevezett **myFrontEndOutbound**.
* Nyilvános IP- **myPublicIPOutbound**társítva.

```azurepowershell-interactive
## Variables for the command ##
$fen = 'myFrontEndOutbound'
$lbn = 'myLoadBalancer'

## Get the load balancer configuration  and apply the frontend config##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerFrontendIPConfig -Name $fen -PublicIpAddress $publicIP | Set-AzLoadBalancer
```

### <a name="create-outbound-pool"></a>Kimenő készlet létrehozása

Hozzon létre egy új kimenő készletet a [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig). 

Alkalmazza a készletet és a frontend IP-címét a terheléselosztó számára a [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* Elnevezett **myBackEndPoolOutbound**.

```azurepowershell-interactive
## Variables for the command ##
$ben = 'myBackEndPoolOutbound'
$lbn = 'myLoadBalancer'
$rg = 'CreatePubLBQS-rg'

## Get the load balancer configuration and create the outbound backend address pool##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerBackendAddressPoolConfig -Name $ben | Set-AzLoadBalancer
```
### <a name="create-outbound-rule-and-apply-to-load-balancer"></a>Kimenő szabály létrehozása és alkalmazása a terheléselosztó számára

Hozzon létre egy új kimenő szabályt a kimenő háttérrendszer-készlethez az [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig). 

Alkalmazza a szabályt a terheléselosztó számára a [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* Elnevezett **myOutboundRule**.
* A terheléselosztó **myLoadBalancer**van társítva.
* A frontend **myFrontEndOutbound**van társítva.
* **Minden**protokoll.
* A **15**üresjárati időkorlátja.
* **10000** kimenő portok.
* A háttér-készlet **myBackEndPoolOutbound**van társítva.
* Az erőforráscsoport **CreatePubLBQS – RG**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$brn = 'myOutboundRule'
$pro = 'All'
$idl = '15'
$por = '10000'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg 

## Apply the outbound rule configuration to the load balancer. ##
$lb | Add-AzLoadBalancerOutBoundRuleConfig -Name $brn -FrontendIPConfiguration $lb.FrontendIpConfigurations[1] -BackendAddressPool $lb.BackendAddressPools[1] -Protocol $pro -IdleTimeoutInMinutes $idl -AllocatedOutboundPort $por | Set-AzLoadBalancer
```

### <a name="add-virtual-machines-to-outbound-pool"></a>Virtuális gépek hozzáadása a kimenő készlethez

Adja hozzá a virtuálisgép-hálózati adaptereket a terheléselosztó kimenő készletéhez az [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)használatával:


#### <a name="vm1"></a>VM1
* A háttérbeli címkészlet **myBackEndPoolOutbound**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hálózati adapter **myNicVM1** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic1 = 'myNicVM1'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic1 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm2"></a>VM2
* A háttérbeli címkészlet **myBackEndPoolOutbound**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hálózati adapter **myNicVM2** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic2 = 'myNicVM2'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic2 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm3"></a>VM3
* A háttérbeli címkészlet **myBackEndPoolOutbound**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hálózati adapter **myNicVM3** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic3 = 'myNicVM3'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic3 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface

```

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

## <a name="create-a-public-ip-address-in-the-basic-sku"></a>Nyilvános IP-cím létrehozása az alapszintű SKU-ban

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használata a következőhöz:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP**néven.
* **CreatePubLBQS – RG**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-frontend-ip"></a>Előtérbeli IP-cím létrehozása

Előtér-IP-cím létrehozása a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Elnevezett **myFrontEnd**.
* Nyilvános IP- **myPublicIP**csatolva.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Háttérbeli címkészlet konfigurálása

Háttérbeli címkészlet létrehozása a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Elnevezett **myBackEndPool**.
* A virtuális gépek ehhez a háttér-készlethez csatlakoznak a hátralévő lépésekben.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP**protokoll.
* A **80**-es port figyelése.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton**keresztül. 
* A Health mintavételi **myHealthProbe**használata.
* **TCP**protokoll.
* A **15 perc**üresjárati időkorlátja.


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Terheléselosztó erőforrás létrehozása

Hozzon létre egy nyilvános Load Balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Elnevezett **myLoadBalancer**
* A **eastus**.
* Az erőforráscsoport **CreatePubLBQS – RG**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network-in-the-basic-sku"></a>Virtuális hálózat konfigurálása az alapszintű SKU-ban

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Elnevezett **myVNet**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* **MyBackendSubnet**nevű alhálózat.
* Virtuális hálózat **10.0.0.0/16**.
* Alhálózat **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Elnevezett **myNSGRuleHTTP**.
* A **http engedélyezésének**leírása.
* Hozzáférés **engedélyezése**.
* Protokoll **(*)**.
* Irány **bejövő**.
* **2000**prioritás.
* Az **Internet**forrása.
* A **(*)** forrásport tartománya.
* A **(*)** célcím előtagja.
* **80**-as célport.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Elnevezett **myNSG**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* Egy változóban tárolt előző lépésekben létrehozott biztonsági szabályokkal.

```azurepowershell
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>Hálózati adapterek létrehozása

Hozzon létre három hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Elnevezett **myNicVM1**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Elnevezett **myNicVM2**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>3. VIRTUÁLIS GÉP

* Elnevezett **myNicVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Rendelkezésre állási csoport létrehozása a virtuális gépekhez

A [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) használatával hozzon létre egy rendelkezésre állási készletet a virtuális gépek számára:

* Elnevezett **myAvSet**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreatePubLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = Get-Credential
```

Hozza létre a virtuális gépeket az alábbiakkal:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Elnevezett **myVM1**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM1**.
* A terheléselosztó **myLoadBalancer**csatolva.
* A **eastus** helyen.
* Az **AlmyAvSeton** rendelkezésre állási csoportba.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Elnevezett **myVM2**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM2**.
* A terheléselosztó **myLoadBalancer**csatolva.
* A **eastus** helyen.
* Az **AlmyAvSeton** rendelkezésre állási csoportba.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* Elnevezett **myVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM3**.
* A terheléselosztó **myLoadBalancer**csatolva.
* A **eastus** helyen.
* Az **AlmyAvSeton** rendelkezésre állási csoportba.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

A három virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.

---

## <a name="install-iis"></a>Az IIS telepítése

A [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) használatával telepítse az egyéni szkriptek bővítményét. 

A bővítmény futtatja a PowerShell Add-WindowsFeature Web-Server az IIS webkiszolgáló telepítéséhez, majd frissíti a Default.htm lapot a virtuális gép állomásneve megjelenítéséhez:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreatePubLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreatePubLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm3"></a>VM3

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreatePubLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM3'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A Load Balancer nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) :

```azurepowershell-interactive
  ## Variables for command. ##
  $rg = 'CreatePubLBQS-rg'
  $ipn = 'myPublicIP'
    
  Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ipn | select IpAddress
```

Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha meg szeretné tekinteni, hogy a terheléselosztó mindhárom virtuális gépen osztja el a forgalmat, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett oldalát, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és a többi erőforrás.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreatePubLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban

* Létrehozott egy standard vagy alapszintű nyilvános Load balancert
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt.
* Tesztelte a terheléselosztó.

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a következővel:..
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)


