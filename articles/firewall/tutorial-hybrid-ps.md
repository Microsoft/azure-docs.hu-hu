---
title: A Azure Firewall konfigurálása a hibrid hálózaton & a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Firewallt a Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 03/26/2021
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: db60c26ed50dae3b4b28a6c44d152a921eb96a69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627557"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Az Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban az Azure PowerShell használatával

Ha a helyszíni hálózatot egy Azure-beli virtuális hálózathoz kapcsolja egy hibrid hálózat létrehozásához, az Azure hálózati erőforrásaihoz való hozzáférés szabályozása egy átfogó biztonsági csomag fontos részét képezi.

Az Azure Firewallal az engedélyezett és letiltott forgalmat meghatározó szabályok segítségével szabályozhatja egy hibrid hálózat hálózati elérését.

Ebben a cikkben három virtuális hálózatot fog létrehozni:

- **VNet-hub** – a tűzfal ebben a virtuális hálózatban található.
- **VNet – küllő** – a küllős virtuális hálózat az Azure-ban található számítási feladatokat jelöli.
- **VNet-helyszíni** – a helyszíni virtuális hálózat egy helyszíni hálózatot jelöl. Tényleges telepítés esetén VPN-vagy ExpressRoute-kapcsolat is csatlakoztatható. Az egyszerűség kedvéért ez a cikk egy VPN Gateway-kapcsolat használatát, valamint egy Azure-beli virtuális hálózatot használ a helyszíni hálózat ábrázolására.

