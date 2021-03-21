---
title: 'Oktatóanyag: virtuális központ biztonságossá tétele Azure PowerShell használatával'
description: Ez a cikk azt ismerteti, hogyan hozhat létre egy Azure-beli virtuális WAN-t egy olyan régióban, Azure Firewall engedélyezve van a központban.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 093b962bb9f42b660fb8cc5c0584ec67ebc87e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789162"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Oktatóanyag: virtuális központ biztonságossá tétele Azure PowerShell használatával

Ebben az oktatóanyagban egy virtuális WAN-példányt hoz létre egy adott régióban található virtuális központtal, és egy Azure Firewall helyez üzembe a virtuális központban a kapcsolatok biztonságossá tételéhez. Ebben a példában a virtuális hálózatok közötti biztonságos kapcsolatot mutatjuk be. A virtuális hálózatok és a helyek közötti, a pont – hely vagy a ExpressRoute ágak közötti forgalmat a Virtual Secure hub is támogatja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A virtuális WAN üzembe helyezése
> * Azure Firewall üzembe helyezése és egyéni útválasztás konfigurálása
> * Kapcsolat tesztelése

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

- PowerShell 7

   Ehhez az oktatóanyaghoz a Azure PowerShell helyileg kell futtatni a PowerShell 7-es verziójában. A PowerShell 7 telepítéséhez lásd: [áttelepítés a Windows powershell 5,1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7)-ből a PowerShell 7-re.
- Az az. Network Version 3.2.0

    Ha az az. Network Version 3.4.0 vagy újabb verzióval rendelkezik, a jelen oktatóanyagban szereplő parancsok némelyikének használatára kell visszalépést végeznie. Az az. Network modul verzióját a paranccsal lehet megtekinteni `Get-InstalledModule -Name Az.Network` . Az az. Network modul eltávolításához futtassa a parancsot `Uninstall-Module -name az.network` . Az az. Network 3.2.0-modul telepítéséhez futtassa a parancsot `Install-Module az.network -RequiredVersion 3.2.0 -force` .

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Kezdeti virtuális WAN-telepítés

Első lépésként be kell állítania néhány változót, és létre kell hoznia az erőforráscsoportot, a virtuális WAN-példányt és a virtuális hubot:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Hozzon létre két virtuális hálózatot, és kapcsolódjon hozzájuk az elosztóhoz a küllők szerint:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

Ezen a ponton teljes mértékben működőképes virtuális WAN-kapcsolat áll rendelkezésre. A biztonság növelése érdekében minden egyes virtuális hubhoz telepítenie kell egy Azure Firewall. A tűzfalszabályok használatával hatékonyan kezelhető a virtuális WAN Azure Firewall példánya. Így a rendszer létrehoz egy tűzfal-házirendet is a következő példában:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

A Azure Firewallról Azure Monitorra történő naplózás engedélyezése nem kötelező, de ebben a példában a tűzfal naplóit használva bizonyíthatja, hogy a forgalom a tűzfalon halad át:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Azure Firewall üzembe helyezése és egyéni útválasztás konfigurálása

Most már rendelkezik egy Azure Firewall a központban, de továbbra is módosítania kell az útválasztást, hogy a virtuális WAN a virtuális hálózatoktól és az ágaktól a tűzfalon keresztül küldje el a forgalmat. Ezt két lépésben hajthatja végre:

1. Az összes virtuális hálózati kapcsolat (és az ág kapcsolatai) konfigurálása az útválasztási táblázatba való propagáláshoz `None` . Ennek a konfigurációnak az a hatása, hogy más virtuális hálózatok és ágak nem fogják megtanulni az előtagokat, így nem kell az útválasztást elérniük.
1. Most már beszúrhat statikus útvonalakat az `Default` útválasztási táblázatba (ahol az összes virtuális hálózat és ág alapértelmezés szerint társítva van), így minden forgalmat elküld a Azure Firewall.

> [!NOTE]
> Az Azure Portalról a Azure Firewall Managerrel való kapcsolat biztonságossá tételekor üzembe helyezett konfiguráció

