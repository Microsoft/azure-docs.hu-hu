---
title: HTTP–HTTPS átirányítás a PowerShell használatával – Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre a HTTP-ről HTTPS-re átirányított forgalmat használó Alkalmazásátjárót a Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/28/2020
ms.author: victorh
ms.openlocfilehash: c9118d0e5b314f05e89334991c68ec1b3b5751e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866304"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>HTTP–HTTPS átirányítást használó Alkalmazásátjáró létrehozása a Azure PowerShell

A TLS/SSL Azure PowerShell lejáró [](overview.md) tanúsítvánnyal és alkalmazásátjáró létrehozásához használhatja a tanúsítványt. Az útválasztási szabályok a HTTP-forgalmat az alkalmazásátjáró HTTPS-portjára irányítják át. Ebben a példában egy [](../virtual-machine-scale-sets/overview.md) virtuálisgép-méretezési készletet is létrehoz az alkalmazásátjáró háttérkészlete számára, amely két virtuálisgép-példányt tartalmaz. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Önaláírt tanúsítvány létrehozása
* Hálózat beállítása
* Alkalmazásátjáró létrehozása a tanúsítvánnyal
* Figyelési és átirányítási szabály hozzáadása
* Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyaghoz a Azure PowerShell 1.0.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az oktatóanyagban használt parancsok futtatásához az parancsot is futtatnia kell az Azure-ral `Login-AzAccount` való kapcsolat létrehozásához.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles környezetben importálni kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban egy önaláírt tanúsítványt hoz létre a [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) parancsmaggal. Az [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) parancsmagot a visszaadott ujjlenyomattal futtatva egy PFX-fájlt exportálhat a tanúsítványból.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Ennek nagyjából a következőképpen kell kinéznie:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

A PFX-fájl létrehozása az ujjlenyomattal:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy *myResourceGroupAG* nevű Azure-erőforráscsoportot a [New-AzResourceGroup használatával.](/powershell/module/az.resources/new-azresourcegroup) 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Hozza létre a *myBackendSubnet* és *a myAGSubnet* alhálózatkonfigurációkat a [New-AzVirtualNetworkSubnetConfig használatával.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Hozza létre a *myVNet* nevű virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) alhálózati konfigurációval. Végül hozza létre a *myAGPublicIPAddress* nevű nyilvános [IP-címet a New-AzPublicIpAddress használatával.](/powershell/module/az.network/new-azpublicipaddress) Ezek az erőforrások biztosítják az alkalmazásátjáró és a hozzá kapcsolódó erőforrások hálózati kapcsolatát.

```powershell
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

Társítsa a korábban létrehozott *myAGSubnet* alhálózatot az alkalmazásátjáróhoz a [New-AzApplicationGatewayIPConfiguration használatával.](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) Rendelje *hozzá a myAGPublicIPAddress* ip-címét az alkalmazásátjáróhoz a [New-AzApplicationGatewayFrontendIPConfig használatával.](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) Ezután létrehozhatja a HTTPS-portot a [New-AzApplicationGatewayFrontendPort használatával.](/powershell/module/az.network/new-azapplicationgatewayfrontendport)

```powershell
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
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>A háttérkészlet létrehozása és beállítása

Hozza létre az *appGatewayBackendPool* nevű háttérkészletet az alkalmazásátjáróhoz a [New-AzApplicationGatewayBackendAddressPool használatával.](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) Konfigurálja a háttérkészlet beállításait a [New-AzApplicationGatewayBackendHttpSettings használatával.](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)

```powershell
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

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttérkészlethez. Ebben a példában egy alapszintű figyelőt hoz létre, amely a gyökér URL-cím HTTPS-forgalmát figyeli. 

Hozzon létre egy tanúsítványobjektumot a [New-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) használatával, majd hozzon létre egy *appGatewayHttpListener* nevű listenert a [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) használatával a korábban létrehozott előtér-konfigurációval, előtérporttal és tanúsítvánnyal. A szabály ahhoz szükséges, hogy a figyelő tudja, melyik háttérkészletet használja a bejövő forgalomhoz. Hozzon létre egy *rule1 nevű* alapszintű szabályt a [New-AzApplicationGatewayRequestRoutingRule parancs használatával.](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogató erőforrásokat, adja meg a *myAppGateway* nevű alkalmazásátjáró paramétereit a [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)használatával, majd hozza létre a [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) használatával a tanúsítvánnyal.

```powershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Figyelési és átirányítási szabály hozzáadása

### <a name="add-the-http-port"></a>A HTTP-port hozzáadása

Adja hozzá a HTTP-portot az alkalmazásátjáróhoz az [Add-AzApplicationGatewayFrontendPort használatával.](/powershell/module/az.network/add-azapplicationgatewayfrontendport)

```powershell
$appgw = Get-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>A HTTP- figyelő hozzáadása

Adja hozzá a *myListener* nevű HTTP- listenert az alkalmazásátjáróhoz az [Add-AzApplicationGatewayHttpListener használatával.](/powershell/module/az.network/add-azapplicationgatewayhttplistener)

```powershell
$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Az átirányítási konfiguráció hozzáadása

Adja hozzá a HTTP–HTTPS átirányítási konfigurációt az alkalmazásátjáróhoz az [Add-AzApplicationGatewayRedirectConfiguration parancs használatával.](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration)

```powershell
$defaultListener = Get-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>Az útválasztási szabály hozzáadása

Adja hozzá az útválasztási szabályt az átirányítási konfigurációval az alkalmazásátjáróhoz az [Add-AzApplicationGatewayRequestRoutingRule használatával.](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)

```powershell
$myListener = Get-AzApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban. Az IP-beállítások konfigurálásakor hozzárendeli a méretezési csoportot a háttérkészlethez.

```powershell
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

```powershell
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

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A [Get-AzPublicIPAddress használatával](/powershell/module/az.network/get-azpublicipaddress) lekérte az alkalmazásátjáró nyilvános IP-címét. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://52.170.203.149

```powershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Biztonsági figyelmeztetés](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Következő lépések

- [HTTP-fejlécek és URL-címek átírása Application Gateway](rewrite-http-headers-url.md)
