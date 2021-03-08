---
title: Gyakori kérdések – Azure ExpressRoute | Microsoft Docs
description: A ExpressRoute GYIK a támogatott Azure-szolgáltatásokkal, a díjszabással, az adatokkal, a kapcsolatokkal, az SLA-val, a szolgáltatókkal és a szolgáltatásokkal, valamint a sávszélességgel és további technikai
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/07/2021
ms.author: duau
ms.openlocfilehash: 59194f8d7b1783867ab1422597b361aa3a4a2a60
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449866"
---
# <a name="expressroute-faq"></a>ExpressRoute – Gyakori kérdések

## <a name="what-is-expressroute"></a>Mi az az ExpressRoute?

A ExpressRoute egy Azure-szolgáltatás, amely lehetővé teszi, hogy a Microsoft-adatközpontok és a helyszíni vagy egy közös elhelyezési létesítményben lévő infrastruktúra között hozzon létre privát kapcsolatokat. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, és nagyobb biztonságot, megbízhatóságot és sebességet biztosítanak, mint a szokásos kapcsolatok az interneten.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Milyen előnyökkel jár a ExpressRoute és a magánhálózati kapcsolatok használata?

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Magasabb szintű biztonságot, megbízhatóságot és sebességet biztosítanak, és az interneten keresztül jellemző kapcsolatokkal alacsonyabb és konzisztens késéssel rendelkeznek. Bizonyos esetekben a helyszíni eszközök és az Azure közötti adatátvitelhez ExpressRoute-kapcsolatok használata jelentős költséghatékonyságot eredményezhet.

### <a name="where-is-the-service-available"></a>Hol érhető el a szolgáltatás?

