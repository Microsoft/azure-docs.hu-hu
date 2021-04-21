---
title: A API Management használata Virtual Network Application Gateway
titleSuffix: Azure API Management
description: Megtudhatja, hogyan lehet beállítani és konfigurálni az Azure API Management belső Virtual Network a Application Gateway (WAF) előtérként való használatával
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6500ecdb811306239951cb339abe2043d77b8cf2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813061"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Az API Management és az Application Gateway integrálása egy belső VNET-en

## <a name="overview"></a><a name="overview"></a> Áttekintés

Az API Management szolgáltatás belső módban Virtual Network konfigurálható, így az csak a belső Virtual Network. Azure Application Gateway egy PAAS-szolgáltatás, amely 7. rétegi terheléselosztást biztosít. Fordítottproxy-szolgáltatásként működik, és az ajánlata között kínál egy Web Application Firewall (WAF).

A API Management virtuális hálózatban kiépített virtuális gépek és a Application Gateway kombinálása a következő forgatókönyveket teszi lehetővé:

* Használja ugyanazt a API Management erőforrást a belső és a külső fogyasztók számára is.
* Használjon egyetlen API Management erőforrást, és az API-k egy részkészlete a API Management felhasználók számára elérhető.
* Kulcsra kapcsolódó módszer a nyilvános internetről API Management a nyilvános internetről való hozzáférés-váltáshoz.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikkben leírt lépésekhez a következőkre lesz szükség:

* Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Tanúsítványok – pfx és cer a fejlesztői portál gazdagépnevéhez használható API-gazdanévhez és pfx-hez.

## <a name="scenario"></a><a name="scenario"></a> Forgatókönyv

Ez a cikk bemutatja, hogyan használható egyetlen API Management szolgáltatás belső és külső felhasználók számára, és hogyan használható egyetlen előtereként a helyszíni és a felhőalapú API-k számára. Azt is látni fogja, hogyan teheti elérhetővé az API-knak csak egy részkészletét (a példában zöld színnel vannak kiemelve) a Külső használathoz az api-kban elérhető útválasztási Application Gateway.

Az első beállítási példában az összes API kezelése csak a saját Virtual Network. A belső felhasználók (narancssárga kiemeléssel kiemelve) hozzáférhetnek az összes belső és külső API-hoz. A forgalom soha nem halad ki az internetre. A nagy teljesítményű kapcsolat Express Route-kapcsolati kapcsolati kapcsolaton keresztül érhető el.

