---
title: Hálózati biztonsági fogalmak és követelmények az Azure-ban | Microsoft Docs
description: Ez a cikk alapvető tudnivalókat tartalmaz az alapvető hálózati biztonsági fogalmakról és követelményekről, valamint arról, hogy az egyes területeken milyen Azure-ajánlatokról van szó.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 4fb4981925593a22ed65df9d3ed0da5f45062f23
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578943"
---
# <a name="azure-network-security-overview"></a>A nagyvállalati hálózati biztonság áttekintése

A hálózati biztonság úgy definiálható, mint az erőforrások jogosulatlan hozzáféréstől vagy támadástól való védelmének folyamata a hálózati forgalomra vonatkozó vezérlők alkalmazásával. A cél az, hogy csak a megbízható forgalom legyen engedélyezett. Az Azure robusztus hálózati infrastruktúrát tartalmaz az alkalmazások és szolgáltatások csatlakozási követelményeinek támogatásához. A hálózati kapcsolat az Azure-ban, a helyszíni és az Azure által üzemeltetett erőforrások, valamint az internetről és az Azure-ból származó erőforrások között lehetséges.

Ez a cikk az Azure által a hálózati biztonság területén kínált lehetőségeket ismerteti. A következőket teheti meg:

* Azure-hálózatkezelés
* Hálózati hozzáférés-vezérlés
* Azure Firewall
* A távoli hozzáférés és a létesítmények közötti kapcsolatok biztonságossá tétele
* Rendelkezésre állás
* Névfeloldás
* Peremhálózati hálózat (DMZ) architektúrája
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Figyelés és fenyegetések észlelése

## <a name="azure-networking"></a>Azure-hálózatkezelés

Az Azure megköveteli, hogy a virtuális gépek egy Azure-Virtual Network kapcsolódjanak. A virtuális hálózatok a fizikai Azure hálózati hálóra épülő logikai szerkezetek. Minden virtuális hálózat el van különítve az összes többi virtuális hálózattól. Ezzel biztosíthatja, hogy a központi telepítések hálózati forgalma ne legyen elérhető más Azure-ügyfelek számára.

További információ:

