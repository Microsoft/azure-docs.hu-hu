---
title: Webes forgalom irányítása URL-cím alapján – Azure PowerShell
description: Megtudhatja, hogyan irányíthatja a webes forgalmat az URL-cím alapján egy adott, skálázható kiszolgálókészletre az Azure PowerShell használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/31/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cc3498c6d8f385bcf63aa7860edd12f9bf343fb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552539"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Webes forgalom irányítása URL-cím alapján az Azure PowerShell használatával

Az Azure PowerShell használatával konfigurálhatja a webes forgalom adott, skálázható kiszolgálókészletre való irányítását az alkalmazás eléréséhez használt URL-cím alapján. Ebben a cikkben egy [Azure-Application Gateway](./overview.md) hoz létre három háttér-készlettel [Virtual Machine Scale sets](../virtual-machine-scale-sets/overview.md)használatával. Mindegyik háttérkészlet egy adott célhoz használható, például: gyakori adatok, képek, videók.  A forgalom különböző készletekhez való irányítása lehetővé teszi, hogy az ügyfelei akkor és azt az információt kapják meg, amelyre szükségük van.

A forgalom irányításának engedélyezéséhez figyelőkhöz hozzárendelt [útválasztási szabályokat](./url-route-overview.md) fog létrehozni, amelyek adott portokat figyelnek annak érdekében, hogy a webes forgalom a készlet megfelelő kiszolgálójára érkezzen meg.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* A hálózat beállítása
* Figyelők, URL-útvonaltérképek és szabályok létrehozása
* Méretezhető háttérkészletek létrehozása

![URL-útválasztási példa](./media/tutorial-url-route-powershell/scenario.png)

Ha szeretné, ezt az eljárást az [Azure CLI](tutorial-url-route-cli.md) vagy a [Azure Portal](create-url-route-portal.md)használatával végezheti el.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Login-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

Az erőforrások létrehozásához szükséges idő miatt az eljárás végrehajtása akár 90 percet is igénybe vehet.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, amely az alkalmazás összes erőforrását tartalmazza. 

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Akár meglévő virtuális hálózatot használ, akár újat hoz létre, csak azt az alhálózatot tartalmazhatja, amelyet kizárólag az alkalmazásátjárókhoz használ. Ebben a cikkben létrehoz egy alhálózatot az Application gatewayhöz és egy alhálózatot a méretezési csoportokhoz. Hozzon létre egy nyilvános IP-címet, amely engedélyezi a hozzáférést az alkalmazásátjáróban található erőforrásokhoz.

Hozza létre az alhálózati konfigurációk *myAGSubnet* és *myBackendSubnet* a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)használatával. Hozza létre a *myVNet* nevű virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) és az alhálózati konfigurációk használatával. Végül pedig hozza létre a *myAGPublicIPAddress* nevű nyilvános IP-címet a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)használatával. Ezek az erőforrások biztosítják az alkalmazásátjáró és a hozzá kapcsolódó erőforrások hálózati kapcsolatát.

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

Ebben a szakaszban az alkalmazásátjárót támogató erőforrásokat, majd az átjárót hozza létre. A létrehozott erőforrások a következők:

- *IP-konfigurációk és* előtér-port – társítja azt az alhálózatot, amelyet korábban az Application gatewayhez hozott létre, és hozzárendel egy portot az eléréséhez.
- *Alapértelmezett készlet* – Minden alkalmazásátjárónak rendelkeznie kell legalább egy háttérkiszolgáló-készlettel.
- *Alapértelmezett figyelő és szabály* – Az alapértelmezett figyelő figyeli a forgalmat a hozzárendelt porton, az alapértelmezett szabály pedig az alapértelmezett készletre irányítja a forgalmat.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

Rendelje hozzá a korábban létrehozott *myAGSubnet* az Application gatewayhez a [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)használatával. Rendeljen *myAGPublicIPAddress* az Application gatewayhez a [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)használatával.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

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

### <a name="create-the-default-pool-and-settings"></a>Az alapértelmezett készlet létrehozása és beállítása

