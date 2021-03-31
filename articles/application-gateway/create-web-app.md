---
title: App Service konfigurálása a PowerShell használatával
titleSuffix: Azure Application Gateway
description: Ez a cikk a webalkalmazások háttérrendszeri gazdagépekként, meglévő vagy új Application Gatewayen történő beállításához nyújt útmutatást.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/15/2019
ms.author: victorh
ms.openlocfilehash: 152f3c3254ab01c8aa61acd12c39bd98c8f55038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84808057"
---
# <a name="configure-app-service-with-application-gateway-using-powershell"></a>App Service konfigurálása a Application Gateway PowerShell használatával

Az Application Gateway lehetővé teszi, hogy egy App Service alkalmazást vagy más, több-bérlős szolgáltatást háttérbeli készlet tagjaként. Ebből a cikkből megtudhatja, hogyan konfigurálhat egy App Service alkalmazást Application Gateway használatával. Az első példa meglévő Application Gateway konfigurálását mutatja be egy webalkalmazás háttérkészlettagként való használatára. A második példa egy új Application Gateway létrehozását mutatja be egy olyan webalkalmazással, amely egy háttérkészlet tagja.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-web-app-behind-an-existing-application-gateway"></a>Webalkalmazás konfigurálása meglévő Application Gateway mögött

Az alábbi példa egy webalkalmazás meglévő Application Gatewayhez háttérkészlettagként történő hozzáadását mutatja be. A `-PickHostNamefromBackendHttpSettings`mintavételi konfiguráció és`-PickHostNameFromBackendAddress` a háttérrendszeri HTTP-beállítások kapcsolóit egyaránt meg kell adni ahhoz, hogy a webalkalmazások működjenek.

```powershell
# FQDN of the web app
$webappFQDN = "<enter your webapp FQDN i.e mywebsite.azurewebsites.net>"

# Retrieve the resource group
$rg = Get-AzResourceGroup -Name 'your resource group name'

# Retrieve an existing application gateway
$gw = Get-AzApplicationGateway -Name 'your application gateway name' -ResourceGroupName $rg.ResourceGroupName

# Define the status codes to match for the probe
$match=New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Add a new probe to the application gateway
Add-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Retrieve the newly added probe
$probe = Get-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw

# Configure an existing backend http settings
Set-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -ApplicationGateway $gw -PickHostNameFromBackendAddress -Port 80 -Protocol http -CookieBasedAffinity Disabled -RequestTimeout 30 -Probe $probe

# Add the web app to the backend pool
Set-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -ApplicationGateway $gw -BackendFqdns $webappFQDN

# Update the application gateway
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="configure-a-web-application-behind-a-new-application-gateway"></a>Webalkalmazás konfigurálása új Application Gateway mögött

Ez a forgatókönyv egy webalkalmazás üzembe helyezését mutatja be az asp.net első lépéseket bemutató webhelyével és egy Application Gatewayjel.

```powershell
# Defines a variable for a dotnet get started web app repository location
$gitrepo="https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git"

# Unique web app name
$webappname="mywebapp$(Get-Random)"

# Creates a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location Eastus

# Create an App Service plan in Free tier.
New-AzAppServicePlan -Name $webappname -Location EastUs -ResourceGroupName $rg.ResourceGroupName -Tier Free

# Creates a web app
$webapp = New-AzWebApp -ResourceGroupName $rg.ResourceGroupName -Name $webappname -Location EastUs -AppServicePlan $webappname

# Configure GitHub deployment from your GitHub repo and deploy once to web app.
$PropertiesObject = @{
    repoUrl = "$gitrepo";
    branch = "master";
    isManualIntegration = "true";
}
Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName $rg.ResourceGroupName -ResourceType Microsoft.Web/sites/sourcecontrols -ResourceName $webappname/web -ApiVersion 2015-08-01 -Force

# Creates a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Creates a vnet for the application gateway
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location EastUs -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for use later
$subnet=$vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location EastUs -AllocationMethod Dynamic

# Create a new IP configuration
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool with the hostname of the web app
$pool = New-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -BackendFqdns $webapp.HostNames

# Define the status codes to match for the probe
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Create a probe with the PickHostNameFromBackendHttpSettings switch for web apps
$probeconfig = New-AzApplicationGatewayProbeConfig -name webappprobe -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Define the backend http settings
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120 -PickHostNameFromBackendAddress -Probe $probeconfig

# Create a new front-end port
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80

# Create a new front end IP configuration
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener using the front-end ip configuration and port created earlier
$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Create a new rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the application gateway SKU to use
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Create the application gateway
$appgw = New-AzApplicationGateway -Name ContosoAppGateway -ResourceGroupName $rg.ResourceGroupName -Location EastUs -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -Probes $probeconfig -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>Az Application Gateway DNS-nevének beszerzése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím esetén az Application Gateway használatához dinamikusan hozzárendelt DNS-névre van szükség, amely nem valódi név. Ha szeretné, hogy a végfelhasználók elérjék az Application Gatewayt, használjon egy Application Gateway nyilvános végpontjára mutató CNAME-rekordot. Az alias létrehozásához az Application Gateway részleteinek beszerzésére és a kapcsolódó IP-cím/DNS-név lekérésére van szükség az Application Gatewayhez csatolt PublicIPAddress elemmel. Ez az Azure DNS-sel vagy más DNS-szolgáltatókkal végezhető el egy olyan CNAME rekord létrehozásával, amely a [nyilvános IP-címre](../dns/dns-custom-domain.md#public-ip-address) mutat. Az A-bejegyzések használata nem javasolt, mivel a virtuális IP-cím változhat az Application Gateway újraindításakor.

```powershell
Get-AzPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : eastus
Id                       : /subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/ContosoAppGateway/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="restrict-access"></a>Hozzáférés korlátozása

A példákban telepített webalkalmazások nyilvános IP-címeket használnak, amelyek közvetlenül az internetről érhetők el. Ez segít a hibaelhárításban az új funkciók megismerése és új dolgok kipróbálása során. Ha azonban éles környezetben szeretne üzembe helyezni egy szolgáltatást, további korlátozásokat is fel szeretne venni.

A webalkalmazásokhoz való hozzáférés korlátozásának egyik módja a [Azure app Service statikus IP-korlátozások](../app-service/app-service-ip-restrictions.md)használata. Például korlátozhatja a webalkalmazást úgy, hogy az csak az Application gatewaytől kapjon forgalmat. Az App Service IP-korlátozási funkciója segítségével az Application Gateway VIP-t csak az elérési címmel listázhatja.

## <a name="next-steps"></a>Következő lépések

Információk az átirányítás konfigurálásáról: [Átirányítás konfigurálása az Application Gatewayen PowerShell-lel](redirect-overview.md).
