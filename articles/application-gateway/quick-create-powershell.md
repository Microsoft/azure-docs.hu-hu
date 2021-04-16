---
title: 'Rövid útmutató: Webes forgalom közvetlen forgalma a PowerShell használatával'
titleSuffix: Azure Application Gateway
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre a Azure PowerShell használatával olyan Azure Application Gateway, amely a webes forgalmat egy háttérkészletben lévő virtuális gépekre irányítja.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: a4aa16ba4334e5f1e035face9549f4c03ad3a14f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538559"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Rövid útmutató: Webes forgalom Azure Application Gateway a Azure PowerShell

Ebben a rövid útmutatóban a Azure PowerShell egy Alkalmazásátjárót hoz létre. Ezután tesztelje, hogy megfelelően működik-e. 

Az Application Gateway a háttérkészlet adott erőforrásaihoz irányítja az alkalmazás webes forgalmát. Figyelőket rendelhet a portokhoz, szabályokat hozhat létre, és erőforrásokat adhat a háttérkészlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű telepítést használ egy nyilvános előtere IP-címével, egy alapszintű listenerrel, amely egyetlen helyet kezel az alkalmazásátjárón, egy egyszerű kérés-útválasztási szabályt és két virtuális gépet a háttérkészletben.

Ezt a rövid útmutatót az [Azure CLI](quick-create-cli.md) vagy a használatával is [Azure Portal.](quick-create-portal.md)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Azure PowerShell 1.0.0-s](/powershell/azure/install-az-ps) vagy újabb verziót (ha helyileg futtatja Azure PowerShell verziót).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Az Azure-hoz való csatlakozáshoz futtassa a(on) `Connect-AzAccount` et.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoporthoz rendeli. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Új erőforráscsoport létrehozásához használja a `New-AzResourceGroup` parancsmagot: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra.  Az Application Gateway alhálózata csak alkalmazásátjárókat tartalmazhat. Más erőforrások nem engedélyezettek.  Létrehozhat egy új alhálózatot a Application Gateway használhat egy meglévőt. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz. Az előtere IP-címét a Application Gateway konfigurálhatja nyilvánosra vagy privátra a saját használatának megfelelően. Ebben a példában egy nyilvános előtere IP-címet fog választani.

1. Hozza létre az alhálózati konfigurációkat a `New-AzVirtualNetworkSubnetConfig` használatával.
2. Hozza létre a virtuális hálózatot az alhálózati konfigurációval a `New-AzVirtualNetwork` használatával. 
3. Hozza létre a nyilvános IP-címet a `New-AzPublicIpAddress` használatával. 
> [!NOTE]
> [A virtuális hálózati szolgáltatásvégpont-szabályzatok](../virtual-network/virtual-network-service-endpoint-policies-overview.md) jelenleg nem támogatottak a Application Gateway alhálózatban.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

1. A használatával hozza létre azt a konfigurációt, amely a létrehozott `New-AzApplicationGatewayIPConfiguration` alhálózatot társítja az alkalmazásátjáróval. 
2. A használatával hozza létre azt a konfigurációt, amely az alkalmazásátjáróhoz korábban létrehozott nyilvános `New-AzApplicationGatewayFrontendIPConfig` IP-címet rendeli hozzá. 
3. A `New-AzApplicationGatewayFrontendPort` használatával rendelje hozzá a 80-as portot az Application Gateway eléréséhez.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>A háttérkészlet létrehozása

1. A `New-AzApplicationGatewayBackendAddressPool` használatával hozza létre az Application Gateway háttérkészletét. A háttérkészlet jelenleg üres. Amikor a következő szakaszban létrehozza a háttérkiszolgálói NIC-ket, hozzáadja őket a háttérkészlethez.
2. Konfigurálja a háttérkészlet beállításait a `New-AzApplicationGatewayBackendHttpSetting` következővel: .

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Figyelő létrehozása és szabály hozzáadása

Az Azure-nak szüksége van egy figyelőre, hogy engedélyezze az Application Gateway számára a forgalom megfelelő átirányítását a háttérkészlethez. Az Azure-nak szüksége van egy szabályra is, amelyből a figyelő tudni tudja, melyik háttérkészletet használja a bejövő forgalomhoz. 

1. Hozzon létre egy figyelőt a használatával a korábban létrehozott előterekonfigurációval `New-AzApplicationGatewayHttpListener` és előtereporttal. 
2. A `New-AzApplicationGatewayRequestRoutingRule` használatával hozzon létre egy *rule1 nevű szabályt.* 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogató erőforrásokat, hozza létre az Application Gatewayt:

1. Az `New-AzApplicationGatewaySku` application gateway paramétereinek megadásához használja a következőt: .
2. Az Application Gateway létrehozásához használja `New-AzApplicationGateway` a következőt: .

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Háttérkiszolgálók

Most, hogy létrehozta a Application Gateway, hozza létre a webhelyeket szolgáltató háttér virtuális gépeket. A háttérkiszolgálók hálózati hálózatokból, virtuálisgép-méretezési készletekből, nyilvános IP-címből, belső IP-címből, teljes tartománynevekből (FQDN) és több-bérlős háttérkiszolgálókból (például Azure App Service. 

Ebben a példában két virtuális gépet hoz létre az Application Gateway háttérkiszolgálóiként való használatra. A virtuális gépekre is telepíti az IIS-t annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Alkalmazásátjárót.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

1. A háttérkészlet nemrégiben Application Gateway konfigurációját a következővel: `Get-AzApplicationGatewayBackendAddressPool` .
2. Hozzon létre egy hálózati adaptert a `New-AzNetworkInterface` használatával.
3. Hozzon létre egy virtuálisgép-konfigurációt a `New-AzVMConfig` segítségével.
4. Hozza létre a virtuális gépet a `New-AzVM` következővel: .

Amikor a következő kódmintát futtatja a virtuális gépek létrehozásához, az Azure kérni fogja a hitelesítő adatokat. Adjon meg egy felhasználónevet és egy jelszót:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az Alkalmazásátjáró létrehozásához nem szükséges az IIS, ebben a rövid útmutatóban telepítette azt annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót.

Használja az IIS-t az Application Gateway tesztelésére:

1. Futtassa `Get-AzPublicIPAddress` a következőt az Application Gateway nyilvános IP-címének leához: . 
2. Másolja és illessze be a nyilvános IP-címet a böngésző címsorába. Amikor frissíti a böngészőt, látnia kell a virtuális gép nevét. Egy érvényes válasz ellenőrzi, hogy az Alkalmazásátjáró sikeresen létrejött-e, és sikeresen tud-e csatlakozni a háttérkiszolgálóhoz.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alkalmazásátjáró tesztelése](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Alkalmazásátjáróval létrehozott erőforrásokra, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor törli az Alkalmazásátjárót és annak összes kapcsolódó erőforrását is. 

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure PowerShell használatával](./tutorial-manage-web-traffic-powershell.md)
