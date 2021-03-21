---
title: Azure Virtual Network GYIK
titlesuffix: Azure Virtual Network
description: Válaszok a Microsoft Azure virtuális hálózatokkal kapcsolatos leggyakrabban felmerülő kérdésekre.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: 61076b49d396c83a67635e46eb4f1b928b3f9e1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933969"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure Virtual Network – Gyakori kérdések (GYIK)

## <a name="virtual-network-basics"></a>Virtual Network alapjai

### <a name="what-is-an-azure-virtual-network-vnet"></a>Mi az Azure Virtual Network (VNet)?
Az Azure Virtual Network (VNet) a saját hálózatának ábrázolása a felhőben. A hálózat az előfizetésre kijelölt Azure-felhő logikai elkülönítése. A virtuális hálózatok használatával virtuális magánhálózatok (VPN) hozhatók létre és kezelhetők az Azure-ban, és opcionálisan összekapcsolhatók a virtuális hálózatok más virtuális hálózatok az Azure-ban, illetve a helyszíni informatikai infrastruktúrával hibrid vagy létesítmények közötti megoldások létrehozásához. Minden létrehozott VNet saját CIDR rendelkezik, és más virtuális hálózatok és helyszíni hálózatokhoz is társítható, feltéve, hogy a CIDR-blokkok nem fedik át egymást. A DNS-kiszolgáló beállításai a virtuális hálózatok, valamint a VNet szegmentálása alhálózatokra is megadhatók.

Virtuális hálózatok használata a következőhöz:

* Hozzon létre egy dedikált privát felhőalapú VNet. Előfordulhat, hogy a megoldáshoz nem szükséges a létesítmények közötti konfiguráció. VNet létrehozásakor a VNet belüli szolgáltatásai és virtuális gépei közvetlenül és biztonságosan kommunikálhatnak egymással a felhőben. A megoldás részeként továbbra is konfigurálhat végponti kapcsolatokat a virtuális gépekhez és az internetes kommunikációt igénylő szolgáltatásokhoz.

* Biztonságosan kiterjesztheti az adatközpontot. A virtuális hálózatok segítségével hagyományos, helyek közötti (S2S) VPN-eket hozhat létre az adatközpont kapacitásának biztonságos skálázásához. A S2S VPN-ek az IPSEC használatával biztonságos kapcsolatot biztosítanak a vállalati VPN-átjáró és az Azure között.

* Hibrid Felhőbeli forgatókönyvek engedélyezése. A virtuális hálózatok révén rugalmasan támogathatja a hibrid felhőalapú forgatókönyvek széles körét. Biztonságosan csatlakoztathatók a felhőalapú alkalmazások bármilyen típusú helyszíni rendszerhez, például nagyszámítógépekhez és UNIX rendszerekhez.

### <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?
A kezdéshez látogasson el a [virtuális hálózat dokumentációjában](./index.yml) . Ez a tartalom áttekintést nyújt az összes VNet-szolgáltatásról, és a központi telepítési információkat tartalmazza.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Használhatok virtuális hálózatok a létesítmények közötti kapcsolat nélkül?
Igen. A VNet a helyszíni csatlakoztatása nélkül is használhatja. Futtathatja például a Microsoft Windows Server Active Directory tartományvezérlőket és a SharePoint-farmokat kizárólag Azure-VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Használhatok WAN-optimalizálást a virtuális hálózatok vagy a VNet és a helyszíni adatközpontok között?
Igen. Az Azure Marketplace-en keresztül üzembe helyezhet egy [WAN-optimalizálási hálózati virtuális készüléket](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) több gyártótól.

## <a name="configuration"></a>Konfiguráció

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Milyen eszközöket használhatok a VNet létrehozásához?
A VNet létrehozásához és konfigurálásához a következő eszközöket használhatja:

