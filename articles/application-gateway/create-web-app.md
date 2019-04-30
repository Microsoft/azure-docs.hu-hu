---
title: Webalkalmazások védelmének biztosítása az Azure Application Gatewayjel – PowerShell
description: Ez a cikk a webalkalmazások háttérrendszeri gazdagépekként, meglévő vagy új Application Gatewayen történő beállításához nyújt útmutatást.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
origin.date: 10/16/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: dcf21fe111ab742074ab4fe580a021338e1f7c43
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122217"
---
# <a name="configure-app-service-with-application-gateway"></a>Az App Service az Application Gateway konfigurálása

Az Application gateway lehetővé teszi egy App Service-alkalmazást vagy egyéb több-bérlős szolgáltatás háttérkészlet-tagként. Ez a cikk bemutatja az App Service-alkalmazások konfigurálása az Application Gatewayen. Az első példa meglévő Application Gateway konfigurálását mutatja be egy webalkalmazás háttérkészlettagként való használatára. A második példa egy új Application Gateway létrehozását mutatja be egy olyan webalkalmazással, amely egy háttérkészlet tagja.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-web-app-behind-an-existing-application-gateway"></a>Webalkalmazás konfigurálása meglévő Application Gateway mögött

Az alábbi példa egy webalkalmazás meglévő Application Gatewayhez háttérkészlettagként történő hozzáadását mutatja be. A `-PickHostNamefromBackendHttpSettings`mintavételi konfiguráció és`-PickHostNameFromBackendAddress` a háttérrendszeri HTTP-beállítások kapcsolóit egyaránt meg kell adni ahhoz, hogy a webalkalmazások működjenek.

```powershell
# FQDN of the web app
$webappFQDN = "<enter your webapp FQDN i.e mywebsite.chinacloudsites.cn>"

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
$rg = New-AzResourceGroup -Name ContosoRG -Location ChinaNorth

# Create an App Service plan in Free tier.
New-AzAppServicePlan -Name $webappname -Location ChinaNorth -ResourceGroupName $rg.ResourceGroupName -Tier Free

# Creates a web app
$webapp = New-AzWebApp -ResourceGroupName $rg.ResourceGroupName -Name $webappname -Location ChinaNorth -AppServicePlan $webappname

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
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for use later
$subnet=$vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location ChinaNorth -AllocationMethod Dynamic

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
$appgw = New-AzApplicationGateway -Name ContosoAppGateway -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -Probes $probeconfig -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>Az Application Gateway DNS-nevének beszerzése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím esetén az Application Gateway használatához dinamikusan hozzárendelt DNS-névre van szükség, amely nem valódi név. Ha szeretné, hogy a végfelhasználók elérjék az Application Gatewayt, használjon egy Application Gateway nyilvános végpontjára mutató CNAME-rekordot. Az alias létrehozásához az Application Gateway részleteinek beszerzésére és a kapcsolódó IP-cím/DNS-név lekérésére van szükség az Application Gatewayhez csatolt PublicIPAddress elemmel. Ez az Azure DNS-sel vagy más DNS-szolgáltatókkal végezhető el egy olyan CNAME rekord létrehozásával, amely a [nyilvános IP-címre](../dns/dns-custom-domain.md#public-ip-address) mutat. Az A-bejegyzések használata nem javasolt, mivel a virtuális IP-cím változhat az Application Gateway újraindításakor.

```powershell
Get-AzPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : chinanorth
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
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.chinacloudapp.cn"
                            }
```

## <a name="restrict-access"></a>Hozzáférés korlátozása

Az ezekben a példákban üzembe helyezett webalkalmazások közvetlenül az internetről elérhető nyilvános IP-címek használatára. A hibaelhárítás, amikor egy új funkciót működésével ismerkedik, és új néhány dolgot, így. Azonban éles üzembe helyezés egy szolgáltatást szeretne, ha szeretné hozzáadhat több korlátozást.

A web Apps korlátozhatja a hozzáférést egy módja [statikus IP-korlátozások az Azure App Service](../app-service/app-service-ip-restrictions.md). Például korlátozhatja a web app, hogy ez csak származó forgalmat megkapja az application gateway. Az app service IP korlátozás szolgáltatás használatával az application gateway virtuális IP-CÍMEK listája csak címként hozzáféréssel rendelkező.

## <a name="next-steps"></a>További lépések

Átirányítás konfigurálásáról további információ: [Átirányítás konfigurálása az Application Gatewayen a PowerShell-lel](redirect-overview.md).

<!-- Update_Description: code update -->