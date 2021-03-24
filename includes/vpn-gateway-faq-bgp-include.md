---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0eb509d543bc9b4a8846319126185eb1f27ec7a2
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953456"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Minden Azure VPN Gateway SKU-n támogatott a BGP?

A BGP az alapszintű SKU kivételével az összes Azure VPN Gateway SKU esetében támogatott.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Használhatom a BGP-t Azure Policy VPN-átjárókkal?

Nem, a BGP-t csak az útválasztási alapú VPN-átjárók támogatják.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Milyen ASN (autonóm rendszerszámok) használhatók?

Saját nyilvános ASN vagy saját ASN is használhat a helyszíni hálózatokhoz és az Azure-beli virtuális hálózatokhoz. Az Azure vagy az IANA által fenntartott tartományok nem használhatók.

Az Azure vagy az IANA az alábbi ASN foglalja le:
* Az Azure által fenntartott ASN:

  * Nyilvános ASN-ek: 8074, 8075, 12076
  * Privát ASN-ek: 65515, 65517, 65518, 65519, 65520
* [Az IANA által fenntartott](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)ASN:

  * 23456, 64496–64511, 65535–65551 és 429496729

Ezek a ASN nem adhatók meg a helyszíni VPN-eszközökhöz az Azure VPN Gateway-hez való csatlakozáskor.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Használhatok 32 bites (4 bájtos) ASN?

Igen, VPN Gateway mostantól támogatja a 32 bites (4 bájtos) ASN. Ha az ASN-t decimális formátumban szeretné konfigurálni, használja a PowerShellt, az Azure CLI-t vagy az Azure SDK-t.

### <a name="what-private-asns-can-i-use"></a>Milyen privát ASN használhatok?

A privát ASN használható tartományok a következők:

* 64512-65514 és 65521-65534

Ezeket a ASN az IANA vagy az Azure nem foglalja le használatra, ezért használható az Azure VPN gatewayhez való hozzárendeléshez.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Milyen címet VPN Gateway használni a BGP-társ IP-címeihez?

Alapértelmezés szerint a VPN Gateway egyetlen IP-címet foglal le a *GatewaySubnet* tartományból az aktív-készenléti VPN-átjárók számára, vagy két IP-címet az aktív-aktív VPN-átjárók számára. Ezeket a címeket a rendszer automatikusan lefoglalja a VPN-átjáró létrehozásakor. A PowerShell használatával kiosztott tényleges BGP IP-címet, vagy a Azure Portal megkeresésével lekérheti. A PowerShellben használja a **Get-AzVirtualNetworkGateway**, és keresse meg a **bgpPeeringAddress** tulajdonságot. A Azure Portal az **átjáró konfigurációja** lapon tekintse meg a **BGP ASN konfigurálása** tulajdonságot.

