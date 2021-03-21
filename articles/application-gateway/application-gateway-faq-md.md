---
title: Gyakran ismételt kérdések az Azure Application Gateway
description: Válaszokat talál az Azure Application Gateway-val kapcsolatos gyakori kérdésekre.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 83779dcd319614ae15de6b7e3e4e3abfd9599089
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102619154"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Gyakori kérdések a Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi gyakori kérdések az Azure Application Gateway.

## <a name="general"></a>Általános kérdések

### <a name="what-is-application-gateway"></a>Mi az Application Gateway?

Az Azure Application Gateway egy Application Delivery Controller (ADC) szolgáltatást biztosít szolgáltatásként. Különböző, 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazásaihoz. Ez a szolgáltatás kiválóan elérhető, méretezhető és teljes körűen felügyelhető az Azure-ban.

### <a name="what-features-does-application-gateway-support"></a>Milyen funkciókat támogat a Application Gateway?

Application Gateway támogatja az automatikus skálázást, a TLS-kiszervezést és a végpontok közötti TLS-t, a webalkalmazási tűzfalat (WAF), a cookie-alapú munkamenet-affinitást, az URL-elérésiút-alapú útválasztást, a többhelyes üzemeltetést és más funkciókat. A támogatott szolgáltatások teljes listájáért lásd: [Bevezetés a Application Gatewayba](./overview.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Miben különböznek Application Gateway és Azure Load Balancer?

A Application Gateway egy 7. rétegbeli terheléselosztó, ami azt jelenti, hogy csak a webes forgalom (HTTP, HTTPS, WebSocket és HTTP/2) használatával működik. Olyan képességeket támogat, mint például a TLS-megszakítás, a cookie-alapú munkamenet-affinitás és a ciklikus multiplexelés a terheléselosztási forgalom számára. Load Balancer terheléselosztási forgalmat a 4-es rétegben (TCP vagy UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Milyen protokollokat támogat a Application Gateway?

A Application Gateway támogatja a HTTP, a HTTPS, a HTTP/2 és a WebSocket használatát.

### <a name="how-does-application-gateway-support-http2"></a>Hogyan támogatja a Application Gateway a HTTP/2-t?

Lásd: [http/2 támogatás](./configuration-listeners.md#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Milyen erőforrások támogatottak a háttérbeli készlet részeként?

Lásd: [támogatott háttérbeli erőforrások](./application-gateway-components.md#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Milyen régiókban Application Gateway elérhető?

A Application Gateway v1 (standard és WAF) a globális Azure minden régiójában elérhető. Az [Azure China 21Vianet](https://www.azure.cn/) és [Azure Government](https://azure.microsoft.com/overview/clouds/government/)is elérhető.

A Application Gateway v2 (Standard_v2 és WAF_v2) rendelkezésre állása: [Application Gateway v2 támogatott régiói](./application-gateway-autoscaling-zone-redundant.md#supported-regions)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Ez az üzembe helyezés a saját előfizetéshez van hozzárendelve, vagy megosztották az ügyfelek között?

Application Gateway egy dedikált üzembe helyezés a virtuális hálózaton.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Támogatja a Application Gateway a HTTP és a HTTPS közötti átirányítást?

Az átirányítás támogatott. Lásd: [Application Gateway átirányítás áttekintése](./redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Milyen sorrendben dolgozza fel a figyelőket?

Tekintse meg a [figyelő feldolgozásának sorrendjét](./configuration-listeners.md#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Hol találom a Application Gateway IP-címet és a DNS-t?

Ha nyilvános IP-címet használ végpontként, a nyilvános IP-cím erőforráson megtalálhatja az IP-és DNS-információkat. Vagy keresse meg a portálon az Application Gateway áttekintés lapján. Ha belső IP-címeket használ, keresse meg az információkat az Áttekintés oldalon.

A v2 SKU esetében nyissa meg a nyilvános IP-erőforrást, és válassza a **konfiguráció** lehetőséget. A DNS-név **címke (nem kötelező)** mezője elérhető a DNS-név konfigurálásához.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Mik a Keep-Alive időtúllépés és a TCP Üresjárati időkorlát beállításai?

Az *életben tartási időkorlát* azt szabályozza, hogy a Application Gateway mennyi ideig várjon, amíg az ügyfél egy másik HTTP-kérést küld egy állandó kapcsolaton, mielőtt újra felhasználja vagy bezárja. A *TCP üresjárati időkorlátja* azt szabályozza, hogy a TCP-kapcsolatok mennyi ideig legyenek nyitva, ha nincs tevékenység. 

A *Keep-Alive időtúllépés* a Application Gateway v1 SKU-ban 120 másodperc, a v2 sku pedig 75 másodperc. A *TCP Üresjárati időkorlát* 4 perces alapértelmezett érték a (z) v1-es és v2-es Application Gateway-alapú virtuális IP-cím (VIP) esetében. A TCP Üresjárati időkorlát értékét a v1-es és v2-es Application Gateway-ben beállíthatja 4 perc és 30 perc között. A v1 és v2 Application Gateway esetén a Application Gateway nyilvános IP-címére kell navigálnia, és módosítania kell a TCP üresjárati időkorlátot a nyilvános IP-cím "konfiguráció" paneljén a portálon. A nyilvános IP-cím TCP üresjárati időtúllépési értékét a PowerShellen keresztül állíthatja be a következő parancsok futtatásával: 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Az IP-cím vagy a DNS-név módosul az Application Gateway élettartama során?

Application Gateway v1 SKU-ban a virtuális IP-cím változhat, ha leállítja és elindítja az Application Gatewayt. Az Application gatewayhez társított DNS-név azonban nem változik az átjáró élettartama során. Mivel a DNS-név nem változik, CNAME aliast kell használnia, és az Application Gateway DNS-címeként kell mutatnia. Application Gateway v2 SKU-ban az IP-címet statikusként állíthatja be, így az IP-cím és a DNS-név nem változik az Application Gateway élettartama során. 

### <a name="does-application-gateway-support-static-ip"></a>Támogatja a Application Gateway a statikus IP-címet?

Igen, a Application Gateway v2 SKU támogatja a statikus nyilvános IP-címeket. A v1 SKU támogatja a statikus belső IP-címeket.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway támogatja több nyilvános IP-címet az átjárón?

Az Application Gateway csak egy nyilvános IP-címet támogat.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Milyen nagy legyen az alhálózat a Application Gateway?

Lásd [Application Gateway alhálózat méretével kapcsolatos szempontokat](./configuration-infrastructure.md#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Telepíthetek több Application Gateway erőforrást egyetlen alhálózatra?

Igen. Egy adott Application Gateway-telepítés több példányán kívül egy másik egyedi Application Gateway erőforrást is kiépíthet egy meglévő alhálózatba, amely egy másik Application Gateway erőforrást tartalmaz.

Egyetlen alhálózat nem támogatja mind a v2, mind a v1 Application Gateway SKU-t.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>A Application Gateway v2 támogatja a felhasználó által megadott útvonalakat (UDR)?

Igen, de csak bizonyos forgatókönyvek. További információ: [Application Gateway infrastruktúra-konfiguráció](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Támogatja a Application Gateway az x által továbbított fejléceket?

Igen. Tekintse [meg a kérelem módosításait](./how-application-gateway-works.md#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Mennyi ideig tart az Application Gateway üzembe helyezése? Működik az Application Gateway a frissítés közben?

Az új Application Gateway v1 SKU üzembe helyezése akár 20 percet is igénybe vehet. A példányok méretének vagy számának módosításai nem zavaróak, és az átjáró ebben az időszakban aktív marad.

A v2 SKU-t használó legtöbb üzemelő példány körülbelül 6 percet vesz igénybe. Azonban a telepítés típusától függően hosszabb időt is igénybe vehet. Ha például több Availability Zones több példányban üzemelő példányok több mint 6 percet vesznek igénybe. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Használhatom az Exchange Servert háttérként a Application Gateway?

Nem. A Application Gateway nem támogatja az e-mail-protokollok, például az SMTP, az IMAP és a POP3 használatát.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Van elérhető útmutató a v1 SKU-ról a v2 SKU-ra való Migrálás céljából?

Igen. Részletekért lásd: az [Azure Application Gateway és a webalkalmazási tűzfal áttelepítésének v1-ről v2-re](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Továbbra is támogatja a Application Gateway v1 SKU-t?

Igen. A Application Gateway v1 SKU továbbra is támogatott lesz. Javasoljuk azonban, hogy a v2-re váltson, hogy kihasználhassa az adott SKU-ban található szolgáltatás frissítéseit. További információ: automatikus [skálázás és zóna – redundáns Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Támogatja az Application Gateway v2 az NTLM-hitelesítéssel történő proxy-kérelmeket?

Nem. A Application Gateway v2 nem támogatja az NTLM-hitelesítéssel történő proxy-kérelmeket.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>A Application Gateway affinitás cookie támogatja a SameSite attribútumot?
Igen, a [Chromium böngésző](https://www.chromium.org/Home) [V80 frissítése](https://chromiumdash.appspot.com/schedule) a SameSite attribútum nélküli http-cookie-kra vonatkozó mandátumot vezetett be SameSite = LAX néven. Ez azt jelenti, hogy a böngésző nem fogja elküldeni a Application Gateway affinitási cookie-t harmadik féltől származó környezetben. 

Ennek a forgatókönyvnek a támogatásához Application Gateway beinjektál egy *ApplicationGatewayAffinityCORS* nevű másik cookie-t a meglévő *ApplicationGatewayAffinity* -cookie mellett.  Ezek a cookie-k hasonlóak, de a *ApplicationGatewayAffinityCORS* -cookie két további attribútummal bővült: *SameSite = none; Biztonságos*. Ezek az attribútumok olyan Sticky-munkameneteket tartanak fenn, amelyek az átszármazási kérelmek esetében is érvényesek További információt a [cookie-alapú affinitás című szakaszban](configuration-http-settings.md#cookie-based-affinity) talál.

## <a name="performance"></a>Teljesítmény

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hogyan támogatja a Application Gateway a magas rendelkezésre állást és a méretezhetőséget?

A Application Gateway v1 SKU támogatja a magas rendelkezésre állási helyzeteket, ha két vagy több példányt telepített. Az Azure ezeket a példányokat a frissítési és a tartalék tartományok között osztja el, így biztosítva, hogy a példányok ne legyenek egyszerre a hibák. A v1 SKU támogatja a skálázhatóságot azáltal, hogy a terhelés megosztásához hozzáad egy átjáró több példányát.

A v2 SKU automatikusan biztosítja, hogy az új példányok a tartalék tartományok és a frissítési tartományok között legyenek elosztva. Ha a zóna-redundancia lehetőséget választja, a legújabb példányok a rendelkezésre állási zónák között is elterjednek, hogy a zónákhoz való sikertelen rugalmasságot nyújtsanak.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hogyan egy DR-forgatókönyvet az adatközpontok között a Application Gateway használatával?

A Traffic Manager használatával terjesztheti a forgalmat több Application Gateway-átjáró között különböző adatközpontokban.

### <a name="does-application-gateway-support-autoscaling"></a>Támogatja Application Gateway az automatikus skálázást?

Igen, a Application Gateway v2 SKU támogatja az automatikus skálázást. További információ: automatikus [skálázás és zóna – redundáns Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>A manuális vagy automatikus vertikális fel-vagy leskálázás miatt leáll az állásidő?

Nem. A példányok a frissítési tartományok és a tartalék tartományok között oszlanak meg.

### <a name="does-application-gateway-support-connection-draining"></a>Támogatja a Application Gateway a kapcsolatok kiürítését?

Igen. A kapcsolatok kiürítését beállíthatja úgy, hogy megszakítsa a háttérbeli készlet tagjainak megváltoztatását. További információ: [a Application Gateway csatlakoztatási kiürítésének szakasza](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Válthatok a példány mérete közepesről nagyra megszakítás nélkül?

Igen.

## <a name="configuration"></a>Konfiguráció

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway mindig virtuális hálózatban van üzembe helyezve?

Igen. A Application Gateway mindig egy virtuális hálózati alhálózaton van üzembe helyezve. Ez az alhálózat csak Application Gateway-t tartalmazhat. További információ: [virtuális hálózati és alhálózati követelmények](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Képes Application Gateway kommunikálni a virtuális hálózatán kívüli példányokkal vagy az előfizetésén kívül?

Ha IP-kapcsolattal rendelkezik, Application Gateway tud kommunikálni a virtuális hálózatán kívüli példányokkal. A Application Gateway az előfizetésen kívüli példányokkal is tud kommunikálni. Ha belső IP-címeket kíván használni a háttérbeli készlet tagjaiként, használja a [Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md) vagy az [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Telepíthetek az Application Gateway alhálózatban bármilyen más szolgáltatást?

Nem. Az alhálózaton azonban más Application Gateway-átjárókat is telepíthet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Támogatottak-e a hálózati biztonsági csoportok az Application Gateway alhálózaton?

Lásd: [hálózati biztonsági csoportok a Application Gateway alhálózatban](./configuration-infrastructure.md#network-security-groups).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Támogatja az Application Gateway alhálózata a felhasználó által megadott útvonalakat?

Tekintse [meg a Application Gateway alhálózat által támogatott, felhasználó által megadott útvonalakat](./configuration-infrastructure.md#supported-user-defined-routes).

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>Támogatottak-e a Application Gateway alhálózat szolgáltatás-végponti házirendek?

Nem. A Storage-fiókok [szolgáltatási végponti házirendjei](../virtual-network/virtual-network-service-endpoint-policies-overview.md) nem támogatottak Application Gateway alhálózatban, és a konfigurálásával letiltja az Azure-infrastruktúra forgalmát.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Milyen korlátozások vonatkoznak a Application Gatewayra? Növelhetim ezeket a korlátokat?

Lásd: [Application Gateway korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Használhatom-e egyidejűleg a külső és belső forgalomhoz Application Gateway is?

Igen. Application Gateway támogatja az Application Gateway egy belső IP-címét és egy külső IP-címét.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Támogatja a Application Gateway a virtuális hálózatok társítását?

Igen. A virtuális hálózatok egymással történő elosztása segít a forgalom terheléselosztásában más virtuális hálózatokban.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Tudok kommunikálni a helyszíni kiszolgálókkal, amikor ExpressRoute vagy VPN-alagutak csatlakoznak?

Igen, amíg a forgalom engedélyezett.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Az egyik háttérbeli készlet számos különböző porton található alkalmazást képes kiszolgálni?

A Service architektúrája támogatott. A különböző portokon történő mintavételhez több HTTP-beállítást is konfigurálnia kell.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Az egyéni mintavételek támogatják a helyettesítő karaktereket vagy a regexet a válaszokban?

Nem. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hogyan történik az útválasztási szabályok feldolgozása Application Gateway?

Lásd [a feldolgozási szabályok sorrendjét](./configuration-request-routing-rules.md#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Az egyéni mintavételek esetében Mit jelent a gazdagép mező?

A gazdagép mező azt a nevet adja meg, amelyet a mintavétel elküldéséhez a többhelyes Application Gatewayn konfigurált. Ellenkező esetben használja a "127.0.0.1" kulcsszót. Ez az érték eltér a virtuális gép gazdagépének nevétől. A formátuma \<protocol\> :// \<host\> : \<port\> \<path\> .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Engedélyezhető Application Gateway csak néhány forrás IP-cím eléréséhez?

Igen. Lásd: [adott forrás IP-címekhez való hozzáférés korlátozása](./configuration-infrastructure.md#allow-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Használhatom ugyanazt a portot mind a nyilvános, mind a privát figyelőkhöz?

Nem.

### <a name="does-application-gateway-support-ipv6"></a>Támogatja az Application Gateway az IPv6-ot?

A Application Gateway v2 jelenleg nem támogatja az IPv6 protokollt. Csak IPv4-t használó kettős verem-VNet működhet, de az átjáró-alhálózatnak csak IPv4-alapúnak kell lennie. A Application Gateway v1 nem támogatja a kettős verem virtuális hálózatok. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Hogyan csak a saját előtérbeli IP-címmel rendelkező Application Gateway v2-t használja?

A Application Gateway v2 jelenleg nem támogatja csak a magánhálózati IP-módot. A következő kombinációkat támogatja
* Privát IP-cím és nyilvános IP-cím
* Csak nyilvános IP-cím

Ha azonban csak privát IP-címmel szeretné használni a Application Gateway v2-t, kövesse az alábbi eljárást:
1. Nyilvános és privát előtér-IP-címmel rendelkező Application Gateway létrehozása
2. Ne hozzon létre figyelőket a nyilvános előtérbeli IP-címhez. A Application Gateway nem fogja figyelni a nyilvános IP-cím forgalmát, ha a rendszer nem hoz létre figyelőket.
3. Hozzon létre és csatoljon egy [hálózati biztonsági csoportot](../virtual-network/network-security-groups-overview.md) a Application Gateway alhálózat számára a következő konfigurációval a prioritás sorrendjében:
    
    a. Engedélyezi a forrásból származó forgalmat a **GatewayManager** szolgáltatás címkéjének és céljának, mint a **65200-65535** **-as** portot. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. Ezek a portok tanúsítvány-hitelesítéssel vannak védve (zárolva). A külső entitások, beleértve az átjáró felhasználói rendszergazdáit, nem indíthatnak módosításokat a végpontokon a megfelelő tanúsítványok nélkül.
    
    b. Adatforgalom engedélyezése a forrástól a **AzureLoadBalancer** -szolgáltatás címkéjének és a célként megadott portnak a **bármely**
    
    c. Minden bejövő forgalom megtagadása az **Internet** -szolgáltatás címkéjének és a célként megadott portnak **megfelelően.** Adja meg ezt a szabályt a *legkevesebb prioritással* a bejövő szabályokban
    
    d. Tartsa meg az alapértelmezett szabályokat, például engedélyezze a VirtualNetwork bejövő beállítást, hogy a magánhálózati IP-címekhez való hozzáférés ne legyen letiltva.
    
    e. A kimenő internetkapcsolatot nem lehet blokkolni. Ellenkező esetben a naplózással, a metrikákkal és az egyéb problémákkal szembesül.

NSG-konfiguráció a magánhálózati IP-címekhez csak hozzáférés: ![ Application Gateway v2 NSG konfiguráció csak magánhálózati IP-hozzáféréshez](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Konfiguráció – TLS

### <a name="what-certificates-does-application-gateway-support"></a>Milyen tanúsítványokat támogat a Application Gateway?

A Application Gateway támogatja az önaláírt tanúsítványokat, a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat, a kiterjesztett ellenőrzési (EV) tanúsítványokat, a többtartományos (SAN) tanúsítványokat és a helyettesítő tanúsítványokat.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Milyen titkosítási csomagokat támogat a Application Gateway?

A Application Gateway a következő titkosítási csomagokat támogatja. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

A TLS-beállítások testreszabásával kapcsolatos további információkért lásd: [a TLS-házirend verzióinak és a titkosítási csomagok konfigurálása Application Gatewayon](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Támogatja a Application Gateway a háttér felé irányuló forgalom újratitkosítását?

Igen. A Application Gateway támogatja a TLS-alapú kiszervezést és a végpontok közötti TLS-t, amely a háttér felé irányuló forgalmat titkosítja.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Beállítható a TLS-házirend a TLS protokoll verzióinak szabályozására?

Igen. A Application Gateway a TLS 1.0, a TLS 1.1 és a TLS 1.2 megtagadására is beállítható. Alapértelmezés szerint az SSL 2,0 és a 3,0 már le van tiltva, és nem konfigurálható.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Konfigurálható a titkosítási csomagok és a házirendek sorrendje?

Igen. Application Gateway a [titkosító csomagok konfigurálhatók](application-gateway-ssl-policy-overview.md). Egyéni házirend definiálásához engedélyezze a következő titkosítási csomagok legalább egyikét. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway a SHA256 használja a háttér-felügyelethez.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Hány TLS/SSL-tanúsítványt támogat Application Gateway?

A Application Gateway legfeljebb 100 TLS/SSL-tanúsítványt támogat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hány hitelesítési tanúsítvány használható a háttérbeli újratitkosításhoz Application Gateway?

A Application Gateway legfeljebb 100 hitelesítési tanúsítványt támogat.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway natív módon integrálható a Azure Key Vaultrel?

Igen, a Application Gateway v2 SKU támogatja a Key Vault. További információért lásd [a Key Vault tanúsítványokkal való TLS-megszakításról szóló részt](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hogyan konfigurálja a HTTPS-figyelőket a következőhöz:. com és .net-helyek? 

Több tartományalapú (gazdagép-alapú) útválasztáshoz létrehozhat többhelyes figyelőket, beállíthat HTTPS protokollt használó figyelőket, és társíthatja a figyelőket az útválasztási szabályokhoz. További információ: [több webhely üzemeltetése Application Gateway használatával](./multiple-site-overview.md).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Használhatok speciális karaktereket a saját. pfx fájl jelszavában?

Nem, csak alfanumerikus karaktereket használjon a. pfx-fájl jelszavában.

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>A saját EV-tanúsítványt a DigiCert állítja ki, és a köztes tanúsítvány vissza lett vonva. Hogyan megújítani a tanúsítványt a Application Gatewayon?

A hitelesítésszolgáltató (CA) böngésző tagjai a közelmúltban közzétett jelentések részletesen ismertetik az ügyfelek, a Microsoft és a szélesebb technológiai Közösség által a nyilvánosan megbízható hitelesítésszolgáltatókra vonatkozó iparági szabványoknak megfelelő, a HITELESÍTÉSSZOLGÁLTATÓ által kibocsátott tanúsítványokat.A nem megfelelő hitelesítésszolgáltatókkal kapcsolatos jelentések itt találhatók:  

* [1649951-es hiba](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [1650910-es hiba](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

Az iparág megfelelőségi követelményeinek megfelelően a CA-szállítók elkezdték visszavonni a nem megfelelő CAs-t és a megfelelő hitelesítésszolgáltatókat, amelyekhez az ügyfeleknek újra ki kell állítani a tanúsítványokat.A Microsoft szorosan együttműködik ezekkel a szállítókkal, hogy csökkentse az Azure-szolgáltatásokkal kapcsolatos lehetséges hatásokat, **azonban a "saját tanúsítvány használata" (BYOC) forgatókönyvekben használt saját tanúsítványok vagy tanúsítványok továbbra is a váratlanul visszavont állapottal rendelkeznek**.

Annak ellenőrzése, hogy az alkalmazás által használt tanúsítványok vissza lettek-e vonva a [DigiCert bejelentésére](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) és a [tanúsítvány-visszavonási nyomon követésre](https://misissued.com/#revoked). Ha visszavonták a tanúsítványokat, vagy visszavonják őket, új tanúsítványokat kell kérnie az alkalmazásokban használt HITELESÍTÉSSZOLGÁLTATÓI gyártótól. Ha el szeretné kerülni az alkalmazás rendelkezésre állását, mert a tanúsítványok váratlanul visszavonják a tanúsítványokat, vagy egy visszavont tanúsítvány frissítését, tekintse meg az Azure Updates-bejegyzéseket az BYOC-t támogató különféle Azure-szolgáltatások szervizelési hivatkozásaira vonatkozóan: https://azure.microsoft.com/updates/certificateauthorityrevocation/

Application Gateway-specifikus információk:

Ha egy visszavont nemzetközi árumegállapodások vagy által kiállított tanúsítványt használ, az alkalmazás rendelkezésre állása megszakadhat, és az alkalmazástól függően előfordulhat, hogy számos hibaüzenetet kap, többek között a következőket: 

1.  Érvénytelen tanúsítvány/visszavont tanúsítvány
2.  A kapcsolat időtúllépés miatt megszakadt
3.  HTTP 502

Ha el szeretné kerülni az alkalmazás megszakítását a probléma miatt, vagy egy visszavont HITELESÍTÉSSZOLGÁLTATÓ újbóli kibocsátását, a következő műveleteket kell végrehajtania: 

1.  A tanúsítványok újbóli kiállításához forduljon a tanúsítványhoz
2.  Az újrakiadást követően frissítse tanúsítványait az Azure Application Gateway/WAF a teljes [megbízhatósági lánctal](/windows/win32/seccrypto/certificate-chains) (levél, köztes, főtanúsítvány). Ha a tanúsítványt használja, a figyelőn vagy a Application Gateway HTTP-beállításain az alábbi lépéseket követve frissítse a tanúsítványokat, és tekintse meg a további információkat tartalmazó dokumentáció hivatkozásait.
3.  Frissítse a háttérbeli alkalmazás-kiszolgálókat az újból kiállított tanúsítvány használatára. Az Ön által használt háttér-kiszolgálótól függően a tanúsítvány frissítési lépései eltérőek lehetnek. Keresse meg a gyártótól származó dokumentációt.

A figyelőben lévő tanúsítvány frissítése:

1.  A [Azure Portal](https://portal.azure.com/)nyissa meg Application Gateway erőforrását
2.  A tanúsítványhoz társított figyelő beállításainak megnyitása
3.  Kattintson a "kijelölt tanúsítvány megújítása vagy szerkesztése" lehetőségre.
4.  Töltse fel az új PFX-tanúsítványt a jelszóval, és kattintson a Mentés gombra.
5.  Nyissa meg a webhelyét, és ellenőrizze, hogy a hely a várt módon működik-e további információkért, és ellenőrizze [a dokumentációt](./renew-certificates.md).

Ha a Application Gateway figyelőben lévő Azure-kulcstartóból származó tanúsítványokra hivatkozik, javasoljuk, hogy végezze el a következő lépéseket a gyors módosításhoz:

1.  A [Azure Portal](https://portal.azure.com/)navigáljon az Azure-beli kulcstartó-beállításokhoz, amelyek a Application Gatewayhoz vannak társítva
2.  A kiállított tanúsítvány hozzáadása/importálása a tárolóban. Az útmutatóval kapcsolatos további információkért tekintse meg [a dokumentációt](../key-vault/certificates/quick-create-portal.md) .
3.  A tanúsítvány importálása után navigáljon a Application Gateway figyelő beállításaihoz, és a "tanúsítvány kiválasztása a Key Vault" alatt kattintson a "tanúsítvány" legördülő listára, és válassza ki a legutóbb hozzáadott tanúsítványt.
4.  Kattintson a Save (Mentés) gombra a Application Gateway TLS-leállításával kapcsolatos további információkért Key Vault tanúsítványokkal kapcsolatban, [itt](./key-vault-certs.md)tájékozódhat.


A tanúsítvány frissítése a HTTP-beállításokban:

Ha a Application Gateway/WAF szolgáltatás v1-es verzióját használja, akkor a háttér-hitelesítési tanúsítványként fel kell töltenie az új tanúsítványt.
1.  A [Azure Portal](https://portal.azure.com/)nyissa meg Application Gateway erőforrását
2.  A tanúsítványhoz társított HTTP-beállítások megnyitása
3.  Kattintson a tanúsítvány hozzáadása gombra, és töltse fel a kiállított tanúsítványt, és kattintson a Save (Mentés) gombra.
4.  A régi tanúsítvány később is eltávolítható, ha a "..." gombra kattint. a régi tanúsítvány melletti beállítások gombra, és válassza a Törlés lehetőséget, majd kattintson a Mentés gombra.
További információkért tekintse meg a [dokumentációt](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers).

Ha a Application Gateway/WAF szolgáltatás v2 SKU-át használja, nem kell feltöltenie az új tanúsítványt a HTTP-beállításokba, mivel a v2 SKU a "megbízható főtanúsítványok" szolgáltatást használja, és itt nem kell műveletet végrehajtania.

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguráció – bejövő forgalom vezérlője AK-hoz

### <a name="what-is-an-ingress-controller"></a>Mi az a beáramló vezérlő?

A Kubernetes lehetővé teszi a létrehozást `deployment` és az `service` erőforrást, hogy a fürtön belül belső hüvelyek csoportját tegye elérhetővé. Ahhoz, hogy a szolgáltatás külsőleg is elérhető legyen, egy [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) erőforrás van meghatározva, amely terheléselosztást, TLS-megszakítást és név-alapú virtuális üzemeltetést biztosít.
Ahhoz, hogy kielégítse ezt az `Ingress` erőforrást, be kell állítani egy bejövő vezérlőt, amely figyeli az erőforrások változásait, `Ingress` és konfigurálja a terheléselosztó-házirendeket.

A Application Gateway beáramlási vezérlő (AGIC) lehetővé teszi az [azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) számára, hogy az [Azure Kubernetes szolgáltatás](https://azure.microsoft.com/services/kubernetes-service/) bemenő példányként is használható legyen, más néven AK-fürtként.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Több Application Gateway-példányt is kezelhet?

Jelenleg a bejövő adatkezelő egyik példánya csak egy Application Gatewayhoz társítható.

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>Miért nem működik együtt a AGIC a kubenet-sel?

A AGIC megpróbálja automatikusan hozzárendelni az útválasztási tábla erőforrását az Application Gateway alhálózathoz, de ezt a művelet nem teszi lehetővé, mert a AGIC nem rendelkezik a megfelelő engedélyekkel. Ha a AGIC nem tudja hozzárendelni az útválasztási táblázatot a Application Gateway alhálózathoz, akkor a AGIC-naplók hibát jeleznek, ami azt jelzi, hogy az AK-fürt által létrehozott útválasztási táblázatot manuálisan kell hozzárendelni a Application Gateway alhálózatához. További információ: [támogatott felhasználó által definiált útvonalak](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>Csatlakozhatok a saját AK-fürthöz, és Application Gateway külön virtuális hálózatban? 

Igen, ha a virtuális hálózatok egyenrangúak, és nincs átfedésben a címtartomány. Ha a kubenet-t futtatja, akkor ügyeljen arra, hogy az AK által generált útválasztási táblázatot társítsa a Application Gateway alhálózathoz. 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>Milyen funkciók nem támogatottak a AGIC-bővítményben? 

Tekintse meg a Helm használatával központilag telepített AGIC és az [itt](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) található AK-bővítmények közötti különbségeket

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>Mikor érdemes használni a bővítményt és a Helm-telepítést? 

Tekintse meg a Helm által központilag üzembe helyezett AGIC közötti különbségeket itt is: [ide](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)tartoznak, különösen azokat a táblákat, amelyeknek a AGIC a Helm-on keresztül üzembe helyezett, az AK-bővítmények által támogatott forgatókönyv (ek) et. Általánosságban elmondható, hogy a Helm használatával történő üzembe helyezés lehetővé teszi a bétaverziós funkciók tesztelését és a pályázók kiadását a hivatalos kiadás előtt. 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>Szabályozható, hogy a AGIC melyik verzióját fogja telepíteni a bővítmény?

Nem, a AGIC bővítmény felügyelt szolgáltatás, ami azt jelenti, hogy a Microsoft automatikusan frissíti a bővítményt a legújabb stabil verzióra. 

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Milyen típusú naplókat Application Gateway nyújtani?

A Application Gateway három naplót biztosít: 

* **ApplicationGatewayAccessLog**: a hozzáférési napló tartalmazza az Application Gateway-felületre küldött összes kérelmet. Az adat magában foglalja a hívó IP-címét, a kért URL-címet, a válasz késését, a visszatérési kódot, valamint a bejövő és kimenő bájtokat. Egy-egy rekordot tartalmaz az Application Gateway-ben.
* **ApplicationGatewayPerformanceLog**: a Teljesítménynapló rögzíti az egyes Application Gateway-átjárók teljesítményadatait. Az adatok közé tartozik az átviteli sebesség, a kiszolgált kérelmek száma, a sikertelen kérelmek száma, valamint az állapot és a nem kifogástalan háttérbeli példányok száma.
* **ApplicationGatewayFirewallLog**: a WAF-mel konfigurált Application Gateway átjárók esetén a tűzfal naplója az észlelési mód vagy a megelőzési mód használatával naplózott kérelmeket tartalmaz.

Minden napló gyűjtése 60 másodpercenként történik. További információ: [a háttér állapota, a diagnosztikai naplók és a metrikák a Application Gatewayhoz](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hogyan tudni, hogy a háttér-készlet tagjainak állapota Kifogástalan-e?

Ellenőrizze az állapotot a PowerShell `Get-AzApplicationGatewayBackendHealth` -parancsmag vagy a portál használatával. További információ: [Application Gateway diagnosztika](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Mi a diagnosztikai naplók adatmegőrzési szabályzata?

A diagnosztikai naplók az ügyfél Storage-fiókjába áramlanak. Az ügyfelek megadhatják az adatmegőrzési szabályzatot a preferencia alapján. A diagnosztikai naplókat egy Event hub-vagy Azure Monitor-naplóba is lehet elküldeni. További információ: [Application Gateway diagnosztika](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hogyan beolvasni a naplókat a Application Gatewayhoz?

A portálon, az Application Gateway menü paneljén válassza a **műveletnapló** lehetőséget a napló eléréséhez. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Megadhatok riasztásokat a Application Gateway?

Igen. Application Gateway a riasztások a metrikák használatára vannak konfigurálva. További információ: [Application Gateway mérőszámok](./application-gateway-metrics.md) és [Riasztási értesítések fogadása](../azure-monitor/alerts/alerts-overview.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hogyan elemezni Application Gateway forgalmi statisztikáit?

A hozzáférési naplókat többféleképpen is megtekintheti és elemezheti. Azure Monitor a naplók, az Excel, a Power BI és így tovább.

Olyan Resource Manager-sablont is használhat, amely a népszerű [GoAccess](https://goaccess.io/) log Analyzert telepíti és futtatja Application Gateway hozzáférési naplókhoz. A GoAccess olyan értékes HTTP-forgalmi statisztikát biztosít, mint például az egyedi látogatók, a kért fájlok, gazdagépek, operációs rendszerek, böngészők és HTTP-állapotkódok. További információ a GitHubon: a [Resource Manager-sablon mappájában található információs fájl](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Mi okozhatja, hogy a háttér állapota ismeretlen állapotot ad vissza?

Általában ismeretlen állapot jelenik meg, ha a háttérhez való hozzáférést egy hálózati biztonsági csoport (NSG), egyéni DNS vagy felhasználó által definiált útválasztás (UDR) blokkolja az Application Gateway alhálózaton. További információ: [a háttér állapota, a diagnosztika naplózása és a Application Gateway mérőszámai](application-gateway-diagnostics.md).

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>Támogatottak-e a NSG flow-naplók Application Gateway v2-alhálózathoz társított NSG?

A platform jelenlegi korlátai miatt, ha a Application Gateway v2 (Standard_v2, WAF_v2) alhálózaton van egy NSG, és ha engedélyezte a NSG flow-naplókat, akkor a determinált viselkedést láthatja, és ez a forgatókönyv jelenleg nem támogatott.

### <a name="where-does-application-gateway-store-customer-data"></a>Hol tárolja Application Gateway az ügyféladatokat?

Application Gateway nem helyezi át és nem tárolja az ügyféladatokat a-ben üzembe helyezett régióból.

## <a name="next-steps"></a>Következő lépések

További információ a Application Gatewayről: [Mi az az Azure Application Gateway?](overview.md).
