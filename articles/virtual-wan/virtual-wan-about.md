---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerje meg Virtual WAN az automatizált, skálázható, ágak közötti kapcsolatot, az elérhető régiókat és partnereket.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ab54192480a1c36a27c175254d6d4d275b67c8b7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835325"
---
# <a name="what-is-azure-virtual-wan"></a>Mi az Azure Virtual WAN?

Azure Virtual WAN egy hálózati szolgáltatás, amely számos hálózatkezelési, biztonsági és útválasztási funkciót kínál egyetlen működési felülethez. Ezek a funkciók közé tartozik az ágak közötti kapcsolat (az Virtual WAN Partnereszközök, például az SD-WAN vagy a VPN CPE kapcsolat automatizálásával), a helyek közötti VPN-kapcsolat, a távoli felhasználói VPN-kapcsolat (pont–hely) kapcsolat, a privát (ExpressRoute-) kapcsolat, a felhőn belüli kapcsolat (virtuális hálózatok tranzitív kapcsolata), a VPN ExpressRoute interkapcsolata, az útválasztás, az Azure Firewall és a privát kapcsolatok titkosítása. Nem kell az összes ilyen esetre szükség ahhoz, hogy használatba Virtual WAN. Egyszerűen csak egyetlen használattal kell kezdenie, majd a fejlődésével módosíthatja a hálózatot.

Az Virtual WAN architektúra egy küllős architektúra, amelybe be van építve az ágak (VPN/SD-WAN-eszközök), a felhasználók (Azure VPN/OpenVPN/IKEv2-ügyfelek), az ExpressRoute-kapcsolati körök és a virtuális hálózatok. Lehetővé teszi [a globális tranzit](virtual-wan-global-transit-network-architecture.md)hálózati architektúrát, ahol a felhőben üzemeltetett hálózat "hubja" tranzitív kapcsolatot tesz lehetővé a különböző típusú küllők között elosztott végpontok között.

Az Azure-régiók központként szolgálnak, amelyekhez csatlakozhat. Minden hub teljes hálóban van csatlakoztatva egy Standard Virtual WAN így a felhasználó könnyedén használhatja a Microsoft gerinchálózatát a bármely bármelyhez (bármely küllő) kapcsolatokhoz. Az SD-WAN/VPN-eszközökkel való küllőkapcsolatok esetében a felhasználók manuálisan állíthatják be azt az Azure Virtual WAN-ban, vagy az Virtual WAN CPE (SD-WAN/VPN) partnermegoldással állíthatják be az Azure-hoz való csatlakozást. Megjelenik a kapcsolatautomatizálást támogató partnerek listája (az eszközadatok Azure-ba való exportálásának képessége, az Azure-konfiguráció letöltése és a kapcsolat létrehozása) a Azure Virtual WAN. További információt a partnerek és [helyek Virtual WAN talál.](virtual-wan-locations-partners.md)

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Ez a cikk gyors áttekintést nyújt a hálózati kapcsolatokról a Azure Virtual WAN. A Virtual WAN a következő előnyöket biztosítja:

* **Integrált csatlakozási megoldások a küllős környezetben:** Automatizálhatja a helyek közötti konfigurációt és a kapcsolatot a helyszíni helyek és egy Azure-központ között.
* **Automatikus küllőbeállítás és -konfiguráció:** A virtuális hálózatok és a számítási feladatok zökkenőmentes csatlakoztatása az Azure-elosztóhoz.
* **Intuitív hibaelhárítás:** Az Azure-ban láthatja a végpontok között folyamatot, majd ezeket az információkat használhatja a szükséges műveletek éhez.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Alapszintű és Standard virtuális WAN-ok

A virtuális WAN-nak két típusa van: Alapszintű és Standard. Az alábbi táblázat az egyes típusokkal elérhető konfigurációkat mutatja be.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

A virtuális WAN frissítésének lépéseiért lásd: Virtuális WAN frissítése [alapszintűről Standardra.](upgrade-virtual-wan.md)

## <a name="architecture"></a><a name="architecture"></a>Architektúra

A Virtual WAN architektúrával és a Virtual WAN való áttelepítésével kapcsolatos információkért tekintse meg a következő cikkeket:

