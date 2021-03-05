---
title: Webes forgalom korlátozása a PowerShell használatával
titleSuffix: Azure Web Application Firewall
description: Megtudhatja, hogyan korlátozhatja a webes forgalmat az Application Gateway webalkalmazási tűzfallal egy Azure PowerShell használatával.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 08/31/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3956c06a0120ad28599c47279b60e6f5dd30204e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174512"
---
# <a name="enable-web-application-firewall-using-azure-powershell"></a>Webalkalmazási tűzfal engedélyezése Azure PowerShell használatával

Egy [webalkalmazási tűzfallal](ag-overview.md) (WAF) korlátozhatja az Application Gateway forgalmát. A WAF [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-szabályokkal védi az alkalmazást. Ezek a szabályok olyan támadások ellen nyújtanak védelmet, mint az SQL-injektálás, a Cross-Site Scripting támadások és a munkamenet-eltérítések. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* A hálózat beállítása
* Alkalmazásátjáró létrehozása engedélyezett WAF-fel
* Virtuálisgép-méretezési csoport létrehozása
* Tárfiók létrehozása és diagnosztika konfigurálása

![Példa webalkalmazási tűzfalra](../media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

Ha szeretné, ezt a cikket a [Azure Portal](application-gateway-web-application-firewall-portal.md) vagy az [Azure CLI](tutorial-restrict-web-traffic-cli.md)használatával végezheti el.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Login-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a *myBackendSubnet* és a *myAGSubnet* nevű alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)használatával. Hozza létre a *myVNet* nevű virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) és az alhálózati konfigurációk használatával. Végül pedig hozza létre a *myAGPublicIPAddress* nevű nyilvános IP-címet a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)használatával. Ezek az erőforrások biztosítják az alkalmazásátjáró és a hozzá kapcsolódó erőforrások hálózati kapcsolatát.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Ebben a szakaszban olyan erőforrásokat hoz létre, amelyek támogatják az Application Gatewayt, majd végül létrehozza és létrehoz egy WAF. A következő erőforrásokat hozza létre:

- *IP-konfigurációk és az előtérbeli port* – A korábban létrehozott alhálózatot társítja az alkalmazásátjáróhoz, és hozzárendel egy portot, amelyen keresztül elérhető.
- *Alapértelmezett készlet* – Minden alkalmazásátjárónak rendelkeznie kell legalább egy háttérkiszolgáló-készlettel.
- *Alapértelmezett figyelő és szabály* – Az alapértelmezett figyelő figyeli a forgalmat a hozzárendelt porton, az alapértelmezett szabály pedig az alapértelmezett készletre irányítja a forgalmat.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

Rendelje hozzá az Application gatewayhez korábban létrehozott *myAGSubnet* a [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)használatával. Rendeljen *myAGPublicIPAddress* az Application gatewayhez a [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)használatával.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>A háttérkészlet létrehozása és beállítása

Hozza létre a *appGatewayBackendPool* nevű háttér-készletet az Application gatewayhez a [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)használatával. Konfigurálja a háttérbeli címkészlet beállításait a [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)használatával.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Az alapértelmezett figyelő és szabály létrehozása

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttércímkészletekhez. Ebben a példában egy alapszintű figyelőt hoz létre, amely a gyökér URL-cím forgalmát figyeli. 

Hozzon létre egy *mydefaultListener* nevű figyelőt a [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) használatával a előtér-konfigurációval és a korábban létrehozott frontend-porttal. A szabály ahhoz szükséges, hogy a figyelő tudja, melyik háttérkészletet használja a bejövő forgalomhoz. Hozzon létre egy *rule1* nevű alapszintű szabályt a [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)használatával.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>Alkalmazásátjáró létrehozása a WAF-fel

Most, hogy létrehozta a szükséges támogatási erőforrásokat, a [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)használatával határozza meg az Application Gateway paramétereit. A [New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/new-azapplicationgatewayfirewallpolicy)használatával állítsa be a tűzfal házirendjét. Ezután hozza létre a *myAppGateway* nevű Application Gatewayt a [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)használatával.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_v2 `
  -Tier WAF_v2 `
  -Capacity 2

$policySetting = New-AzApplicationGatewayFirewallPolicySetting -Mode Prevention -State Enabled -MaxRequestBodySizeInKb 100 -MaxFileUploadInMb 256

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -PolicySetting $PolicySetting

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -FirewallPolicy $wafPolicy
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban. Az IP-beállítások konfigurálásakor hozzárendeli a méretezési csoportot a háttérkészlethez.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Az IIS telepítése

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tárfiók létrehozása és diagnosztika konfigurálása

Ebben a cikkben az Application Gateway egy Storage-fiók használatával tárolja az adatgyűjtési és-megelőzési célokat. Az adatok rögzítéséhez Azure Monitor naplókat vagy Event hub-t is használhat.

### <a name="create-the-storage-account"></a>A tárfiók létrehozása

Hozzon létre egy *myagstore1* nevű Storage [-fiókot a New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)használatával.

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Konfigurálja a diagnosztikát az adatok ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog és ApplicationGatewayFirewallLog naplókba való rögzítéséhez a [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting)használatával.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Category ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) használatával lekérheti az Application Gateway nyilvános IP-címét. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](../media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az Application Gatewayt és az összes kapcsolódó erőforrást a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Következő lépések

[Webalkalmazási tűzfalszabályok testreszabása](application-gateway-customize-waf-rules-portal.md)
