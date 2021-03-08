---
title: 'Oktatóanyag: webalkalmazás-hozzáférés fejlesztése – Azure Application Gateway'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy automatikus skálázási, zóna-redundáns Application Gateway-t a fenntartott IP-címmel Azure PowerShell használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2a756313a4659dfc531289c2c86890371f700367
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452288"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Oktatóanyag: webalkalmazás-hozzáférés fejlesztését javító Application Gateway létrehozása

Ha Ön rendszergazda, aki a webalkalmazások hozzáférésének fejlesztését illeti, optimalizálhatja az Application Gateway-t az ügyfelek igényei szerint méretezhetővé, és több rendelkezésre állási zónára is kiterjedhet. Ez az oktatóanyag segítséget nyújt az Azure Application Gateway szolgáltatásainak konfigurálásához: automatikus skálázás, zóna-redundancia és fenntartott VIP-EK (statikus IP-cím). A probléma megoldásához Azure PowerShell parancsmagokat és a Azure Resource Manager üzembe helyezési modellt kell használnia.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Autoscale virtuális hálózat létrehozása
> * Fenntartott nyilvános IP-cím létrehozása
> * Az Application Gateway-infrastruktúra beállítása
> * Automatikus méretezés megadása
> * Application Gateway létrehozása
> * Az alkalmazásátjáró tesztelése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ehhez az oktatóanyaghoz helyi felügyeleti Azure PowerShell munkamenetet kell futtatnia. Telepítenie kell a Azure PowerShell modul 1.0.0 vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Connect-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az elérhető helyek egyikén.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles környezetben importálnia kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban egy önaláírt tanúsítványt hoz létre a [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsmaggal. Az [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) parancsmagot a visszaadott ujjlenyomattal futtatva egy PFX-fájlt exportálhat a tanúsítványból.

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

A pfx-fájl létrehozásához használja az ujjlenyomatot. Cserélje le a változót az *\<password>* Ön által választott jelszóra:

```powershell
$pwd = ConvertTo-SecureString -String "<password>" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```


## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy dedikált alhálózattal rendelkező virtuális hálózatot egy automatikus skálázási Application Gateway számára. Jelenleg az egyes dedikált alhálózatokon csak egy automatikus skálázású Application Gateway helyezhető üzembe.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Fenntartott nyilvános IP-cím létrehozása

A PublicIPAddress kiosztási módszerének meghatározása **statikusként**. Az automatikus skálázású Application Gateway virtuális IP-címe csak statikus lehet. A dinamikus IP-címek nem használhatók. Csak a standard PublicIPAddress termékváltozat támogatott.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard -Zone 1,2,3
```

## <a name="retrieve-details"></a>Részletek beolvasása

Egy helyi objektumban található erőforráscsoport, alhálózat és IP adatainak beolvasása az Application Gateway IP-konfigurációs adatainak létrehozásához.

```azurepowershell
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="create-web-apps"></a>Webalkalmazások létrehozása

Két webes alkalmazás konfigurálása a háttér-készlethez. Cserélje *\<site1-name>* le *\<site-2-name>* a és a nevet a tartományban található egyedi nevekre `azurewebsites.net` .

```azurepowershell
New-AzAppServicePlan -ResourceGroupName $rg -Name "ASP-01"  -Location $location -Tier Basic `
   -NumberofWorkers 2 -WorkerSize Small
New-AzWebApp -ResourceGroupName $rg -Name <site1-name> -Location $location -AppServicePlan ASP-01
New-AzWebApp -ResourceGroupName $rg -Name <site2-name> -Location $location -AppServicePlan ASP-01
```

## <a name="configure-the-infrastructure"></a>Az infrastruktúra konfigurálása

Konfigurálja az IP-konfigurációt, az előtér-IP-konfigurációt, a háttér-készletet, a HTTP-beállításokat, a tanúsítványt, a portot, a figyelőt és a szabályt azonos formátumban a meglévő standard Application Gateway-átjáróra. Az új termékváltozat a standard termékváltozattal megegyező objektummodellt követi.

Cserélje le a két webalkalmazás teljes tartománynevét (például: `mywebapp.azurewebsites.net` ) a $Pool változó definíciójában.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses <your first web app FQDN>, <your second web app FQDN>
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled -PickHostNameFromBackendAddress
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Automatikus méretezés megadása

Most megadhatja az Application Gateway automatikus skálázási konfigurációját. 

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
Ebben a módban az Application Gateway az alkalmazás forgalmi mintázata alapján automatikus skálázású.

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Hozza létre az Application Gatewayt, és tartalmazzon redundancia-zónákat és az automatikus skálázási konfigurációt.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az Application Gateway nyilvános IP-címének lekéréséhez használja a Get-AzPublicIPAddress. Másolja a nyilvános IP-címet vagy a DNS nevét, majd illessze be a böngésző címsorába.

```azurepowershell
$pip = Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
$pip.IpAddress
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Először vizsgálja meg az Application Gateway használatával létrehozott erőforrásokat. Ezután, ha már nincs rá szükség, az `Remove-AzResourceGroup` paranccsal eltávolíthatja az erőforráscsoportot, az Application Gatewayt és az összes kapcsolódó erőforrást.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásátjáró létrehozása URL-alapú útválasztási szabályokkal](./tutorial-url-route-powershell.md)