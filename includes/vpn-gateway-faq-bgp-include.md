---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 608b148dc3929065df44530da65e695df19be03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79485921"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Minden Azure VPN Gateway SKU-n támogatott a BGP?
A BGP az alapszintű SKU kivételével az összes Azure VPN Gateawy SKU-t támogatja.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Használhatom a BGP-t Azure házirendalapú VPN Gatewayekkel?
Nem, a BGP csak az útvonalalapú VPN Gatewayeken támogatott.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Használhatok privát ASN-eket (Autonomous System Numbers)?
Igen, a helyszíni hálózatokhoz és az Azure virtuális hálózatokhoz is használhat saját nyilvános ASN-eket vagy titkos ASN-eket.

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Használhatok 32 bites (4 bájtos) ASN (autonóm rendszerek számát)?
Igen, az Azure VPN Gateways mostantól támogatja a 32 bites (4 bájtos) ASN. A PowerShell/CLI/SDK használatával az ASN-t decimális formátumban kell konfigurálni.

### <a name="are-there-asns-reserved-by-azure"></a>Vannak ASN-ek, amelyeket az Azure lefoglal?
Igen, az Azure a következő ASN-eket foglalja le a belső és a külső társviszonyok esetén:

* Nyilvános ASN-ek: 8074, 8075, 12076
* Privát ASN-ek: 65515, 65517, 65518, 65519, 65520

Ezeket az ASN-eket nem adhatja meg a helyszíni VPN-eszközök számára, amikor Azure VPN Gateway átjárókhoz csatlakozik.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Vannak olyan ASN-ek, amelyeket nem használhatok?
Igen, a következő ASN-eket [az IANA fenntartja](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml), és ezért nem állíthatók be az Azure VPN Gateway átjárón:

23456, 64496–64511, 65535–65551 és 429496729

### <a name="what-private-asns-can-i-use"></a>Milyen privát ASN használhatok?
A használható privát ASN használható tartománya:

* 64512-65514, 65521-65534

Ezeket a ASN az IANA vagy az Azure nem foglalja le használatra, ezért használható az Azure-VPN Gatewayhoz való hozzárendeléshez.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Használhatom ugyanazt az ASN-t a helyszíni VPN-hálózatokhoz és az Azure VNetekhez?
Nem, a helyszíni hálózatokhoz és az Azure VNetekhez különböző ASN-eket kell hozzárendelnie, ha BGP-vel kapcsolja össze őket. Az Azure VPN Gatewayek alapértelmezett hozzárendelt ASN-je a 65515, függetlenül attól, hogy a BGP engedélyezve van-e a létesítmények közötti kapcsolathoz. Ezt az alapértelmezett értéket felülírhatja, ha a VPN Gateway létrehozása során egy eltérő ASN-t rendel hozzá, vagy ha az átjáró létrehozása után módosítja az ASN-t. A helyszíni ASN-eket a megfelelő Azure helyi hálózati átjáróhoz kell hozzárendelni.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Milyen címelőtagokat ajánlanak majd az Azure VPN Gatewayek?
Az Azure VPN Gatewayek a helyszíni BGP-eszközök alábbi elérési útjait ajánlják:

* Az Ön VNet-címelőtagjait
* Az Azure VPN Gatewayhez csatlakozó egyes helyi hálózati átjárók címelőtagjait
* Az Azure VPN Gatewayhez csatlakozó más BGP társviszony-munkamenetektől tanult elérési utakat, **kivéve a VNet-előtagok által átfedett alapértelmezett elérési uta(ka)t**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Hány előtagokat lehet hirdetni az Azure VPN Gateway-ben?
Akár 4000 előtagokat is támogatunk. A rendszer eldobja a BGP-munkameneteket, ha az előtagok száma meghaladja a korlátot.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Meghirdethetem az Azure VPN Gateway átjárókhoz vezető alapértelmezett útvonalat (0.0.0.0/0)?
Igen.

Vegye figyelembe, hogy ez a VNet teljes kimenő forgalmát a helyszíni hely felé tereli, és a VNeten található virtuális gépek nem lesznek képesek nyilvános kommunikációt fogadni közvetlenül az internetről, például az internetről a virtuális gépekre irányuló RDP- vagy SSH-kommunikációt.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Meghirdethetem pontosan ugyanazokat az előtagokat, mint a virtuális hálózataim előtagjai?

Nem, ha bármelyik virtuális hálózatának a címéhez tartozó előtagot meghirdeti, azokat az Azure platform blokkolni vagy szűrni fogja. Olyan előtagot azonban meghirdethet, amelynek a virtuális hálózaton belüli állomások a részhalmazát alkotják. 