Ha a helyszíni VPN-útválasztók az **APIPA** IP-címeket (169.254. x. x) használják a BGP IP-címekként, meg kell adnia egy további **Azure APIPA BGP IP-címet** az Azure VPN-átjárón. Az Azure VPN Gateway kiválasztja az APIPA-címet, amelyet a helyi hálózati átjáróban megadott helyszíni APIPA BGP-társsal, vagy a nem APIPA, helyszíni BGP-partner magánhálózati IP-címével kíván használni. További információ: [configure BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Mik a BGP-társ IP-címeire vonatkozó követelmények a VPN-eszközön?

A helyszíni BGP-társ címe nem egyezhet meg a VPN-eszköz nyilvános IP-címével vagy a VPN-átjáró virtuális hálózati címével. Használjon egy másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Az eszközön lévő visszacsatolási felülethez (normál IP-cím vagy APIPA-cím) rendelt cím is lehet. Ha az eszköz a BGP-hez tartozó APIPA-címet használ, meg kell adnia egy APIPA BGP IP-címet az Azure VPN Gateway-ben a [BGP konfigurálása](../articles/vpn-gateway/bgp-howto.md)című cikkben leírtak szerint. A helyet a megfelelő helyi hálózati átjáróban kell megadni.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Mit kell megadni a helyi hálózati átjáróhoz a BGP használatakor?

> [!IMPORTANT]
>
>Ez a korábban dokumentált követelmény változása. Ha BGP-t használ egy kapcsolatban, hagyja üresen a **címterület** mezőt a megfelelő helyi hálózati átjáró-erőforráshoz. Az Azure VPN Gateway a helyi BGP-társ IP-címén belsőleg helyez el egy gazdagép-útvonalat az IPsec-alagúton keresztül. Ne adja hozzá a/32 útvonalat a **címterület** mezőben. Redundáns, és ha APIPA-címet használ a helyszíni VPN-eszköz BGP-IP-címével, azt nem lehet felvenni ebbe a mezőbe. Ha a **címterület** mezőben más előtagokat is hozzáad, azok statikus útvonalként lesznek hozzáadva az Azure VPN Gateway-hez a BGP-n keresztül megszerzett útvonalakon felül.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Használhatom ugyanazt az ASN-t a helyszíni VPN-hálózatokhoz és az Azure-beli virtuális hálózatokhoz is?

Nem, különböző ASN kell hozzárendelni a helyszíni hálózatokhoz és az Azure-beli virtuális hálózatokhoz, ha azokat a BGP-vel együtt csatlakoztatja. Az Azure VPN-átjárók alapértelmezett ASN-je a 65515, függetlenül attól, hogy a BGP engedélyezve van-e, vagy sem a létesítmények közötti kapcsolathoz. Felülbírálhatja ezt az alapértelmezett értéket, ha hozzárendel egy másik ASN-t a VPN-átjáró létrehozásakor, vagy az ASN-t az átjáró létrehozása után is módosíthatja. A helyszíni ASN hozzá kell rendelnie a megfelelő Azure-beli helyi hálózati átjáróhoz.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Milyen címelőtagokat ajánlanak majd az Azure VPN Gatewayek?

Az átjárók a következő útvonalakat hirdetik a helyszíni BGP-eszközökön:

* A virtuális hálózati címek előtagjai.
* Az Azure VPN Gateway-hez csatlakozó helyi hálózati átjárók címeinek előtagjai.
* Az Azure VPN Gateway-hez csatlakozó más BGP-társi munkamenetekről származó útvonalak, kivéve a virtuális hálózat előtagjával átfedésben lévő alapértelmezett útvonalat vagy útvonalakat.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Hány előtagokat tud hirdetni az Azure VPN Gateway?

Az Azure VPN Gateway akár 4000 előtagot is támogat. A rendszer eldobja a BGP-munkameneteket, ha az előtagok száma meghaladja a korlátot.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Meghirdethetem az Azure VPN Gateway átjárókhoz vezető alapértelmezett útvonalat (0.0.0.0/0)?

Igen. Vegye figyelembe, hogy ez az összes virtuális hálózat kimenő forgalmát kényszeríti a helyszíni hely felé. Emellett megakadályozza, hogy a virtuális hálózati virtuális gépek közvetlenül az internetről fogadják el nyilvános kommunikációt, ilyen például az internetről a virtuális gépekre irányuló RDP vagy SSH.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Megtehetem a virtuális hálózat előtagjainak pontos előtagjait?

Nem, ugyanazokat az előtagokat hirdeti meg, mint a virtuális hálózati címek előtagjainak valamelyikét, az Azure blokkolja vagy szűri azokat. Azonban olyan előtagot is megadhat, amely a virtuális hálózatban található elemek kibővített változata. 

Ha például a virtuális hálózat a 10.0.0.0/16 címtartomány használatát használta, akkor a 10.0.0.0/8 is meghirdethető. A 10.0.0.0/16 vagy a 10.0.0.0/24 nem hirdethető meg.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Használhatom a BGP-t a virtuális hálózatok közötti kapcsolatokkal?

Igen, a BGP-t is használhatja a létesítmények közötti kapcsolatokhoz és a virtuális hálózatok közötti kapcsolatokhoz.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kombinálhatom a BGP-t nem BGP-kapcsolatokkal az Azure VPN Gatewayeknél?

Igen, kombinálhatja a BGP- és nem BGP-kapcsolatokat ugyanazon Azure VPN Gatewaynél.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Támogatja az Azure VPN Gateway a BGP-tranzit útválasztást?

Igen, a BGP-tranzit útválasztása támogatott, azzal a kivétellel, hogy az Azure VPN Gateway-átjárók nem hirdetik az alapértelmezett útvonalakat más BGP-partnereknek. Ha több Azure VPN-átjárón keresztül szeretné engedélyezni az átviteli útválasztást, engedélyeznie kell a BGP-t a virtuális hálózatok közötti összes köztes kapcsolaton. További információ: [About BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Használhatok több alagutat egy Azure VPN Gateway és a helyszíni hálózat között?

Igen, több helyek közötti (S2S) VPN-alagutat is létrehozhat egy Azure VPN Gateway és a helyszíni hálózat között. Vegye figyelembe, hogy ezek az alagutak az Azure VPN Gateway-átjárók összes bújtatási számán alapulnak, és engedélyeznie kell a BGP-t mindkét alagúton.

Ha például két redundáns alagútja van az Azure VPN Gateway és az egyik helyszíni hálózat között, akkor 2 alagutat használnak az Azure VPN Gateway teljes kvótáján kívül.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Használhatok több alagutat két Azure-beli virtuális hálózat között a BGP-vel?

Igen, de a virtuális hálózati átjárók legalább egyikének aktív-aktív konfigurációban kell lennie.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Használhatom a BGP-t a S2S VPN-hez egy Azure-ExpressRoute és a S2S VPN együttélési konfigurációjában?

Igen.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Mit kell felvennem a helyszíni VPN-eszközön a BGP társviszony-munkamenethez?

Adja hozzá a VPN-eszközön az Azure BGP-társ IP-címének gazdagépi útvonalát. Ez az útvonal az IPsec S2S VPN-alagútra mutat. Ha például az Azure VPN-társ IP-címe 10.12.255.30, akkor a VPN-eszköz megfelelő IPsec-alagút felületének következő ugrási felületével adja hozzá a 10.12.255.30 gazdagépi útvonalát.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Támogatja a Virtual Network Gateway a BFD-t a S2S-kapcsolatokhoz a BGP-vel?

Nem. A kétirányú továbbítási észlelés (BFD) egy olyan protokoll, amellyel a BGP használatával gyorsabban észlelheti a szomszéd-állásidőt, mint a Standard BGP "Keepalives" használatával. A BFD olyan másodlagos időzítőket használ, amelyek LAN-környezetekben működnek, de nem a nyilvános interneten vagy a nagykiterjedésű hálózati kapcsolatokon keresztül.

A nyilvános interneten keresztül létesített kapcsolatok esetében, hogy bizonyos csomagok késleltetve vagy akár eldobása nem szokatlan, az agresszív időzítők bevezetésével instabillá teheti a stabilitást. Ez a bizonytalanság azt eredményezheti, hogy az útvonalakat a BGP fogja megnedvesíteni. Alternatív megoldásként konfigurálhatja a helyszíni eszközt az alapértelmezettnél alacsonyabb időzítővel, 60 – másodperces "életben tartási" intervallummal és a 180-másodperces időzítővel. Ez gyorsabb konvergencia-időt eredményez.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Az Azure VPN Gateway-átjárók BGP-társas munkameneteket vagy kapcsolatokat kezdeményeznek?

Az átjáró a VPN-átjárók magánhálózati IP-címeivel a helyi hálózati átjáró erőforrásaiban megadott helyszíni BGP-társ IP-címekre vonatkozó BGP-társi munkameneteket fog kezdeményezni. Ez attól függetlenül, hogy a helyszíni BGP IP-címei az APIPA-tartományhoz vagy a hagyományos magánhálózati IP-címekhez tartoznak-e. Ha a helyszíni VPN-eszközök az APIPA-IP-címeket használják, a BGP-hangszórót a kapcsolatok elindításához kell konfigurálnia.