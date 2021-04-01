---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerje meg a virtuális WAN-alapú, automatikusan méretezhető ág-ág kapcsolatot, az elérhető régiókat és a partnereket.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 641ed73f507d35d7af548d0164ef8e80979be217
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96530476"
---
# <a name="what-is-azure-virtual-wan"></a>Mi az Azure Virtual WAN?

Az Azure Virtual WAN egy hálózati szolgáltatás, amely számos hálózati, biztonsági és útválasztási funkciót biztosít, így egyetlen operatív felületet biztosíthat. Ezen funkciók közé tartoznak az ág-kapcsolat (a virtuális WAN-partneri eszközöktől, például az SD-WAN vagy a VPN CPE-től származó kapcsolat automatizálásán keresztül), helyek közötti VPN-kapcsolat, távoli felhasználói VPN (pont – hely) kapcsolat, magánhálózati (ExpressRoute) kapcsolat, felhőn belüli kapcsolat (virtuális hálózatok tranzitív kapcsolata), VPN-ExpressRoute közötti kapcsolat, útválasztás, Azure Firewall és titkosítás a magánhálózati kapcsolathoz. A virtuális WAN használatának megkezdéséhez nem szükséges az összes ilyen használati eset. Egyszerűen csak egy használati esettel kezdheti meg a használatát, majd a kialakulásának megfelelően módosíthatja a hálózatot.

A virtuális WAN-architektúra az ágak (VPN/SD-WAN-eszközök), a felhasználók (Azure VPN/OpenVPN/IKEv2-ügyfelek), a ExpressRoute-áramkörök és a virtuális hálózatok számára beépített méretezési és teljesítményű sugaras architektúra. Lehetővé teszi a [globális átviteli hálózati architektúra](virtual-wan-global-transit-network-architecture.md)használatát, ahol a felhőben üzemeltetett hálózat "hub" lehetővé teszi az olyan végpontok közötti tranzitív kapcsolódást, amelyek különböző típusú "küllők" között terjeszthetők.

Az Azure-régiók olyan hubokként szolgálnak, amelyekhez csatlakozhat. Az összes hub egy szabványos virtuális WAN-kapcsolaton keresztül csatlakozik a teljes hálóhoz, így a felhasználó könnyedén használhatja a Microsoft-gerincet bármilyen (küllős) kapcsolathoz. Az SD-WAN/VPN-eszközökkel való küllős kapcsolódás esetén a felhasználók manuálisan is be tudják állítani az Azure Virtual WAN-ban, vagy a virtuális WAN CPE (SD-WAN/VPN) partneri megoldás használatával állíthatnak be kapcsolatot az Azure-ral. A kapcsolat automatizálását támogató partnereink listáját (az eszköz adatainak az Azure-ba való exportálásának, az Azure-konfiguráció letöltésének és a kapcsolat létesítésének lehetőségét) az Azure Virtual WAN használatával találhatja meg. További információkért lásd a [Virtual WAN-partnerek és-helyszínek](virtual-wan-locations-partners.md) című cikket.

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Ez a cikk gyors áttekintést nyújt az Azure Virtual WAN hálózati kapcsolatáról. A Virtual WAN a következő előnyöket biztosítja:

* **Integrált csatlakozási megoldások a központban és küllő:** A helyek közötti konfiguráció és a helyszíni helyek és az Azure hub közötti kapcsolat automatizálása.
* **Automatikus küllőbeállítás és -konfiguráció:** A virtuális hálózatok és a számítási feladatok zökkenőmentes csatlakoztatása az Azure-elosztóhoz.
* **Intuitív hibaelhárítás:** Megtekintheti a végpontok közötti folyamatot az Azure-on belül, majd ezekkel az információkkal elvégezheti a szükséges műveleteket.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Alapszintű és standard virtuális WAN

A virtuális WAN-hálózatok két típusa létezik: alapszintű és standard. A következő táblázat az egyes típusok elérhető konfigurációit mutatja be.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