![Tűzfal a hibrid hálózatban](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Változók deklarálása
* A tűzfal hub virtuális hálózatának létrehozása
* A küllős virtuális hálózat létrehozása
* Helyszíni virtuális hálózat létrehozása
* A tűzfal konfigurálása és üzembe helyezése
* A VPN-átjárók létrehozása és csatlakoztatása
* A hub és a küllős virtuális hálózatok egyenrangúak
* Az útvonalak létrehozása
* A virtuális gépek létrehozása
* A tűzfal tesztelése

Ha az oktatóanyag elvégzése helyett a Azure Portalt szeretné használni, tekintse meg a következőt [: oktatóanyag: Azure Firewall telepítése és konfigurálása hibrid hálózaton a Azure Portal használatával](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez helyileg kell futtatni a PowerShellt. Telepítenie kell a Azure PowerShell-modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

Három alapvető követelménynek kell teljesülnie, hogy ez a forgatókönyv megfelelően működjön:

- Egy felhasználó által megadott útvonal (UDR) a küllő alhálózaton, amely a Azure Firewall IP-címére mutat alapértelmezett átjáróként. Ezen az útválasztási táblázatban **le kell tiltani** a virtuális hálózati átjáró útvonalának propagálását.
- A központi átjáró alhálózatán található UDR a tűzfal IP-címére kell mutatnia a küllő hálózatok következő ugrásakor.

   Nem szükséges UDR a Azure Firewall alhálózaton, mivel a BGP-ből tanulja meg az útvonalakat.
- A VNet-Hub-tól a VNet-Spoke felé irányuló társviszony létesítésekor az **AllowGatewayTransit** beállítást, a VNet-Spoke-tól a VNet-Hub felé irányuló társviszony létesítésekor pedig a **UseRemoteGateways** beállítást adja meg.

Tekintse meg a jelen cikk [útvonalak létrehozása](#create-the-routes) című szakaszát, amelyből megtudhatja, hogyan jönnek létre ezek az útvonalak.

>[!NOTE]
>Az Azure Firewallnak közvetlen internetkapcsolatra van szüksége. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a Azure Firewall kényszerített bújtatási módban kell konfigurálnia. Ha ez egy meglévő Azure Firewall, amely kényszerített bújtatási módban nem konfigurálható újra, javasoljuk, hogy adjon hozzá egy 0.0.0.0/0 UDR a AzureFirewallSubnet, amely a közvetlen internetkapcsolat **fenntartása érdekében a** **NextHopType** értékre van beállítva.
>
>További információ: [Azure Firewall kényszerített bújtatás](forced-tunneling.md).

>[!NOTE]
>A közvetlenül összekapcsolt virtuális hálózatok közötti forgalom közvetlenül akkor is átirányítva van, ha egy UDR az alapértelmezett átjáróként való Azure Firewallre mutat. Ha ebben a forgatókönyvben az alhálózatot alhálózati forgalomra szeretné küldeni a tűzfalra, a UDR mindkét alhálózaton explicit módon tartalmaznia kell a célként megadott alhálózat hálózati előtagot.

A kapcsolódó Azure PowerShell dokumentációjának áttekintéséhez tekintse meg a [Azure PowerShell-referenciát](/powershell/module/az.network/new-azfirewall).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="declare-the-variables"></a>Változók deklarálása

A következő példában deklaráljuk a változókat a jelen cikk értékei alapján. Bizonyos esetekben előfordulhat, hogy egyes értékeket le kell cserélnie a saját előfizetésében való működésre. Ha szükséges, módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>A tűzfal hub virtuális hálózatának létrehozása

Először hozza létre azt az erőforráscsoportot, amely tartalmazza a cikk erőforrásait:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Adja meg a virtuális hálózatban szerepeltetni kívánt alhálózatokat:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Most hozza létre a tűzfal hub virtuális hálózatot:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Igényeljen egy nyilvános IP-címet a virtuális hálózatához létrehozandó VPN-átjáró számára. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása a VPN-átjáró számára dinamikusan történik.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>A küllős virtuális hálózat létrehozása

Adja meg a küllős virtuális hálózatban szerepeltetni kívánt alhálózatokat:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

A küllős virtuális hálózat létrehozása:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Helyszíni virtuális hálózat létrehozása

Adja meg a virtuális hálózatban szerepeltetni kívánt alhálózatokat:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Most hozza létre a helyszíni virtuális hálózatot:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Igényeljen egy nyilvános IP-címet a virtuális hálózat számára létrehozandó átjáró számára. Látható, hogy az *AllocationMethod* értéke **Dynamic**. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>A tűzfal konfigurálása és üzembe helyezése

Most telepítse a tűzfalat a hub virtuális hálózatba.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Hálózati szabályok konfigurálása

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>A VPN-átjárók létrehozása és csatlakoztatása

A hub és a helyszíni virtuális hálózatok VPN-átjárón keresztül kapcsolódnak egymáshoz.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>VPN-átjáró létrehozása a hub virtuális hálózathoz

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Most hozzon létre egy VPN-átjárót a hub virtuális hálózathoz. A hálózatról hálózati konfigurációkhoz Útvonalalapú VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>VPN-átjáró létrehozása a helyszíni virtuális hálózathoz

Hozza létre a VPN-átjáró konfigurációját. A VPN-átjáró konfigurációja határozza meg az alhálózatot és a használandó nyilvános IP-címet.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Most hozza létre a VPN-átjárót a helyszíni virtuális hálózathoz. A hálózatról hálózati konfigurációkhoz Útvonalalapú VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>A VPN-kapcsolatok létrehozása

Most már létrehozhatja a VPN-kapcsolatokat a hub és a helyszíni átjárók között.

#### <a name="get-the-vpn-gateways"></a>A VPN-átjárók lekérése

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>A kapcsolatok létrehozása

Ebben a lépésben létrehozza a kapcsolódást a hub virtuális hálózatról a helyszíni virtuális hálózatra. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Hozza létre a helyszíni és a hub közötti virtuális hálózati kapcsolatokat. Ez a lépés hasonló az előzőhöz, kivéve, ha a VNet-Onpremt a VNet-hubhoz hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat néhány perc alatt létrejön.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>A kapcsolat ellenőrzése

A sikeres kapcsolatok ellenőrzéséhez használja a *Get-AzVirtualNetworkGatewayConnection* parancsmagot a *hibakereséshez* vagy anélkül. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Ha a rendszer arra kéri, válassza az **A** lehetőséget az összes (**All**) futtatásához. A példában a *-Name* a tesztelni kívánt kapcsolatok nevére utal.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot *Csatlakoztatva*, és láthatja a bemenő és kimenő bájtokat.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>A hub és a küllős virtuális hálózatok egyenrangúak

A hub és a küllős virtuális hálózatok most már egyenrangúak.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Az útvonalak létrehozása

Ezután hozzon létre néhány útvonalat:

- Egy útvonalat a központi átjáró alhálózatától a küllő alhálózatához a tűzfal IP-címén keresztül
- Egy alapértelmezett útvonalat a küllő alhálózattól a tűzfal IP-címén keresztül

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a küllős számítási feladatokat és a helyszíni virtuális gépeket, és helyezze őket a megfelelő alhálózatokra.

### <a name="create-the-workload-virtual-machine"></a>A számítási feladatot futtató virtuális gép létrehozása

Hozzon létre egy virtuális gépet a küllős virtuális hálózaton, és futtassa a nyilvános IP-cím nélküli IIS-t, és engedélyezze a pingelést a alkalmazásban.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>A helyszíni virtuális gép létrehozása

Ez egy egyszerű virtuális gép, amelyet a Távoli asztal a nyilvános IP-címhez való kapcsolódáshoz használ. Innentől kezdve a tűzfalon keresztül csatlakozhat a helyszíni kiszolgálóhoz. Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Először kérje le, majd jegyezze fel a **VM-küllős-01** virtuális gép magánhálózati IP-címét.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Az Azure Portalról csatlakozzon a **VM-Onprem** virtuális géphez.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Nyisson meg egy webböngészőt a **VM-helyszíni**, és keresse meg a http:// \<VM-spoke-01 private IP\> .

Az Internet Information Services alapértelmezett oldalának kell megjelennie.

A **VM-Onprem** virtuális gépről nyisson meg egy távoli asztalt a **VM-spoke-01** gépre a magánhálózati IP-címen.

A kapcsolatnak sikeresen létre kell jönnie, és be kell tudnia jelentkezni a választott falhasználónévvel és jelszóval.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

<!---- You can ping the server on the spoke VNet.--->
- Böngészhet a webkiszolgálón a küllős virtuális hálózaton.
- Az RDP használatával kapcsolódhat a kiszolgálóhoz a küllős virtuális hálózaton.

Ezután módosítsa a tűzfal hálózati szabálygyűjteményének műveletét **Deny** (Megtagadás) értékre annak ellenőrzéséhez, hogy a tűzfalszabályok a vártnak megfelelően működnek-e. A következő szkript futtatásával módosíthatja a szabálygyűjtemény műveletét **Deny** (Megtagadás) értékre.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Most futtassa újra az ellenőrzéseket. Ezúttal mindegyiknek sikertelennek kell lennie. A módosított szabályok ellenőrzése előtt zárja be a meglévő távoli asztalokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rájuk szükség, törölje az **FW-Hybrid-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>Következő lépések

A következő lépésben monitorozhatja az Azure Firewall naplóit.

[Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./firewall-diagnostics.md)