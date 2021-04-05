---
title: Végpontok közötti TLS konfigurálása az Azure Application Gateway
description: Ez a cikk bemutatja, hogyan konfigurálható a végpontok közötti TLS az Azure Application Gateway a PowerShell használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397807"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Teljes körű TLS konfigurálása az Application Gateway használatával a PowerShell-lel

## <a name="overview"></a>Áttekintés

Az Azure Application Gateway támogatja a forgalom végpontok közötti titkosítását. Application Gateway leállítja a TLS/SSL-kapcsolatokat az Application Gateway-ben. Az átjáró Ezután alkalmazza az útválasztási szabályokat a forgalomra, majd újratitkosítja a csomagot, és a megadott útválasztási szabályok alapján továbbítja a csomagot a megfelelő háttér-kiszolgálóra. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz.

Application Gateway támogatja az egyéni TLS-beállítások definiálását. Emellett a következő protokoll-verziók letiltását is támogatja: **TLS 1.0**, **TLS 1.1** és **TLS 1.2**, valamint a használandó titkosítási csomagok meghatározása és a preferencia sorrendje. A konfigurálható TLS-beállításokkal kapcsolatos további tudnivalókért tekintse meg a [TLS-házirend áttekintése](application-gateway-SSL-policy-overview.md)című témakört.

> [!NOTE]
> Az SSL 2,0 és az SSL 3,0 alapértelmezés szerint le van tiltva, ezért nem engedélyezhető. Nem biztonságosnak minősülnek, és nem használhatók Application Gateway.

![esettanulmány képe][scenario]

## <a name="scenario"></a>Eset

Ebből a forgatókönyvből megtudhatja, hogyan hozhat létre egy Application Gateway-t a végpontok közötti TLS és a PowerShell használatával.

Ez a forgatókönyv a következőket teszi:

* Hozzon létre egy **appgw-RG** nevű erőforráscsoportot.
* Hozzon létre egy **appgwvnet** nevű virtuális hálózatot a **10.0.0.0/16** címtartomány megadásával.
* Hozzon létre két alhálózatot, amelyek neve **appgwsubnet** és **appsubnet**.
* Hozzon létre egy kisméretű Application Gatewayt, amely támogatja a TLS protokoll verzióit és a titkosítási csomagokat korlátozó végpontok közötti TLS-titkosítást.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha a végpontok közötti TLS-t egy Application gatewayrel szeretné konfigurálni, akkor az átjáróhoz és a tanúsítványokhoz szükséges tanúsítvány szükséges a háttér-kiszolgálókhoz. Az átjáró tanúsítványa egy szimmetrikus kulcsnak a TLS protokoll specifikációja alapján történő származtatása céljából használatos. Ezután a szimmetrikus kulcs titkosítva lesz, és visszafejti az átjárónak eljuttatott forgalmat. Az átjáró tanúsítványának személyes információcsere (PFX) formátumban kell lennie. Ez a fájlformátum lehetővé teszi az Application Gateway által igényelt titkos kulcs exportálását a forgalom titkosításának és visszafejtésének elvégzéséhez.

A végpontok közötti TLS-titkosításhoz a háttérnek explicit módon engedélyeznie kell az Application Gateway számára. Töltse fel a háttér-kiszolgálók nyilvános tanúsítványát az Application gatewaybe. A tanúsítvány hozzáadásával biztosítható, hogy az Application Gateway csak az ismert háttérbeli példányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

A konfigurációs folyamatról a következő szakaszokban olvashat.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Ez a szakasz végigvezeti az Application Gatewayt tartalmazó erőforráscsoport létrehozásán.

1. Jelentkezzen be Azure-fiókjába.

   ```powershell
   Connect-AzAccount
   ```