A virtuális WAN verziófrissítésének lépéseiért lásd: [virtuális WAN frissítése alapszintről standard verzióra](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Architektúra

A virtuális WAN architektúrával és a virtuális WAN-ra való áttelepítéssel kapcsolatos információkért tekintse meg a következő cikkeket:

* [Virtuális WAN-architektúra](migrate-from-hub-spoke-topology.md)
* [Globális átviteli hálózati architektúra](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-erőforrások

Végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehozni:

* **virtualWAN:** A virtualWAN erőforrás az Azure-hálózat egy virtuális átfedését jelképezi, amely különféle erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A virtuális WAN-kapcsolaton keresztüli virtuális hubok nem kommunikálnak egymással.

* **Elosztó:** A virtuális elosztó egy, a Microsoft által felügyelt virtuális hálózat. A hub különböző szolgáltatási végpontokat tartalmaz a kapcsolatok engedélyezéséhez. A helyszíni hálózatról (vpnsite) csatlakozhat egy VPN Gateway a virtuális központban, csatlakoztathatja a ExpressRoute-áramköröket egy virtuális központhoz, vagy akár a mobil felhasználókat is csatlakoztathatja egy pont – hely típusú átjáróhoz a virtuális központban. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. Ha például virtuális WAN-t használ, akkor nem hoz létre helyek közötti kapcsolatokat a helyszíni helyről közvetlenül a VNet. Ehelyett hozzon létre egy helyek közötti kapcsolódást a hubhoz. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül.

* **Elosztó virtuális hálózati kapcsolata:** Az elosztó virtuális hálózati kapcsolata erőforrás az elosztó a virtuális hálózathoz való zökkenőmentes kapcsolódását biztosítja.

* **Központ – központ kapcsolatok:** A hubok egy virtuális WAN-kapcsolaton keresztül csatlakoznak egymáshoz. Ez azt jelenti, hogy egy helyi hubhoz csatlakozó ág, felhasználó vagy VNet a csatlakoztatott hubok teljes rácsvonal-architektúrájának használatával kommunikálhat egy másik fiókteleppel vagy VNet. A virtuális hálózatok egy, a virtuális központon keresztül áthaladó hubhoz, valamint az virtuális hálózatok-n keresztül is csatlakoztathatja a hub – hub kapcsolati keretrendszer használatával.

* **Elosztó útválasztási táblázata:** Létrehozhat egy virtuális elosztási útvonalat, és alkalmazhatja az útvonalat a virtuális elosztó útválasztási táblázatára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

**További virtuális WAN-erőforrások**

* **Webhely:** Ez az erőforrás csak a helyek közötti kapcsolatokhoz használatos. A hely erőforrása **vpnsite**. Ez a helyszíni VPN-eszközt és a hozzá tartozó beállításokat jelenti. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Csatlakozási típusok

A virtuális WAN a következő típusú kapcsolatokat teszi lehetővé: helyek közötti VPN, felhasználói VPN (pont – hely) és ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Webhelyek közötti VPN-kapcsolatok

A helyek közötti IPsec-/IKE-(IKEv2-) kapcsolaton keresztül kapcsolódhat az Azure-beli erőforrásokhoz. További információ: [helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md). 

Az ilyen típusú kapcsolathoz VPN-eszköz vagy virtuális WAN-partner eszköz szükséges. A virtuális WAN-partnerek automatizálást biztosítanak a kapcsolódáshoz, amely lehetővé teszi az eszköz adatainak az Azure-ba való exportálását, az Azure-konfiguráció letöltését és az Azure-beli virtuális WAN-hubhoz való kapcsolat létesítését. Az elérhető partnerek és helyszínek listáját a [virtuális WAN-partnerek és-helyszínek](virtual-wan-locations-partners.md) című cikkben találja. Ha a VPN/SD-WAN-eszköz szolgáltatója nem szerepel az említett hivatkozásban, akkor a kapcsolat beállításához leegyszerűsítheti a helyek közötti [kapcsolat virtuális WAN használatával történő létrehozását](virtual-wan-site-to-site-portal.md) .

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Felhasználói VPN (pont – hely) kapcsolatok

Az Azure-beli erőforrásokhoz IPsec/IKE (IKEv2) vagy OpenVPN-kapcsolat használatával kapcsolódhat. Az ilyen típusú kapcsolathoz a VPN-ügyfél konfigurálására van szükség az ügyfélszámítógépen. További információ: [pont – hely kapcsolat létrehozása](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-kapcsolatok
A ExpressRoute lehetővé teszi a helyszíni hálózat csatlakoztatását az Azure-hoz egy privát kapcsolaton keresztül. A kapcsolat létrehozásával kapcsolatban lásd: [ExpressRoute-kapcsolat létrehozása virtuális WAN használatával](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>VNet kapcsolatok

Egy Azure-beli virtuális hálózatot csatlakoztathat egy virtuális hubhoz. További információ: [a VNet összekötése egy hubhoz](virtual-wan-site-to-site-portal.md#vnet).

### <a name="transit-connectivity"></a><a name="transit"></a>Átviteli kapcsolat

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Átviteli kapcsolat a virtuális hálózatok között

A virtuális WAN lehetővé teszi az átvitelt a virtuális hálózatok között. A virtuális hálózatok virtuális hálózati kapcsolaton keresztül csatlakozhat egy virtuális hubhoz. A **standard virtuális WAN** virtuális hálózatok közötti tranzit-kapcsolat engedélyezve van az útválasztók jelenléte esetén minden virtuális központban. Ez az útválasztó a virtuális központ első létrehozásakor jön létre.

Az útválasztó négy útválasztási állapottal rendelkezhet: kiépített, kiépítés, sikertelen vagy nincs. Az **útválasztási állapot** a Azure Portal található a virtuális központ lapra való navigálással.

* A **none** állapot azt jelzi, hogy a virtuális központ nem tudta kiépíteni az útválasztót. Ez akkor fordulhat *elő, ha* a virtuális WAN alaptípusú, vagy ha a virtuális hub üzembe helyezése az elérhető szolgáltatás előtt megtörtént.
* A **sikertelen** állapot azt jelzi, hogy hiba történt A példány létrehozásakor. Az útválasztó létrehozásához vagy alaphelyzetbe állításához keresse meg az **útválasztó alaphelyzetbe állítása** lehetőséget, és navigáljon a Azure Portal virtuális központ áttekintés lapjára.

Minden virtuális központ útválasztója legfeljebb 50 GB/s-os összesített átviteli sebességet támogat. A virtuális hálózati kapcsolatok közötti kapcsolat azt feltételezi, hogy az összes, egyetlen virtuális hubhoz csatlakozó virtuális hálózatok összesen 2000 virtuális gép számítási feladatát veszi figyelembe.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Átviteli kapcsolat VPN-és ExpressRoute között

A virtuális WAN lehetővé teszi a VPN-és a ExpressRoute közötti átviteli kapcsolatot. Ez azt jelenti, hogy a VPN-kapcsolattal rendelkező helyek vagy a távoli felhasználók kommunikálhatnak a ExpressRoute-kapcsolattal rendelkező webhelyekkel. A rendszer implicit feltételezést is feltételez, hogy az **ág – ág jelző** engedélyezve van, és a BGP a VPN-és a ExpressRoute-kapcsolatok esetében támogatott. Ez a jelző a Azure Portal Azure virtuális WAN-beállításai között található. Az összes útválasztási kezelést a virtuális központ útválasztója biztosítja, amely a virtuális hálózatok közötti átvitelt is lehetővé teszi.

### <a name="custom-routing"></a><a name="routing"></a>Egyéni Útválasztás

A Virtual WAN speciális útválasztási fejlesztéseket biztosít. Lehetőség van egyéni útválasztási táblázatok beállítására, a virtuális hálózati útválasztás optimalizálására az Útválasztás társításával és propagálásával, logikailag csoportosíthatja a táblákat címkékkel, és egyszerűbbé teheti a számos hálózati virtuális berendezés (NVA) vagy a megosztott szolgáltatások útválasztási forgatókönyveit.

### <a name="global-vnet-peering"></a><a name="global"></a>Globális VNet-társítás

A globális VNet-társítás egy mechanizmust biztosít két virtuális hálózatok különböző régiókban való összekapcsolásához. A virtuális WAN-ban a virtuális hálózati kapcsolatok virtuális hálózatok csatlakoznak a virtuális hubokhoz. A felhasználónak nem kell explicit módon beállítania a globális VNet-társítást. Az azonos régióban található virtuális központhoz csatlakoztatott virtuális hálózatok VNet. Egy másik régióban lévő virtuális központhoz csatlakoztatott virtuális hálózatok globális VNet-megterhelési díjat von maga után.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>ExpressRoute-forgalom titkosítása

Az Azure Virtual WAN lehetővé teszi a ExpressRoute-forgalom titkosítását. A technika titkosított átvitelt biztosít a helyszíni hálózatok és az Azure-beli virtuális hálózatok között a ExpressRoute keresztül, a nyilvános interneten keresztül vagy nyilvános IP-címek használata nélkül. További információ: [IPSec over ExpressRoute for Virtual WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Helyek

A tartózkodási helyről a [virtuális WAN-partnerek és-helyek](virtual-wan-locations-partners.md) című cikkben talál további információt.

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Útválasztási táblázatok az alapszintű és a standard szintű virtuális WAN-hoz

Az útválasztási táblák mostantól társítási és propagálási funkciókkal rendelkeznek. A már meglévő útválasztási táblázat olyan útválasztási táblázat, amely nem rendelkezik ezekkel a funkciókkal. Ha már meglévő útvonalak vannak a hub-útválasztásban, és az új képességeket szeretné használni, vegye figyelembe a következőket:

* **Standard szintű virtuális WAN-ügyfelek meglévő útvonalakkal a virtuális központban**: ha a Azure Portalben már meglévő útvonalak vannak az Útválasztás szakaszban, először törölnie kell őket, majd kísérletet kell készítenie az új útválasztási táblák létrehozásához (a Azure Portal-ben lévő hub Route Tables (útválasztási táblázatok) szakaszában is elérhetők. Javasoljuk, hogy a DELETE lépést a virtuális WAN összes hubok esetében végezze el.

* **Alapszintű virtuális WAN-ügyfelek meglévő útvonalakkal a virtuális központban**: ha a Azure Portalben már meglévő útvonalak vannak az Útválasztás szakaszban, először törölnie kell őket, majd **frissítenie** kell az alapszintű virtuális WAN-t a standard Virtual WAN-ra. Lásd: [virtuális WAN frissítése alapszintről standard verzióra](upgrade-virtual-wan.md). Javasoljuk, hogy a DELETE lépést a virtuális WAN összes hubok esetében végezze el.

## <a name="faq"></a><a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Mi újság?

Fizessen elő az RSS-hírcsatornára, és tekintse meg a virtuális WAN legújabb funkcióinak frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN) oldalon.

## <a name="next-steps"></a>Következő lépések

[Helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
