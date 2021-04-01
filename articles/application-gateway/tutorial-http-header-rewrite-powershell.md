---
title: Azure-Application Gateway létrehozása & a HTTP-fejlécek újraírása
description: Ez a cikk az Azure-Application Gateway létrehozásával és a HTTP-fejlécek újraírásával kapcsolatos információkat tartalmaz a Azure PowerShell használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 4a1a122eb7b5b0abcc47cd321c74267a1a4aecda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93396855"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Application Gateway létrehozása és a HTTP-fejlécek újraírása

Az új automatikus [skálázás és a Zone-redundáns Application Gateway SKU](./application-gateway-autoscaling-zone-redundant.md) létrehozásakor az Azure PowerShell használatával KONFIGURÁLHATÓK a [HTTP-kérések és a válaszok fejlécének újraírására szolgáló szabályok](rewrite-http-headers.md) .

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Autoscale virtuális hálózat létrehozása
* Fenntartott nyilvános IP-cím létrehozása
* Az Application Gateway-infrastruktúra beállítása
* Adja meg a HTTP-fejléc Újraírási szabályának konfigurációját
* Automatikus méretezés megadása
* Application Gateway létrehozása
* Az alkalmazásátjáró tesztelése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez Azure PowerShell helyileg kell futtatnia. Az az modul Version 1.0.0 vagy újabb verziójának telepítve kell lennie. Futtassa `Import-Module Az` a parancsot, majd `Get-Module Az` Keresse meg a verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az elérhető helyek egyikén.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
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
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Részletek beolvasása

Egy helyi objektumban található erőforráscsoport, alhálózat és IP adatainak beolvasása az Application Gateway IP-konfigurációs adatainak létrehozásához.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Az infrastruktúra konfigurálása

Konfigurálja az IP-konfigurációt, az előtér-IP-konfigurációt, a háttér-készletet, a HTTP-beállításokat, a tanúsítványt, a portot és a figyelőt azonos formátumban a meglévő standard Application Gateway-átjáróhoz. Az új termékváltozat a standard termékváltozattal megegyező objektummodellt követi.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Adja meg a HTTP-fejléc Újraírási szabályának konfigurációját

Konfigurálja a HTTP-fejlécek újraírásához szükséges új objektumokat:

- **RequestHeaderConfiguration**: ezzel az objektummal megadhatja az újraírni kívánt kérelem fejlécének mezőit, valamint azt az új értéket, amelyet az eredeti fejléceknek újra kell írnia.
- **ResponseHeaderConfiguration**: ezzel az objektummal megadhatja az újraírni kívánt válasz fejléc-mezőket, valamint azt az új értéket, amelyet az eredeti fejléceknek újra kell írnia.
- **ActionSet**: ez az objektum tartalmazza a fent megadott kérelem és válasz fejlécek konfigurációit. 
- **RewriteRule**: ez az objektum tartalmazza a fent megadott összes *actionSets* . 
- **RewriteRuleSet**– ez az objektum tartalmazza az összes *rewriteRules* , és csatolni kell egy kérelem útválasztási szabályához – alapszintű vagy elérésiút-alapú.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Útválasztási szabály meghatározása

Hozzon létre egy kérelem-útválasztási szabályt. A létrehozást követően az Újraírási konfiguráció a forrás-figyelőhöz az útválasztási szabály használatával van csatolva. Alapszintű útválasztási szabály használatakor a fejléc-Újraírási konfiguráció egy forrás-figyelőhöz van társítva, és a globális fejléc újraírása. Elérésiút-alapú útválasztási szabály használatakor a rendszer a fejléc-Újraírási konfigurációt az URL elérési útja alapján határozza meg. Tehát csak a helyek adott elérési útjára vonatkozik. Az alábbiakban egy alapszintű útválasztási szabály jön létre, és a rendszer csatolja az Újraírási szabályt.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Automatikus méretezés megadása

Most megadhatja az Application Gateway automatikus skálázási konfigurációját. Az Application Gateway két automatikus skálázási típust támogat:

* **Rögzített kapacitású mód**. Ebben a módban az Application Gateway nem automatikus skálázású, és rögzített skálázásiegység-kapacitással működik.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Automatikus skálázási mód**. Ebben a módban az Application Gateway az alkalmazás forgalmi mintázata alapján automatikus skálázású.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Hozza létre az Application Gatewayt, és tartalmazzon redundancia-zónákat és az automatikus skálázási konfigurációt.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az Application Gateway nyilvános IP-címének lekéréséhez használja a Get-AzPublicIPAddress. Másolja a nyilvános IP-címet vagy a DNS nevét, majd illessze be a böngésző címsorába.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Először vizsgálja meg az Application Gateway használatával létrehozott erőforrásokat. Ezután, ha már nincs rá szükség, az `Remove-AzResourceGroup` paranccsal eltávolíthatja az erőforráscsoportot, az Application Gatewayt és az összes kapcsolódó erőforrást.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Következő lépések

- [Alkalmazásátjáró létrehozása URL-alapú útválasztási szabályokkal](./tutorial-url-route-powershell.md)