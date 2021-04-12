---
title: Kölcsönös hitelesítés konfigurálása az Azure Application Gateway a PowerShell használatával
description: Megtudhatja, hogyan konfigurálhat egy Application Gateway a kölcsönös hitelesítéshez a PowerShell használatával
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231432"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Kölcsönös hitelesítés konfigurálása Application Gateway PowerShell-lel (előzetes verzió)
Ez a cikk azt ismerteti, hogyan használhatja a PowerShellt a Application Gateway kölcsönös hitelesítésének konfigurálására. A kölcsönös hitelesítés azt jelenti, Application Gateway hitelesíti a kérést küldő ügyfelet a Application Gatewayra feltöltött ügyféltanúsítvány használatával. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ehhez a cikkhez az Azure PowerShell-modul 1.0.0-es vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Login-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="before-you-begin"></a>Előkészületek

A kölcsönös hitelesítés Application Gatewayhoz való konfigurálásához szükség van egy ügyféltanúsítványt az átjáróra való feltöltéshez. Az ügyféltanúsítványt a rendszer a tanúsítvány érvényesítésére fogja használni az ügyfél Application Gateway. Tesztelési célból önaláírt tanúsítványt is használhat. Ez azonban nem javasolt az éles számítási feladatokhoz, mivel azok nehezebben kezelhetők és nem teljesen biztonságosak.

Ha többet szeretne megtudni, különösen arról, hogy milyen típusú Ügyféltanúsítványok tölthetők fel, tekintse meg [a kölcsönös hitelesítés áttekintését Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Először hozzon létre egy új erőforráscsoportot az előfizetésében. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Helyezzen üzembe egy virtuális hálózatot a Application Gateway telepítéséhez a alkalmazásban.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Nyilvános IP-cím létrehozása

Hozzon létre egy nyilvános IP-címet, amelyet a Application Gateway használhat. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>A Application Gateway IP-konfigurációjának létrehozása

Hozza létre az IP-konfigurációkat és a frontend portját. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>A frontend SSL konfigurálása 

Konfigurálja a Application Gatewayhoz tartozó SSL-tanúsítványokat.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Ügyfél-hitelesítés konfigurálása 

Konfigurálja az ügyfél-hitelesítést a Application Gateway. A megbízható ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak itt való kinyerésével kapcsolatos további információkért lásd: [megbízható ügyfél hitelesítésszolgáltatói tanúsítványainak kinyerése](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Győződjön meg arról, hogy a teljes ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványlánc egy fájlban van feltöltve, és a fájlnak csak egy lánca van.  

> [!NOTE]
> Javasoljuk, hogy a TLS 1,2-et a kölcsönös hitelesítéssel használja, mivel a TLS 1,2 a jövőben lesz felhatalmazva. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>A háttérrendszer-készlet és-beállítások konfigurálása

Állítsa be a háttér-készletet és a Application Gateway beállításait. Igény szerint beállíthatja a háttérbeli megbízható legfelső szintű tanúsítványt a végpontok közötti SSL-titkosításhoz.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>A szabály konfigurálása

Hozzon létre egy szabályt a Application Gateway.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Alapértelmezett SSL-szabályzat beállítása a jövőbeli figyelőkhöz

Beállított egy figyelőhöz tartozó SSL-szabályzatot a kölcsönös hitelesítés beállítása során. Ebben a lépésben megadhatja az alapértelmezett SSL-házirendet a létrehozott jövőbeli figyelőkhöz. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>A Application Gateway létrehozása

A fent létrehozott mindent a Application Gateway üzembe helyezésével teheti meg.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az Application Gatewayt és az összes kapcsolódó erőforrást a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával.

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Lejárt ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak megújítása

Abban az esetben, ha az ügyfél HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa lejárt, a következő lépésekkel frissítheti az átjáró tanúsítványát: 

1. Bejelentkezés az Azure-ba
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. A Application Gateway konfigurációjának beolvasása
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. A megbízható ügyféltanúsítvány eltávolítása az átjáróról 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Az új tanúsítvány hozzáadása az átjáróhoz 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Az átjáró frissítése az új tanúsítvánnyal 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Következő lépések

- [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)