![URL-útvonal](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> A kezdés előtt

* Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. A telepítési utasításokat [lásd: Install Azure PowerShell.](/powershell/azure/install-az-ps) 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Mire van szükség az integráció létrehozásához a API Management és Application Gateway?

* **Háttérkiszolgáló-készlet:** Ez a szolgáltatás belső virtuális IP-API Management címe.
* **A háttérkiszolgáló-készlet beállításai:** Minden készlet rendelkezik olyan beállításokkal, mint a port, a protokoll és a cookie-alapú affinitás. Ezek a beállítások a készletben található összes kiszolgálóra érvényesek.
* **Előoldali port:** Ez az Application Gatewayen megnyitott nyilvános port. Az azt el érő forgalom az egyik háttérkiszolgálóra lesz átirányítva.
* **Figyelő:** A figyelő rendelkezik egy előoldali porttal, egy protokollal (Http vagy Https, ezek az értékek megkülönböztetik a kis- és nagybetűket) és a TLS/SSL-tanúsítvány nevével (TLS-alapú kiszervezés konfigurálása esetén).
* **Szabály:** A szabály egy háttérkiszolgáló-készlethez köti a figyelőt.
* **Egyéni állapot-mintavétel:** Application Gateway alapértelmezés szerint IP-címalapú mintavételekkel deríti ki, hogy a BackendAddressPool mely kiszolgálói aktívak. A API Management szolgáltatás csak a megfelelő állomásfejléccel válaszol a kérésekre, ezért az alapértelmezett mintavételek meghiúsulnak. Egyéni állapot-mintavételt kell meghatározni, hogy az Application Gateway megállapítsa, hogy a szolgáltatás működik- és továbbítja-e a kéréseket.
* **Egyéni tartományi tanúsítványok:** A API Management eléréséhez létre kell hoznia egy CNAME-leképezést az állomásnevének és az Application Gateway DNS-névnek. Ez biztosítja, hogy az állomásnév fejléce és a Application Gateway szolgáltatásnak küldött tanúsítvány, amely API Management APIM érvényesként ismerhető fel. Ebben a példában két tanúsítványt használunk a háttérhez és a fejlesztői portálhoz.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> A API Management és Application Gateway

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Hozzon létre egy Virtual Network, alhálózatot és nyilvános IP-címet a Application Gateway. Hozzon létre egy másik alhálózatot a API Management.
3. Hozzon létre egy API Management szolgáltatást a fent létrehozott VNET-alhálózaton belül, és győződjön meg arról, hogy a Belső módot használja.
4. Állítson be egy egyéni tartománynevet a API Management szolgáltatásban.
5. Hozzon létre Application Gateway konfigurációs objektumot.
6. Hozzon létre egy Application Gateway erőforrást.
7. Hozzon létre egy CNAME nevet a nyilvános DNS-Application Gateway a API Management gazdagépnévhez.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>A fejlesztői portál külső elérhetősése Application Gateway

Ebben az útmutatóban a  fejlesztői portált a külső közönség számára is elérhetővé fogjuk Application Gateway. További lépésekre van szükség a fejlesztői portál figyelőjének, mintavételének, beállításainak és szabályainak létrehozásához. Minden részletet a megfelelő lépésekben talál.

> [!WARNING]
> Ha Azure AD-t vagy külső hitelesítést használ, engedélyezze a [cookie-alapú munkamenet-affinitás](../application-gateway/features.md#session-affinity) funkciót a Application Gateway.

> [!WARNING]
> Ha meg Application Gateway, hogy a WAF feltörje az OpenAPI-specifikáció letöltését a fejlesztői portálon, le kell tiltania a `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` tűzfalszabályt.
> 
> Application Gateway WAF-szabályok, amelyek megszakíthatják a portál működését:
> 
> - `920300`, `920330` `931130` , , , , `942100` , , , , a felügyeleti `942110` `942180` `942200` `942260` `942340` `942370` módhoz
> - `942200`, `942260` `942370` , , a `942430` közzétett `942440` portálhoz

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-ba

```powershell
Connect-AzAccount
```

Hitelesítés a hitelesítő adataival.

### <a name="step-2"></a>2. lépés

Válassza ki a kívánt előfizetést.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az Application Gateway létrehozására vonatkozó összes parancs ugyanazt az erőforráscsoportot használja.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Hozzon létre egy Virtual Network és egy alhálózatot az Application Gatewayhez

Az alábbi példa bemutatja, hogyan hozhat létre Virtual Network a Resource Manager.

### <a name="step-1"></a>1. lépés

Rendelje hozzá a 10.0.0.0/24 címtartományt ahhoz az alhálózati változóhoz, amely a Application Gateway létrehozásához Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2. lépés

Rendelje hozzá a 10.0.1.0/24 címtartományt ahhoz az alhálózati változóhoz, API Management az alhálózati Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3. lépés

Hozzon létre Virtual Network **appgwvnet** nevű erőforráscsoportot az **apim-appGw-RG** erőforráscsoportban az USA nyugati régiójában. Használja a 10.0.0.0/16 előtagot a 10.0.0.0/24 és a 10.0.1.0/24 alhálózatokkal.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4. lépés

Alhálózati változó hozzárendelése a következő lépésekhez

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Belső módban konfigurált virtuális hálózatban API Management szolgáltatás létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre API Management szolgáltatást egy csak belső hozzáférésre konfigurált virtuális hálózatban.

### <a name="step-1"></a>1. lépés

Hozzon létre egy API Management Virtual Network objektumot a fent $apimsubnetdata alhálózat használatával.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>2. lépés

Hozzon létre egy API Management szolgáltatást a Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

A fenti parancs sikeres elérése után tekintse meg a belső [VNET-API Management](api-management-using-with-internal-vnet.md#apim-dns-configuration) eléréséhez szükséges DNS-konfigurációt. Ez a lépés fél óránál tovább is tart.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Egyéni tartománynév beállítása a API Management

> [!IMPORTANT]
> Az [új fejlesztői portálon](api-management-howto-developer-portal.md) az alábbi lépéseken kívül API Management engedélyezni a kapcsolatot a API Management felügyeleti végpontjára.

### <a name="step-1"></a>1. lépés

Inicializálja a következő változókat a tartományok titkos kulcsait tartalmazó tanúsítványok adataival. Ebben a példában a és a `api.contoso.net` et `portal.contoso.net` használjuk.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>2. lépés

Hozza létre és állítsa be az állomásnév konfigurációs objektumát a proxyhoz és a portálhoz.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Az örökölt fejlesztői portál kapcsolatának konfigurálását a következőre kell `-HostnameType DeveloperPortal` `-HostnameType Portal` cserélnie: .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy **publicIP01** nyilvános IP-erőforrást az erőforráscsoportban.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-application-gateway-configuration"></a>Application Gateway-konfiguráció létrehozása

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítani. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2. lépés

Konfigurálja az előoldali IP-portot a nyilvános IP-végponthoz. Ez a port az a port, amelyhez a végfelhasználók csatlakoznak.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>3. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4. lépés

Konfigurálja a tanúsítványokat a Application Gateway, amely az áthaladó forgalom visszafejtéséhez és újratitkosíttatásához lesz használva.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>5. lépés

Hozza létre a http- figyelőket a Application Gateway. Rendelje hozzájuk az előoldali IP-konfigurációt, a portot és a TLS/SSL-tanúsítványokat.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>6. lépés

Hozzon létre egyéni mintavételeket a API Management `ContosoApi` szolgáltatásproxy tartományvégpontjára. Az elérési út egy alapértelmezett állapotvégpont, amely az összes API Management `/status-0123456789abcdef` található. A TLS-/SSL-tanúsítvánnyal való biztonságossá teendő egyéni mintavételi `api.contoso.net` gazdanévként állítsa be a következőt: .

> [!NOTE]
> Az állomásnév az alapértelmezett proxy gazdaneve, amely akkor van konfigurálva, amikor egy nevű szolgáltatás `contosoapi.azure-api.net` létrejön a `contosoapi` nyilvános Azure-ban.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7. lépés

Töltse fel a TLS-kompatibilis háttérkészlet erőforrásain használni szükséges tanúsítványt. Ez ugyanaz a tanúsítvány, amelyet a 4. lépésben adott meg.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>8. lépés

Konfigurálja a HTTP-háttérbeállításokat a Application Gateway. Ez magában foglalja a háttérkérések időkorlátának beállítását, amely után azok megszakadnak. Ez az érték eltér a mintavételi időkorrekta értéktől.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9. lépés

Konfigurálja az **apimbackend**  nevű háttér-IP-címkészletet a fent létrehozott virtuális API Management IP-címével.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>10. lépés

Hozzon létre szabályokat a Application Gateway alapszintű útválasztás használatára.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Módosítsa a -RuleType és az útválasztás beállítását a fejlesztői portál egyes lapjaihoz való hozzáférés korlátozásához.

### <a name="step-11"></a>11. lépés

Konfigurálja a példányok számát és méretét a Application Gateway. Ebben a példában a [WAF termékváltozatot](../web-application-firewall/ag/ag-overview.md) használjuk az erőforrás API Management érdekében.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>12. lépés

Konfigurálja úgy a WAF-et, hogy "Megelőzés" módban legyen.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Create Application Gateway

Hozzon létre Application Gateway az előző lépések összes konfigurációs objektumával.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME: API Management proxy gazdaneve a nyilvános DNS-névre a Application Gateway erőforrásban

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím használata esetén a Application Gateway dinamikusan hozzárendelt DNS-névre van szükség, amely nem mindig egyszerű.

Az Application Gateway DNS-nevével kell létrehozni egy CNAME rekordot, amely erre a DNS-névre mutat (például a fenti `api.contoso.net` példákban). Az előtere IP CNAME rekord konfiguráláshoz a PublicIPAddress elem használatával Application Gateway le az ip-cím és a hozzá tartozó IP-cím/DNS-név adatait. Az A-rekordok használata nem ajánlott, mivel a VIP változhat az átjáró újraindításakor.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Összefoglalás
A API Management virtuális hálózatban konfigurált Azure-beli virtuális gépek egyetlen átjáróillesztőt kínálnak az összes konfigurált API-hoz, függetlenül attól, hogy azok a helyszínen vagy a felhőben vannak üzemeltetve. Az Application Gateway API Management integrálása biztosítja azt a rugalmasságot, amely lehetővé teszi, hogy bizonyos API-k elérhetők legyenek az interneten, valamint hogy egy Web Application Firewall-t biztosít a API Management-példány előtereként.

## <a name="next-steps"></a><a name="next-steps"></a> Következő lépések
* További információ a Azure Application Gateway
  * [Application Gateway áttekintés](../application-gateway/overview.md)
  * [Application Gateway Web Application Firewall](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway elérésiút-alapú útválasztás használatával](../application-gateway/tutorial-url-route-powershell.md)
* További információ a virtuális API Management virtuális gépekről
  * [A API Management csak a VNET-en belül érhető el](api-management-using-with-internal-vnet.md)
  * [Az API Management használata virtuális hálózatban](api-management-using-with-vnet.md)
