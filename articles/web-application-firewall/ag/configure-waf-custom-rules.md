---
title: Egyéni v2-szabályok konfigurálása a PowerShell használatával
titleSuffix: Azure Web Application Firewall
description: Ismerje meg, hogyan konfigurálhatja a webalkalmazási tűzfal (WAF) v2 egyéni szabályait a Azure PowerShell használatával. A tűzfalon áthaladó minden egyes kérelem esetében kiértékelheti a saját szabályait.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/21/2020
ms.author: victorh
ms.openlocfilehash: 2572e30c02552859eb5c61915a9ef524c0c6cc70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83758962"
---
# <a name="configure-web-application-firewall-v2-on-application-gateway-with-a-custom-rule-using-azure-powershell"></a>Webalkalmazási tűzfal v2 konfigurálása Application Gateway egyéni szabállyal Azure PowerShell használatával

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Az egyéni szabályok lehetővé teszik saját szabályok kiértékelését a webalkalmazási tűzfalon (WAF) v2-en keresztül áthaladó kérelmek esetében. Ezek a szabályok magasabb prioritással rendelkeznek, mint a felügyelt szabálykészlet többi szabálya. Az egyéni szabályok egy művelettel rendelkeznek (az engedélyezéshez vagy a blokkoláshoz), egy egyeztetési feltételt és egy operátort, amely lehetővé teszi a teljes testreszabást.

Ez a cikk létrehoz egy Application Gateway WAF v2-et, amely egyéni szabályt használ. Az egyéni szabály blokkolja a forgalmat, ha a kérelem fejléce User-Agent *evilbot* tartalmaz.

További példák az egyéni szabályokra: [Egyéni webalkalmazási tűzfalszabályok létrehozása és használata](create-custom-waf-rules.md)

Ha azt szeretné, hogy az ebben a cikkben szereplő Azure PowerShell egy olyan folyamatos parancsfájlban fusson, amelyet másolhat, beilleszthet és futtathat, tekintse meg az [Azure Application Gateway PowerShell-minták](powershell-samples.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Ha a Azure PowerShell helyi telepítését és használatát választja, akkor ehhez a parancsfájlhoz a Azure PowerShell modul 2.1.0 vagy újabb verziójára van szükség.

1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.
2. Az Azure-beli kapcsolatok létrehozásához futtassa a parancsot `Connect-AzAccount` .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="example-script"></a>Példaszkript

### <a name="set-up-variables"></a>Változók beállítása

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Statikus nyilvános VIP létrehozása

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>Készlet és frontend-port létrehozása

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Figyelő, http-beállítás, szabály és az autoskálázás létrehozása

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-two-custom-rules-and-apply-it-to-waf-policy"></a>Hozzon létre két egyéni szabályt, és alkalmazza azt a WAF szabályzatra

```azurepowershell
# Create WAF config
$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention" -RuleSetType "OWASP" -RuleSetVersion "3.0"
# Create a User-Agent header custom rule 
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  
$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block
 
# Create a geo-match custom rule
$var2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $var2 -Operator GeoMatch -MatchValue "US"  -NegationCondition $False
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name allowUS -Priority 14 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

# Create a firewall policy
$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -CustomRule $rule,$rule2
```

### <a name="create-the-application-gateway"></a>A Application Gateway létrehozása

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection  $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts $fp01 -HttpListeners $listener01 `
  -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
  -WebApplicationFirewallConfig $wafConfig `
  -FirewallPolicy $wafPolicy
```

## <a name="update-your-waf"></a>A WAF frissítése

A WAF létrehozása után a következő kódhoz hasonló eljárással frissítheti azt:

```azurepowershell
# Get the existing policy
$policy = Get-AzApplicationGatewayFirewallPolicy -Name $policyName -ResourceGroupName $RGname
# Add an existing rule named $rule
$policy.CustomRules.Add($rule)
# Update the policy
Set-AzApplicationGatewayFirewallPolicy -InputObject $policy
```

## <a name="next-steps"></a>Következő lépések

[További információ a webalkalmazási tűzfalról Application Gateway](ag-overview.md)