2. Válassza ki a forgatókönyvhöz használni kívánt előfizetést.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Hozzon létre egy erőforráscsoportot. (Hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

A következő példa egy virtuális hálózatot és két alhálózatot hoz létre. Az Application Gateway tárolására egy alhálózat szolgál. A másik alhálózatot a webalkalmazást futtató háttérbeli végpontok használják.

1. Rendeljen hozzá egy címtartományt az Application gatewayhez használni kívánt alhálózathoz.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Az Application gatewayhez konfigurált alhálózatokat megfelelően kell méretezni. Az Application Gateway legfeljebb 10 példányra konfigurálható. Az egyes példányok egy IP-címet vesznek fel az alhálózatból. Az alhálózatok túl kis része hátrányosan befolyásolhatja az Application Gateway horizontális felskálázását.
   >

2. Rendeljen hozzá egy címtartományt a háttér-címkészlet használatára.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Hozzon létre egy virtuális hálózatot az előző lépésekben definiált alhálózatokkal.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. A következő lépésekben használandó virtuális hálózati erőforrás és alhálózati erőforrások beolvasása.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-erőforrást, amelyet az Application gatewayhez kíván használni. Ez a nyilvános IP-cím az alábbi lépések egyikében használatos.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> A Application Gateway nem támogatja a megadott tartományi címkével létrehozott nyilvános IP-cím használatát. Csak a dinamikusan létrehozott tartományi címkével rendelkező nyilvános IP-címek támogatottak. Ha az Application gatewayhez felhasználóbarát DNS-nevet igényel, javasoljuk, hogy CNAME rekordot használjon aliasként.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

Az Application Gateway létrehozása előtt minden konfigurációs elem be van állítva. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

1. Hozzon létre egy Application Gateway IP-konfigurációt. Ezzel a beállítással konfigurálható, hogy az Application Gateway mely alhálózatokat használja. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Előtér-IP-konfiguráció létrehozása. Ez a beállítás egy privát vagy nyilvános IP-címet képez le az Application Gateway elülső végéhez. A következő lépés társítja a nyilvános IP-címet az előző lépésben az előtér-IP-konfigurációval.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Konfigurálja a háttérbeli IP-címkészletet a háttér-webkiszolgálók IP-címeivel. Ezek az IP-címek fogadják majd az előtérbeli IP-cím végpontból érkező hálózati forgalmat. Cserélje le a minta IP-címeit a saját alkalmazás IP-címeinek végpontokra.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > A teljes tartománynév (FQDN) szintén érvényes érték, amelyet a rendszer a háttér-kiszolgálókhoz tartozó IP-cím helyett használ. Ezt a **-BackendFqdns** kapcsoló használatával engedélyezheti. 

4. Konfigurálja az előtér-IP-portot a nyilvános IP-végponthoz. Ez a port az a port, amelyhez a végfelhasználók csatlakoznak.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurálja az Application Gateway tanúsítványát. Ez a tanúsítvány az Application Gateway forgalmának visszafejtésére és újratitkosítására szolgál.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Ez a minta konfigurálja a TLS-kapcsolatban használt tanúsítványt. A tanúsítványnak. pfx formátumúnak kell lennie, és a jelszónak 4 – 12 karakterből kell állnia.

6. Hozza létre a HTTP-figyelőt az Application Gateway számára. Rendelje hozzá a használni kívánt előtér-IP-konfigurációt, portot és TLS/SSL-tanúsítványt.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Töltse fel a TLS-t támogató háttérbeli készlet erőforrásaihoz használni kívánt tanúsítványt.

   > [!NOTE]
   > Az alapértelmezett mintavétel lekéri a nyilvános kulcsot a háttér IP-címéhez tartozó *alapértelmezett* TLS-kötésből, és összehasonlítja az itt megadott nyilvános kulcs értékét. 
   > 
   > Ha a háttérben használ állomásfejléc-t és Kiszolgálónév jelzése (SNI), akkor előfordulhat, hogy a beolvasott nyilvános kulcs nem az a kívánt hely, ahová a forgalom áramlik. Ha kétségei vannak, látogasson el a https://127.0.0.1/ háttér-kiszolgálók elemre, és erősítse meg, hogy melyik tanúsítványt használja a rendszer az *alapértelmezett* TLS-kötéshez. Az adott kérelemből származó nyilvános kulcs használata ebben a szakaszban. Ha a gazdagép-fejléceket és a SNI HTTPS-kötéseken használja, és nem kap választ és tanúsítványt a https://127.0.0.1/ háttér-kiszolgálókon futó kézi böngészőtől, akkor a rájuk vonatkozó alapértelmezett TLS-kötést kell beállítania. Ha ezt nem teszi meg, a mintavétel meghiúsul, és a háttér nem engedélyezett.
   
   A Application Gateway SNI kapcsolatos további információkért lásd: a TLS-leállítás [és a teljes körű TLS](ssl-overview.md)-végpont áttekintése a Application Gateway használatával.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Az előző lépésben megadott tanúsítványnak a háttérben lévő. pfx-tanúsítvány nyilvános kulcsának kell lennie. Exportálja a tanúsítványt (nem a főtanúsítványt) az igénylés, a bizonyíték és az indoklás (CER) formátumba, és használja azt ebben a lépésben. Ez a lépés lehetővé teszi a háttér használatát az Application Gateway használatával.

   Ha a Application Gateway v2 SKU-t használja, akkor hitelesítési tanúsítvány helyett hozzon létre egy megbízható főtanúsítványt. További információ: [a végpontok közötti TLS áttekintése Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku)használatával:

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurálja a HTTP-beállításokat az Application Gateway háttérrendszer számára. Rendelje hozzá az előző lépésben feltöltött tanúsítványt a HTTP-beállításokhoz.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   A Application Gateway v2 SKU esetében használja a következő parancsot:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Hozzon létre egy terheléselosztó-útválasztási szabályt, amely a terheléselosztó viselkedését konfigurálja. Ebben a példában egy alapszintű ciklikus multiplexelés szabály jön létre.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurálja az Application Gateway példányméretét. A rendelkezésre álló méretek **standard \_ kisméretű**, standard szintű **\_ közepes** és **standard \_ méretűek**.  A kapacitáshoz az elérhető értékek **1** – **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > A példányok száma 1 lehet tesztelési célra kiválasztható. Fontos tudni, hogy a két példány alatti példányszám nem vonatkozik az SLA-ra, ezért nem ajánlott. Kis átjárókat kell használni a fejlesztési teszteléshez, és nem éles környezetben.

11. Konfigurálja az Application Gateway-ben használni kívánt TLS-házirendet. Application Gateway támogatja a TLS protokoll verzióinak minimális verziójának megadását.

    A következő értékek a definiálható protokoll-verziók listáját jelentik:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    A következő példa a protokoll minimális verzióját állítja be **TLSv1_2re** , és engedélyezi a **TLS- \_ ECDHE \_ ECDSA \_ \_ AES \_ 128 \_ GCM \_ sha256**, **TLS \_ ECDHE \_ ECDSA és \_ \_ AES \_ 256 \_ GCM \_ SHA384**, valamint **\_ a TLS RSA és \_ \_ AES \_ 128 \_ GCM \_ sha256** .

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az összes fenti lépés használatával hozza létre az Application Gatewayt. Az átjáró létrehozása olyan folyamat, amely hosszú időt vesz igénybe.

V1 SKU esetén használja az alábbi parancsot.
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

A v2 SKU esetében használja az alábbi parancsot.
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Új tanúsítvány alkalmazása, ha a háttérbeli tanúsítvány lejárt

Ezzel az eljárással új tanúsítványt alkalmazhat, ha a háttérbeli tanúsítvány lejárt.

1. Kérje le a frissíteni kívánt Application Gateway-átjárót.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Adja hozzá az új tanúsítvány-erőforrást a. cer fájlból, amely tartalmazza a tanúsítvány nyilvános kulcsát, és ugyanezt a tanúsítványt is hozzáadhatja a figyelőhöz a TLS-lezáráshoz az Application gatewayben.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Szerezze be az új hitelesítési tanúsítvány objektumot egy változóba (TypeName: Microsoft. Azure. commands. Network. models. PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Rendelje hozzá az új tanúsítványt a **BackendHttp** -beállításhoz, és tekintse át a $AuthCert változóval. (Adja meg a módosítani kívánt HTTP-beállítás nevét.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Véglegesítse a változást az Application gatewayben, és adja át a $out változóban lévő új konfigurációt.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Nem használt lejárt tanúsítvány eltávolítása a HTTP-beállításokból

Ezzel az eljárással távolíthatja el a nem használt lejárt tanúsítványokat a HTTP-beállítások közül.

1. Kérje le a frissíteni kívánt Application Gateway-átjárót.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. A törölni kívánt hitelesítési tanúsítvány nevének listázása.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Távolítsa el a hitelesítési tanúsítványt az Application gatewayből.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Véglegesítse a módosítást.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>TLS protokoll verzióinak korlátozása meglévő Application Gateway-átjárón

Az előző lépések végigvezetik a teljes körű TLS-alkalmazás létrehozásán, és bizonyos TLS protokoll-verziók letiltásán. Az alábbi példa letiltja bizonyos TLS-házirendeket egy meglévő Application Gateway-példányon.

1. Kérje le a frissíteni kívánt Application Gateway-átjárót.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Adja meg a TLS-házirendet. A következő példában a **TLS 1.0** és a **TLS 1.1** le van tiltva, és a titkosító csomagok **TLS \_ ECDHE \_ ECDSA \_ \_ AES \_ 128 \_ GCM \_ sha256**, **TLS \_ ECDHE \_ ECDSA és \_ \_ AES \_ 256 \_ GCM \_ SHA384**, valamint a **TLS RSA és \_ \_ az \_ AES \_ 128 \_ GCM \_ sha256** az egyetlen megengedett.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Végül frissítse az átjárót. Ez az utolsó lépés egy hosszan futó feladat. Ha elkészült, a végpontok közötti TLS konfigurálva van az Application Gateway-ben.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Application Gateway DNS-név beszerzése

Az átjáró létrehozása után a következő lépés az előtér konfigurálása a kommunikációhoz. A Application Gateway egy nyilvános IP-cím használatakor dinamikusan hozzárendelt DNS-nevet igényel, amely nem barátságos. Annak biztosítása érdekében, hogy a végfelhasználók elérjék az Application Gatewayt, használhat egy CNAME rekordot, amely az Application Gateway nyilvános végpontján mutat. További információ: [Egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Alias konfigurálásához az Application Gateway és a hozzá tartozó IP/DNS-név adatait az Application gatewayhez csatolt **PublicIPAddress** elem használatával kérheti le. Az Application Gateway DNS-nevének használatával hozzon létre egy olyan CNAME rekordot, amely a két webalkalmazást erre a DNS-névre mutat. Nem javasoljuk a-Records használatát, mert a virtuális IP-cím megváltozhat az Application Gateway újraindításakor.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Következő lépések

További információ a webalkalmazások biztonságának a webalkalmazási tűzfallal való megkeményedéséről Application Gatewayon keresztül: [webalkalmazási tűzfal – áttekintés](../web-application-firewall/ag/ag-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png