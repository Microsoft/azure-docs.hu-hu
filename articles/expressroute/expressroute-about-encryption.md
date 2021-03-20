---
title: 'Azure ExpressRoute: a titkosításról'
description: A ExpressRoute-titkosítás ismertetése.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 693d2304324bdfcac298b3e20ddd0d882a16533c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92899875"
---
# <a name="expressroute-encryption"></a>ExpressRoute titkosítása
 
A ExpressRoute több titkosítási technológiát támogat a hálózat és a Microsoft hálózata közötti adatforgalom titkosságának és integritásának biztosításához.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Pont-pont típusú titkosítás MACsec – gyakori kérdések
A MACsec egy [IEEE standard](https://1.ieee802.org/security/802-1ae/). Az adathordozó-hozzáférés-vezérlési (MAC) vagy a 2. hálózati rétegben lévő adattitkosítást titkosítja. A MACsec a hálózati eszközök és a Microsoft hálózati eszközei közötti fizikai kapcsolatok titkosítására is használható, ha a [ExpressRoute Direct](expressroute-erdirect-about.md)használatával csatlakozik a Microsofthoz. A MACsec alapértelmezés szerint le van tiltva a ExpressRoute Direct-portokon. Saját MACsec-kulcsot használ a titkosításhoz, és tárolja azt [Azure Key Vaultban](../key-vault/general/overview.md). Ön dönti el, hogy mikor kell elforgatni a kulcsot. Tekintse meg az alábbi további gyakori kérdéseket.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Engedélyezhető a MACsec a ExpressRoute-szolgáltató által kiépített ExpressRoute-áramkörön?
Nem. A MACsec egy fizikai kapcsolat összes forgalmát egy entitás (például az ügyfél) tulajdonában lévő kulccsal titkosítja. Ezért csak a közvetlen ExpressRoute érhető el.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Titkosítható a ExpressRoute Direct-portok néhány ExpressRoute-áramköre, és az azonos portokon lévő áramköröket titkosítatlan formában is hagyom? 
Nem. Ha a MACsec engedélyezve van az összes hálózati vezérlési forgalom, például a BGP-adatforgalom, és a vásárlói adatforgalom titkosítva van. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Ha engedélyezem vagy letiltom a MACsec, vagy frissíti a MACsec kulcsot, akkor a helyszíni hálózat elveszti a kapcsolatot a Microsofttal a ExpressRoute-en keresztül?
Igen. A MACsec-konfiguráció esetében csak az előmegosztott kulcsos üzemmódot támogatjuk. Ez azt jelenti, hogy frissítenie kell a kulcsot az eszközein és a Microsofton is (API-n keresztül). Ez a változás nem atomi, így elveszíti a kapcsolódást, ha a két oldal között kulcsfontosságú eltérés van. Javasoljuk, hogy a konfiguráció módosításához ütemezzen egy karbantartási időszakot. A leállás csökkentése érdekében javasoljuk, hogy a hálózati forgalom másik hivatkozásra való átváltását követően a konfigurációt egy ExpressRoute közvetlen kapcsolaton keresztül frissítse.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>A forgalom továbbra is áramlik, ha a MACsec kulcs nem egyezik az eszközök és a Microsoft között?
Nem. Ha a MACsec konfigurálva van, és a kulcs nem egyezik, elveszíti a kapcsolatot a Microsofttal. Más szóval nem fogunk visszaesni a nem titkosított kapcsolatokba, és kitéve az adatait. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Engedélyezi a MACsec a ExpressRoute a hálózati teljesítmény közvetlen romlását?
A MACsec titkosítás és visszafejtés az általunk használt útválasztó hardverén történik. Az oldalunkon nincs hatással a teljesítményre. Érdemes azonban megkeresni a hálózati szolgáltatót a használt eszközökhöz, és látnia kell, hogy a MACsec rendelkezik-e teljesítménnyel.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>Mely titkosítási csomagok támogatottak a titkosításhoz?
Csak az AES-128 és az AES-256 [kiterjesztett csomagok számozási](https://1.ieee802.org/security/802-1aebw/) verziója támogatott. Emellett le kell tiltania a [biztonságos csatorna azonosítóját (sci)](https://wikipedia.org/wiki/IEEE_802.1AE) az eszköz MACsec-konfigurációjában. 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Végpontok közötti titkosítás az IPsec-ben – gyakori kérdések
Az IPsec egy [IETF standard](https://tools.ietf.org/html/rfc6071). A Internet Protocol (IP) szintjén vagy a 3. hálózati rétegben titkosítja az adatfájlokat. A helyszíni hálózat és az Azure-beli virtuális hálózat (VNET) közötti végpontok közötti kapcsolat titkosításához használhatja az IPsec protokollt. Tekintse meg az alábbi további gyakori kérdéseket.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Engedélyezhető az IPsec a ExpressRoute Direct-portok MACsec kívül is?
Igen. A MACsec biztosítja az Ön és a Microsoft közötti fizikai kapcsolatokat. Az IPsec biztosítja a végpontok közötti kapcsolatot az Azure-beli virtuális hálózatok között. Egymástól függetlenül is engedélyezheti őket. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Használhatom az Azure VPN Gatewayt az IPsec-alagút Azure-beli privát kapcsolaton keresztüli beállításához?
Igen. Ha elfogadja az Azure Virtual WAN-t, az [alábbi lépéseket](../virtual-wan/vpn-over-expressroute.md) követve titkosíthatja a végpontok közötti kapcsolatokat. Ha rendszeres Azure-VNET rendelkezik, az [alábbi lépéseket](../vpn-gateway/site-to-site-vpn-private-peering.md) követve létrehozhat egy IPsec-alagutat az Azure VPN Gateway és a helyszíni VPN-átjáró között.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Milyen átviteli sebességre lesz szükség az IPsec az ExpressRoute-kapcsolatban való engedélyezése után?
Ha az Azure VPN Gateway használatban van, ellenőrizze a [teljesítménnyel kapcsolatos számokat](../vpn-gateway/vpn-gateway-about-vpngateways.md). Ha a rendszer külső VPN-átjárót használ, ellenőrizze a teljesítménnyel kapcsolatos számokat a szállítónál.

## <a name="next-steps"></a>Következő lépések
A MACsec-konfigurációval kapcsolatos további információkért tekintse meg a [MACsec konfigurálása](expressroute-howto-macsec.md) című témakört.

Az IPsec-konfigurációval kapcsolatos további információkért tekintse meg az [IPSec konfigurálása](site-to-site-vpn-over-microsoft-peering.md) című témakört.
