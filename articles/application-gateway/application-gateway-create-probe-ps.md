---
title: Egyéni mintavétel létrehozása a PowerShell használatával
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egyéni mintavételt a Application Gatewayhez a PowerShell használatával a Resource Managerben
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 63f4b28915a932398e5417f8ea9be3fe4d68e705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397841"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Egyéni mintavétel létrehozása az Azure Application Gatewayhoz a PowerShell használatával Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy egyéni mintavételt ad hozzá egy meglévő Application gatewayhez a PowerShell használatával. Az egyéni mintavételek olyan alkalmazások esetében hasznosak, amelyek adott állapot-ellenőrzési oldallal rendelkeznek, illetve olyan alkalmazásokhoz, amelyek nem adnak választ az alapértelmezett webalkalmazásra.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Application Gateway létrehozása egyéni mintavételsel

### <a name="sign-in-and-create-resource-group"></a>Jelentkezzen be, és hozzon létre egy erőforráscsoportot

1. `Connect-AzAccount`A hitelesítéshez használja a következőt:.

   ```powershell
   Connect-AzAccount
   ```

1. Szerezze be a fiók előfizetéseit.

   ```powershell
   Get-AzSubscription
   ```

1. Válassza ki, hogy melyek Azure-előfizetését használja.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Hozzon létre egy erőforráscsoportot. Ezt a lépést kihagyhatja, ha rendelkezik egy meglévő erőforráscsoporthoz.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

Az előző példában létrehoztunk egy **appgw-RG** nevű ERŐFORRÁSCSOPORTOT az **USA nyugati** régiójában.

### <a name="create-a-virtual-network-and-a-subnet"></a>Hozzon létre egy virtuális hálózatot és egy alhálózatot

A következő példa létrehoz egy virtuális hálózatot és egy alhálózatot az Application Gateway számára. Az Application Gateway használatához saját alhálózat szükséges. Emiatt az Application Gateway számára létrehozott alhálózatnak kisebbnek kell lennie, mint a VNET, hogy más alhálózatokat lehessen létrehozni és használni.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy **publicIP01** nevű, nyilvános IP-címhez tartozó erőforrást az **appgw-rg** nevű erőforráscsoportban, az USA nyugati régiójában. Ez a példa egy nyilvános IP-címet használ az Application Gateway előtér-IP-címéhez.  Az Application Gateway használatához a nyilvános IP-címnek dinamikusan létrehozott DNS-névnek kell lennie, ezért a nem adható meg a `-DomainNameLabel` nyilvános IP-cím létrehozásakor.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítania. Az alábbi példa az Application Gateway-erőforrásokhoz szükséges konfigurációs elemeket hozza létre.

| **Összetevő** | **Leírás** |
|---|---|
| **Átjáró IP-konfigurációja** | Egy Application Gateway IP-konfigurációja.|
| **Háttérkészlet** | IP-címek, FQDN-k vagy hálózati adapterek készlete, amelyek a webalkalmazást futtató alkalmazás-kiszolgálókhoz tartoznak.|
| **Állapotteszt** | A háttérbeli készlet tagjai állapotának figyelésére szolgáló egyéni mintavétel|
| **HTTP-beállítások** | Beállítások gyűjteménye, beleértve a portot, a protokollt, a cookie-alapú affinitást, a mintavételt és az időkorlátot.  Ezek a beállítások határozzák meg, hogy a rendszer hogyan irányítja át a forgalmat a háttér-készlet tagjai számára|
| **Előtér-port** | Az a port, amelyre az Application Gateway figyeli a forgalmat|
| **Figyelő** | Egy protokoll, a előtér-IP-konfiguráció és a frontend-port kombinációja. Ez az, ami figyeli a bejövő kérelmeket.
|**Szabály**| A megfelelő háttérbe irányítja a forgalmat a HTTP-beállítások alapján.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Mintavétel hozzáadása meglévő Application Gateway-átjáróhoz

A következő kódrészlet hozzáadja a mintavételt egy meglévő Application Gateway-átjáróhoz.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Mintavétel eltávolítása egy meglévő Application gatewayből

A következő kódrészlet eltávolítja a mintavételt egy meglévő Application gatewayből.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Az Application Gateway DNS-nevének beszerzése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím esetén az Application Gateway használatához dinamikusan hozzárendelt DNS-névre van szükség, amely nem valódi név. Ha szeretné, hogy a végfelhasználók elérjék az Application Gatewayt, használjon egy Application Gateway nyilvános végpontjára mutató CNAME-rekordot. [Egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). A művelet végrehajtásához az Application Gateway részleteinek beszerzésére és a kapcsolódó IP/DNS-név lekérésére van szükség az Application Gatewayhez csatolt PublicIPAddress használatával. Az Application Gateway DNS-nevének használatával létrehozhat egy CNAME rekordot, amely a két webalkalmazást erre a DNS-névre irányítja. Az A-bejegyzések használata nem javasolt, mivel a virtuális IP-cím változhat az Application Gateway újraindításakor.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan konfigurálhatja a TLS-kiszervezést: a [TLS kiszervezésének konfigurálása](./tutorial-ssl-powershell.md)