Ha például a virtuális hálózat a 10.0.0.0/16 címteret használja, akkor meghirdetheti a 10.0.0.0/8 előtagot. A 10.0.0.0/16 vagy 10.0.0.0/24 előtagot azonban nem hirdetheti meg.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Használhatom a BGP-t a VNet–VNet kapcsolatokhoz?
Igen, a BGP-t létesítmények közötti és VNet–VNet kapcsolatokhoz is használhatja.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kombinálhatom a BGP-t nem BGP-kapcsolatokkal az Azure VPN Gatewayeknél?
Igen, kombinálhatja a BGP- és nem BGP-kapcsolatokat ugyanazon Azure VPN Gatewaynél.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Támogatja az Azure VPN Gateway a BGP-tranzit útválasztást?
Igen, a BGP-tranzit útválasztás támogatott, azzal a kivétellel, hogy az Azure VPN Gatewayek **NEM** ajánlják fel más BGP-társak alapértelmezett elérési útjait. A tranzit útválasztást úgy engedélyezheti több Azure VPN Gatewayen, ha az összes köztes VNet–VNet kapcsolaton engedélyezi a BGP-t. További információ: [About BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Használhatok több alagutat az Azure VPN Gateway és a helyszíni hálózat között?
Igen, több S2S VPN-alagutat is létrehozhat az Azure VPN Gateway és a helyszíni hálózata között. Vegye figyelembe, hogy ezek az alagutak beleszámítanak az Azure VPN Gatewayek alagútjainak teljes számába, és mindkét alagúton engedélyeznie kell a BGP-t.

Ha például két redundáns alagútja van az Azure VPN Gateway és az egyik helyszíni hálózat között, akkor ezek 2 alagutat használnak fel az Azure VPN Gateway teljes kvótájából (ez a Standard esetében 10, a HighPerformance esetében pedig 30).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Használhatok több alagutat két BGP-vel rendelkező Azure VNet között?
Igen, de a virtuális hálózati átjárók legalább egyikének aktív-aktív konfigurációban kell lennie.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Használhatok BGP-t az S2S VPN-hez egy olyan konfigurációban, amelyben az ExpressRoute és az S2S VPN is jelen van?
Igen. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Milyen címet használ az Azure VPN Gateway a BGP-társgép IP-címéhez?
Az Azure VPN Gateway egyetlen IP-címet oszt ki a GatewaySubnet tartományból az aktív-készenléti VPN-átjárók számára, vagy két IP-címet az aktív VPN-átjárók számára. A PowerShell (Get-AzVirtualNetworkGateway, a "bgpPeeringAddress" tulajdonság) vagy a Azure Portal (a BGP ASN konfigurálása az átjáró konfigurálása lapon) kiosztott tényleges BGP IP-címe (ke) t lekérheti.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Mik a BGP-társ IP-címének követelményei a VPN-eszközön?
A helyszíni BGP-társ címe **nem** egyezhet meg a VPN-eszköz nyilvános IP-címével vagy a VPN Gateway vnet. Használjon egy más IP-címet a BGP-társgép IP-címeként a VPN-eszközön. Ez lehet egy olyan cím is, amely az eszköz visszacsatolási hálózatához van rendelve, de vegye figyelembe, hogy APIPA-cím (169.254.x.x) nem lehet. Ezt a címet a helyet jelölő megfelelő Helyi hálózati átjáróban kell megadni.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Mit adjak meg a Helyi hálózati átjáró címelőtagjaként a BGP használatakor?
Az Azure helyi hálózati átjárója adja meg a kezdeti címelőtagokat a helyszíni hálózat számára. A BGP-vel lefoglalhatja a BGP-társgép IP-címének gazdaelőtagját (/32 előtag) a helyszíni hálózat címtereként. Ha a BGP-társgép IP-címe 10.52.255.254, akkor a jelen helyszíni hálózatot képviselő helyi hálózati átjáró localNetworkAddressSpace értékéhez a „10.52.255.254/32” IP-címet kell megadnia. Ezzel biztosíthatja, hogy az Azure VPN Gateway az S2S VPN-alagúton keresztül hozza létre a BGP-munkamenetet.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Mit kell felvennem a helyszíni VPN-eszközön a BGP társviszony-munkamenethez?
Vegye fel a VPN-eszközén az Azure BGP-társgép IP-címének gazdaútvonalát, amely az IPsec S2S VPN-alagútra mutat. Ha például az Azure VPN-társgép IP-címe „10.12.255.30”, akkor a VPN-eszköz megfelelő IPsec alagútkapcsolatának a „10.12.255.30” címre mutató gazdaútvonalat kell megadnia egy következő ugrás felülettel.
