---
title: Belső átirányítás a PowerShell használatával
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre olyan Application Gateway-t, amely a belső webes forgalmat átirányítja a kiszolgálók megfelelő háttér-készletéből az Azure PowerShell használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/28/2020
ms.author: victorh
ms.openlocfilehash: d838fe1d1015e1913c8aa28a122b06d108fb4676
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91446641"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-azure-powershell"></a>Belső átirányítással rendelkező Application Gateway létrehozása Azure PowerShell használatával

Az Azure PowerShell használatával konfigurálhatja a [webes forgalom átirányítását](multiple-site-overview.md) az [Application Gateway](overview.md)létrehozásakor. Ebben a cikkben a háttér-készletet egy virtuálisgép-méretezési csoport használatával definiálja. Ezután a figyelőket és szabályokat a saját tartományán alapulva konfigurálhatja, hogy a webes forgalom a megfelelő készlethez jusson. Ez a cikk azt feltételezi, hogy több tartománya van, és példákat használ a *www- \. contoso.com* és a *www- \. contoso.org*.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* A hálózat beállítása
* Application Gateway létrehozása
* Figyelők és átirányítási szabály hozzáadása
* Virtuálisgép-méretezési csoport létrehozása a háttér-készlettel
* CNAME rekord létrehozása a tartományban

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Hozza létre a *myBackendSubnet* és a *myAGSubnet* alhálózat-konfigurációit a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)használatával. Hozza létre a *myVNet* nevű virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) és az alhálózati konfigurációk használatával. Végül pedig hozza létre a *myAGPublicIPAddress* nevű nyilvános IP-címet a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)használatával. Ezek az erőforrások biztosítják az alkalmazásátjáró és a hozzá kapcsolódó erőforrások hálózati kapcsolatát.

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
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

Rendelje hozzá az Application gatewayhez korábban létrehozott *myAGSubnet* a [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)használatával. Rendeljen *myAGPublicIPAddress* az Application gatewayhez a [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)használatával. Ezután a [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport)használatával hozhatja létre a http-portot.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>A háttérkészlet létrehozása és beállítása

Hozzon létre egy *contosoPool* nevű háttér-készletet az Application gatewayhez a [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)használatával. Konfigurálja a háttér-készlet beállításait a [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)használatával.

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-first-listener-and-rule"></a>Az első figyelő és szabály létrehozása

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttérkészlethez. Ebben a cikkben két figyelőt hoz létre a két tartományhoz. Ebben a példában a rendszer figyelőket hoz létre a *www \. contoso.com* és a *www \. contoso.org* tartományához.

Hozza létre az első, *contosoComListener* nevű figyelőt a [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) használatával a előtér-konfigurációval és a korábban létrehozott frontend-porttal. A szabály ahhoz szükséges, hogy a figyelő tudja, melyik háttérkészletet használja a bejövő forgalomhoz. Hozzon létre egy *contosoComRule* nevű alapszintű szabályt a [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)használatával.

```azurepowershell-interactive
$contosoComlistener = New-AzApplicationGatewayHttpListener `
  -Name contosoComListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.com"
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoComRule `
  -RuleType Basic `
  -HttpListener $contosoComListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogatási erőforrásokat, a [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)használatával adja meg a *myAppGateway* nevű Application Gateway paramétereit, majd hozza létre a [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)használatával.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $contosoComListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-the-second-listener"></a>A második figyelő hozzáadása

Adja hozzá a *contosoOrgListener* nevű figyelőt, amely szükséges a forgalom átirányításához a [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener)használatával.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$frontendPort = Get-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -ApplicationGateway $appgw
$ipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name contosoOrgListener `
  -Protocol Http `
  -FrontendIPConfiguration $ipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.org"
Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Az átirányítás konfigurációjának hozzáadása

A figyelő átirányítását az [Add-AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration)használatával konfigurálhatja. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoComlistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoComListener `
  -ApplicationGateway $appgw
$contosoOrglistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectOrgtoCom `
  -RedirectType Found `
  -TargetListener $contosoComListener `
  -IncludePath $true `
  -IncludeQueryString $true
Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-second-routing-rule"></a>A második útválasztási szabály hozzáadása

Ezután hozzárendelheti az átirányítás konfigurációját egy új, *contosoOrgRule* nevű szabályhoz az [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)használatával.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoOrglistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name redirectOrgtoCom `
  -ApplicationGateway $appgw   
Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name contosoOrgRule `
  -RuleType Basic `
  -HttpListener $contosoOrgListener `
  -RedirectConfiguration $redirectConfig
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy virtuálisgép-méretezési csoport jön létre, amely támogatja a létrehozott háttér-készletet. A létrehozott méretezési csoport neve *myvmss* , és két virtuálisgép-példányt tartalmaz, amelyeken az IIS-t telepíti. Az IP-beállítások konfigurálásakor hozzárendeli a méretezési csoportot a háttérkészlethez.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
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

## <a name="create-cname-record-in-your-domain"></a>CNAME rekord létrehozása a tartományban

Az alkalmazásátjáró nyilvános IP-címmel történő létrehozása után lekérheti a DNS-címet, és a segítségével létrehozhat egy CNAME rekordot a tartományban. A [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) használatával lekérheti az Application Gateway DNS-címeit. Másolja a DNSSettings *fqdn* értékét, és használja a létrehozandó CNAME rekord értékeként. Az A rekordok használata nem javasolt, mivel a virtuális IP-cím változhat az alkalmazásátjáró újraindításakor.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Adja meg a tartománya nevét a böngésző címsorában. Például: `https://www.contoso.com`.

![Contoso webhely tesztelése az alkalmazásátjáróban](./media/redirect-internal-site-powershell/application-gateway-iistest.png)

Módosítsa a címeket a másik tartományra, például `https://www.contoso.org` láthatja, hogy a forgalom át lett irányítva a figyelőhöz a www- \. contoso.com.

## <a name="next-steps"></a>Következő lépések

- [Application Gateway átirányítási áttekintés](redirect-overview.md)