Első lépésként konfigurálja a virtuális hálózati kapcsolatokat az `None` útválasztási táblázatba való propagáláshoz:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Most folytathatja a második lépéssel, hogy hozzáadja a statikus útvonalakat az `Default` útválasztási táblázathoz. Ebben a példában a Azure Firewall Manager által generált alapértelmezett konfigurációt kell alkalmaznia a virtuális WAN-kapcsolat biztonságossá tételekor, de a statikus útvonalon lévő előtagok listájának módosításával az adott követelményekhez igazodhat:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Kapcsolat tesztelése

Most már rendelkezik egy teljes körűen működőképes biztonságos hubhoz. A kapcsolat teszteléséhez szüksége lesz egy virtuális gépre az elosztóhoz csatlakozó minden egyes küllős virtuális hálózaton:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

A tűzfal-házirend alapértelmezett konfigurációja az összes eldobása. Ezért néhány szabályt konfigurálnia kell. Kezdje a DNAT-szabályokkal, hogy a teszt virtuális gépek elérhetők legyenek a tűzfal nyilvános IP-címe felett:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Most beállíthat néhány példát a szabályokra. Definiáljon egy olyan hálózati szabályt, amely engedélyezi az SSH-forgalmat, valamint egy olyan szabályt, amely engedélyezi az internetes hozzáférést a teljes tartománynévhez `ifconfig.co` . Ez az URL-cím a HTTP-kérelemben szereplő forrás IP-címet adja vissza:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

A forgalom tényleges továbbítása előtt ellenőrizheti a virtuális gépek érvényes útvonalait. Tartalmazniuk kell a virtuális WAN (Plus RFC1918) által megszerzett előtagokat `0.0.0.0/0` , de nem a másik küllő előtagját:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Most hozzon ki forgalmat az egyik virtuális gépről a másikba, és ellenőrizze, hogy el lett-e dobva a Azure Firewall. A következő SSH-parancsokban el kell fogadnia a virtuális gépek ujjlenyomatait, és meg kell adnia a virtuális gépek létrehozásakor megadott jelszót. Ebben a példában öt ICMP echo-kérést küldünk a virtuális gépről a spoke1-ből a spoke2-be, valamint egy TCP-kapcsolódási kísérletet a 22-es porton a Linux segédprogrammal `nc` (a `-vz` jelzővel, amely csak egy csatlakozási kérelmet küld, és megjeleníti az eredményt). Látnia kell a pingelést, és a TCP-csatlakozási kísérlet a 22-es porton sikeres, mivel ezt a korábban konfigurált hálózati szabály engedélyezi:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Az internetes forgalmat is ellenőrizheti. A segédprogramon keresztüli HTTP-kérelmeket `curl` a tűzfal házirendjében () megadott FQDN-re `ifconfig.co` kell tenni, de a többi célhelyre irányuló HTTP-kérések sikertelenek lesznek (ebben a példában a teszttel `bing.com` ):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

A csomagok eldobásának legegyszerűbb módja, ha a tűzfal a naplókat ellenőrzi. Mivel úgy konfigurálta a Azure Firewall, hogy a naplókat a Azure Monitorba küldje, a Kusto lekérdezési nyelv segítségével kérheti le a kapcsolódó naplókat a Azure Monitor:

> [!NOTE]
> A naplók megjelenése körülbelül 1 percet vesz igénybe Azure Monitor

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

Az előző parancsban eltérő bejegyzéseket kell látnia:

* Az SSH-kapcsolatban DNAT'ed
* Eldobott ICMP-csomagok a küllős virtuális gépek között (10.1.1.4 és 10.1.2.4)
* Engedélyezett SSH-kapcsolatok a virtuális gépek között a küllők között

Itt látható egy példa a fenti parancs által létrehozott kimenetre:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Ha szeretné megtekinteni az alkalmazás-szabályok naplóit (az engedélyezett és a megtagadott HTTP-kapcsolatok leírását), vagy a naplók megjelenítésének módját, más KQL-lekérdezésekkel is próbálkozhat. A [Azure Firewall Azure monitor naplófájljaiban](../firewall/firewall-workbook.md)talál néhány példát.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tesztkörnyezet törléséhez távolítsa el az erőforráscsoportot az összes befoglalt objektummal:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a megbízható biztonsági partnerekről](trusted-security-partners.md)