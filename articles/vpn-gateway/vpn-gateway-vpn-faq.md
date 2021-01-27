---
title: Azure VPN Gateway GYIK
description: A VPN Gatewayjel kapcsolatos gyakori kérdések. Gyakori kérdések a Microsoft Azure Virtual Network telephelyek közötti kapcsolatairól, a hibrid konfigurációjú kapcsolatokról és a VPN-átjárókról.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 467c2b9fe8758db5c1da43a65c1bfde133df0823
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880101"
---
# <a name="vpn-gateway-faq"></a>VPN Gateway – gyakori kérdések

## <a name="connecting-to-virtual-networks"></a><a name="connecting"></a>Csatlakozás virtuális hálózatokhoz

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Összekapcsolhatok eltérő Azure-régiókban található virtuális hálózatokat?

Igen. Nincs régiókorlátozás. A virtuális hálózatok összekapcsolhatók az azonos régióban vagy más Azure-régiókban található virtuális hálózatokkal is. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Összekapcsolhatok egymással különböző előfizetésekben található virtuális hálózatokat?

Igen.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Csatlakozhatok több helyhez egyetlen virtuális hálózatból?

A Windows PowerShell és az Azure REST API-k használatával kapcsolódhat több helyhez is. Lásd a gyakori kérdések [Többhelyes és virtuális hálózatok közötti kapcsolatok](#V2VMulti) című szakaszát.

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>Van a VPN-átjáró aktív-aktívként való beállításának díja?

Nem. 

### <a name="what-are-my-cross-premises-connection-options"></a>Mik a lehetőségeim létesítmények közötti kapcsolat esetén?

A következő létesítmények közötti kapcsolattípusok támogatottak:

* Helyek közötti kapcsolat – VPN-kapcsolat IPsec (IKE v1 és IKE v2) használatával. Ehhez a kapcsolattípushoz VPN-eszköz vagy RRAS szükséges. További információ: [Helyek közötti kapcsolat](./tutorial-site-to-site-portal.md).
* Pont–hely kapcsolat – VPN-kapcsolat SSTP (Secure Socket Tunneling Protocol) vagy IKE v2 használatával. Ehhez a kapcsolattípushoz nem szükséges VPN-eszköz. További információ: [Pont–hely kapcsolat](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Virtuális hálózatok közötti kapcsolat – A helyek közötti kapcsolattal megegyező kapcsolattípus. A virtuális hálózatok közötti kapcsolat egy IPsec-et (IKE v1 és IKE v2) használó VPN-kapcsolat, nem szükséges hozzá VPN-eszköz. További információ: [Virtuális hálózatok közötti kapcsolat](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Többhelyes kapcsolat – Ez a helyek közötti kapcsolat egy változata, amely több helyszíni hely csatlakoztatását teszi lehetővé egy virtuális hálózathoz. További információ: [Többhelyes kapcsolat](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – a ExpressRoute egy privát kapcsolat az Azure-ral a WAN-ból, nem VPN-kapcsolattal a nyilvános interneten keresztül. További információk: [ExpressRoute Technical Overview](../expressroute/expressroute-introduction.md) (Az ExpressRoute műszaki áttekintése) és [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) (ExpressRoute – gyakori kérdések).

További információk a VPN Gateway-kapcsolatokról: [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Mi a különbség a helyek közötti és a pont–hely kapcsolatok között?

A **helyek közötti** (IPsec/IKE VPN-alagút) konfigurációk az Ön telephelye és az Azure között vannak. Ez azt jelenti, hogy a helyszínen található számítógépek bármelyikéről csatlakozhat a virtuális hálózaton belüli virtuális gépek vagy szerepkörpéldányok bármelyikéhez az útválasztás és az engedélyek konfigurációjától függően. Ez ideális megoldás folyamatosan elérhető létesítmények közötti kapcsolatokhoz, és hibrid konfigurációkhoz is használható. Ez a kapcsolattípus IPsec VPN-készüléket használ (hardvereszközt vagy szoftverkészüléket), amelyet a hálózat szélén kell üzembe helyezni. Ilyen típusú kapcsolatok létrehozásához külsőleg megtekinthető IPv4-címnek kell lennie.

A **pont–hely** (SSTP-alapú VPN) konfigurációkkal csatlakozhat egy tetszőleges helyen található számítógépről a virtuális hálózata összes eleméhez. Ez a típus a Windows beépített VPN-ügyfelét használja. A pont–hely konfiguráció részeként telepíteni kell egy tanúsítványt és egy VPN-ügyfélkonfigurációs csomagot, amelyben azok a beállítások találhatók, amelyeket a számítógépe használ a virtuális hálózatban található virtuális gépekhez vagy szerepkörpéldányokhoz való csatlakozáshoz. Ez ideális megoldás, ha csatlakozni szeretne egy virtuális hálózathoz, de nem a helyszínen tartózkodik, valamint akkor is jól használható, ha nincs hozzáférése VPN-hardverhez vagy kifelé irányuló IPv4-címhez, amelyek a helyek közötti kapcsolatok kialakításához szükségesek.

A virtuális hálózatát konfigurálhatja úgy, hogy helyek közötti és pont–hely kapcsolatokat használjon egyidejűleg – ha a helyek közötti kapcsolatot útvonalalapú VPN-típussal hozza létre az átjáróhoz. Az útvonalalapú VPN-típusok korábbi megnevezése dinamikus átjáró volt a klasszikus üzemi modellben.

## <a name="virtual-network-gateways"></a><a name="gateways"></a>Virtuális hálózati átjárók

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>A VPN Gateway virtuális hálózati átjáró?

A VPN Gateway a virtuális hálózati átjárók egy típusa. A VPN Gateway titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül a virtuális hálózat és az Ön telephelye között. VPN Gateway használatával a virtuális hálózatok között is továbbíthat adatforgalmat. VPN Gateway létrehozásakor a „Vpn” -GatewayType értéket használja. További információ: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Mik azok a házirendalapú (statikus útválasztású) átjárók?

A házirendalapú átjárók házirendalapú VPN-kapcsolatokat valósítanak meg. A házirendalapú VPN-ek a helyszíni hálózat és az Azure VNet közötti címelőtag-kombinációk alapján titkosítják és irányítják a csomagokat az IPsec-alagutakon keresztül. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-konfigurációban.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Mik azok az útvonalalapú (dinamikus útválasztású) átjárók?

Az útvonalalapú átjárók útvonalalapú VPN-kapcsolatokat valósítanak meg. Az útvonalalapú VPN-ek „útvonalakat” használnak az IP-továbbítási vagy útvonalválasztási táblán, hogy a csomagokat a megfelelő alagútkapcsolatokhoz irányítsák. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. Az útvonalalapú VPN-ek házirendje (vagy forgalomválasztója) bármely két elem közöttiként (vagy helyettesítő karakterekként) van konfigurálva.

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>Frissíthetem a házirend-alapú VPN-átjárót a Route-alapúra?

Nem. Az Azure VNet-átjáró típusa nem módosítható házirendalapúról útvonalalapúra vagy viszont. Ilyenkor törölni kell, majd újra létrehozni az átjárót, amely folyamat mintegy 60 percet vesz igénybe. Az átjáró IP-címe és az előmegosztott kulcs (PSK) nem marad meg.
1. Törölje a törölni kívánt átjáróval társított kapcsolatokat.
1. Törölje az átjárót:
   - [Azure Portalra](vpn-gateway-delete-vnet-gateway-portal.md)
   - [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
   - [Azure PowerShell – klasszikus](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. [Hozzon létre egy új, a kívánt típusú átjárót, és fejezze be a VPN-beállítást](./tutorial-site-to-site-portal.md#VNetGateway).

### <a name="do-i-need-a-gatewaysubnet"></a>Szükségem van GatewaySubnetre?

Igen. Az átjáróalhálózat tartalmazza a virtuális hálózati átjáró-szolgáltatások által használt IP-címeket. A virtuális hálózati átjáró konfigurálásához létre kell hozni egy átjáróalhálózatot a virtuális hálózathoz. A megfelelő működéshez az összes átjáró-alhálózatnak a „GatewaySubnet” névvel kell rendelkeznie. Ne nevezze el másként az átjáróalhálózatát, és ne helyezzen üzembe rajta virtuális gépeket vagy más eszközt.

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáróalhálózatban lévő IP-címeket az átjárószolgáltatás számára foglalja le a rendszer. Egyes konfigurációk a többinél nagyobb számú IP-cím kiosztását követelik meg az átjárószolgáltatásokhoz. Győződjön meg arról, hogy az átjáróalhálózat elég IP-címet tartalmaz a későbbi növekedéshez és az esetleges új kapcsolatkonfigurációk kialakításához. Tehát, míg egyes konfigurációkhoz létrehozhat kicsi, akár /29-es méretű átjáróalhálózatot is, ajánlott /27-est vagy nagyobbat létrehozni (/27, /26, /25 stb.). Vizsgálja meg a létrehozni kívánt konfiguráció követelményeit és ellenőrizze, hogy az átjáró-alhálózat megfelel-e ezeknek a követelményeknek.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Telepíthetek virtuális gépeket vagy szerepkörpéldányokat az átjáróalhálózatomra?

Nem.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Megszerezhetem a VPN-átjáróm IP-címét, mielőtt létrehozom az átjárót?

A zóna-redundáns és a zónákhoz tartozó átjárók (az az _átjáró a_ névben) mind a _standard SKU_ Azure nyilvános IP-erőforrásra támaszkodnak. Az Azure standard SKU nyilvános IP-erőforrásainak statikus kiosztási módszert kell használniuk. Ezért a VPN-átjáróhoz tartozó nyilvános IP-címet azonnal megkapja a használni kívánt szabványos SKU nyilvános IP-erőforrás létrehozásakor.

A nem zónában lévő és nem zónákhoz tartozó átjárók (az az átjárók,  amelyek nem _rendelkeznek a_ névben) nem KAPHATJÁK meg a VPN-átjáró IP-címét a létrehozás előtt. Az IP-cím csak akkor változik, ha törli, majd újra létrehozza a VPN-átjárót.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>Kérhetek statikus nyilvános IP-címet a VPN-átjáróm számára?

A fentiekben leírtaknak megfelelően a zóna-redundáns és a zónákhoz tartozó átjárók (az az _átjáró, amelynek_ a neve) mind a _standard SKU_ Azure nyilvános IP-erőforrásra támaszkodnak. Az Azure standard SKU nyilvános IP-erőforrásainak statikus kiosztási módszert kell használniuk.

A nem zóna nélküli és nem zónákhoz tartozó átjárók (az az átjárók, amelyek _nem_ rendelkeznek _a_ névben) esetében csak a dinamikus IP-címek hozzárendelése támogatott. Ez azonban nem jelenti azt, hogy az IP-cím a VPN-átjáróhoz való hozzárendelése után módosul. A VPN-átjáró IP-címe csak akkor változik, ha az átjárót törli, majd újra létrehozza. A VPN-átjáró nyilvános IP-címe nem változik a VPN-átjáró további belső karbantartásának és frissítésének átméretezése, alaphelyzetbe állítása vagy befejezése után.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hogyan történik a VPN-alagút hitelesítése?

Az Azure VPN PSK (előmegosztott kulcsos) hitelesítést használ. A VPN-alagút létrehozásakor létrehozunk egy előmegosztott kulcsot (PSK) is. Az automatikusan létrehozott PSK-t az Előmegosztott kulcs beállítása PowerShell-parancsmaggal vagy a REST API-vel lecserélheti a sajátjára.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Használhatom-e az Előmegosztott kulcs beállítása API-t a házirendalapú (statikus útválasztású) átjárói VPN konfigurálásához?

Igen, az Előmegosztott kulcs beállítása API és PowerShell-parancsmag használható az Azure házirendalapú (statikus) VPN-ek és útvonalalapú (dinamikus) VPN-ek konfigurálásához is.

### <a name="can-i-use-other-authentication-options"></a>Használhatok más hitelesítési módszert?

Hitelesítésként csak az előmegosztott kulcsok (PSK-k) használhatók.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hogyan határozhatom meg, milyen adatforgalom haladjon át a VPN-átjárón?

#### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

* PowerShell esetén: Használja az „AddressPrefix” parancsot a helyi hálózati átjáró forgalmának meghatározásához.
* Azure Portal esetén: Lépjen a Helyi hálózati átjáró > Konfiguráció > Címtér felületre.

#### <a name="classic-deployment-model"></a>Klasszikus üzemi modell

* Azure Portal esetén: Lépjen a klasszikus virtuális hálózat > VPN connections (VPN-kapcsolatok) > Site-to-site VPN connections (Helyek közötti VPN-kapcsolatok) > Helyi hely neve > Helyi hely > Client address space (Ügyfélcímtér) felületre. 

### <a name="can-i-configure-force-tunneling"></a>Konfigurálhatok kényszerített bújtatást?

Igen. Lásd: [Kényszerített bújtatás konfigurálása](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Használhatom a NAT-T-T a VPN-kapcsolatokon?

Igen, a NAT-bejárás (NAT-T) támogatott. Az Azure VPN Gateway nem hajtja végre a NAT-hoz hasonló funkciókat a belső csomagokon az IPsec-alagutak között.  Ebben a konfigurációban ellenőrizze, hogy a helyszíni eszköz kezdeményezi-e az IPSec-alagutat.

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Üzembe helyezhetem a saját VPN-kiszolgálómat az Azure-ban, és csatlakozhatok vele a helyszíni hálózatomhoz?

Igen, az Azure-ban üzembe helyezheti saját VPN-átjáróit vagy -kiszolgálóit az Azure Piactérről, vagy saját VPN-útválasztók létrehozásával. Ilyenkor a virtuális hálózatában felhasználó által definiált útvonalakat kell konfigurálnia, hogy az adatforgalom megfelelően legyen irányítva a helyszíni hálózatai és a virtuális hálózatainak alhálózatai között.

### <a name="why-are-certain-ports-opened-on-my-virtual-network-gateway"></a><a name="gatewayports"></a>Miért nyílnak meg bizonyos portok a virtuális hálózati átjárón?

Ezek szükségesek az Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). A megfelelő tanúsítványok nélkül a külső entitások – például az átjárók ügyfelei – nem gyakorolhatnak semmilyen hatást a végpontokra.

A virtuális hálózati átjáró alapvetően egy többhelyű eszköz, amely egy hálózati adaptert használ az ügyfél magánhálózaton, és egy hálózati ADAPTERt a nyilvános hálózatra. Az Azure-infrastruktúra entitásai megfelelőségi okokból nem csatlakozhatnak az ügyfelek magánhálózataihoz, így az infrastruktúra-kommunikációhoz nyilvános végpontokat kell használniuk. A nyilvános végpontokat az Azure biztonsági naplózás rendszeresen ellenőrzi.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>További információk az átjárótípusokról, a követelményekről és az adatátviteli sebességről

További információ: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="site-to-site-connections-and-vpn-devices"></a><a name="s2s"></a>Helyek közötti kapcsolatok és VPN-eszközök

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Mit érdemes figyelembe venni a VPN-eszköz kiválasztásakor?

Eszközszállítói partnereinkkel különböző standard helyek közötti VPN-eszközöket ellenőriztünk. A kompatibilis VPN-eszközök, a hozzájuk tartozó konfigurációs útmutatók vagy minták, valamint az eszközökre vonatkozó műszaki adatok listája a [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md) című cikkben található. A listán kompatibilisként szereplő eszközcsaládokba tartozó összes eszköz működik a virtuális hálózatokkal. A VPN-eszköz konfigurálásához tekintse meg az eszközkonfigurációs mintát, vagy kövesse a megfelelő eszközcsaládhoz tartozó hivatkozást.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Hol találom a VPN-eszközök konfigurációs beállításait?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Hogyan szerkeszthetem a VPN-eszközök konfigurációs mintáit?

Az eszközök konfigurációs mintáinak szerkesztésével kapcsolatos információkért tekintse meg a [minták szerkesztésével](vpn-gateway-about-vpn-devices.md#editing) kapcsolatos részt.

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Hol találom az IPsec/IKE-paramétereket?

Az IPsec/IKE-paraméterekkel kapcsolatos információkért tekintse meg a [paraméterekkel](vpn-gateway-about-vpn-devices.md#ipsec) kapcsolatos részt.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Miért áll le a házirendalapú VPN-alagutam, amikor nincs adatforgalom?

Ez normális működés házirendalapú (más néven statikus útválasztású) VPN-átjárók esetében. Ha az alagúton átmenő adatforgalom több mint 5 percig inaktív, a rendszer megszakítja az alagutat, Amikor az adatforgalom megindul bármelyik irányba, az alagút azonnal újra létrejön.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Csatlakozhatok az Azure-hoz szoftveres VPN-nel?

A helyek közötti létesítmények közötti konfigurációkhoz támogatottak a Windows Server 2012 útválasztási és távelérési (RRAS) kiszolgálók is.

Az egyéb szoftveres VPN-megoldások szintén működhetnek, ha megfelelnek az iparági szabványos IPsec-megvalósításoknak. A konfigurációs és támogatási útmutatáshoz vegye fel a kapcsolatot a szoftver szállítójával.

## <a name="how-do-i-change-the-authentication-type-for-my-point-to-site-connections"></a>Hogyan módosíthatja a pont – hely kapcsolat hitelesítési típusát?

A pont – hely kapcsolatokhoz tartozó hitelesítési módszert a VPN Gateway alatti **pont – hely konfiguráció** szakaszban módosíthatja, és a kívánt választógombot kell ellenőrizni. Az aktuális lehetőségek az **Azure-tanúsítvány, a RADIUS-hitelesítés és a Azure Active Directory**. Vegye figyelembe, hogy az aktuális ügyfelek **nem tudnak csatlakozni** a változás után, amíg az új profil le nem töltődik, és nincs konfigurálva az ügyfélen.

## <a name="point-to-site-using-native-azure-certificate-authentication"></a><a name="P2S"></a>Pont–hely kapcsolat az Azure natív tanúsítványalapú hitelesítésének használatával

Ez a szakasz a Resource Manager-alapú üzemi modellre vonatkozik.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="point-to-site-using-radius-authentication"></a><a name="P2SRADIUS"></a>Pont–hely kapcsolat RADIUS-hitelesítés használatával

Ez a szakasz a Resource Manager-alapú üzemi modellre vonatkozik.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="vnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>Virtuális hálózatok közötti kapcsolat és többhelyes kapcsolatok

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Használhatok Azure VPN Gateway átjárót az adatforgalomhoz a helyszíni helyeim között vagy egy másik virtuális hálózatba?

**Resource Manager-alapú üzemi modell**<br>
Igen. További információért lásd a [BGP](#bgp) szakaszt.

**Klasszikus üzemi modell**<br>
Az Azure VPN Gateway-átjárókon keresztüli adatátvitel a klasszikus üzemi modellel lehetséges, de ez a hálózati konfigurációs fájlban statikusan meghatározott címterekre hagyatkozik. A BGP jelenleg nem támogatott az Azure Virtual Networkökhöz és VPN Gateway-átjárókhoz a klasszikus üzemi modell használatával. BGP nélkül az átviteli címterek manuális meghatározása sok hibalehetőséggel jár, ezért nem ajánlott.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Egy adott virtuális hálózaton az Azure ugyanazt az IPsec/IKE előmegosztott kulcsot hozza létre az összes VPN-kapcsolathoz?

Nem, az Azure alapértelmezés szerint különböző előmegosztott kulcsokat hoz létre a különböző VPN-kapcsolatokhoz. Azonban a VPN-átjáró kulcsának megadása REST API-val vagy PowerShell-parancsmaggal tetszőlegesen megadhatja a kulcs értékét. A kulcsnak nyomtatható ASCII-karakternek kell lennie.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Nagyobb sávszélességhez jutok több helyek közötti VPN használatával, mint egyetlen virtuális hálózattal?

Nem, az összes VPN-alagút, így a pont–hely VPN-ek is ugyanazt az Azure VPN Gateway átjárót és elérhető sávszélességet használják.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Konfigurálhatok több alagutat a virtuális hálózatom és a helyszíni helyem között többhelyes VPN használatával?

Igen, de mindkét alagúton ugyanarra a helyre kell konfigurálnia a BGP-t.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Használhatok pont–hely VPN-t több VPN-alagúttal a virtuális hálózatomhoz?

Igen, a pont–hely (P2S) VPN-ek több helyszíni helyhez és egyéb virtuális hálózatokhoz csatlakozó VPN-átjárókkal is használhatók.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Csatlakoztathatok IPsec VPN-ekkel rendelkező virtuális hálózatot az ExpressRoute-kapcsolatcsoportomhoz?

Igen, ez támogatott. További információk: [Párhuzamosan fennálló ExpressRoute- és helyek közötti VPN-kapcsolatok konfigurálása](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="ipsecike-policy"></a><a name="ipsecike"></a>IPsec/Internetes kulcscsere házirendje

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="bgp"></a><a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a><a name="vms"></a>Több helyszínt érintő kapcsolatok és virtuális gépek

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Ha a virtuális gépem egy virtuális hálózaton található, és rendelkezem egy létesítmények közötti kapcsolattal, hogyan csatlakozhatok a virtuális géphez?

Több lehetősége van. Ha az RDP engedélyezve van a virtuális gép számára, a magánhálózati IP-címmel csatlakozhat a virtuális géphez. Ebben az esetben meg kell adnia azt a magánhálózati IP-címet és a portot, amelyhez csatlakozni szeretne (a portszám általában 3389). a virtuális gép portját pedig konfigurálnia kell az adatátvitelhez.

A magánhálózati IP-címmel egy, a virtuális hálózaton található másik virtuális gépről is csatlakoztathat a virtuális géphez. Ha a virtuális hálózatán kívüli helyről csatlakozik, nem használhatja az RDP-t a virtuális gépéhez való csatlakozáshoz a magánhálózati IP-címmel. Ha például pont–hely virtuális hálózatot konfigurált, és nem létesít kapcsolatot a számítógépéről, nem csatlakozhat a virtuális gépéhez a magánhálózati IP-címmel.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Ha a virtuális gépem létesítmények közötti kapcsolattal rendelkező virtuális hálózaton található, a virtuális gépem teljes adatforgalma ezen a kapcsolaton halad át?

Nem. Csak az az adatforgalom fog áthaladni a virtuális hálózati átjárón, amely a virtuális hálózat Ön által meghatározott helyi hálózati IP-címtartományaiban található cél-IP-címmel rendelkezik. A virtuális hálózaton belüli cél-IP-címmel rendelkező adatforgalom a virtuális hálózaton belül marad. Az egyéb adatforgalom a terheléselosztón keresztül a nyilvános hálózatok felé lesz irányítva, vagy ha kényszerített bújtatást használ, akkor az Azure VPN Gatewayen megy keresztül.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Hogyan háríthatom el egy virtuális gép RDP-kapcsolatának hibáit?

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## <a name="virtual-network-faq"></a><a name="faq"></a>Virtual Network GYIK

A virtuális hálózatokkal kapcsolatos további információk: [Virtual Network FAQ](../virtual-network/virtual-networks-faq.md) (Virtual Network – gyakori kérdések).

## <a name="next-steps"></a>További lépések

* További információk a VPN Gatewayről: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).
* További információk a VPN Gateway konfigurációs beállításairól: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).

**Az "OpenVPN" az OpenVPN Inc védjegye.**