A szolgáltatás helyének és elérhetőségének megtekintéséhez tekintse meg a következő oldalt: [ExpressRoute Partners és Locations](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hogyan használhatom a ExpressRoute-t a Microsofthoz való kapcsolódáshoz, ha nem rendelkezem partnerekkel az egyik ExpressRoute-szolgáltatói partnerrel?

Kiválaszthat egy regionális fuvarozót és egy, a támogatott Exchange-szolgáltatói hellyel rendelkező tartományi Ethernet-kapcsolatot is. Ezt követően a Microsoftot a szolgáltató helye szerint használhatja. Tekintse át az [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) utolsó szakaszát, és ellenőrizze, hogy a szolgáltató megtalálható-e valamelyik Exchange-helyen. Ezután egy ExpressRoute-áramkört rendelhet a szolgáltatón keresztül az Azure-hoz való kapcsolódáshoz.

### <a name="how-much-does-expressroute-cost"></a>Mennyibe kerül a ExpressRoute?

A díjszabással kapcsolatos információk [megtekintéséhez](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>Ha fizetnem kell egy adott sávszélesség ExpressRoute, akkor a bejövő és a kimenő forgalomhoz külön-külön kell lefoglalni a sávszélességet?

Igen, a ExpressRoute áramköri sávszélesség duplex. Ha például egy 200 Mbps ExpressRoute áramkört vásárol, a kimenő forgalomért 200 Mbps-t, a kimenő forgalmat pedig 200 Mbps-ra vásárolhatja meg.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Ha fizetnem kell egy adott sávszélesség ExpressRoute áramkörének, akkor a saját hálózati szolgáltatótól vásárolt magánhálózati kapcsolatnak azonos sebességgel kell esnie?

Nem. A szolgáltatói sebességtől függetlenül is vásárolhat privát kapcsolatokat. Az Azure-hoz való kapcsolódás azonban a megvásárolt ExpressRoute-áramköri sávszélességre korlátozódik.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>Ha fizetnem kell egy adott sávszélesség ExpressRoute, akkor használhatom a beszerzett sávszélességet?

Igen, a ExpressRoute-áramkör másodlagos kapcsolatán elérhető sávszélességgel akár kétszer is felhasználhatja a sávszélesség-korlátot. Az áramkör beépített redundancia úgy van konfigurálva, hogy az elsődleges és másodlagos kapcsolatok, a megszerzett sávszélesség és a Microsoft vállalati peremhálózati útválasztói (Msee) használatával legyenek beállítva. Ha szükséges, a másodlagos kapcsolaton keresztül elérhető sávszélesség további forgalomhoz is használható. Mivel a másodlagos kapcsolatok a redundancia szempontjából jelentenek, azonban nem garantált, és nem használható tovább a hosszabb ideig tartó forgalomhoz. Ha többet szeretne megtudni arról, hogyan használható mindkét connnections a forgalom továbbítására, tekintse meg a [használat elérési utat](./expressroute-optimize-routing.md#solution-use-as-path-prepending)a következő helyen:.

Ha úgy tervezi, hogy csak az elsődleges kapcsolatát használja a forgalom továbbítására, a kapcsolat sávszélessége javítva lesz, és a rendszer a túlfizetést próbálja meg növelni. Ha a forgalom egy ExpressRoute-átjárón keresztül áramlik át, az átjáró SKU-jának sávszélessége rögzített, és nem használható fel. Az egyes átjárók SKU-jának sávszélességét lásd: [a ExpressRoute virtuális hálózati átjárók ismertetése](expressroute-about-virtual-network-gateways.md#aggthroughput).

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Használhatom ugyanazt a magánhálózati hálózati kapcsolatokat a virtuális hálózattal és más Azure-szolgáltatásokkal egyidejűleg?

Igen. A beállítás után egy ExpressRoute áramkör lehetővé teszi a virtuális hálózatokon és más Azure-szolgáltatásokon belüli szolgáltatások elérését. A virtuális hálózatokhoz a privát kapcsolati útvonalon és más szolgáltatásokon keresztül kapcsolódhat a Microsoft-partneri útvonalon keresztül.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hogyan hirdetik meg a virtuális hálózatok a ExpressRoute-alapú privát kiszolgálókon?

Az ExpressRoute-átjáró meghirdeti az Azure-VNet tartozó *címterület (ka)* t, az alhálózat szintjén nem szerepelhet/nem zárható ki. Mindig a meghirdetett VNet-címtartomány. Továbbá, ha a VNet-társítást használja, és a "távoli átjáró használata" beállítás engedélyezve van, a rendszer a meghirdetett VNet VNet is közzéteszi.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Hány előtagokat lehet meghirdetni egy VNet a helyszíni ExpressRoute-alapú privát partneri kapcsolaton?

Egyetlen ExpressRoute-kapcsolaton keresztül legfeljebb 200 előtagot lehet meghirdetni, vagy az átjáró-továbbítást használó VNet-társításon keresztül. Ha például a 199-es címtartomány egyetlen VNet van csatlakoztatva egy ExpressRoute-áramkörhöz, a rendszer az összes előtagok 199-os részét a helyszínen hirdeti meg. Ha van olyan VNet, amely lehetővé teszi, hogy az átjárók átvitele 1 címtartomány és 150 küllős virtuális hálózatok engedélyezve legyen a "távoli átjáró engedélyezése" lehetőség használatával, az átjáróval üzembe helyezett VNet a 151 előtagokat hirdeti meg a helyszínen.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Mi történik, ha túllépem a ExpressRoute-kapcsolatok előtag-korlátját?

A ExpressRoute áramkör és az átjáró (és az átjárót használó virtuális hálózatok, ha van ilyen) közötti kapcsolat leáll. A rendszer újra létrehozza, ha az előtag korlátja már nem lépi túl.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Szűrhetim a helyszíni hálózatról érkező útvonalakat?

Az útvonalak szűrésének és belefoglalásának egyetlen módja a helyszíni peremhálózati útválasztó. A felhasználó által megadott útvonalak hozzáadhatók a VNet az adott útválasztásra, de ez statikus, és nem része a BGP-hirdetménynek.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>A ExpressRoute kínál szolgáltatói szerződés (SLA)?

További információ: [EXPRESSROUTE SLA](https://azure.microsoft.com/support/legal/sla/) oldal.

## <a name="supported-services"></a>Támogatott szolgáltatások

A ExpressRoute [három útválasztási tartományt](expressroute-circuit-peerings.md) támogat a különböző típusú szolgáltatások esetében: privát társítás, Microsoft-társítás és nyilvános társ (elavult).

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

**Támogatott**

* Virtuális hálózatok, beleértve az összes virtuális gépet és a Cloud Servicest

### <a name="microsoft-peering"></a>Microsoftos társhálózat-létesítés

Ha a ExpressRoute-áramkör engedélyezve van az Azure Microsoft-partneri kapcsolathoz, az Azure-ban az áramkörön használt [nyilvános IP-címtartományok](../virtual-network/public-ip-addresses.md#public-ip-addresses) is elérhetők. Az Azure Microsoft-partnerek hozzáférést biztosítanak a jelenleg az Azure-ban üzemeltetett szolgáltatásokhoz (az áramköri SKU-tól függően földrajzi korlátozásokkal). Egy adott szolgáltatás rendelkezésre állásának ellenőrzéséhez ellenőrizheti a szolgáltatás dokumentációját, és ellenőrizheti, hogy van-e fenntartott tartomány közzétéve az adott szolgáltatás számára. Ezután keresse meg a cél szolgáltatás IP-tartományait, és hasonlítsa össze az [Azure IP-tartományok és szolgáltatások címkék – nyilvános felhő XML-fájljában](https://www.microsoft.com/download/details.aspx?id=56519)felsorolt tartományokkal. Azt is megteheti, hogy megnyit egy támogatási jegyet a kérdéses szolgáltatáshoz a tisztázás érdekében.

**Támogatott**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI az Azure regionális közösségén keresztül érhető el, [itt](/power-bi/service-admin-where-is-my-tenant-located) találhatja meg a Power bi bérlő régióját.
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure globális szolgáltatások közössége)
* Azure nyilvános IP-címek IaaS (Virtual Machines, Virtual Network átjárók, terheléselosztó stb.)  
* A többi Azure-szolgáltatás többsége is támogatott. Ellenőrizze, hogy van-e közvetlenül a támogatás ellenőrzéséhez használni kívánt szolgáltatás.

**Nem támogatott:**

* Tartalomkézbesítési hálózat (CDN)
* Azure Front Door
* [Windows virtuális asztal](https://azure.microsoft.com/services/virtual-desktop/)
* Multi-Factor Authentication kiszolgáló (örökölt)
* Traffic Manager

### <a name="public-peering"></a>Nyilvános társviszony-létesítés

A nyilvános társítás le lett tiltva az új ExpressRoute-áramkörökben. Az Azure-szolgáltatások mostantól elérhetők a Microsoft-partnerek számára. Ha olyan áramkört hoz létre, amely a nyilvános társítást megelőzően lett létrehozva, akkor a kívánt szolgáltatástól függően használhatja a Microsoft társközi vagy nyilvános társítását.

További információ és konfigurációs lépések a nyilvános társítással kapcsolatban: [ExpressRoute Public-peering](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Miért látom a "meghirdetett nyilvános előtagok" állapotát "érvényesítés szükséges" állapotra a Microsoft-társak konfigurálása során?

A Microsoft ellenőrzi, hogy a megadott "meghirdetett nyilvános előtagok" és "peer ASN" (vagy "Customer ASN") hozzá van-e rendelve az Internet Routing beállításjegyzékben. Ha egy másik entitásból kéri le a nyilvános előtagokat, és ha a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, akkor az automatikus ellenőrzés nem fejeződik be, és manuális ellenőrzésre van szükség. Ha az automatikus ellenőrzés meghiúsul, az "érvényesítés szükséges" üzenet jelenik meg.

Ha az "érvényesítés szükséges" üzenet jelenik meg, Gyűjtse össze azokat a dokumentumokat, amelyek megjelenítik a nyilvános előtagokat a szervezethez az útválasztási beállításjegyzékben szereplő előtagok tulajdonosának listáján, majd a dokumentumok manuális ellenőrzéshez való beküldéséhez az alább látható módon nyisson meg egy támogatási jegyet.

![Képernyőfelvétel: új támogatási kérés (támogatási jegy) "a nyilvános előtagok tulajdonjogának igazolása".](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Támogatott-e a Dynamics 365 a ExpressRoute-on?

A Dynamics 365-és Common Data Service-(CDS-) környezetek az Azure-ban futnak, ezért az ügyfelek az Azure-erőforrások mögöttes ExpressRoute-támogatás előnyeit élvezik. Ha az útválasztó szűrője tartalmazza azokat az Azure-régiókat, amelyeken a Dynamics 365/CDS-környezetek futnak, csatlakozhat a szolgáltatási végpontokhoz.

> [!NOTE]
> Az [ExpressRoute Premium](#expressroute-premium) **nem** szükséges a Dynamics 365-hez az Azure ExpressRoute-kapcsolaton keresztül, ha az ExpressRoute-áramkör ugyanazon a [geopolitikai régión](./expressroute-locations-providers.md#expressroute-locations)belül van üzembe helyezve.

## <a name="data-and-connections"></a>Az adatkezelés és a kapcsolatok

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Korlátozzák a ExpressRoute használatával átvihető adatok mennyiségét?

Nem állítunk be korlátot az adatátvitel mennyiségétől függően. A sávszélesség díjszabásával kapcsolatban tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Milyen ExpressRoute támogatja a kapcsolatok sebességét?

Támogatott sávszélesség-ajánlatok:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Mely szolgáltatók érhetők el?

Lásd: [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) a szolgáltatók és a helyszínek listájához.

## <a name="technical-details"></a>Technikai részletek

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Mik a helyszíni hely és az Azure közötti csatlakozás technikai követelményei?

A követelményekért tekintse meg a [ExpressRoute előfeltételek oldalát](expressroute-prerequisites.md) .

### <a name="are-connections-to-expressroute-redundant"></a>A ExpressRoute redundáns kapcsolatok vannak?

Igen. Minden ExpressRoute-áramkör redundáns, magas rendelkezésre állást biztosító kapcsolattal rendelkezik.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Megszakad a kapcsolat, ha az egyik ExpressRoute-hivatkozás meghiúsul?

Ha az egyik kapcsolat meghiúsul, nem fogja elveszíteni a kapcsolatot. Redundáns kapcsolat áll rendelkezésre a hálózat terhelésének támogatásához és a ExpressRoute-áramkör magas rendelkezésre állásának biztosításához. Emellett az áramköri szintű rugalmasság eléréséhez egy másik egyenrangú helyen is létrehozhat egy áramkört.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hogyan a redundancia megvalósítását a privát partnereken?

Több ExpressRoute-áramkör különböző, egymástól eltérő helyekről vagy akár négy kapcsolatból is csatlakozhat ugyanahhoz a virtuális hálózathoz, hogy magas rendelkezésre állást biztosítson abban az esetben, ha egyetlen áramkör elérhetetlenné válik. Ezután az egyik helyi kapcsolathoz is [hozzárendelhet nagyobb súlyokat](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection) egy adott áramkör előnyben részesítése érdekében. Erősen ajánlott, hogy az ügyfelek legalább két ExpressRoute-áramkört állítsanak be az egyes meghibásodási pontok elkerülése érdekében. 

[Itt](./designing-for-high-availability-with-expressroute.md) megtekintheti a magas rendelkezésre állást és [itt](./designing-for-disaster-recovery-with-expressroute-privatepeering.md) megtervezheti a vész-helyreállítást.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hogyan valósítható meg a redundancia a Microsoft-partnereken?

Erősen ajánlott, hogy ha az ügyfelek Microsoft-partneri kapcsolattal érik el az Azure-beli nyilvános szolgáltatásokat, például az Azure Storage vagy az Azure SQL szolgáltatást, valamint azokat az ügyfeleket, amelyek a Microsoft-partneri kapcsolatot használják a Microsoft 365, hogy az egyetlen meghibásodási pont elkerülése érdekében több áramkört implementálnak a különböző társi helyeken. Az ügyfelek megtehetik ugyanazt az előtagot mindkét áramkörön, és az [elérési út](./expressroute-optimize-routing.md#solution-use-as-path-prepending) alapján vagy különböző előtagokat is megadhatnak a helyi elérési út meghatározásához.

A magas rendelkezésre állás kialakításához [itt](./designing-for-high-availability-with-expressroute.md) talál további információt.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hogyan magas rendelkezésre állást biztosít a ExpressRoute-hez csatlakoztatott virtuális hálózatokon?

Magas rendelkezésre állást érhet el, ha akár négy ExpressRoute-áramkört csatlakoztat a virtuális hálózathoz, vagy ha a ExpressRoute-áramköröket különböző, egymással összekapcsoló (például Szingapúr, Szingapúr2) virtuális hálózathoz csatlakoztatja. Ha egy ExpressRoute-áramkör leáll, a kapcsolat feladatátvételt hajt végre egy másik ExpressRoute-áramkörön. Alapértelmezés szerint a virtuális hálózatot elhagyó forgalom irányítása egyenlő a több útvonalos útválasztás (ECMP) alapján. A kapcsolat súlyozásával az egyik áramkört használhatja egy másikhoz. További információ: az [ExpressRoute-útválasztás optimalizálása](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hogyan, hogy az Azure-beli nyilvános szolgáltatásokhoz, például az Azure Storage-hoz és az Azure SQL-hez a Microsoft társközi vagy nyilvános társ-kezeléshez szánt forgalma előnyben részesített a ExpressRoute útvonalon?

Meg kell valósítania az útválasztó (k) *helyi preferencia* attribútumát annak biztosításához, hogy a helyszínről az Azure-ba irányuló útvonal mindig a ExpressRoute-áramkör (ek) re legyen előnyben részesítve.

További részletek [itt](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings) találhatók a BGP-útvonal kiválasztása és az általános útválasztó-konfigurációk című részben. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Ha a felhőalapú Exchange-ben nem közös helyen vagyok, és a szolgáltató pont-pont típusú kapcsolatot biztosít, akkor két fizikai kapcsolatot kell rendelni a helyszíni hálózat és a Microsoft között?

Ha a szolgáltató két Ethernet virtuális áramkört tud létrehozni a fizikai kapcsolaton keresztül, akkor csak egy fizikai kapcsolatra van szükség. A fizikai kapcsolatok (például optikai Fiber) egy 1. rétegbeli (L1) eszközön megszakadnak (lásd a képet). A két Ethernet virtuális áramkör különböző VLAN-azonosítókkal van megjelölve, egyet az elsődleges áramkörhöz, egyet pedig a másodlagoshoz. Ezek a VLAN-azonosítók a külső 802.1 Q Ethernet-fejlécben találhatók. A belső 802.1 Q Ethernet-fejléc (nem látható) egy adott ExpressRoute- [útválasztási tartományra](expressroute-circuit-peerings.md)van leképezve.

![Diagram, amely kiemeli az 1. réteg (L1) elsődleges és másodlagos virtuális áramköröket, amelyek az ügyfél és a ExpressRoute közötti váltások közötti fizikai kapcsolatot alkotják.](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Ki tudom terjeszteni az egyik VLAN-t az Azure-ra az ExpressRoute használatával?

Nem. A 2. rétegbeli csatlakozási bővítmények nem támogatottak az Azure-ban.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Az előfizetésem több ExpressRoute-áramkörrel is rendelkezhet?

Igen. Az előfizetéshez több ExpressRoute áramkör is tartozhat. Az alapértelmezett korlát értéke 10. Ha szükséges, lépjen kapcsolatba Microsoft ügyfélszolgálata a korlát növeléséhez.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Használhatok ExpressRoute-áramköröket különböző szolgáltatóktól?

Igen. Több szolgáltatóval is rendelkezhet ExpressRoute áramkörökkel. Minden ExpressRoute-áramkör csak egy szolgáltatóhoz van társítva. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Két ExpressRoute-társítási helyet látok ugyanazon a metrón, például Szingapúrban és Szingapúr2. Melyik társítási helyet érdemes választani a ExpressRoute-áramkör létrehozásához?
Ha a szolgáltató mindkét helyen ExpressRoute kínál, akkor használhatja a szolgáltatót, és bármelyik helyet kiválaszthatja a ExpressRoute beállításához. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Használhatok több ExpressRoute-áramkört ugyanazon a metrón? Hivatkozhatok ugyanahhoz a virtuális hálózathoz?

Igen. Több ExpressRoute-áramkört is megadhat ugyanahhoz vagy más szolgáltatóhoz. Ha a metrón több ExpressRoute-társítási hely található, és az áramkör különböző helyen jön létre, akkor ugyanahhoz a virtuális hálózathoz kapcsolhatja őket. Ha az áramkör ugyanazon a helyen jön létre, akkor akár négy áramkört is csatolhat ugyanahhoz a virtuális hálózathoz.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>A virtuális hálózatok összekapcsolása egy ExpressRoute-áramkörrel Hogyan

Az alapszintű lépések a következők:

* Hozzon létre egy ExpressRoute áramkört, és engedélyezze azt a szolgáltatónál.
* Önnek vagy a szolgáltatónak konfigurálnia kell a BGP-társak (ka) t.
* A virtuális hálózat összekapcsolása a ExpressRoute áramkörrel.

További információ: [ExpressRoute-munkafolyamatok az áramkör kiépítés és az áramköri állapotokhoz](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Vannak kapcsolati határok az ExpressRoute-áramkörhöz?

Igen. A [ExpressRoute partnerek és helyszínek](expressroute-locations.md) című cikk áttekintést nyújt az ExpressRoute-áramkör kapcsolati határairól. A ExpressRoute áramkörhöz való kapcsolódás egyetlen geopolitikai régióra korlátozódik. A kapcsolat a ExpressRoute prémium funkció engedélyezésével bővíthető több geopolitikai régióra.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Több virtuális hálózathoz is hivatkozhatok egy ExpressRoute-áramkörhöz?

Igen. Akár 10 virtuális hálózati kapcsolattal is rendelkezhet standard ExpressRoute áramkörben, és akár 100-ig, prémium szintű [ExpressRoute-áramkörön](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Több Azure-előfizetésem is van, amelyek virtuális hálózatokat tartalmaznak. Összekapcsolhatok különálló előfizetésben lévő virtuális hálózatokat egyetlen ExpressRoute-áramkörre?

Igen. Akár 10 virtuális hálózatot is összekapcsolhat ugyanabban az előfizetésben, mint az áramkör vagy a különböző előfizetések egyetlen ExpressRoute áramkör használatával. Ezt a korlátot növelheti a ExpressRoute prémium funkciójának engedélyezésével. Vegye figyelembe, hogy a dedikált áramkör kapcsolati és sávszélességi díjai a ExpressRoute Circuit tulajdonosára lesznek alkalmazva. minden virtuális hálózat ugyanazt a sávszélességet használja.

További információ: ExpressRoute- [áramkör megosztása több előfizetés között](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Több Azure-előfizetésem van társítva különböző Azure Active Directory bérlőhöz vagy Nagyvállalati Szerződés regisztrációhoz. Csatlakozhatok különálló bérlőket és regisztrációkat tartalmazó virtuális hálózatokat egyetlen ExpressRoute-áramkörhöz sem ugyanabban a bérlőben vagy regisztrációban?

Igen. A ExpressRoute-engedélyek az előfizetések, a bérlők és a regisztrációs határok kiterjedhetnek, és nincs szükség további konfigurálásra. Vegye figyelembe, hogy a dedikált áramkör kapcsolati és sávszélességi díjai a ExpressRoute Circuit tulajdonosára lesznek alkalmazva. minden virtuális hálózat ugyanazt a sávszélességet használja.

További információ: ExpressRoute- [áramkör megosztása több előfizetés között](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Az azonos áramkörhöz csatlakoztatott virtuális hálózatok egymástól elszigeteltek?

Nem. Útválasztási szempontból az ugyanahhoz a ExpressRoute-áramkörhöz kapcsolódó összes virtuális hálózat ugyanahhoz az útválasztási tartományhoz tartozik, és nem különíthető el egymástól. Ha az útvonal elkülönítésére van szüksége, létre kell hoznia egy külön ExpressRoute áramkört.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Több ExpressRoute-áramkörhöz is kapcsolódhat egy virtuális hálózat?

Igen. Egyetlen virtuális hálózatot legfeljebb négy ExpressRoute-áramkörhöz kapcsolhat ugyanazon vagy különböző egyenrangú helyeken. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Hozzáférhetek az internethez a ExpressRoute-áramkörökhöz csatlakoztatott virtuális hálózatokról?

Igen. Ha nem hirdette meg az alapértelmezett útvonalakat (0.0.0.0/0) vagy az internetes útvonal előtagjait a BGP-munkameneten keresztül, csatlakozhat az internethez egy ExpressRoute-áramkörhöz kapcsolódó virtuális hálózatból.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Letilthatom az internetkapcsolatot a ExpressRoute-áramkörökhöz csatlakoztatott virtuális hálózatokkal?

Igen. Az alapértelmezett útvonalak (0.0.0.0/0) megadásával letilthatja az összes internetkapcsolatot a virtuális hálózaton belül üzembe helyezett virtuális gépekre, és átirányíthatja az összes forgalmat a ExpressRoute áramkörön keresztül.

> [!NOTE]
> Ha a 0.0.0.0/0 hirdetett útvonal a meghirdetett útvonalakról (például leállás vagy helytelen konfiguráció miatt) visszavonásra kerül, az Azure [rendszerútvonalat](../virtual-network/virtual-networks-udr-overview.md#system-routes) biztosít a csatlakoztatott Virtual Network erőforrásaihoz az internethez való kapcsolódás biztosításához.  Annak biztosítása érdekében, hogy a kimenő forgalom az internetre le legyen tiltva, ajánlott egy hálózati biztonsági csoportot elhelyezni az összes alhálózaton az internetes forgalomra vonatkozó kimenő megtagadási szabállyal.

Ha az alapértelmezett útvonalakat hirdeti meg, a Microsoft-partnereken (például az Azure Storage-ban és az SQL Database-ben) kínált szolgáltatásokra is kényszerítjük a forgalmat a helyszíni környezetbe. Az útválasztókat úgy kell konfigurálnia, hogy az Azure-ba irányuló forgalmat a Microsoft-partneri útvonalon vagy az interneten keresztül adja vissza. Ha engedélyezte a szolgáltatási végpontot a szolgáltatáshoz, a szolgáltatás felé irányuló forgalom nem kényszerül a telephelyére. A forgalom az Azure gerinc hálózatán belül marad. További információ a szolgáltatási végpontokról: [virtuális hálózati szolgáltatás végpontjai](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Az ugyanahhoz a ExpressRoute-áramkörhöz kapcsolódó virtuális hálózatok kommunikálnak egymással?

Igen. Az ugyanahhoz a ExpressRoute-áramkörhöz csatlakozó virtuális hálózatokban üzembe helyezett virtuális gépek kommunikálhatnak egymással. Javasoljuk, hogy a kommunikáció elősegítése érdekében a [virtuális hálózatok](../virtual-network/virtual-network-peering-overview.md) összevonását állítsa be.

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>Létrehozhatok helyek közötti VPN-kapcsolatokat a virtuális hálózathoz a ExpressRoute együtt?

Igen. A ExpressRoute a helyek közötti VPN-kapcsolatokkal együtt is létezhetnek. Lásd: [a ExpressRoute és a helyek közötti egyidejű kapcsolatok konfigurálása](expressroute-howto-coexist-resource-manager.md).

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Hogyan engedélyezi a helyek közötti VPN-kapcsolat és a ExpressRoute közötti útválasztást?

Ha engedélyezni szeretné a Expressoute-hez csatlakoztatott ág és a telephelyek közötti VPN-kapcsolathoz csatlakoztatott ág közötti útválasztást, be kell állítania az [Azure Route Servert](../route-server/expressroute-vpn-support.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Miért van társítva nyilvános IP-cím a ExpressRoute-átjáróhoz egy virtuális hálózaton?

A nyilvános IP-cím csak belső felügyeletre szolgál, és nem jelent biztonsági kockázatot a virtuális hálózatnak.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Korlátozva van a meghirdethető útvonalak száma?

Igen. A Microsoft-partneri kapcsolathoz legfeljebb 4000 útvonal 200-előtagokat fogadunk el. Ha engedélyezi a ExpressRoute prémium funkciót, a 10 000-es útvonalakat növelheti a privát partnerek számára.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Vannak korlátozások a BGP-munkamenetben meghirdethető IP-tartományokra?

Nem fogadunk el privát előtagokat (RFC1918) a Microsoft peering BGP-munkamenethez. A Microsofton és a privát társon is elfogadunk minden előtag-méretet (legfeljebb/32).

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Mi történik, ha túllépem a BGP-korlátokat?

A BGP-munkamenetek el lesznek dobva. A rendszer alaphelyzetbe állítja őket, ha az előtagok száma a határérték alá esik.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Mi a ExpressRoute BGP-tartási idő? Módosítható?

A tartási idő 180. A Keep-Alive üzenetek küldése minden 60 másodpercenként történik. Ezek olyan rögzített beállítások a Microsoft oldalon, amelyek nem módosíthatók. Lehetőség van különböző időzítők konfigurálására, és ennek megfelelően a BGP-munkamenet paramétereinek egyeztetésére is sor kerül.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Megváltoztathatom egy ExpressRoute áramkör sávszélességét?

Igen, megpróbálkozhat a ExpressRoute-áramkör sávszélességének növelésével a Azure Portalban vagy a PowerShell használatával. Ha rendelkezésre áll kapacitás azon a fizikai porton, amelyen az áramkör létrejött, a módosítás sikeres lesz. 

Ha a módosítás meghiúsul, az azt jelenti, hogy nincs elegendő kapacitás az aktuális porton, és létre kell hoznia egy új ExpressRoute áramkört a magasabb sávszélességgel, vagy ha nincs további kapacitás ezen a helyen, ebben az esetben nem fogja tudni megnövelni a sávszélességet. 

Emellett a kapcsolat szolgáltatójának kell követnie, hogy a sávszélesség-növekedés támogatásához frissítse a hálózatokon belüli szabályozásokat. Azonban nem csökkentheti a ExpressRoute áramkör sávszélességét. Létre kell hoznia egy új ExpressRoute-áramkört alacsonyabb sávszélességgel, és törölnie kell a régi áramkört.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hogyan módosítja egy ExpressRoute áramkör sávszélességét?

A ExpressRoute áramkör sávszélességét az REST API vagy a PowerShell-parancsmag használatával frissítheti.

## <a name="expressroute-premium"></a>Prémium szintű ExpressRoute

### <a name="what-is-expressroute-premium"></a>Mi a ExpressRoute Premium?

A ExpressRoute Premium a következő funkciók gyűjteménye:

* Megnövelt útválasztási táblázat a 4000 útvonalról 10 000 útvonalra a privát társak számára.
* Nagyobb számú virtuális hálózatok és ExpressRoute Global Reach kapcsolat, amely engedélyezhető egy ExpressRoute-áramkörön (az alapértelmezett érték 10). További információ: [ExpressRoute-korlátok](#limits) tábla.
* Kapcsolódás a Microsoft 365hoz
* Globális kapcsolat a Microsoft Core hálózaton keresztül. Most már összekapcsolhat egy VNet az egyik geopolitikai régióban egy másik régióban lévő ExpressRoute-áramkörrel.<br>
    **Példák:**

    *  Az Európa nyugati régiójában létrehozott VNet egy Szilícium-völgyben létrehozott ExpressRoute-áramkörhöz csatolhatja. 
    *  A Microsoft-társak a többi geopolitikai régió előtagjait is meghirdetik, így például az SQL Azure Nyugat-Európában, a Szilícium-völgyben található áramkörről.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Hány virtuális hálózatok-és Global Reach ExpressRoute-kapcsolat engedélyezhető a ExpressRoute-áramkörön, ha engedélyezve van a ExpressRoute Premium?

Az alábbi táblázatok a ExpressRoute korlátait, valamint a virtuális hálózatok és a Global Reach ExpressRoute számát mutatja a ExpressRoute-áramkörön belül:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hogyan az ExpressRoute Premium engedélyezése?

A ExpressRoute prémium funkciói engedélyezhető, ha a szolgáltatás engedélyezve van, és az áramkör állapotának frissítésével leállíthatók. A prémium szintű ExpressRoute az áramkör-létrehozási időben engedélyezheti, vagy meghívhatja a REST API/PowerShell-parancsmagot is.

### <a name="how-do-i-disable-expressroute-premium"></a>Hogyan letiltja a ExpressRoute Premiumot?

A ExpressRoute Premium letiltásához hívja meg a REST API vagy a PowerShell-parancsmagot. A ExpressRoute Premium letiltása előtt meg kell győződnie arról, hogy a kapcsolatnak meg kell felelnie az alapértelmezett határértékeknek. Ha a kihasználtsága az alapértelmezett határértékeken túlmutat, a ExpressRoute Premium letiltására irányuló kérelem sikertelen lesz.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kiválaszthatom, hogy milyen funkciókat szeretnék kiválasztani a prémium szintű szolgáltatáskészlet alapján?

Nem. A funkciók nem választhatók ki. A prémium szintű ExpressRoute bekapcsolásakor minden funkciót engedélyezünk.

### <a name="how-much-does-expressroute-premium-cost"></a>Mennyibe kerül a ExpressRoute díja?

Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>A standard szintű ExpressRoute díjak mellett fizetnem kell a ExpressRoute premiumért?

Igen. A prémium szintű ExpressRoute díjait a ExpressRoute áramköri díjakra és a kapcsolati szolgáltató által igényelt díjakra kell alkalmazni.

## <a name="expressroute-local"></a>Helyi ExpressRoute
### <a name="what-is-expressroute-local"></a>Mi az a ExpressRoute local?
A helyi ExpressRoute a standard SKU és a prémium SKU mellett a ExpressRoute áramkör SKU-jának. A helyi legfontosabb funkciója, hogy egy ExpressRoute-társítási helyen lévő helyi áramkör csak egy vagy két Azure-régióhoz fér hozzá, vagy közel azonos metróhoz. Ezzel szemben a standard szintű áramkör hozzáférést biztosít a geopolitikai területeken lévő összes Azure-régióhoz, valamint egy prémium szintű áramkört az összes Azure-régióhoz. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Milyen előnyökkel jár a ExpressRoute helyi?
A standard vagy prémium szintű ExpressRoute-áramkör esetében a kimenő adatforgalomért nem kell külön fizetnie a kimenő adatforgalomért a ExpressRoute helyi áramköre számára. Más szóval a ExpressRoute helyi díja adatátviteli díjakat is tartalmaz. A helyi ExpressRoute sokkal gazdaságosabb megoldás, ha nagy mennyiségű adat átvitelére van szükség, és az adatokhoz privát kapcsolaton keresztül kapcsolódhat a kívánt Azure-régiók közelében lévő ExpressRoute-partneri helyhez. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Milyen funkciók érhetők el, és mi nem a ExpressRoute helyi rendszeren?
A standard ExpressRoute áramkörhöz képest a helyi áramkör ugyanazokkal a szolgáltatásokkal rendelkezik, mint a következő:
* Az Azure-régiókhoz való hozzáférés hatóköre a fent leírtak szerint
* A ExpressRoute Global Reach helyi rendszeren nem érhető el

A helyi ExpressRoute is ugyanazok a korlátozások vonatkoznak az erőforrásokra (például a virtuális hálózatok száma) a standard értékre. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Hol van a ExpressRoute helyi elérhetősége, és mely Azure-régiók vannak leképezve az összes társítási helyhez?
A ExpressRoute local (helyi) lehetőség azon a társítási helyeken érhető el, ahol egy vagy két Azure-régiót zártak be. Nem érhető el olyan egyenrangú helyen, ahol az adott államban vagy régióban, illetve országban vagy régióban nem található Azure-régió. Tekintse meg a pontos leképezéseket a [helyszínek lapon](expressroute-locations-providers.md).  

## <a name="expressroute-for-microsoft-365"></a>Microsoft 365 ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Hogyan hozzon létre egy ExpressRoute-áramkört Microsoft 365-szolgáltatásokhoz való kapcsolódáshoz?

1. Tekintse át a [ExpressRoute előfeltételek oldalát](expressroute-prerequisites.md) , és győződjön meg arról, hogy megfelel a követelményeknek.
2. A kapcsolati igények teljesítésének biztosításához tekintse át a szolgáltatók és a helyszínek listáját a [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) című cikkben.
3. A kapacitásra vonatkozó követelmények megtervezéséhez tekintse át a [hálózati tervezést és a teljesítmény finomhangolását Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance).
4. A munkafolyamatokban felsorolt lépéseket követve állíthatja be a kapcsolati [ExpressRoute munkafolyamatait az áramköri kiépítési és az áramköri állapotokhoz](expressroute-workflows.md).

> [!IMPORTANT]
> Győződjön meg arról, hogy a ExpressRoute Premium bővítmény engedélyezve van a Microsoft 365 szolgáltatásokhoz való kapcsolódás konfigurálásakor.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>Támogatja a meglévő ExpressRoute-áramkörök a Microsoft 365 szolgáltatásokhoz való kapcsolódást?

Igen. A meglévő ExpressRoute-áramkör úgy konfigurálható, hogy támogassa a Microsoft 365 szolgáltatásokhoz való kapcsolódást. Győződjön meg arról, hogy elegendő kapacitása van a Microsoft 365 szolgáltatásokhoz való kapcsolódáshoz és a prémium szintű bővítmény engedélyezéséhez. A [Microsoft 365 hálózati tervezése és teljesítmény-finomhangolása](/microsoft-365/enterprise/network-planning-and-performance) megkönnyíti a kapcsolati igények megtervezését. Lásd még: [ExpressRoute-kör létrehozása és módosítása](expressroute-howto-circuit-classic.md).

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>Milyen Microsoft 365 szolgáltatások érhetők el egy ExpressRoute-kapcsolaton keresztül?

A ExpressRoute-on keresztül támogatott szolgáltatások naprakész listáját a [Microsoft 365 URL-címek és IP-címtartományok](/microsoft-365/enterprise/urls-and-ip-address-ranges) lapon találja.

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Mennyibe kerül a Microsoft 365 szolgáltatások ExpressRoute?

Microsoft 365-szolgáltatások esetében engedélyezni kell a prémium szintű bővítményt. A költségekért tekintse meg a [díjszabás részleteit ismertető oldalt](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>Milyen régiókat ExpressRoute a támogatott Microsoft 365?

További információ: [ExpressRoute-partnerek és-helyszínek](expressroute-locations.md) .

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Hozzáférhetek Microsoft 365 az interneten keresztül, még akkor is, ha a ExpressRoute konfigurálva lett a saját szervezetem számára?

Igen. Microsoft 365 szolgáltatási végpontok elérhetők az interneten keresztül, annak ellenére, hogy a ExpressRoute konfigurálva van a hálózathoz. Ha a tartózkodási hely hálózata úgy van konfigurálva, hogy a ExpressRoute keresztül kapcsolódjon Microsoft 365-szolgáltatásokhoz, ellenőrizze a szervezet hálózati csapatát.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Hogyan tervezhetem meg a magas rendelkezésre állást az Azure ExpressRoute Microsoft 365 hálózati forgalmához?
Tekintse meg a [magas rendelkezésre állásra és a feladatátvételre](/microsoft-365/enterprise/network-planning-with-expressroute) vonatkozó javaslatot az Azure ExpressRoute

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Hozzáférhetek az Office 365 amerikai kormányzati Közösség (GCC) szolgáltatásaihoz az Azure US government ExpressRoute áramkörén keresztül?

Igen. Az Office 365 GCC szolgáltatási végpontok elérhetők az Azure USA kormányzati ExpressRoute. Azonban először meg kell nyitnia egy támogatási jegyet a Azure Portalon, hogy megismertesse a Microsoft számára közzétenni kívánt előtagokat. Az Office 365 GCC-szolgáltatásokhoz való kapcsolódás a támogatási jegy feloldása után lesz létrehozva. 

## <a name="route-filters-for-microsoft-peering"></a>A Microsoft-társak útválasztási szűrői

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Először kapcsolom be a Microsoft-társat, milyen útvonalakat látok?

Egyetlen útvonal sem jelenik meg. Az előtag-hirdetmények indításához csatolni kell egy útvonal-szűrőt az áramkörhöz. Útmutatásért lásd: [útválasztási szűrők konfigurálása a Microsoft-partnerek](how-to-routefilter-powershell.md)számára.

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Bekapcsoltam a Microsoft-társat, és most megpróbálom kijelölni az Exchange Online-t, de a hiba miatt nem vagyok engedélyezve.

Az útválasztási szűrők használatakor bármely ügyfél bekapcsolhatja a Microsoft-partneri kapcsolatot. A Microsoft 365-szolgáltatások használatakor azonban továbbra is engedélyezni kell a Microsoft 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Engedélyeztem a Microsoft-társat a 2017-as augusztus 1. előtt, hogyan használhatom az útválasztási szűrők előnyeit?

A meglévő áramkör továbbra is meghirdeti a Microsoft 365 előtagjait. Ha az Azure-beli nyilvános előtagok hirdetményeit ugyanazon Microsoft-társon keresztül szeretné felvenni, akkor létrehozhat egy útvonal-szűrőt, kiválaszthatja a meghirdetett szolgáltatásokat (beleértve a szükséges Microsoft 365-szolgáltatás (oka) t), és csatolja a szűrőt a Microsoft-társához. Útmutatásért lásd: [útválasztási szűrők konfigurálása a Microsoft-partnerek](how-to-routefilter-powershell.md)számára.

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Van egy helyen a Microsoft-társam, most pedig megpróbálom engedélyezni egy másik helyen, és nem látok előtagokat.

* Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás előtagja lesz, még akkor is, ha az útvonal-szűrők nincsenek meghatározva.

* A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő. Alapértelmezés szerint nem jelenik meg előtag.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>Közvetlen ExpressRoute

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>Adatvédelem

### <a name="does-the-expressroute-service-store-customer-data"></a>Az ExpressRoute szolgáltatás tárolja az ügyféladatokat?

Nem.