* Azure Portal
* PowerShell
* Azure CLI
* Egy hálózati konfigurációs fájl (netcfg – csak a klasszikus virtuális hálózatok). Tekintse meg a [VNet konfigurálása hálózati konfigurációs fájl használatával](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) című cikket.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Milyen címtartományokat használhatok a virtuális hálózatokon?
Azt javasoljuk, hogy használja a [1918-es számú RFC-dokumentumban](https://tools.ietf.org/html/rfc1918)felsorolt címtartományt, amelyeket az IETF a magánjellegű, nem irányítható tárolóhelyek számára is meghatároz:
* 10.0.0.0-10.255.255.255 (10/8 előtag)
* 172.16.0.0-172.31.255.255 (172.16/12 előtag)
* 192.168.0.0-192.168.255.255 (192.168/16 előtag)

Előfordulhat, hogy más címterület is működhet, de nem lehet mellékhatása.

Emellett a következő címtartományok nem vehetők fel:
* 224.0.0.0/4 (csoportos küldés)
* 255.255.255.255/32 (szórásos)
* 127.0.0.0/8 (visszacsatolási)
* 169.254.0.0/16 (hivatkozás – helyi)
* 168.63.129.16/32 (belső DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Használhatok nyilvános IP-címeket az virtuális hálózatok?
Igen. A nyilvános IP-címtartományok részletes ismertetését lásd: [virtuális hálózat létrehozása](manage-virtual-network.md#create-a-virtual-network). A nyilvános IP-címek nem érhetők el közvetlenül az internetről.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Korlátozva van az alhálózatok száma az VNet?
Igen. További részletekért lásd az [Azure-korlátokat](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) . Az alhálózati címek nem lehetnek átfedésben egymással.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Vannak korlátozások az IP-címek ezen alhálózatokon belüli használatára?
Igen. Az Azure minden alhálózaton lefoglal 5 IP-címet. Ezek az x. x. x. 0-x. x. x. 3 és az alhálózat utolsó címe. az x. x. x. 1 – x. x. x. 3 Az Azure-szolgáltatások minden alhálózatán le van foglalva.   
- x. x. x. 0: hálózati címe
- x. x. x. 1: az Azure által az alapértelmezett átjáró számára fenntartott
- x. x. x. 2, x. x. x. 3: az Azure által fenntartott Azure DNS IP-címek leképezése a VNet-területre
- x. x. x. 255: hálózati szórási címe

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Milyen kicsi és mekkora lehet a virtuális hálózatok és az alhálózatok mérete?
A legkisebb támogatott IPv4-alhálózat a/29, a legnagyobb pedig/8 (CIDR alhálózati definíciók használatával).  Az IPv6-alhálózatok méretének pontosan/64-nek kell lennie.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Használhatom a VLAN-okat az Azure-ba a virtuális hálózatok használatával?
Nem. A virtuális hálózatok 3. rétegbeli átfedések. Az Azure nem támogatja a 2. rétegbeli szemantika használatát.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Megadhatok egyéni útválasztási házirendeket a virtuális hálózatok és az alhálózatokon?
Igen. Létrehozhat egy útválasztási táblázatot, és hozzárendelheti egy alhálózathoz. További információ az Azure-beli útválasztásról: [útválasztás – áttekintés](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Támogatja a csoportos vagy szórásos küldést a virtuális hálózatok?
Nem. A csoportos küldés és a szórás nem támogatott.

### <a name="what-protocols-can-i-use-within-vnets"></a>Milyen protokollokat használhatok a virtuális hálózatok-en belül?
A virtuális hálózatok esetében TCP-, UDP- és ICMP TCP/IP-protokollokat lehet használni. Az egyéni küldési szolgáltatás támogatott a virtuális hálózatokon belül, kivéve a Dynamic Host Configuration Protocol (DHCP) egyéni küldési szolgáltatással (forrásport: UDP/68 / célport: UDP/67) és a 65330-as UDP-forrásporton keresztüli használatát, amely a gazdagép számára van fenntartva. A csoportos küldés, a szórás, az IP-be bújtatott IP beágyazott csomagok és a Generic Routing Encapsulation- (GRE-) csomagok blokkolva vannak a virtuális hálózatokon belül. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Pingelhető az alapértelmezett útválasztók egy VNet belül?
Nem.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Használhatom a tracertt a kapcsolat diagnosztizálására?
Nem.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Alhálózatokat is Hozzáadhatok a VNet létrehozása után?
Igen. Az alhálózatok bármikor hozzáadhatók a virtuális hálózatok, feltéve, hogy az alhálózati címtartomány nem része egy másik alhálózatnak, és rendelkezésre áll a virtuális hálózat címtartományból elérhető szabad terület.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Megváltoztathatom az alhálózat méretét, miután Létrehoztam?
Igen. Egy alhálózatot akkor vehet fel, távolíthat el, bővíthet vagy szűkíthet, ha nincsenek benne üzembe helyezett virtuális gépek vagy szolgáltatások.

### <a name="can-i-modify-vnet-after-i-created-them"></a>Módosíthatom a vnet a létrehozása után?
Igen. Hozzáadhat, eltávolíthat és módosíthat a VNet által használt CIDR-blokkokat.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Ha egy VNet futtatom a szolgáltatásokat, csatlakozhatok az internethez?
Igen. A VNet belül üzembe helyezett összes szolgáltatás csatlakozhat az internethez. Ha többet szeretne megtudni az Azure-beli kimenő internetkapcsolatokról, tekintse meg a [Kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört. Ha a Resource Manageren keresztül központilag telepített erőforráshoz szeretne bejövő kapcsolatot létesíteni, az erőforráshoz hozzá kell rendelni egy nyilvános IP-címet. A nyilvános IP-címekkel kapcsolatos további tudnivalókért tekintse meg a [nyilvános IP-címek](virtual-network-public-ip-address.md)című témakört. Az Azure-ban üzembe helyezett összes Azure Cloud Service-hez egy nyilvánosan elérhető virtuális IP-cím tartozik. A virtuális gépekhez tartozó Péter-szerepkörök és-végpontok bemeneti végpontokat határozhat meg, amelyek lehetővé teszik, hogy ezek a szolgáltatások fogadják az internetről érkező kapcsolatokat.

### <a name="do-vnets-support-ipv6"></a>Támogatja a virtuális hálózatok az IPv6-ot?
Igen, a virtuális hálózatok csak IPv4 vagy kettős verem lehet (IPv4 + IPv6).  Részletekért lásd: [az IPv6 Azure-beli virtuális hálózatok áttekintése](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>VNet span régiók?
Nem. Egy VNet csak egyetlen régióra korlátozódik. A virtuális hálózatok azonban kiterjedhetnek rendelkezésre állási zónákra. A rendelkezésre állási zónákkal kapcsolatos további információkért tekintse meg [a rendelkezésre állási zónák áttekintését](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A virtuális hálózatokat különböző régiókban is összekapcsolhatjuk a virtuális hálózatok összevonásával. Részletekért lásd: a [Virtual Network peering áttekintése](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Összekapcsolhatok egy VNet egy másik VNet az Azure-ban?
Igen. Az egyik VNet egy másik VNet is összekapcsolhatók a következők használatával:
- **Virtuális hálózat** társítása: további részletekért lásd: [VNet peering – áttekintés](virtual-network-peering-overview.md)
- **Azure-VPN Gateway**: a részletekért tekintse meg [a VNet-VNet kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört. 

## <a name="name-resolution-dns"></a>Névfeloldás (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Mik a virtuális hálózatok DNS-beállításai?
A [virtuális gépek és a szerepkör-példányok oldalának névfeloldásra vonatkozó](virtual-networks-name-resolution-for-vms-and-role-instances.md) döntési táblázata végigvezeti Önt az összes rendelkezésre álló DNS-beállításon.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Megadhatok DNS-kiszolgálókat a VNet?
Igen. A DNS-kiszolgáló IP-címeit a VNet beállításai között adhatja meg. A beállítás a VNet összes virtuális gépe alapértelmezett DNS-kiszolgáló (k) ként lesz alkalmazva.

### <a name="how-many-dns-servers-can-i-specify"></a>Hány DNS-kiszolgálót adhatok meg?
Az [Azure korlátainak](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)áttekintése.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Megváltoztathatom a DNS-kiszolgálókat a hálózat létrehozása után?
Igen. Bármikor módosíthatja a DNS-kiszolgáló listáját a VNet. Ha módosítja a DNS-kiszolgáló listáját, akkor a VNet összes érintett virtuális gépe DHCP-címbérlet megújítását kell végrehajtania, hogy az új DNS-beállítások érvénybe lépjenek. A Windows operációs rendszert futtató virtuális gépek esetében `ipconfig /renew` közvetlenül a virtuális gépre írhat. Más operációsrendszer-típusok esetében tekintse meg a DHCP-bérlet megújítási dokumentációját az adott operációsrendszer-típushoz. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Mi az Azure által biztosított DNS, és működik a virtuális hálózatok?
Az Azure által biztosított DNS egy több-bérlős DNS-szolgáltatás, amelyet a Microsoft kínál. Az Azure regisztrálja az összes virtuális gépet és a felhőalapú szolgáltatás szerepkör-példányát a szolgáltatásban. Ez a szolgáltatás névfeloldást biztosít az állomásnév alapján a virtuális gépek és a szerepkör-példányok számára, amely ugyanabban a felhőalapú szolgáltatásban található, valamint a virtuális gépek és a szerepkör-példányok teljes tartományneve azonos VNet. A DNS szolgáltatással kapcsolatos további tudnivalókért tekintse meg [a virtuális gépek és a Cloud Services szerepkör-példányok](virtual-networks-name-resolution-for-vms-and-role-instances.md)névfeloldását ismertető témakört.

Az Azure által biztosított DNS-t használó VNet esetében az első 100-es felhőalapú szolgáltatásra korlátozás vonatkozik. Ha saját DNS-kiszolgálót használ, akkor ez a korlátozás nem érvényes.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Lehet-e felülbírálni a DNS-beállításokat a virtuális gépen vagy a felhőalapú szolgáltatáson?
Igen. A DNS-kiszolgálókat virtuális gépenként vagy a Cloud Service-ben beállíthatja az alapértelmezett hálózati beállítások felülbírálásához. Javasoljuk azonban, hogy a lehető legnagyobb mértékben használja a hálózati szintű DNS-t.

### <a name="can-i-bring-my-own-dns-suffix"></a>Használhatom a saját DNS-utótagot?
Nem. Nem adhat meg egyéni DNS-utótagot a virtuális hálózatok.

## <a name="connecting-virtual-machines"></a>Virtuális gépek csatlakoztatása

### <a name="can-i-deploy-vms-to-a-vnet"></a>Telepíthetek virtuális gépeket egy VNet?
Igen. A Resource Manager-alapú üzemi modellen keresztül üzembe helyezett virtuális géphez csatlakoztatott összes hálózati adaptert (NIC) csatlakoztatni kell egy VNet. A klasszikus üzemi modellel üzembe helyezett virtuális gépek opcionálisan csatlakozhatnak egy VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Milyen típusú IP-címeket lehet hozzárendelni a virtuális gépekhez?
* **Magánjellegű:** Az egyes virtuális gépeken belül minden hálózati adapterhez hozzárendelve. A címeket a rendszer a statikus vagy a dinamikus módszer használatával rendeli hozzá. A magánhálózati IP-címek a VNet alhálózati beállításaiban megadott tartományhoz vannak rendelve. A klasszikus üzemi modellen keresztül üzembe helyezett erőforrások magánhálózati IP-címeket kapnak, még akkor is, ha nem csatlakoznak egy VNet. A kiosztási módszer működése eltérő attól függően, hogy az erőforrást a Resource Managerrel vagy a klasszikus üzemi modellel telepítették-e: 

  - **Resource Manager**: a dinamikus vagy statikus metódushoz rendelt magánhálózati IP-címek továbbra is hozzá vannak rendelve egy virtuális géphez (Resource Manager), amíg az erőforrást nem törlik. A különbség az, hogy kijelöli a statikus használatakor hozzárendelni kívánt címeket, az Azure pedig dinamikus használata esetén választ. 
  - **Klasszikus**: a dinamikus módszerhez rendelt magánhálózati IP-címek megváltozhatnak, ha a virtuális gép (klasszikus) VM újraindul, miután a leállított (fel nem foglalt) állapotú. Ha meg kell győződnie arról, hogy a klasszikus üzemi modellel központilag telepített erőforrások magánhálózati IP-címe soha nem változik, rendeljen hozzá egy magánhálózati IP-címet a statikus metódushoz.

* **Nyilvános:** Opcionálisan hozzá van rendelve a Azure Resource Manager üzemi modellel üzembe helyezett virtuális gépekhez csatolt hálózati adapterekhez. A címeket a statikus vagy dinamikus kiosztási módszerrel lehet hozzárendelni. A klasszikus üzemi modellel telepített összes virtuális gép és Cloud Services szerepkör-példány egy felhőalapú szolgáltatásban található, amely egy *dinamikus*, nyilvános virtuális IP-címet (VIP) rendel hozzá. Egy [fenntartott IP-címnek](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)nevezett nyilvános *statikus* IP-cím opcionálisan VIP-ként is hozzárendelhető. Nyilvános IP-címeket rendelhet az egyes virtuális gépekhez, vagy Cloud Services a klasszikus üzemi modellel telepített szerepkör-példányokat. Ezeket a címeket [példányok szintjének nyilvános IP-](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) címének NEVEZZÜK (ILPIP-címek, és dinamikusan is hozzárendelhetők.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Foglalhatok le egy magánhálózati IP-címet egy olyan virtuális géphez, amelyet később létre fogok hozni?
Nem. Privát IP-címet nem foglalhat le. Ha a magánhálózati IP-cím elérhető, a DHCP-kiszolgáló egy virtuális géphez vagy szerepkör-példányhoz rendeli hozzá. Előfordulhat, hogy a virtuális gép nem az, amelyhez hozzá szeretné rendelni a magánhálózati IP-címet. Megváltoztathatja azonban egy már létrehozott virtuális gép magánhálózati IP-címét bármely elérhető magánhálózati IP-címhez.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>A magánhálózati IP-címek módosulnak a VNet található virtuális gépek esetében?
Attól függ. Ha a virtuális gépet a Resource Managerrel telepítette, nem, függetlenül attól, hogy az IP-cím hozzá lett-e rendelve a statikus vagy dinamikus kiosztási módszerhez. Ha a virtuális gépet a klasszikus üzemi modellen keresztül telepítették, a dinamikus IP-címek megváltozhatnak, amikor a virtuális gép elindul a leállított (lefoglalt) állapot után. A rendszer a virtuális gép törlésekor a telepítési modellel üzembe helyezett virtuális gépről bocsátja ki a címeket.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Lehet-e manuálisan hozzárendelni IP-címeket a hálózati adapterekhez a virtuális gép operációs rendszerén belül?
Igen, de nem ajánlott, ha szükséges, például ha több IP-címet rendel egy virtuális géphez. Részletekért lásd: [több IP-cím hozzáadása egy virtuális géphez](virtual-network-multiple-ip-addresses-portal.md#os-config). Ha egy virtuális géphez csatlakoztatott Azure hálózati adapterhez hozzárendelt IP-cím és a virtuális gép operációs rendszerének IP-címe eltérő, elveszíti a kapcsolatot a virtuális géppel.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Ha leállítom a Cloud Service üzembe helyezési pontját, vagy leállítja a virtuális gépet az operációs rendszerből, mi történik az IP-címekkel?
Semmit nem fogok észlelni. Az IP-címek (nyilvános VIP, Public és Private) továbbra is hozzá vannak rendelve a Cloud Service üzembe helyezési ponthoz vagy virtuális géphez.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Áthelyezhetek virtuális gépeket az egyik alhálózatból egy másik alhálózatba egy VNet újratelepítése nélkül?
Igen. További információt a [virtuális gép vagy szerepkör példányának áthelyezése másik alhálózatra](/previous-versions/azure/virtual-network/virtual-networks-move-vm-role-to-subnet) című cikkben talál.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Tudok statikus MAC-címeket konfigurálni a virtuális géphez?
Nem. A MAC-címek nem konfigurálhatók statikusan.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>A MAC-címe ugyanaz marad, mint a virtuális gép létrehozása után?
Igen, a MAC-címe ugyanaz marad, mint a Resource Manager és a klasszikus üzemi modelleken keresztül üzembe helyezett virtuális gépek, egészen addig, amíg el nem távolítják. Korábban a MAC-címe megjelent, ha a virtuális gép le lett állítva (felszabadítva), de most a MAC-címe is megmarad, még akkor is, ha a virtuális gép a felszabadított állapotban van. A MAC-cím továbbra is a hálózati adapterhez lesz rendelve, amíg a hálózati adaptert nem törlik, vagy az elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt magánhálózati IP-cím módosul. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Csatlakozhatok az internethez egy VNet található virtuális gépről?
Igen. Egy VNet belül telepített összes virtuális gép és Cloud Services szerepkör-példány csatlakozhat az internethez.

## <a name="azure-services-that-connect-to-vnets"></a>Virtuális hálózatok-hez csatlakozó Azure-szolgáltatások

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Használhatok Azure App Service Web Apps VNet?
Igen. Web Apps központilag telepítheti a VNet egy szolgáltató (App Service Environment) használatával, összekapcsolhatja az alkalmazásait a virtuális hálózatok a VNet-integrációval, és zárolhatja az alkalmazás felé irányuló bejövő forgalmat a szolgáltatási végpontokkal. További információért tekintse át a következő cikkeket:

* [Hálózati szolgáltatások App Service](../app-service/networking-features.md)
* [Web Apps létrehozása App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Alkalmazás integrálása egy Azure-beli virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Hozzáférési korlátozások App Service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Telepíthetek Cloud Services webes és feldolgozói szerepkörökkel (VNet)?
Igen. A virtuális hálózatok belül (opcionálisan) Cloud Services szerepkör-példányokat is telepíthet. Ehhez meg kell adnia a VNet nevét és a szerepkör/alhálózat hozzárendeléseit a szolgáltatás konfigurációjának hálózati konfiguráció szakaszában. Nem kell frissítenie a bináris fájljait.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Összekapcsolhatok egy virtuálisgép-méretezési készletet egy VNet?
Igen. A virtuálisgép-méretezési csoportnak egy VNet kell kapcsolódnia.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Létezik-e olyan Azure-szolgáltatások teljes listája, amelyek segítségével erőforrásokat telepíthetek egy VNet?
Igen, részletekért lásd: [Virtual Network Integration for Azure Services](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Hogyan lehet korlátozni az Azure Pásti-erőforrásokhoz való hozzáférést egy VNet?

Az Azure Pásti-szolgáltatásokkal (például az Azure Storage és a Azure SQL Database) keresztül üzembe helyezett erőforrások a virtuális hálózati szolgáltatás-végpontok vagy az Azure privát kapcsolatok használatával korlátozhatják a VNet hálózati hozzáférését. Részletekért lásd: [Virtual Network szolgáltatás-végpontok áttekintése](virtual-network-service-endpoints-overview.md), [Azure Private link – áttekintés](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Áthelyezhetem a szolgáltatásaikat a virtuális hálózatok-be és ki?
Nem. A szolgáltatások nem helyezhetők át a virtuális hálózatok és a szolgáltatásba. Ha egy erőforrást át szeretne helyezni egy másik VNet, törölnie kell, majd újra kell telepítenie az erőforrást.

## <a name="security"></a>Biztonság

### <a name="what-is-the-security-model-for-vnets"></a>Mi a virtuális hálózatok biztonsági modellje?
A virtuális hálózatok el vannak különítve egymástól, és az Azure-infrastruktúrában üzemeltetett egyéb szolgáltatások. A VNet egy megbízhatósági kapcsolat határa.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Korlátozható a bejövő vagy a kimenő forgalom a VNet-hez csatlakoztatott erőforrásokra?
Igen. A [hálózati biztonsági csoportokat](./network-security-groups-overview.md) az egyes alhálózatokra alkalmazhatja egy VNet, VNet csatlakoztatott hálózati adapterek vagy mindkettő használatával.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Használhatok tűzfalat a VNet-hez csatlakoztatott erőforrások között?
Igen. Az Azure Marketplace-en keresztül üzembe helyezhet egy [tűzfal hálózati virtuális készüléket](https://azure.microsoft.com/marketplace/?term=firewall) több gyártótól.

### <a name="is-there-information-available-about-securing-vnets"></a>Van elérhető információ a virtuális hálózatok biztonságossá tételéről?
Igen. Részletekért lásd: az [Azure hálózati biztonság áttekintése](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="do-virtual-networks-store-customer-data"></a>A virtuális hálózatok tárolják az ügyféladatokat?
Nem. A virtuális hálózatok nem tárolnak ügyféladatokat. 

## <a name="apis-schemas-and-tools"></a>API-k, sémák és eszközök

### <a name="can-i-manage-vnets-from-code"></a>Kezelhetem a virtuális hálózatok a kódból?
Igen. A virtuális hálózatok REST API-kat használhat a [Azure Resource Manager](/rest/api/virtual-network) és a [klasszikus](/previous-versions/azure/ee460799(v=azure.100)) üzembe helyezési modellekben.

### <a name="is-there-tooling-support-for-vnets"></a>Támogatott-e a virtuális hálózatok-eszközök támogatása?
Igen. További információ a használatáról:
- A virtuális hálózatok üzembe helyezésének Azure Portal a [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) és a [klasszikus](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) üzembe helyezési modelleken keresztül.
- PowerShell a [Resource Managerrel](/powershell/module/az.network) és a [klasszikus](/powershell/module/servicemanagement/azure.service/) üzemi modellel telepített virtuális hálózatok kezeléséhez.
- Az Azure parancssori felülete (CLI) a [Resource Managerrel](/cli/azure/network/vnet) és a [klasszikus](/previous-versions/azure/virtual-machines/azure-cli-arm-commands?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) üzemi modellel üzembe helyezett virtuális hálózatok üzembe helyezéséhez és kezeléséhez.  

## <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

### <a name="what-is-vnet-peering"></a>Mi a VNet-társítás?
A VNet-társítás (vagy virtuális hálózati társítás) lehetővé teszi a virtuális hálózatok összekapcsolását. A virtuális hálózatok közötti VNet-társítási kapcsolat lehetővé teszi, hogy a forgalmat az IPv4-címeken keresztül továbbítsa egymás között. A társ-virtuális hálózatok található virtuális gépek kommunikálhatnak egymással, mintha ugyanazon a hálózaton belül vannak. Ezek a virtuális hálózatok lehetnek ugyanabban a régióban vagy különböző régiókban (más néven globális VNet-társítás) is. Az Azure-előfizetések között VNet-társítási kapcsolatok is létrehozhatók.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Létrehozhatok egyenrangú kapcsolatot egy VNet egy másik régióban?
Igen. A globális VNet-társítás lehetővé teszi, hogy a különböző régiókban található társ-virtuális hálózatok. A globális VNet-társítás az összes Azure-beli nyilvános régióban, valamint a kínai Felhőbeli régiókban és a kormányzati Felhőbeli régiókban érhető el. Az Azure nyilvános régióiról nem lehet globális társat csatlakozni a nemzeti Felhőbeli régiókhoz.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Milyen megkötések vonatkoznak a globális VNet-társításokra és-Terheléselosztóokra?
Ha a két különböző régióban található két virtuális hálózat a globális VNet-társításhoz kapcsolódik, nem tud olyan erőforrásokhoz csatlakozni, amelyek alapszintű Load Balancer mögött találhatók a Load Balancer előtér-IP-címén. Ez a korlátozás nem létezik standard Load Balancerhoz.
A következő erőforrások használhatnak alapszintű Load Balancert, ami azt jelenti, hogy a Load Balancer előtér-IP-címe nem érhető el a globális VNet-társításon keresztül. A globális VNet-társítások használata azonban lehetővé teszi az erőforrások közvetlen elérését a saját VNet IP-címein keresztül, ha ez engedélyezve van. 
- Alapszintű terheléselosztó mögötti virtuális gépek
- Virtuálisgép-méretezési csoportok alapszintű terheléselosztó 
- Redis Cache 
- Application Gateway (v1) SKU
- Service Fabric
- API Management
- Active Directory-tartomány szolgáltatás (Hozzáadás)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service-környezet

Ezekhez az erőforrásokhoz a ExpressRoute vagy a VNet – VNet használatával csatlakozhat a VNet-átjárók segítségével.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Engedélyezhető a VNet-társítás, ha a virtuális hálózatok különböző Azure Active Directory bérlőn belüli előfizetésekhez tartoznak?
Igen. Ha az előfizetések különböző Azure Active Directory bérlőhöz tartoznak, létrehozhatók VNet-társítások (helyi vagy globális). Ezt a portálon, a PowerShellen vagy a CLI-n keresztül teheti meg.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>A VNet-társítási kapcsolat *kezdeményezett* állapotban van, miért nem tudok csatlakozni?
Ha a társ-kapcsolat *kezdeményezett* állapotban van, ez azt jelenti, hogy csak egy hivatkozást hozott létre. Sikeres kapcsolat létrehozásához kétirányú hivatkozást kell létrehozni. Ha például az A társ VNet a B VNet, a hivatkozást létre kell hozni a Társviszonyban áll-ből a b-be és a b-ből a Társviszonyban áll-be. Mindkét hivatkozás létrehozásakor a rendszer a *csatlakoztatott* állapotot fogja módosítani.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>A VNet-társítási kapcsolat *leválasztott* állapotban van, miért nem hozható létre egyenrangú kapcsolat?
Ha a VNet-társítási kapcsolat *leválasztott* állapotban van, az azt jelenti, hogy az egyik létrehozott hivatkozás törölve lett. Ha újra létre szeretne hozni egy társ-kapcsolatot, törölnie kell a hivatkozást, majd újra létre kell hoznia.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Használhatom a VNet egy másik előfizetésben lévő VNet?
Igen. A partneri virtuális hálózatok az előfizetések és régiók között is.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Összekapcsolható két virtuális hálózatok a megfelelő vagy átfedésben lévő címtartományok között?
Nem. A VNet társításának engedélyezéséhez a Címterület nem tartalmazhat átfedést.

### <a name="can-i-peer-a-vnet-to-two-different-vnets-with-the-the-use-remote-gateway-option-enabled-on-both-the-peerings"></a>A VNet két különböző virtuális hálózatok is csatlakozhatnak a "távoli átjáró használata" lehetőséggel mindkét társon?
Nem. A "távoli átjáró használata" beállítás csak az egyik virtuális hálózatok engedélyezhető.

### <a name="how-much-do-vnet-peering-links-cost"></a>Mennyibe kerül a VNet peering Links?
Nem számítunk fel díjat a VNet-társi kapcsolatok létrehozásához. A rendszer felszámítja az adatátvitelt a partneri kapcsolatokon keresztül. [Lásd itt](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Titkosítva van-e a VNet-társítási forgalom?
Ha az Azure-forgalom átkerül az adatközpontok között (a Microsoft vagy a Microsoft nevében nem szabályozott fizikai határokon kívül), a [MACsec adatkapcsolati rétegének titkosítása](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) a mögöttes hálózati hardveren alapul.  Ez a VNet-társítási forgalomra vonatkozik.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Miért van a társam kapcsolata *leválasztott* állapotban?
A VNet-társítási kapcsolatok *leválasztott* állapotba kerülnek, ha töröl egy VNet-társítási hivatkozást. A sikeres társ-létesítési kapcsolat létrehozásához mindkét hivatkozást törölnie kell.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Ha a társam Társviszonyban áll a b és a VNetC, a Társviszonyban áll és a VNetC is, ez azt jelenti, hogy a b?
Nem. A tranzitív peering nem támogatott. Ehhez a társ-Társviszonyban áll és a VNetC kell elvégeznie.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Van-e sávszélességre vonatkozó korlátozás a társi kapcsolatok esetében?
Nem. A VNet-kezelés, akár a helyi, akár a globális, nem ró sávszélesség-korlátozást. A sávszélességet csak a virtuális gép vagy a számítási erőforrás korlátozza.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Hogyan tudom elhárítani a VNet-társítási problémákat?
Itt talál egy [útmutatót a hibakeresőhöz](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) .

## <a name="virtual-network-tap"></a>Virtuális hálózat TAP-jai

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Mely Azure-régiók érhetők el a virtuális hálózatok számára?
A Virtual Network TAP Preview minden Azure-régióban elérhető. A figyelt hálózati adapterek, a virtuális hálózat KOPPINTson az erőforrás lehetőségre, és a gyűjtő vagy elemzési megoldásnak ugyanabban a régióban kell lennie.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Virtual Network KOPPINTson a támogatott a tükrözött csomagok szűrési képességeinek támogatása lehetőségre?
A szűrési képességek nem támogatottak a virtuális hálózatban koppintson az Előnézet elemre. Ha egy KOPPINTó konfigurációt ad hozzá egy hálózati adapterhez, a hálózati adapteren lévő összes bejövő és kimenő forgalom részletes másolatát a rendszer a KOPPINTÁS célhelyére továbbítja.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Több KOPPINTó konfiguráció is hozzáadható egy figyelt hálózati adapterhez?
A figyelt hálózati adapternek csak egyetlen KOPPINTÁSi konfigurációja lehet. Egyeztessen az egyes [partneri megoldásokkal](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) , hogy képes legyen több példányban továbbítani a TAP-forgalmat az Ön által választott analitikai eszközökre.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Ugyanez a virtuális hálózat is KOPPINThat az erőforrások összesített forgalmára a figyelt hálózati adapterek közül több virtuális hálózaton?
Igen. Ugyanezen a virtuális hálózati KOPPINTÁSi erőforráson keresztül a tükrözött forgalom összesíthető a figyelt hálózati adapterekről az azonos előfizetésben vagy egy másik előfizetésben található, egymáshoz tartozó virtuális hálózatokban. A virtuális hálózat KOPPINTson az erőforrásra, és a cél terheléselosztó vagy a célként megadott hálózati adapternek ugyanahhoz az előfizetéshez kell tartoznia. Az összes előfizetésnek ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Vannak-e az éles forgalomra vonatkozó teljesítményi megfontolások, ha engedélyezem a virtuális hálózatok konfigurációját egy hálózati adapteren?

A virtuális hálózat KOPPINTÁS előzetes verzióban érhető el. Az előzetes verzió ideje alatt nincs szolgáltatói szerződés. A képesség nem használható éles számítási feladatokhoz. Ha egy virtuális gép hálózati adaptere egy KOPPINTÁSi konfigurációval van engedélyezve, ugyanazokat az erőforrásokat kell használni az Azure-gazdagépen, amelyet a virtuális géphez rendeltek, hogy elküldjék az éles forgalmat a tükrözési funkció végrehajtásához és a tükrözött csomagok elküldéséhez. A megfelelő [linuxos](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windowsos](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretének kiválasztásával biztosíthatja, hogy elegendő erőforrás álljon rendelkezésre ahhoz, hogy a virtuális gép el lehessen küldeni az üzemi forgalmat és a tükrözött forgalmat.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>A gyorsított hálózatkezelés [Linux](create-vm-accelerated-networking-cli.md) vagy [Windows rendszeren](create-vm-accelerated-networking-powershell.md) támogatott a Virtual Network-sel?

Hozzáadhat egy KOPPINTó konfigurációt egy olyan virtuális géphez csatlakoztatott hálózati adapterhez, amely a gyorsított hálózatkezeléssel van ellátva. A virtuális gép teljesítményét és késését azonban érinteni fogja a rendszer, mivel a rendszer a tükrözési forgalom kiszervezését jelenleg nem támogatja az Azure gyorsított hálózatkezeléssel.

## <a name="virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Mi a műveletek megfelelő sorozata egy Azure-szolgáltatáshoz tartozó szolgáltatási végpontok beállításához?
Az Azure-szolgáltatások erőforrásait két lépésben kell biztonságossá tenni a szolgáltatási végpontokon keresztül:
1. Kapcsolja be a szolgáltatási végpontokat az Azure szolgáltatáshoz.
2. VNet ACL-ek beállítása az Azure szolgáltatásban.

Az első lépés egy hálózati oldali művelet, a második lépés pedig egy szolgáltatás-erőforrás oldali művelet. Mindkét lépést elvégezheti ugyanazzal a rendszergazdával vagy a különböző rendszergazdákkal a rendszergazdai szerepkörhöz megadott Azure RBAC engedélyek alapján. Javasoljuk, hogy a VNet ACL-ek beállítása előtt először kapcsolja be a virtuális hálózat szolgáltatási végpontját. Ezért a lépéseket a fent felsorolt sorrendben kell végrehajtani a VNet szolgáltatás-végpontok beállításához.

>[!NOTE]
> A fent ismertetett műveleteket is el kell végeznie ahhoz, hogy korlátozni lehessen az Azure-szolgáltatás hozzáférését az engedélyezett VNet és alhálózathoz. Csak a hálózati oldalon lévő Azure-szolgáltatáshoz tartozó végpontok bekapcsolása nem biztosítja a korlátozott hozzáférést. Emellett a VNet ACL-eket is be kell állítania az Azure-szolgáltatási oldalon.

Bizonyos szolgáltatások (például az SQL és a CosmosDB) engedélyezik a kivételeket a fenti sorozatban a **IgnoreMissingVnetServiceEndpoint** jelzőn keresztül. Ha a jelző értéke TRUE ( **igaz**), a VNet ACL-ek az Azure-szolgáltatás oldalán állíthatók be a hálózati oldalon lévő szolgáltatási végpontok beállítása előtt. Az Azure-szolgáltatások biztosítják ezt a jelzőt arra az esetre, ha az adott IP-tűzfalak az Azure-szolgáltatásokon vannak konfigurálva, és a hálózati oldalon a szolgáltatási végpontok bekapcsolása a kapcsolat csökkenését eredményezi, mivel a forrás IP-cím a nyilvános IPv4-címről a magánhálózati címekre változik. A VNet ACL-ek beállítása az Azure-szolgáltatási oldalon a szolgáltatási végpontok hálózati oldalon való beállítása előtt elkerülheti a kapcsolatok eldobását.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Az Azure-szolgáltatások az ügyfél által biztosított Azure-beli virtuális hálózaton találhatók? Hogyan működik az VNet-szolgáltatás végpontja az Azure-szolgáltatásokkal?

Nem, nem minden Azure-szolgáltatás található az ügyfél virtuális hálózatában. Az Azure-beli adatszolgáltatások, például az Azure Storage, az Azure SQL és a Azure Cosmos DB többsége több-bérlős szolgáltatás, amely nyilvános IP-címeken keresztül érhető el. Az Azure [-szolgáltatások Virtual](virtual-network-for-azure-services.md)Network Integration szolgáltatásával kapcsolatos további információkért tekintse meg a következőt:. 

Ha a VNet szolgáltatás-végpontok funkciót használja (bekapcsolja a VNet szolgáltatás végpontját a hálózati oldalon, és beállítja a megfelelő VNet ACL-eket az Azure-szolgáltatás oldalán), az Azure-szolgáltatásokhoz való hozzáférés egy engedélyezett VNet és alhálózatra korlátozódik.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Hogyan biztosítja a VNet szolgáltatás végpontja a biztonságot?

Az VNet szolgáltatási végpont funkciója (a VNet szolgáltatás végpontjának bekapcsolása a hálózati oldalon, valamint a megfelelő VNet ACL-ek beállítása az Azure-szolgáltatás oldalán) korlátozza az Azure-szolgáltatás hozzáférését az engedélyezett VNet és alhálózathoz, így biztosítva a hálózati szintű biztonságot és az Azure-szolgáltatás forgalmának elkülönítését. A VNet szolgáltatás-végpontokat használó összes forgalom a Microsoft gerincén halad át, így biztosítva a nyilvános internetről egy másik elkülönítési réteget. Emellett az ügyfelek dönthetnek úgy, hogy teljes mértékben letiltják a nyilvános internet-hozzáférést az Azure-szolgáltatás erőforrásaihoz, és csak a virtuális hálózatról engedélyezik a forgalmat az IP-tűzfal és a VNet ACL-ek kombinációjával, így az Azure-szolgáltatások erőforrásainak jogosulatlan hozzáférés elleni védelme.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Mi a VNet Service Endpoint Protect-VNet-erőforrások vagy az Azure-szolgáltatás?
A VNet szolgáltatás-végpontok segítenek az Azure-szolgáltatások erőforrásainak védelmében. A VNet-erőforrások védelme hálózati biztonsági csoportokon keresztül történik (NSG).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Van a VNet-szolgáltatási végpontok használatának díja?

Nem, a VNet szolgáltatási végpontok használata nem jár további díjszabással.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Be lehet kapcsolni a VNet szolgáltatás-végpontokat, és be kell állítani a VNet hozzáférés-vezérlési listákat, ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak?

Igen, ez lehetséges. A virtuális hálózatok és az Azure-szolgáltatási erőforrások lehetnek azonos vagy eltérő előfizetésekben. Az egyetlen követelmény, hogy a virtuális hálózatnak és az Azure-szolgáltatások erőforrásainak ugyanahhoz a Active Directory (AD) bérlőhöz kell tartozniuk.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Be lehet kapcsolni a VNet szolgáltatási végpontokat, és be kell állítani a VNet ACL-eket, ha a virtuális hálózat és az Azure-szolgáltatás erőforrásai különböző AD-bérlők közé tartoznak?
Igen, az Azure Storage és a Azure Key Vault szolgáltatás-végpontok használata esetén is lehetséges. A többi szolgáltatás esetében az VNet szolgáltatási végpontok és a VNet ACL-ek nem támogatottak az AD-bérlők között.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>A helyszíni eszköz IP-címe az Azure Virtual Network Gateway (VPN) vagy a ExpressRoute Gateway használatával csatlakozik az Azure Pásti szolgáltatáshoz a VNet szolgáltatás végpontjai felett?
Alapértelmezés szerint a virtuális hálózatokhoz biztosított Azure-szolgáltatási erőforrások nem érhetők el helyszíni hálózatokról. Ha engedélyezni szeretné a forgalmat a helyszíni környezetből, engedélyeznie kell a nyilvános (jellemzően NAT) IP-címeket is a helyszíni vagy ExpressRoute. Ezek az IP-címek az Azure szolgáltatási erőforrásaihoz tartozó IP-tűzfal konfigurációján keresztül adhatók hozzá.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Használhatom a VNet szolgáltatás-végpont funkciót az Azure-szolgáltatás több alhálózatra történő biztonságossá tételéhez egy virtuális hálózaton vagy több virtuális hálózaton belül?
Ahhoz, hogy az Azure-szolgáltatások több alhálózatra is biztonságossá váljon egy virtuális hálózaton vagy több virtuális hálózaton belül, engedélyezze a szolgáltatási végpontokat az egyes alhálózatokon egymástól függetlenül, majd a megfelelő VNet ACL-ek beállításával gondoskodjon az Azure szolgáltatási erőforrásairól az összes alhálózathoz.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Hogyan szűrhetik a kimenő forgalmat egy virtuális hálózatról az Azure-szolgáltatásokra, és továbbra is használhatom a szolgáltatási végpontokat?
Ha egy virtuális hálózatról szeretné megvizsgálni vagy szűrni egy Azure-szolgáltatásra irányuló forgalmat, üzembe helyezhet egy hálózati virtuális berendezést a virtuális hálózaton belül. Ezután szolgáltatási végpontokat alkalmazhat arra az alhálózatra, ahol a hálózati virtuális berendezés üzembe lett helyezve, és az Azure-szolgáltatások erőforrásai csak erre az alhálózatra VNet ACL-eken keresztül is biztonságossá tehetők. Ez a forgatókönyv akkor is hasznos lehet, ha a virtuális hálózatról csak bizonyos Azure-erőforrásokra szeretné korlátozni az Azure-szolgáltatások elérését a hálózati virtuális berendezés szűrése használatával. További információkért lásd a [kimenő forgalommal és a hálózati virtuális berendezésekkel](/azure/architecture/reference-architectures/dmz/nva-ha) foglalkozó témakört.

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Mi történik, ha olyan Azure-szolgáltatásfiók elérésére kerül sor, amely a VNet kívülről engedélyezett virtuális hálózati hozzáférés-vezérlési listával (ACL) rendelkezik?
A rendszer a HTTP 403 vagy a HTTP 404 hibát adja vissza.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Különböző régiókban létrehozott virtuális hálózatok alhálózatai férhetnek hozzá egy másik régióhoz tartozó Azure-szolgáltatásfiók eléréséhez? 
Igen, a legtöbb Azure-szolgáltatás esetében a különböző régiókban létrehozott virtuális hálózatok hozzáférhetnek az Azure-szolgáltatásokhoz egy másik régióban az VNet szolgáltatás-végpontokon keresztül. Ha például egy Azure Cosmos DB-fiók az USA nyugati régiójában vagy az USA keleti régiójában található, és a virtuális hálózatok több régióban találhatók, akkor a virtuális hálózat hozzáférhet a Azure Cosmos DBhoz. A Storage és az SQL kivételeket jelent, és regionális jellegűek, és a virtuális hálózatnak és az Azure szolgáltatásnak ugyanabban a régióban kell lennie.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Lehet egy Azure-szolgáltatás VNet ACL-t és IP-tűzfalat is tartalmaz?
Igen, a VNet ACL és egy IP-tűzfal együtt is létezhet. Mindkét szolgáltatás kiegészíti egymást az elkülönítés és a biztonság biztosításához.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Mi történik, ha töröl egy olyan virtuális hálózatot vagy alhálózatot, amelyen be van kapcsolva a szolgáltatási végpont az Azure-szolgáltatáshoz?
A virtuális hálózatok és az alhálózatok törlése független művelet, és akkor is támogatott, ha a szolgáltatási végpontok be vannak kapcsolva az Azure-szolgáltatásokhoz. Azokban az esetekben, amikor az Azure-szolgáltatások VNet ACL-ek vannak beállítva, a virtuális hálózatok és az alhálózatok esetében az adott Azure-szolgáltatáshoz társított VNet ACL-információk le vannak tiltva, ha olyan VNet vagy alhálózatot törölnek, amelyen engedélyezve van a VNet szolgáltatás végpontja.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Mi történik, ha egy VNet szolgáltatás-végponttal rendelkező Azure-szolgáltatásfiók törölve lett?
Az Azure-szolgáltatásfiók törlése független művelet, és akkor is támogatott, ha a szolgáltatás végpontja engedélyezve van a hálózati oldalon, és a VNet ACL-ek az Azure szolgáltatás oldalán vannak beállítva. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Mi történik egy erőforrás forrás IP-címével (például egy alhálózatban lévő virtuális géppel), amelynek engedélyezve van a VNet Service Endpoint?
Ha a virtuális hálózati szolgáltatás végpontjai engedélyezve vannak, a virtuális hálózat alhálózatán lévő erőforrások forrás IP-címei az Azure-szolgáltatás felé irányuló forgalomhoz tartozó nyilvános IPV4-címeket használják az Azure Virtual Network magánhálózati IP-címeire. Vegye figyelembe, hogy ez olyan IP-tűzfalakat okozhat, amelyek az Azure-szolgáltatásokon korábban az Azure-szolgáltatások esetében a nyilvános IPV4-címekre vannak beállítva. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>A szolgáltatási végpont útvonala mindig elsőbbséget élvez?
A szolgáltatási végpontok olyan rendszerútvonalat vesznek fel, amely elsőbbséget élvez a BGP-útvonalakkal szemben, és optimális útválasztást biztosít a szolgáltatás végponti forgalmához A szolgáltatási végpontok mindig közvetlenül a virtuális hálózatról végzik a szolgáltatás forgalmát a Microsoft Azure gerinc hálózatán. További információ arról, hogy az Azure hogyan válasszon útvonalat: [Azure virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).

### <a name="do-service-endpoints-work-with-icmp"></a>Működnek a szolgáltatás-végpontok az ICMP protokollal?
Nem, az olyan alhálózatból származó ICMP-forgalom, amelyeken engedélyezve vannak a szolgáltatási végpontok, nem veszi át a szolgáltatás bújtatási útvonalát a kívánt végpontra. A szolgáltatási végpontok csak a TCP-forgalmat kezelik. Ez azt jelenti, hogy ha szolgáltatási végpontokon keresztül szeretné tesztelni a késést vagy a végponthoz való kapcsolódást, az olyan eszközök, mint a ping és a tracert nem jelenítik meg az alhálózaton belüli erőforrások valódi elérési útját.
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Hogyan működik az alhálózat NSG a szolgáltatási végpontokkal?
Az Azure-szolgáltatás eléréséhez NSG kell a kimenő kapcsolat használatát. Ha a NSG az összes internetes kimenő forgalomra megnyitják, akkor a szolgáltatási végpont forgalmának működnie kell. A kimenő forgalmat a szolgáltatási IP-címekre is korlátozhatja, csak a szolgáltatás címkéit használva.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Milyen engedélyek szükségesek a szolgáltatási végpontok beállításához?
A szolgáltatási végpontokat a virtuális hálózatokon a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználóktól függetlenül lehet konfigurálni. Az Azure-szolgáltatási erőforrások VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a **Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action** szolgáltatáshoz az alhálózatok hozzáadásához. Ez az engedély alapértelmezés szerint szerepel a beépített szolgáltatás-rendszergazdai szerepkörben, és egyéni szerepkörök létrehozásával módosítható. További információk a beépített szerepkörökről és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Szűrhetik-e a virtuális hálózati forgalmat az Azure-szolgáltatásokra, így csak bizonyos Azure-szolgáltatások erőforrásait VNet szolgáltatási végpontokon keresztül? 

A Virtual Network (VNet) szolgáltatás végponti házirendjei lehetővé teszik a virtuális hálózati forgalom szűrését az Azure-szolgáltatásokra, így csak bizonyos Azure-szolgáltatások erőforrásai jelennek meg a szolgáltatási végpontokon. A végponti szabályzatok részletes hozzáférés-vezérlést biztosítanak az Azure-szolgáltatások virtuális hálózati forgalmáról. A szolgáltatás-végponti szabályzatokról [itt](virtual-network-service-endpoint-policies-overview.md)talál további információt.

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Támogatja az Azure Active Directory (Azure AD) a VNet-szolgáltatási végpontok támogatását?

Azure Active Directory (Azure AD) nem támogatja natív módon a szolgáltatási végpontokat. [Itt](./virtual-network-service-endpoints-overview.md)megtekintheti az VNet szolgáltatási végpontokat támogató Azure-szolgáltatások teljes listáját. Vegye figyelembe, hogy a szolgáltatások támogatása szolgáltatási végpontok területen felsorolt "Microsoft. AzureActiveDirectory" címkét a rendszer az 1. generációs ADLS-végpontok támogatásához használja. Az 1. generációs ADLS-hez készült Virtual Network Integration for Azure Data Lake Storage Gen1 a virtuális hálózat és a Azure Active Directory (Azure AD) közötti virtuális hálózati szolgáltatás végpontjának biztonságát használja a hozzáférési jogkivonat további biztonsági jogcímeinek létrehozásához. Ezután e jogcímek használatával hitelesíti a virtuális hálózatot az 1. generációs Data Lake Storage-fiókkal, és engedélyezi a hozzáférést. További információ az [1. generációs VNet-integrációról Azure Data Lake Store](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Van-e korlátozás arra vonatkozóan, hogy hány VNet szolgáltatás-végpontot állíthatok be a VNet?
A virtuális hálózatok VNet-végpontok teljes száma nincs korlátozva. Az Azure-szolgáltatási erőforrások (például egy Azure Storage-fiók) esetében a szolgáltatások korlátozhatják az erőforrás biztosításához használt alhálózatok számát. A következő táblázat néhány példát mutat be: 

|Azure-szolgáltatás| VNet szabályok korlátai|
|---|---|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure Synapse Analytics|   128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store v1|  100|
 
>[!NOTE]
> A korlátok az Azure-szolgáltatás belátása szerint változnak. A szolgáltatások részleteiért tekintse meg a vonatkozó szolgáltatási dokumentációt. 