* [Virtual WAN architektúra](migrate-from-hub-spoke-topology.md)
* [Globális átviteli hálózati architektúra](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-erőforrások

Végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehozni:

* **virtualWAN:** A virtualWAN erőforrás az Azure-hálózat egy virtuális átfedését jelképezi, amely különféle erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A virtuális Virtual WAN nem kommunikálnak egymással.

* **Elosztó:** A virtuális elosztó egy, a Microsoft által felügyelt virtuális hálózat. A központ különböző szolgáltatásvégpontokat tartalmaz a kapcsolat engedélyezéséhez. A helyszíni hálózatról (vpnsite) csatlakozhat a virtuális központon belüli VPN Gateway-hoz, ExpressRoute-kapcsolatcsoportokat csatlakoztathat egy virtuális központhoz, vagy akár a mobilfelhasználókat egy pont–hely átjáróhoz is csatlakoztathatja a virtuális központban. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. A virtuális hálózat Virtual WAN például nem hoz létre hely–hely kapcsolatot a helyszíni oldalról közvetlenül a virtuális hálózatra. Ehelyett hozzon létre egy hely–hely kapcsolatot a központtal. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül.

* **Elosztó virtuális hálózati kapcsolata:** Az elosztó virtuális hálózati kapcsolata erőforrás az elosztó a virtuális hálózathoz való zökkenőmentes kapcsolódását biztosítja.

* **Hub–hub kapcsolat:** A hubok mind kapcsolódnak egymáshoz egy virtuális WAN-ban. Ez azt jelenti, hogy egy helyi központhoz csatlakoztatott ág, felhasználó vagy virtuális hálózat képes kommunikálni egy másik ággal vagy virtuális hálózattal a csatlakoztatott központok teljes hálóarchitektúrája segítségével. A központon belüli virtuális hálózatokat a virtuális központon áthaladó virtuális hálózatokhoz, valamint a központok közötti virtuális hálózatokhoz is csatlakoztathatja a központhoz csatlakoztatott keretrendszer használatával.

* **Elosztó útválasztási táblázata:** Létrehozhat egy virtuális elosztási útvonalat, és alkalmazhatja az útvonalat a virtuális elosztó útválasztási táblázatára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

**További Virtual WAN erőforrások**

* **Hely:** Ez az erőforrás csak a hely–hely kapcsolatokhoz használható. A helyerőforrás a **vpnsite.** Ez a helyszíni VPN-eszközt és annak beállításait jelöli. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Kapcsolattípusok

Virtual WAN kapcsolatok a következő típusú kapcsolatokat teszik lehetővé: a hely–hely VPN, a felhasználói VPN (pont–hely) és az ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Webhelyek közötti VPN-kapcsolatok

Az Azure-beli erőforrásokhoz hely–hely IPsec/IKE (IKEv2) kapcsolaton keresztül csatlakozhat. További információ: [Create a site-to-site connection using Virtual WAN.](virtual-wan-site-to-site-portal.md) 

Az ilyen típusú kapcsolathoz VPN-eszközre vagy Virtual WAN partnereszközre van szükség. Virtual WAN-partnerek automatizálást biztosítanak a kapcsolathoz, amely lehetővé teszi az eszközadatok Azure-ba történő exportálását, az Azure-konfiguráció letöltését és az Azure Virtual WAN kapcsolat létesítét. Az elérhető partnerek és helyek listáját a partnereket és [helyeket Virtual WAN és helyeket.](virtual-wan-locations-partners.md) Ha a VPN-/SD-WAN-eszközszolgáltatója nem szerepel az említett hivatkozáson, akkor a kapcsolat beállítására használhatja [a "Hely–hely](virtual-wan-site-to-site-portal.md) kapcsolat létrehozása a Virtual WAN használatával" részletes útmutatót.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Felhasználói VPN-kapcsolatok (pont–hely)

Az Azure-beli erőforrásaihoz IPsec/IKE (IKEv2) vagy OpenVPN kapcsolaton keresztül csatlakozhat. Az ilyen típusú kapcsolathoz konfigurálni kell egy VPN-ügyfelet az ügyfélszámítógépen. További információ: [Pont–hely kapcsolat létrehozása.](virtual-wan-point-to-site-portal.md)

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-kapcsolatok
Az ExpressRoute lehetővé teszi a helyszíni hálózat privát kapcsolaton keresztüli csatlakoztatását az Azure-hoz. A kapcsolat létrehozásához lásd: [ExpressRoute-kapcsolat létrehozása](virtual-wan-expressroute-portal.md)az Virtual WAN.

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Hub–VNet kapcsolatok

Azure-beli virtuális hálózatot csatlakoztathat egy virtuális központhoz. További információ: [A virtuális hálózat csatlakoztatása egy központhoz.](virtual-wan-site-to-site-portal.md#vnet)

### <a name="transit-connectivity"></a><a name="transit"></a>Tranzit kapcsolat

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Virtuális hálózatok közötti tranzit kapcsolat

Virtual WAN lehetővé teszi a virtuális hálózatok közötti tranzit kapcsolatot. A virtuális hálózatok virtuális hálózati kapcsolaton keresztül csatlakoznak egy virtuális központhoz. A Standard virtuális hálózatok virtuális hálózatai közötti Virtual WAN **engedélyezve** van, mert minden virtuális központban jelen van egy útválasztó. Ez az útválasztó a virtuális központ első létrehozásakor példányosul.

Az útválasztónak négy útválasztási állapota lehet: Kiépítve, Kiépítés, Sikertelen vagy Nincs. Az **Útválasztás állapota** a virtuális központ Azure Portal található.

* A **Nincs** állapot azt jelzi, hogy a virtuális központ nem létesíti ki az útválasztót. Ez akkor fordulhat elő, Virtual WAN alapszintű *,* vagy ha a virtuális központot még a szolgáltatás elérhetővé helyezése előtt telepítették.
* A **Sikertelen állapot** a példányosítás során hibát jelez. Az útválasztó példányosul vagy alaphelyzetbe állításához  keresse meg az Útválasztó alaphelyzetbe állítása lehetőséget, ha megnyitja a virtuális központ Áttekintés lapját a Azure Portal.

Minden virtuális elosztó útválasztója legfeljebb 50 Gbit/s összesített átviteli sebességet támogat. A virtuális hálózati kapcsolatok közötti kapcsolat összesen 2000 virtuálisgép-számítási feladatot feltételez az egyetlen virtuális központhoz csatlakoztatott összes virtuális hálózaton.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Tranzit kapcsolat a VPN és az ExpressRoute között

Virtual WAN lehetővé teszi a VPN és az ExpressRoute közötti tranzit kapcsolatot. Ez azt jelenti, hogy a VPN-hez csatlakoztatott helyek vagy a távoli felhasználók kommunikálhatnak az ExpressRoute-kapcsolattal rendelkező helyekkel. Az is implicit feltételezés, hogy az ágak közötti jelző engedélyezve van, és a BGP támogatott a **VPN-** és ExpressRoute-kapcsolatokban. Ez a jelző az alkalmazás Azure Virtual WAN található Azure Portal. Minden útvonalkezelést a virtuális központ útválasztója biztosít, amely a virtuális hálózatok közötti tranzit kapcsolatot is lehetővé teszi.

### <a name="custom-routing"></a><a name="routing"></a>Egyéni útválasztás

Virtual WAN speciális útválasztási fejlesztéseket biztosít. Lehetőség az egyéni útvonaltáblák beállítására, a virtuális hálózatok útválasztásának útvonal-társítással és -propagálással való optimalizálására, az útvonaltáblák címkékkel való logikai csoportosítására, valamint számos hálózati virtuális berendezés (NVA) vagy megosztott szolgáltatások útválasztási forgatókönyvének egyszerűsítésére.

### <a name="global-vnet-peering"></a><a name="global"></a>Globális virtuális hálózatok közötti társviszony

A globális virtuális hálózatok közötti társviszony-létesítés lehetővé teszi két különböző régióban található virtuális hálózat csatlakoztatását. Ebben Virtual WAN virtuális hálózati kapcsolatok virtuális hálózatokat csatlakoztatnak a virtuális központokhoz. A felhasználónak nem kell explicit módon beállítania a globális virtuális hálózatok közötti társviszonyt. Az azonos régióban lévő virtuális központhoz csatlakoztatott virtuális hálózatok virtuális hálózatok közötti társviszony-létesítésért díjat kell fizetni. A másik régióban lévő virtuális központhoz csatlakoztatott virtuális hálózatokért globális virtuális társhálózat-létesítésért kell díjat fizetni.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>ExpressRoute-forgalom titkosítása

Azure Virtual WAN ExpressRoute-forgalmat titkosíthatja. A technika titkosított átvitelt biztosít a helyszíni hálózatok és az Azure-beli virtuális hálózatok között az ExpressRoute-on keresztül anélkül, hogy a nyilvános interneten keresztül vagy nyilvános IP-címeket használná. További információ: [IPsec over ExpressRoute for Virtual WAN.](vpn-over-expressroute.md)

## <a name="locations"></a><a name="locations"></a>Helyek

Helyinformációkért tekintse meg a partnerek [Virtual WAN helyekkel kapcsolatos cikket.](virtual-wan-locations-partners.md)

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Útválasztási táblázatok alapszintű és standard virtuális WAN-okhoz

Az útvonaltáblák mostantól társítási és propagálási funkciókkal is rendelkezik. A már meglévő útvonaltábla olyan útvonaltábla, amely nem rendelkezik ezekkel a funkciókkal. Ha már meglévő útvonalakkal rendelkezik a központi útválasztásban, és az új képességeket szeretné használni, vegye figyelembe a következőket:

* **Standard** Virtual WAN-ügyfelek, akik már meglévő útvonalakkal vannak a virtuális központban: Ha az Azure Portal-központ Útválasztás szakaszában már meglévő útvonalakkal is vannak, először törölnie kell őket, majd meg kell kísérelnünk új útvonaltáblákat létrehozni (amelyek a központ Útvonaltáblák szakaszában érhetők el a Azure Portal). Javasoljuk, hogy a törlési lépést az összes központon egy Virtual WAN.

* **Alapszintű** Virtual WAN-ügyfelek, akik már meglévő útvonalakkal vannak a virtuális központban: Ha az Azure Portal-központ Útválasztás szakaszában már vannak útvonalak, először törölnie kell őket, majd frissítenie kell az Alapszintű Virtual WAN-t Standard Virtual WAN.  Lásd: [Virtuális WAN frissítése alapszintűről Standardra.](upgrade-virtual-wan.md) Javasoljuk, hogy a törlési lépést minden központon egy Virtual WAN.

## <a name="faq"></a><a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Mi újság?

Iratkozzon fel az RSS-hírcsatornára, és tekintse meg a Virtual WAN funkciófrissítéseit az [Azure-frissítések oldalon.](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN)

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy hely–hely kapcsolatot a Virtual WAN](virtual-wan-site-to-site-portal.md)