Hozza létre a *appGatewayBackendPool* nevű alapértelmezett háttér-készletet az Application gatewayhez a [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)használatával. Konfigurálja a háttér-készlet beállításait a [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)használatával.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Az alapértelmezett figyelő és szabály létrehozása

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttérkészlethez. Ebben a cikkben két figyelőt hoz létre. Az első létrehozandó alapszintű figyelő a gyökér URL-cím forgalmát figyeli. A második létrehozandó alapszintű figyelő adott URL-címek forgalmát figyeli.

Hozza létre a *myDefaultListener* nevű alapértelmezett figyelőt a [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) használatával a előtér-konfigurációval és a korábban létrehozott frontend-porttal. 

A szabály ahhoz szükséges, hogy a figyelő tudja, melyik háttérkészletet használja a bejövő forgalomhoz. Hozzon létre egy *rule1* nevű alapszintű szabályt a [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)használatával.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
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

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogatási erőforrásokat, a [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)használatával adja meg a *myAppGateway* nevű Application Gateway paramétereit, majd hozza létre a [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)használatával.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

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
  -Sku $sku
```

Az Application Gateway létrehozása akár 30 percet is igénybe vehet. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra. 

Ezen a ponton egy olyan Application Gateway található, amely figyeli a 80-es port forgalmát, és egy alapértelmezett kiszolgálói készletbe küldi a forgalmat.

### <a name="add-image-and-video-backend-pools-and-port"></a>Kép- és videó-háttérkészletek és port hozzáadása

Adja hozzá a *imagesBackendPool* és a *videoBackendPool* nevű backend-készleteket az Application Gateway [Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Adja hozzá a készletek előtérbeli portját a [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport)használatával. Küldje el az Application Gateway módosításait a [set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway)használatával.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

Az alkalmazásátjáró frissítése akár 20 percet is igénybe vehet.

### <a name="add-backend-listener"></a>Háttérfigyelő hozzáadása

Adja hozzá a *backendListener* nevű háttér-figyelőt, amely szükséges a forgalom irányításához a [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener)használatával.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>URL-útvonaltérkép hozzáadása

Az URL-útvonaltérképek biztosítják, hogy az alkalmazáshoz küldött URL-címek adott háttérkészletekhez lesznek irányítva. Hozzon létre egy *imagePathRule* és *videoPathRule* nevű URL-elérésiút-térképeket a [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) és a [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig)használatával.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSetting `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>Útválasztási szabály hozzáadása

Az útválasztási szabály az URL-térképet társítja a létrehozott figyelőhöz. Adja hozzá a *Rule2* nevű szabályt az [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)használatával.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása

Ebben a példában három virtuálisgép-méretezési csoportot hoz létre, amelyek támogatják a három létrehozott háttérkészletet. A *myvmss1*, *myvmss2* és *myvmss3* nevű méretezési csoportokat hozza létre. Az IP-beállítások konfigurálásakor hozzárendeli a méretezési csoportot a háttérkészlethez.

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

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2_v2 `
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
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Az IIS telepítése

Minden méretezési csoport két virtuálisgép-példányt tartalmaz, amelyekre az IIS-t telepíti.  A rendszer létrehoz egy minta lapot az Application Gateway működésének ellenőrzéséhez.

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az Application Gateway nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) . Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például:, `http://52.168.55.24` , `http://52.168.55.24:8080/images/test.htm` , vagy `http://52.168.55.24:8080/video/test.htm` .

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Módosítsa az URL-címet a http:// &lt; IP-cím &gt; : 8080/images/test.htm értékre, és cserélje le az IP-cím IP &lt; -címét &gt; , és az alábbi példához hasonlóan kell megjelennie:

![Tesztképek URL-címe az alkalmazásátjáróban](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Módosítsa az URL-címet http:// &lt; IP-cím &gt; : 8080/video/test.htm értékre, és cserélje le az IP-cím IP &lt; -címét &gt; , és az alábbi példához hasonlóan kell megjelennie:

![Tesztvideó URL-címe az alkalmazásátjáróban](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az Application Gatewayt és az összes kapcsolódó erőforrást a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Következő lépések

[Forgalom átirányítása URL-cím alapján](./tutorial-url-redirect-powershell.md)