* [A virtuális hálózat áttekintése](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Hálózati hozzáférés-vezérlés

A hálózati hozzáférés-vezérlés a virtuális hálózatban lévő adott eszközökre vagy alhálózatokra való kapcsolódás korlátozására szolgál. A hálózati hozzáférés-vezérlés célja, hogy korlátozza a hozzáférést a virtuális gépekhez és a szolgáltatásokhoz a jóváhagyott felhasználókhoz és eszközökhöz. A hozzáférés-vezérlés a virtuális gép vagy szolgáltatás kapcsolatainak engedélyezéséhez vagy megtagadásához szükséges döntéseken alapul.

Az Azure számos különböző típusú hálózati hozzáférés-vezérlést támogat, például a következőket:

* Hálózati réteg vezérlő
* Irányítás és kényszerített bújtatás
* Virtuális hálózati biztonsági berendezések

### <a name="network-layer-control"></a>Hálózati réteg vezérlő

A biztonságos üzembe helyezéshez valamilyen mérték szükséges a hálózati hozzáférés-vezérléshez. A hálózati hozzáférés-vezérlés célja, hogy korlátozza a virtuális gépek kommunikációját a szükséges rendszerekkel. Más kommunikációs kísérletek le vannak tiltva.

> [!NOTE]
> A tárolási tűzfalak az [Azure Storage biztonsági áttekintése című](../../storage/blobs/security-recommendations.md) cikkben találhatók.

#### <a name="network-security-rules-nsgs"></a>Hálózati biztonsági szabályok (NSG)

Ha alapszintű hálózati szintű hozzáférés-vezérlésre van szüksége (IP-cím és TCP vagy UDP protokollok alapján), használhat hálózati biztonsági csoportokat (NSG). Az NSG egy alapszintű, állapot-nyilvántartó, csomagszűrő tűzfal, amely lehetővé teszi, hogy [5 rekord](https://www.techopedia.com/definition/28190/5-tuple)alapján vezérelje a hozzáférést. A NSG funkciókkal egyszerűsítheti a felügyeletet, és csökkentheti a konfigurációs hibák esélyét:

* A **kibővített biztonsági szabályok** egyszerűsítik a NSG, és lehetővé teszik, hogy összetett szabályokat hozzon létre ahelyett, hogy több egyszerű szabályt kellene létrehoznia ugyanazon eredmény eléréséhez.
* A **szolgáltatás-címkék** olyan Microsoft által létrehozott címkék, amelyek IP-címek egy csoportját jelölik. Dinamikusan frissülnek, hogy olyan IP-tartományokat tartalmazzanak, amelyek megfelelnek a címkébe belefoglalást definiáló feltételeknek. Ha például olyan szabályt szeretne létrehozni, amely a keleti régió összes Azure-tárolójára vonatkozik, használhatja a Storage. EastUS
* Az **alkalmazás biztonsági csoportjai** lehetővé teszik erőforrások üzembe helyezését az alkalmazási csoportokban, valamint az ilyen erőforrásokhoz való hozzáférés szabályozását az adott alkalmazáscsoport használatára vonatkozó szabályok létrehozásával. Ha például a "webkiszolgálók" alkalmazáscsoport üzembe helyezett webkiszolgálókkal rendelkezik, létrehozhat egy olyan szabályt, amely a 443-es forgalmat az internetről a "webkiszolgálók" NSG összes rendszerére engedélyezi.

A NSG nem biztosítanak alkalmazás-rétegbeli ellenőrzést vagy hitelesített hozzáférés-vezérlést.

További információ:

* [Hálózati biztonsági csoportok](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC – a virtuális gép elérési ideje

Az [Azure Security Center](../../security-center/security-center-introduction.md) kezelheti a virtuális gépek NSG, és zárolhatja a virtuális géphez való hozzáférést, amíg egy felhasználó nem rendelkezik a megfelelő Azure szerepköralapú hozzáférés-vezérléssel az [Azure RBAC](../../role-based-access-control/overview.md) -engedélyek hozzáférés kéréséhez. Ha a felhasználó sikeresen engedélyezte az ASC-t, a módosításokat végez a NSG, hogy engedélyezze a hozzáférést a kiválasztott portokhoz a megadott időpontig. Ha az idő lejár, a rendszer visszaállítja a NSG az előző biztonságos állapotba.

További információ:

* [Elég Azure Security Center az időben való hozzáféréshez](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A szolgáltatási végpontok egy másik módszer a forgalom szabályozására. A támogatott szolgáltatásokkal való kommunikációt korlátozhatja a közvetlen kapcsolaton keresztüli virtuális hálózatok. A VNet a megadott Azure-szolgáltatás felé irányuló forgalom a Microsoft Azure gerinc hálózatán marad.  

További információ:

* [Szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Irányítás és kényszerített bújtatás

A virtuális hálózatok útválasztási viselkedésének szabályozása kritikus fontosságú. Ha az Útválasztás helytelenül van konfigurálva, a virtuális gépen futó alkalmazások és szolgáltatások csatlakozhatnak jogosulatlan eszközökhöz, beleértve a lehetséges támadók által birtokolt és üzemeltetett rendszereket is.

Az Azure Networking lehetővé teszi a virtuális hálózatokon lévő hálózati forgalom útválasztási viselkedésének testreszabását. Ez lehetővé teszi, hogy megváltoztassa a virtuális hálózat alapértelmezett útválasztási táblázatának bejegyzéseit. Az útválasztási viselkedés vezérlésével meggyőződhet róla, hogy egy adott eszközről vagy eszközről érkező összes forgalom egy adott helyen belép vagy elhagyja a virtuális hálózatot.

Előfordulhat például, hogy virtuális hálózati biztonsági berendezéssel rendelkezik a virtuális hálózaton. Győződjön meg arról, hogy a virtuális hálózat felé irányuló és onnan érkező összes forgalom áthalad a virtuális biztonsági berendezésen. Ezt úgy teheti meg, hogy a [felhasználó által megadott útvonalakat](../../virtual-network/virtual-networks-udr-overview.md) (UDR) konfigurálja az Azure-ban.

A [kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus, amellyel biztosítható, hogy a szolgáltatások ne kezdeményezzenek kapcsolatot az internethez csatlakozó eszközökhöz. Vegye figyelembe, hogy ez nem egyezik a bejövő kapcsolatok fogadásával, majd válaszol rájuk. Az előtér-webkiszolgálóknak válaszolnia kell az internetes gazdagépekről érkező kérésekre, és így az internetre irányuló forgalom engedélyezve van a webkiszolgálók felé, és a webkiszolgálók is képesek válaszolni.

A nem kívánja engedélyezni, hogy egy előtér-webkiszolgáló kezdeményezse a kimenő kérelmeket. Az ilyen kérések biztonsági kockázatot jelenthetnek, mivel ezek a kapcsolatok a kártevők letöltéséhez használhatók. Ha azt szeretné, hogy ezek az előtér-kiszolgálók kezdeményezik a kimenő kérelmeket az internetre, érdemes lehet kényszeríteni őket a helyszíni webproxyk átlépésére. Ez lehetővé teszi, hogy kihasználhassa az URL-szűrést és a naplózást.

Ehelyett kényszerített bújtatást szeretne használni Ennek megelőzésére. Ha engedélyezi a kényszerített bújtatást, az internetre irányuló összes kapcsolat kényszerítve van a helyszíni átjárón keresztül. A kényszerített bújtatást a UDR előnyeinek kihasználásával állíthatja be.

További információ:

* [Mi a felhasználó által megadott útvonalak és IP-továbbítás](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági berendezések

Habár a NSG, a UDR és a kényszerített bújtatás biztosítja a biztonságot az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model)hálózati és átviteli rétegei számára, érdemes lehet biztonsági szintet is engedélyezni a hálózatnál magasabb szinten.

A biztonsági követelmények például a következők lehetnek:

* Hitelesítés és engedélyezés az alkalmazáshoz való hozzáférés engedélyezése előtt
* Behatolás-észlelés és behatolási válasz
* Alkalmazások rétegének vizsgálata magas szintű protokollokhoz
* URL-szűrés
* Hálózati szintű víruskereső és antimalware
* Robot elleni védelem
* Alkalmazás-hozzáférés-vezérlés
* További DDoS Protection (az Azure-háló által biztosított DDoS-védelem felett)

Ezeket a kibővített hálózati biztonsági funkciókat Azure-partneri megoldás használatával érheti el. Az Azure [piactéren](https://azure.microsoft.com/marketplace/)megtekintheti a legfrissebb Azure Partner Network biztonsági megoldásokat, és megkeresheti a "biztonság" és a "hálózati biztonság" kifejezést.

## <a name="azure-firewall"></a>Azure Firewall

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy szolgáltatásként nyújtott teljesen állapotalapú tűzfal, beépített magas rendelkezésre állással és korlátlan felhőalapú skálázhatósággal. Néhány funkció többek között:

* Magas rendelkezésre állás
* Felhő méretezhetősége
* Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok
* Hálózati forgalomra vonatkozó szűrési szabályok

További információ:

* [Azure Firewall áttekintése](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>A távoli hozzáférés és a létesítmények közötti kapcsolatok biztonságossá tétele

Az Azure-erőforrások beállítását, konfigurálását és felügyeletét távolról kell végrehajtani. Emellett előfordulhat, hogy a helyszíni és az Azure-beli nyilvános felhőben található összetevőket tartalmazó [hibrid informatikai](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) megoldásokat is telepíteni kíván. Ezek a forgatókönyvek biztonságos távoli hozzáférést igényelnek.

Az Azure Networking a következő biztonságos távelérési forgatókönyveket támogatja:

* Különálló munkaállomások összekötése egy virtuális hálózattal
* Helyszíni hálózat összekapcsolása virtuális hálózattal VPN-kapcsolattal
* Helyszíni hálózat csatlakoztatása egy dedikált WAN-kapcsolattal rendelkező virtuális hálózathoz
* Virtuális hálózatok összekötése egymással

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Különálló munkaállomások összekötése egy virtuális hálózattal

Előfordulhat, hogy a virtuális gépek és a szolgáltatások az Azure-ban való kezeléséhez engedélyezni szeretné az egyes fejlesztőket vagy műveleti munkatársakat. Tegyük fel például, hogy egy virtuális hálózaton lévő virtuális géphez kell hozzáférést biztosítania. A biztonsági házirend azonban nem engedélyezi az RDP-vagy SSH-távoli hozzáférést az egyes virtuális gépekhez. Ebben az esetben egy [pont – hely típusú VPN-](../../vpn-gateway/point-to-site-about.md) kapcsolat is használható.

A pont – hely VPN-kapcsolat lehetővé teszi, hogy beállítson egy privát és biztonságos kapcsolatot a felhasználó és a virtuális hálózat között. A VPN-kapcsolat létrejötte után a felhasználó RDP-vagy SSH-kapcsolatot létesíthet a VPN-kapcsolaton keresztül a virtuális hálózat bármely virtuális gépén. (Ez azt feltételezi, hogy a felhasználó hitelesítése és engedélyezése is megtörténik.) A pont – hely VPN a következőket támogatja:

* A Secure Socket Tunneling Protocol (SSTP) egy saját SSL-alapú VPN-protokoll. Az SSL VPN-megoldás képes behatolni a tűzfalakba, mivel a legtöbb tűzfal a 443-as TCP-portot nyitja meg, amely a TLS/SSL protokollt használja. Az SSTP csak Windows-eszközökön támogatott. Az Azure a Windows összes olyan verzióját támogatja, amely SSTP-t (Windows 7 és újabb) tartalmaz.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

További információ:

* [Pont – hely kapcsolat konfigurálása egy virtuális hálózathoz a PowerShell használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Helyszíni hálózat összekapcsolása virtuális hálózattal VPN-kapcsolattal

Előfordulhat, hogy a teljes vállalati hálózatot vagy annak részeit egy virtuális hálózatra szeretné kapcsolni. Ez gyakori a hibrid IT-forgatókönyvekben, ahol [a szervezetek a helyszíni adatközpontot az Azure-ba terjesztik](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Számos esetben a szervezetek az Azure-ban és a helyszínen található szolgáltatások részeit üzemeltetik. Így például megtehetik, ha egy megoldás előtér-webkiszolgálókat tartalmaz az Azure-ban, és helyszíni háttér-adatbázisokat tartalmaz. A "létesítmények közötti" kapcsolatok ezen típusai biztonságosabbá teszik az Azure-beli erőforrások kezelését, és olyan forgatókönyveket tesznek lehetővé, mint például a Active Directory tartományvezérlők kiterjesztése az Azure-ba.

Ennek egyik módja, ha [helyek közötti VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)-t használ. A helyek közötti VPN és a pont – hely típusú VPN közötti különbség az, hogy az utóbbi egyetlen eszközt csatlakoztat egy virtuális hálózathoz. A helyek közötti VPN a teljes hálózatot (például a helyszíni hálózatot) egy virtuális hálózathoz csatlakoztatja. A két hálózat közötti pont-pont típusú VPN-ek a biztonságos IPsec-alagút módú VPN protokollt használják.

További információ:

* [Resource Manager-VNet létrehozása helyek közötti VPN-kapcsolattal a Azure Portal használatával](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Információk a VPN Gateway-ről](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Helyszíni hálózat csatlakoztatása egy dedikált WAN-kapcsolattal rendelkező virtuális hálózathoz

A pont – hely és a helyek közötti VPN-kapcsolatok a létesítmények közötti kapcsolat engedélyezéséhez hatékonyak. Egyes szervezetek azonban azt gondolják, hogy a következő hátrányai vannak:

* A VPN-kapcsolatok az interneten keresztül helyezik át az adatátvitelt. Ez a kapcsolat elérhetővé teszi az adatáthelyezést a nyilvános hálózaton keresztül felmerülő esetleges biztonsági problémákra. Emellett az internetkapcsolat megbízhatósága és rendelkezésre állása nem garantálható.
* Előfordulhat, hogy a virtuális hálózatokkal létesített VPN-kapcsolatok nem rendelkeznek a sávszélességgel bizonyos alkalmazások és célok esetében, mivel ezek max. körülbelül 200 Mbps.

Azok a szervezetek, amelyeknek a legmagasabb szintű biztonságra és rendelkezésre állásra van szükségük a létesítmények közötti kapcsolatok esetében, jellemzően dedikált WAN-hivatkozásokat használnak a távoli helyekhez való csatlakozáshoz. Az Azure lehetővé teszi egy dedikált WAN-kapcsolat használatát, amellyel a helyszíni hálózatot csatlakoztathatja egy virtuális hálózathoz. Az Azure ExpressRoute, az expressz Route Direct és az Express Route globális elérhetősége lehetővé teszi ezt.

További információ:

* [Az ExpressRoute technikai áttekintése](../../expressroute/expressroute-introduction.md)
* [Közvetlen ExpressRoute](../../expressroute/expressroute-erdirect-about.md)
* [Expressz útvonal globális elérhetősége](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Virtuális hálózatok összekötése egymással

Számos virtuális hálózatot használhat az üzemelő példányokhoz. Ennek számos oka lehet. Érdemes lehet leegyszerűsíteni a felügyeletet, vagy érdemes lehet nagyobb biztonságot használni. Függetlenül attól, hogy a különböző virtuális hálózatok erőforrásainak elhelyezésére milyen motiváció van, előfordulhat, hogy az egyes hálózatokon lévő erőforrások egymáshoz való kapcsolódáshoz szükségesek.

Az egyik lehetőség az, hogy az egyik virtuális hálózaton lévő szolgáltatásokhoz kapcsolódjon egy másik virtuális hálózat szolgáltatásaihoz, az interneten keresztül "visszakapcsolva". A kapcsolódás egy virtuális hálózaton kezdődik, az interneten keresztül történik, majd visszatér a célként megadott virtuális hálózatra. Ez a beállítás lehetővé teszi az internetes kommunikációban rejlő biztonsági problémákhoz való kapcsolódást.

Jobb megoldás lehet egy helyek közötti VPN létrehozása, amely két virtuális hálózat között csatlakozik. Ez a módszer ugyanazt az [IPSec Tunnel Mode](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) protokollt használja, mint a fent említett, telephelyek közötti VPN-kapcsolat.

Ennek a megközelítésnek az az előnye, hogy a VPN-kapcsolat az Azure hálózati hálón keresztül lett létrehozva az interneten keresztüli csatlakozás helyett. Ez egy további biztonsági réteget biztosít, amely az interneten keresztül csatlakozó helyek közötti VPN-kapcsolatokhoz hasonlít.

További információ:

* [VNet-VNet közötti kapcsolatok konfigurálása Azure Resource Manager és a PowerShell használatával](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

A virtuális hálózatok összekapcsolásának másik módja a  [VNET](../../virtual-network/virtual-network-peering-overview.md). Ez a funkció lehetővé teszi két Azure-hálózat összekapcsolását, hogy a közöttük zajló kommunikáció a Microsoft gerinc-infrastruktúrán keresztül történjen anélkül, hogy az interneten keresztül kellene történnie. A VNET-társítás két virtuális hálózatok tud összekötni ugyanazon a régión belül vagy két virtuális hálózatok az Azure-régiók között. A NSG használható a különböző alhálózatok vagy rendszerek közötti kapcsolatok korlátozására.

## <a name="availability"></a>Rendelkezésre állás

A rendelkezésre állás a biztonsági programok egyik kulcsfontosságú összetevője. Ha a felhasználók és a rendszerek nem férnek hozzá a hálózaton keresztüli hozzáféréshez, a szolgáltatás veszélynek minősülhet. Az Azure a következő magas rendelkezésre állási mechanizmusokat támogató hálózatkezelési technológiákat tartalmaz:

* HTTP-alapú terheléselosztás
* Hálózati szintű terheléselosztás
* Globális terheléselosztás

A terheléselosztás olyan mechanizmus, amelynek célja a kapcsolatok egyenlő elosztása több eszköz között. A terheléselosztás célja:

* A rendelkezésre állás növeléséhez. Ha terheléselosztást hajt végre több eszköz között, egy vagy több eszköz elérhetetlenné válhat anélkül, hogy veszélyeztetné a szolgáltatást. A többi online eszközön futó szolgáltatások továbbra is kiszolgálják a szolgáltatás tartalmát.
* A teljesítmény növeléséhez. Ha több eszköz között terheléselosztási kapcsolatokat használ, egyetlen eszköznek sem kell kezelnie az összes feldolgozást. Ehelyett a tartalom kiszolgálására és a memóriára vonatkozó igények több eszköz között oszlanak meg.

### <a name="http-based-load-balancing"></a>HTTP-alapú terheléselosztás

Azok a szervezetek, amelyek webalapú szolgáltatásokat futtatnak, gyakran egy HTTP-alapú Load balancert szeretnének a webszolgáltatások előtt. Ez a teljesítmény és a magas rendelkezésre állás megfelelő szintjének biztosításában nyújt segítséget. A hagyományos, hálózat alapú terheléselosztó hálózati és átviteli rétegbeli protokollokon alapulnak. A HTTP-alapú terheléselosztó másfelől a HTTP protokoll jellemzői alapján hozza meg a döntéseket.

Az Azure Application Gateway HTTP-alapú terheléselosztást biztosít a webalapú szolgáltatásokhoz. Application Gateway a következőket támogatja:

* Cookie-alapú munkamenet-affinitás. Ez a funkció gondoskodik arról, hogy a terheléselosztó mögötti kiszolgálók egyikén létesített kapcsolatok érintetlenek maradnak az ügyfél és a kiszolgáló között. Ez biztosítja a tranzakciók stabilitását.
* TLS-kiszervezés. Amikor egy ügyfél csatlakozik a terheléselosztó szolgáltatáshoz, a rendszer a HTTPS (TLS) protokoll használatával titkosítja a munkamenetet. A teljesítmény növeléséhez azonban a HTTP (titkosítatlan) protokoll használatával csatlakozhat a terheléselosztó és a terheléselosztó mögötti webkiszolgáló között. Ezt "TLS-alapú kiszervezésnek" nevezzük, mert a terheléselosztó mögötti webkiszolgálók nem tapasztalják a titkosítással kapcsolatos processzorok terhelését. A webkiszolgálók ezért gyorsabban lekérhetik a szolgáltatást.
* URL-alapú tartalom-útválasztás. Ez a funkció lehetővé teszi, hogy a terheléselosztó meghozzon döntéseket a kapcsolatok továbbításának helyétől a cél URL-cím alapján. Ez sokkal rugalmasabb, mint az IP-címeken alapuló terheléselosztási döntéseket biztosító megoldások.

További információ:

* [Az Application Gateway áttekintése](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Hálózati szintű terheléselosztás

A HTTP-alapú terheléselosztással szemben a hálózati szintű terheléselosztás az IP-cím és a port (TCP vagy UDP) számán alapuló döntéseket tesz.
A hálózati szintű terheléselosztás előnyeit az Azure-ban Azure Load Balancer segítségével érheti el. A Load Balancer főbb jellemzői a következők:

* Hálózati szintű terheléselosztás IP-cím és portszámok alapján.
* Az alkalmazási réteg protokolljának támogatása.
* Terheléselosztás az Azure Virtual Machines és a Cloud Services szerepkör-példányok között.
* Az internetre irányuló (külső terheléselosztás) és az internetkapcsolattal nem rendelkező (belső terheléselosztási) alkalmazások és virtuális gépek esetében is használható.
* Végpontok figyelése, amely annak meghatározására szolgál, hogy a terheléselosztó mögötti egyik szolgáltatás elérhetetlenné vált-e.

További információ:

* [Internetkapcsolattal rendelkező terheléselosztó több virtuális gép vagy szolgáltatás között](../../load-balancer/load-balancer-overview.md)
* [A belső Load Balancer áttekintése](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Globális terheléselosztás

Egyes szervezetek a lehető legmagasabb szintű rendelkezésre állást szeretnék használni. A cél elérésének egyik módja az alkalmazások üzemeltetése globálisan elosztott adatközpontokban. Ha egy alkalmazás a világ különböző pontjain található adatközpontokban üzemel, lehetséges, hogy egy egész geopolitikai régió elérhetetlenné válik, és továbbra is az alkalmazás működik.

Ez a terheléselosztási stratégia teljesítménybeli előnyöket is eredményezhet. A szolgáltatásra irányuló kéréseket a kérést elküldő eszközhöz legközelebb lévő adatközponthoz irányíthatja.

Az Azure-ban a globális terheléselosztás előnyeit az Azure Traffic Manager segítségével érheti el.

További információ:

* [A Traffic Manager ismertetése](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Névfeloldás

A névfeloldás kritikus funkció az Azure-ban üzemeltetett összes szolgáltatáshoz. Biztonsági szempontból a névfeloldási funkció sérülése egy támadónak a webhelyekről érkező kérések átirányítására is vezethet. A biztonságos névfeloldás az összes felhőben üzemeltetett szolgáltatás követelménye.

A névfeloldásnak két típusa van:

* Belső névfeloldás. Ezt a virtuális hálózatokon, a helyszíni hálózatokon vagy mindkettőn futó szolgáltatások használják. A belső névfeloldáshoz használt nevek nem érhetők el az interneten keresztül. Az optimális biztonság érdekében fontos, hogy a belső névfeloldási séma ne legyen elérhető a külső felhasználók számára.
* Külső név feloldása. Ezt a helyszíni hálózatokon és virtuális hálózatokon kívüli személyek és eszközök használják. Ezek az interneten látható nevek, amelyek a felhőalapú szolgáltatásokhoz való közvetlen kapcsolódásra szolgálnak.

Belső névfeloldás esetén két lehetőség közül választhat:

* Egy virtuális hálózati DNS-kiszolgáló. Új virtuális hálózat létrehozásakor létrejön egy DNS-kiszolgáló. Ez a DNS-kiszolgáló fel tudja oldani a virtuális hálózaton található gépek nevét. Ez a DNS-kiszolgáló nem konfigurálható, az Azure Fabric Manager felügyeli, és így segít a névfeloldási megoldás biztonságossá tételében.
* Saját DNS-kiszolgáló használata. Lehetősége van arra, hogy egy saját DNS-kiszolgálót helyez üzembe a virtuális hálózaton. Ez a DNS-kiszolgáló lehet egy Active Directory integrált DNS-kiszolgáló vagy egy Azure-partner által biztosított dedikált DNS-kiszolgálói megoldás, amelyet az Azure piactéren szerezhet be.

További információ:

* [A virtuális hálózat áttekintése](../../virtual-network/virtual-networks-overview.md)
* [Virtuális hálózat által használt DNS-kiszolgálók kezelése](../../virtual-network/manage-virtual-network.md#change-dns-servers)

A külső névfeloldáshoz két lehetőség közül választhat:

* Saját külső DNS-kiszolgálót üzemeltet a helyszínen.
* Saját külső DNS-kiszolgálót üzemeltet a szolgáltatóval.

Számos nagyméretű szervezet üzemelteti a saját DNS-kiszolgálóit a helyszínen. Ezt azért tehetik meg, mert a hálózati szakértelemmel és a globális jelenléttel rendelkeznek.

A legtöbb esetben jobb a DNS-névfeloldási szolgáltatások üzemeltetése a szolgáltatónál. Ezek a szolgáltatók rendelkeznek a hálózati szakértelemmel és a globális jelenléttel a névfeloldási szolgáltatások rendkívül magas rendelkezésre állásának biztosítása érdekében. A rendelkezésre állás elengedhetetlen a DNS-szolgáltatások számára, mivel ha a névfeloldási szolgáltatások meghiúsulnak, akkor az internet felé irányuló szolgáltatások nem lesznek elérhetők.

Az Azure egy magas rendelkezésre állású, nagy teljesítményű külső DNS-megoldást biztosít, Azure DNS formájában. Ez a külső névfeloldási megoldás kihasználja a globális Azure DNS infrastruktúrát. Lehetővé teszi a tartomány üzemeltetését az Azure-ban, ugyanazokkal a hitelesítő adatokkal, API-kkal, eszközökkel és számlázással, mint a többi Azure-szolgáltatás. Az Azure részeként a platformon beépített erős biztonsági vezérlőket is örökli.

További információ:

* [Azure DNS áttekintése](../../dns/dns-overview.md)
* [Azure DNS privát zónák](../../dns/private-dns-overview.md) lehetővé teszik, hogy egyéni DNS-megoldás hozzáadása nélkül konfigurálja az Azure-erőforrások magánhálózati DNS-neveit az automatikusan hozzárendelt nevek helyett.

## <a name="perimeter-network-architecture"></a>Peremhálózati architektúra

Számos nagyméretű szervezet használ peremhálózat-hálózatot a hálózatok szegmentálásához, és létrehoz egy puffer-zónát az Internet és a szolgáltatásaik között. A hálózat peremhálózati része alacsony biztonsági zónának minősül, és a rendszer nem helyezi át a nagy értékű eszközöket a hálózati szegmensbe. Általában olyan hálózati biztonsági eszközöket fog látni, amelyek hálózati adapterrel rendelkeznek a peremhálózaton lévő hálózati szegmensben. Egy másik hálózati adapter csatlakozik egy olyan hálózathoz, amely olyan virtuális gépekkel és szolgáltatásokkal rendelkezik, amelyek fogadják a bejövő kapcsolatokat az internetről.

A peremhálózati hálózatokat többféleképpen is megtervezheti. A peremhálózat üzembe helyezésének döntését, majd azt, hogy milyen típusú peremhálózaton kell használni, ha úgy dönt, hogy az egyiket használja, a hálózati biztonsági követelményektől függ.

További információ:

* [Microsoft Cloud szolgáltatások és hálózati biztonság](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadás megpróbál kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.
A **Microsoft az Azure** platform részeként ismert DDoS Protectiont biztosít. Ez díjmentesen elérhető, és a gyakori hálózati szintű támadások valós idejű mérséklését is magában foglalja. A DDoS Protection **alapszintű** védelme mellett a **standard** beállítás is engedélyezhető. DDoS Protection a standard funkciók a következők:

* **Natív platform-integráció:** Natív módon integrálva az Azure-ba. A Azure Portal konfigurációját tartalmazza. A DDoS Protection standard értelmezése az erőforrásokat és az erőforrás-konfigurációt.
* **Kulcsrakész védelem:** Az egyszerűsített konfiguráció azonnal megvédi a virtuális hálózat összes erőforrását, amint DDoS Protection standard engedélyezve van. Nincs szükség beavatkozásra vagy felhasználói definícióra. A rendszer azonnal DDoS Protection a standardot, és automatikusan csökkenti a támadásokat, amint azt észlelte.
* **Folyamatos forgalom figyelése:** Az alkalmazás forgalmi mintáit napi 24 órában, a hét 7 napján, a DDoS-támadások mutatóit keresve figyeli. A rendszer a védelmi házirendek túllépése esetén csökkenti a megoldást.
* **Támadásokkal kapcsolatos kockázatcsökkentő jelentések** A támadás-elhárítási jelentések összesített hálózati flow-adatokat használnak az erőforrásokra irányuló támadásokkal kapcsolatos részletes információk biztosításához.
* A **támadásokkal szembeni kockázatcsökkentő folyamat naplófájljai** A támadás-elhárítási folyamat naplói lehetővé teszik, hogy közel valós időben tekintse át az eldobott forgalmat, a továbbított forgalmat és az egyéb támadási adatokat az aktív DDoS-támadás során.
* **Adaptív hangolás:** Az intelligens forgalmi profilkészítés az idő múlásával megtanulja az alkalmazás forgalmát, és kiválasztja és frissíti a szolgáltatás számára legmegfelelőbb profilt. A profil a forgalmi változások időbeli változásával változik. 3. réteg – 7. rétegbeli védelem: teljes stack DDoS-védelmet biztosít a webalkalmazási tűzfallal való használat esetén.
* **Széleskörű kockázatcsökkentő skála:** Több mint 60 különböző támadási típust lehet mérsékelni, globális kapacitással, hogy védelmet nyújtson a legnagyobb ismert DDoS-támadásokkal szemben.
* **Támadási metrikák:** Az egyes támadások összesített metrikái a Azure Monitoron keresztül érhetők el.
* **Támadási riasztások:** A riasztások a támadás indításakor és leállításakor, valamint a támadás időtartamán keresztül konfigurálhatók a beépített támadási metrikák használatával. A riasztások integrálva vannak az operatív szoftverbe, például Microsoft Azure a monitorozási naplók, a splunk, az Azure Storage, az E-mail és a Azure Portal.
* **Cost garancia:**  Adatátviteli és alkalmazás-kibővített szolgáltatási kreditek a dokumentált DDoS-támadásokhoz.
* **DDoS gyors reagálás** DDoS Protection a standard szintű ügyfelek most már hozzáférhetnek a gyors reagálású csapathoz egy aktív támadás során. A DRR a támadásokat és a támadás utáni elemzést is segíti a támadásokkal szemben.


További információ:

* [A DDOS Protection áttekintése](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Az Azure bevezetési szolgáltatásával meghatározhatja, kezelheti és figyelheti a webes forgalom globális útválasztását. Optimalizálja a forgalom útválasztását a legjobb teljesítmény és a magas rendelkezésre állás érdekében. Az Azure Front Doorral egyéni webalkalmazási tűzfalszabályok (WAF-szabályok) készítésével hozzáférés-vezérlést valósíthat meg, amellyel megvédheti a HTTP/HTTPS-számítási feladatait attól, hogy feltörjék őket az ügyféloldali IP-cím, országkód és HTTP-paraméterek alapján. Emellett a bejárati ajtó is lehetővé teszi a díjszabási szabályok megadását a rosszindulatú robot-forgalom elleni küzdelemre, beleértve a TLS-kiszervezést és a HTTP/HTTPS-kérelmeket, az alkalmazás-réteg feldolgozását.

Maga a Front Door platform alapszintű Azure DDoS Protectionnel rendelkezik. További védelemként engedélyezheti a virtuális hálózatain a Standard szintű Azure DDoS Protectiont, így automatikus finomhangolással és kárenyhítéssel megvédheti erőforrásait a hálózati rétegből érkező (TCP/UDP) támadásokkal szemben. A bejárati ajtó egy 7. rétegbeli fordított proxy, amely lehetővé teszi, hogy a webes forgalom a háttér-kiszolgálókon haladjon át, és alapértelmezés szerint blokkolja a más típusú forgalmat.

További információ:

* Az Azure bejárati ajtójának összes funkcióját ismertető további információkért tekintse át az [Azure bejárati ajtót – áttekintés](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít. A Traffic Manager DNS használatával a leginkább megfelelő szolgáltatási végpontra irányítja az ügyfélkéréseket a forgalom-útválasztási módszer és a végpont állapota alapján. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A Traffic Manager figyeli a végpontokat, és nem irányítja át a forgalmat a nem elérhető végpontokra.

További információ:

* [Az Azure Traffic Manager áttekintése](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Figyelés és fenyegetések észlelése

Az Azure olyan lehetőségeket kínál, amelyekkel a kulcsfontosságú területen a hálózati forgalom korai észlelése, monitorozása és begyűjtése és áttekintése végezhető el.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Az Azure Network Watcher segítségével elháríthatja a hibaelhárítást, és a biztonsági problémák azonosítását segítő teljes új eszközkészletet biztosít.

A [biztonsági csoport nézet](../../network-watcher/network-watcher-security-group-view-overview.md) a Virtual Machines naplózási és biztonsági megfelelőségét segíti. Ezzel a szolgáltatással programozott ellenőrzéseket végezhet, összehasonlítva a szervezet által meghatározott alapházirendeket az egyes virtuális gépekre érvényes szabályokkal. Ez segítséget nyújt a konfigurációs eltolódások azonosításához.

A [csomagok rögzítése](../../network-watcher/network-watcher-packet-capture-overview.md) lehetővé teszi a virtuális gép felé irányuló és onnan érkező hálózati forgalom rögzítését. Gyűjthet hálózati statisztikát és elháríthatja az alkalmazással kapcsolatos problémákat, ami felbecsülhetetlen lehet a hálózati behatolások vizsgálatakor. Ezt a funkciót együtt is használhatja Azure Functions a hálózati rögzítés elindítására adott Azure-riasztásokra adott válaszként.

További információ a Network Watcherről és a laborok néhány funkciójának teszteléséről: az [Azure Network Watcher monitorozásának áttekintése](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> A szolgáltatás rendelkezésre állásával és állapotával kapcsolatos legfrissebb értesítésekért tekintse meg az [Azure Updates oldalt](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

A Azure Security Center segít megakadályozni, észlelni és reagálni a fenyegetésekre, és az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között, segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és számos biztonsági megoldással működnek.

A Security Center segítségével optimalizálhatja és figyelheti a hálózati biztonságot:

* Hálózati biztonsági javaslatok biztosítása.
* A hálózati biztonsági konfiguráció állapotának figyelése.
* Riasztást küld a hálózati fenyegetésekről, mind a végponti, mind a hálózati szinten.

További információ:

* [Az Azure Security Center bemutatása](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>Virtual Network KOPPINTson

Az Azure Virtual Network (terminál-hozzáférési pont) funkció lehetővé teszi a virtuális gép hálózati forgalmának folyamatos továbbítását egy hálózati csomag gyűjtője vagy analitikai eszköze számára. A gyűjtő vagy az elemzési eszközt egy hálózati virtuális berendezési partner kapja meg. Ugyanazt a virtuális hálózati KOPPINTÁS erőforrást használhatja az azonos vagy különböző előfizetésekben található több hálózati adapterről érkező forgalom összesítésére.

További információ:

* [Virtuális hálózat TAP-jai](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Naplózás

A hálózati szintű naplózás a hálózati biztonsági forgatókönyvek egyik kulcsfontosságú funkciója. Az Azure-ban a hálózati szintű naplózási információk beszerzéséhez naplózhatja a NSG-hez kapott adatokat. A NSG-naplózással a következő információkat kapja meg:

* [Tevékenységek naplói](../../azure-monitor/essentials/platform-logs-overview.md). Ezen naplók használatával megtekintheti az Azure-előfizetésekhez küldött összes műveletet. Ezek a naplók alapértelmezés szerint engedélyezve vannak, és a Azure Portalon belül is használhatók. Korábban naplózási vagy operatív naplók néven ismertek.
* Eseménynaplók. Ezek a naplók információt nyújtanak arról, hogy milyen NSG-szabályok lettek alkalmazva.
* Számlálónaplók. Ezek a naplók lehetővé teszik, hogy az egyes NSG-szabályok hányszor lettek alkalmazva a forgalom megtagadására vagy engedélyezésére.

A naplók megtekintéséhez és elemzéséhez használhatja a [Microsoft Power bi](https://powerbi.microsoft.com/what-is-power-bi/)hatékony adatvizualizációs eszközt is.
További információ:

* [Hálózati biztonsági csoportok naplóinak Azure Monitor (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md)