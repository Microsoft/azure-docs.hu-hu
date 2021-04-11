---
title: TLS-házirend konfigurálása a PowerShell használatával
titleSuffix: Azure Application Gateway
description: Ez a cikk útmutatást nyújt a TLS-szabályzat Azure-beli konfigurálásához Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: ed81ca2c8f409b7f6c4a6f30f9bf75e594c6c208
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221704"
---
# <a name="configure-tls-policy-versions-and-cipher-suites-on-application-gateway"></a>A TLS-szabályzatverziók és a titkosítócsomagok konfigurálása az Application Gatewayen

Ismerje meg, hogyan konfigurálhatja a TLS/SSL-házirend verzióit és a titkosítási csomagokat Application Gatewayon. Kiválaszthatja az előre definiált szabályzatok listáját, amelyek a TLS-házirend verzióinak és az engedélyezett titkosítási csomagok különböző konfigurációit tartalmazzák. Lehetősége van arra is, hogy [Egyéni TLS-házirendet](#configure-a-custom-tls-policy) határozzon meg a követelmények alapján.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Javasoljuk, hogy a TLS 1,2-et használja a TLS protokoll minimális verziójaként a Application Gateway jobb biztonsága érdekében. 

## <a name="get-available-tls-options"></a>Elérhető TLS-beállítások beolvasása

A `Get-AzApplicationGatewayAvailableSslOptions` parancsmag az elérhető előre definiált szabályzatok, a rendelkezésre álló titkosítási csomagok és a konfigurálható protokollok listáját tartalmazza. Az alábbi példa egy példát mutat be a parancsmag futtatásához.

```
DefaultPolicy: AppGwSslPolicy20150501
PredefinedPolicies:
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20150501
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401S

AvailableCipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
    TLS_RSA_WITH_AES_128_GCM_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA256
    TLS_RSA_WITH_AES_128_CBC_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA
    TLS_RSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_3DES_EDE_CBC_SHA
    TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

AvailableProtocols:
    TLSv1_0
    TLSv1_1
    TLSv1_2
```

## <a name="list-pre-defined-tls-policies"></a>Előre definiált TLS-házirendek listázása

Az Application Gateway három előre definiált szabályzatot tartalmaz, amelyek felhasználhatók. A `Get-AzApplicationGatewaySslPredefinedPolicy` parancsmag lekéri ezeket a szabályzatokat. Minden házirendben engedélyezve vannak a protokollok és a titkosítási csomagok. Ezek az előre definiált szabályzatok használatával gyorsan konfigurálható a TLS-házirend az Application Gateway-ben. Alapértelmezés szerint a **AppGwSslPolicy20150501** van kiválasztva, ha nincs megadva adott TLS-házirend.

A következő kimenet egy példa a futtatásra `Get-AzApplicationGatewaySslPredefinedPolicy` .

```
Name: AppGwSslPolicy20150501
MinProtocolVersion: TLSv1_0
CipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
 ...
Name: AppGwSslPolicy20170401
MinProtocolVersion: TLSv1_1
CipherSuites:
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
...
```

## <a name="configure-a-custom-tls-policy"></a>Egyéni TLS-házirend konfigurálása

Egyéni TLS-házirend konfigurálásakor a következő paramétereket kell megadnia: PolicyType, MinProtocolVersion, CipherSuite és ApplicationGateway. Ha más paramétereket próbál átadni, hibaüzenet jelenik meg a Application Gateway létrehozásakor vagy frissítésekor. 

Az alábbi példa egyéni TLS-házirendet állít be egy Application gatewayen. Beállítja a protokoll minimális verzióját, `TLSv1_1` és engedélyezi a következő titkosítási csomagokat:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

> [!IMPORTANT]
> Egyéni TLS-házirend konfigurálásakor TLS_RSA_WITH_AES_256_CBC_SHA256t kell kiválasztani. Az Application Gateway ezt a titkosítási csomagot használja a háttér-felügyelethez. Ezt bármilyen más lakosztállyal együtt is használhatja, de ezt is ki kell választani. 

```powershell
# get an application gateway resource
$gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroup AdatumAppGatewayRG

# set the TLS policy on the application gateway
Set-AzApplicationGatewaySslPolicy -ApplicationGateway $gw -PolicyType Custom -MinProtocolVersion TLSv1_1 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"

# validate the TLS policy locally
Get-AzApplicationGatewaySslPolicy -ApplicationGateway $gw

# update the gateway with validated TLS policy
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="create-an-application-gateway-with-a-pre-defined-tls-policy"></a>Egy előre definiált TLS-házirenddel rendelkező Application Gateway létrehozása

Az előre definiált TLS-szabályzatok konfigurálásakor a következő paramétereket kell megadnia: PolicyType, PolicyName és ApplicationGateway. Ha más paramétereket próbál átadni, hibaüzenet jelenik meg a Application Gateway létrehozásakor vagy frissítésekor.

Az alábbi példa egy új Application Gateway-t hoz létre egy előre definiált TLS-házirenddel.

```powershell
# Create a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location "East US"

# Create a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with a 10.0.0.0/16 address space
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for later use
$subnet = $vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create an ip configuration object
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool for backend web servers
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

# Define the backend http settings to be used.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

# Create a new port for TLS
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 443

# Upload an existing pfx certificate for TLS offload
$password = ConvertTo-SecureString -String "P@ssw0rd" -AsPlainText -Force
$cert = New-AzApplicationGatewaySslCertificate -Name cert01 -CertificateFile C:\folder\contoso.pfx -Password $password

# Create a frontend IP configuration for the public IP address
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener with the certificate, port, and frontend ip.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a new rule for backend traffic routing
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the size of the application gateway
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Configure the TLS policy to use a different pre-defined policy
$policy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Create the application gateway.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName $rg.ResourceGroupName -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

## <a name="update-an-existing-application-gateway-with-a-pre-defined-tls-policy"></a>Meglévő Application Gateway frissítése előre definiált TLS-házirenddel

Egyéni TLS-házirend beállításához adja meg a következő paramétereket: **PolicyType**, **MinProtocolVersion**, **CipherSuite** és **ApplicationGateway**. Egy előre definiált TLS-házirend beállításához adja meg a következő paramétereket: **PolicyType**, **PolicyName** és **ApplicationGateway**. Ha más paramétereket próbál átadni, hibaüzenet jelenik meg a Application Gateway létrehozásakor vagy frissítésekor.

A következő példában az egyéni házirendhez és az előre definiált házirendhez is vannak kódok. A használni kívánt szabályzat megjegyzésének visszaírása.

```powershell
# You have to change these parameters to match your environment.
$AppGWname = "YourAppGwName"
$RG = "YourResourceGroupName"

$AppGw = get-Azapplicationgateway -Name $AppGWname -ResourceGroupName $RG

# Choose either custom policy or predefined policy and uncomment the one you want to use.

# TLS Custom Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Custom -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_RSA_WITH_AES_128_CBC_SHA256" -ApplicationGateway $AppGw

# TLS Predefined Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S" -ApplicationGateway $AppGW

# Update AppGW
# The TLS policy options are not validated or updated on the Application Gateway until this cmdlet is executed.
$SetGW = Set-AzApplicationGateway -ApplicationGateway $AppGW
```

## <a name="next-steps"></a>Következő lépések

A HTTP-forgalom HTTPS-végpontra való átirányításának megismeréséhez látogasson el [Application Gateway átirányítási áttekintésre](./redirect-overview.md) . 

Tekintse meg a figyelőre vonatkozó SSL-szabályzatok beállítását az [SSL-figyelő speciális házirendjének beállítása a portálon keresztül](./application-gateway-configure-listener-specific-ssl-policy.md)