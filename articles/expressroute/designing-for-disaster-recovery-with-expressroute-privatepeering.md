---
title: 'Azure ExpressRoute: a vész-helyreállítás tervezése'
description: Ez az oldal a vész-helyreállítási architektúrával kapcsolatos javaslatokat tartalmaz az Azure ExpressRoute használata során.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202413"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Vészhelyzeti helyreállítás tervezése ExpressRoute-alapú privát partnerekkel

A ExpressRoute magas rendelkezésre állású, hogy a szolgáltatói szintű magánhálózati kapcsolatot biztosítson a Microsoft erőforrásaihoz. Más szóval, a Microsoft hálózaton belüli ExpressRoute útvonalon nem létezik egyetlen meghibásodási pont. A ExpressRoute áramkör rendelkezésre állásának maximalizálására szolgáló tervezési szempontokat lásd: [a magas rendelkezésre állás tervezése a ExpressRoute][HA].

Azonban a Murphy népszerű példabeszédének betartása –*Ha bármi rosszat tud,* ez a cikk figyelembe veszi, hogy olyan megoldásokra koncentrálunk, amelyek túlmutatnak az egyetlen ExpressRoute áramkörrel kapcsolatos hibákon. Más szóval, ebben a cikkben megvizsgáljuk a hálózati architektúra szempontjait a robusztus háttérbeli hálózati kapcsolat kiépítéséhez a vész-helyreállításhoz a Geo-redundáns ExpressRoute-áramkörök használatával.

>[!NOTE]
>Az ebben a cikkben ismertetett fogalmak akkor is érvényesek, ha egy ExpressRoute-áramkör virtuális WAN vagy azon kívül jön létre.
>

## <a name="need-for-redundant-connectivity-solution"></a>Redundáns csatlakozási megoldásra van szükség

Vannak olyan lehetőségek és példányok, ahol a teljes regionális szolgáltatás (a Microsoft, a hálózati szolgáltatók, az ügyfél vagy más felhőalapú szolgáltatók) csökkentett teljesítményű. Az ilyen regionális szolgáltatások hatásának alapvető oka a természetes szerencsétlenség. Ezért az üzletmenet folytonossága és a kritikus fontosságú alkalmazások esetében fontos, hogy megtervezze a vész-helyreállítási feladatait.   

Függetlenül attól, hogy az üzleti szempontból kritikus fontosságú alkalmazásokat egy Azure-régióban vagy a helyszínen, vagy bárhol máshol futtatja, egy másik Azure-régiót is használhat feladatátvételi helyként. A következő cikkek az alkalmazások és a előtér-hozzáférési perspektívák vész-helyreállítását tárgyalják:

- [Nagyvállalati szintű vészhelyreállítás][Enterprise DR]
- [SMB-vészhelyreállítás az Azure Site Recoveryvel][SMB DR]

Ha a helyszíni hálózat és a Microsoft közötti ExpressRoute kapcsolatot a kritikus fontosságú műveletekhez használja, a vész-helyreállítási tervnek tartalmaznia kell a Geo-redundáns hálózati kapcsolatot is. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Több ExpressRoute-áramkör használatának kihívásai

Ha ugyanazokat a hálózatokat több kapcsolattal is összekapcsolja, a hálózatok közötti párhuzamos útvonalakat is be kell vezetnie. A párhuzamos elérésű útvonalak, ha nincsenek megfelelően összeállítva, aszimmetrikus útválasztáshoz vezethetnek. Ha a PATH-ban állapot-nyilvántartó entitások (például NAT, tűzfal) vannak, akkor az aszimmetrikus útválasztás blokkolhatja a forgalom áramlását.  A ExpressRoute privát társítási útvonala általában nem az állapot-nyilvántartó entitásokon, például a NAT-ban vagy a tűzfalakon keresztül nem fog megjelenni. Ezért a ExpressRoute-alapú privát kapcsolaton keresztüli aszimmetrikus útválasztás nem feltétlenül blokkolja a forgalom áramlását.
 
Azonban, ha terheléselosztást végez a földrajzilag redundáns párhuzamos elérési utak között, függetlenül attól, hogy van-e állapot-nyilvántartó entitása, vagy sem, akkor inkonzisztens hálózati teljesítményt tapasztalhat. Ebből a cikkből megtudhatja, hogyan lehet kezelni ezeket a kihívásokat.

## <a name="small-to-medium-on-premises-network-considerations"></a>Kis-és közepes helyszíni hálózati megfontolások

Tekintsük át a példában szereplő hálózatot a következő ábrán. A példában a Geo-redundáns ExpressRoute kapcsolat létrejön a contoso helyszíni helye és a contoso VNet egy Azure-régióban. A diagramon a Solid zöld vonal az előnyben részesített útvonalat jelzi (az 1. ExpressRoute keresztül), a pontozott érték pedig a készenléti útvonalat jelöli (a ExpressRoute 2 használatával).

[![1]][1]

A vész-helyreállítási ExpressRoute-kapcsolat tervezésekor a következőket kell figyelembe vennie:

- a Geo-redundáns ExpressRoute áramkörök használata
- különféle szolgáltatói hálózat (ok) használata különböző ExpressRoute áramkörhöz
- a ExpressRoute áramkör megtervezése a [magas rendelkezésre állás][HA] érdekében
- a különböző ExpressRoute-áramkör leállítása az ügyfél hálózatának különböző helyén

Alapértelmezés szerint, ha az útvonalakat az összes ExpressRoute azonos módon hirdeti meg, az Azure az összes ExpressRoute-útvonalon terheléselosztást hajt végre a helyszíni, a ECMP-útválasztást használó útvonalon.

Azonban a Geo-redundáns ExpressRoute áramkörök esetében figyelembe kell venni a különböző hálózati elérési utakat (különösen a hálózati késés esetében). Ahhoz, hogy a normál működés során konzisztens hálózati teljesítményt kapjon, érdemes inkább a minimális késést biztosító ExpressRoute-áramkört használni.

Az Azure-t úgy befolyásolhatja, hogy egy ExpressRoute áramkört válasszon egy másikat a következő módszerek egyikének használatával (a hatékonyság sorrendjében látható):

- az előnyben részesített ExpressRoute áramkörhöz képest további speciális útvonal hirdetése a többi ExpressRoute-áramkörhöz képest
- a kapcsolat magasabb súlyozásának beállítása a virtuális hálózatot az előnyben részesített ExpressRoute-áramkörhöz összekötő kapcsolathoz
- az útvonalak közzététele a kevésbé előnyben részesített ExpressRoute-áramkörön, az elérési úttal meghaladva

### <a name="more-specific-route"></a>Konkrétabb útvonal

A következő ábra a ExpressRoute útvonal kijelölésének befolyásolását mutatja be, pontosabb útválasztási hirdetmény használatával. Az illusztrált példában a contoso helyszíni/24 IP-tartománya két/25 címtartományként van meghirdetve az előnyben részesített útvonalon (1. ExpressRoute) és/24 a készenléti úton (ExpressRoute 2) keresztül.

[![2]][2]

Mivel a/25 pontosabb, a/24-höz képest az Azure a 10.1.11.0/24 felé irányuló forgalmat a normál állapotú ExpressRoute 1 értékkel küldi el. Ha a ExpressRoute 1 kapcsolata is meghalad, akkor a VNet csak a 10.1.11.0/24 útvonal hirdetményét fogja látni, csak a ExpressRoute 2 használatával. Ezért a készenléti áramkör a meghibásodási állapotban is használatos.

### <a name="connection-weight"></a>Kapcsolatok súlyozása

Az alábbi képernyőképen az ExpressRoute-kapcsolatok súlyozásának konfigurálását mutatjuk be Azure Portalon keresztül.

[![3]][3]

A következő ábra a ExpressRoute útvonal kijelölésének a kapcsolatok súlyozásával való befolyásolását szemlélteti. Az alapértelmezett kapcsolatok súlya 0. Az alábbi példában az 1. ExpressRoute-hez való kapcsolatok súlya 100-ként van konfigurálva. Ha egy VNet egynél több ExpressRoute áramkörön keresztül meghirdetett útvonal-előtagot kap, a VNet előnyben részesíti a legnagyobb súllyal való kapcsolatot.

[![4]][4]

Ha a ExpressRoute 1 kapcsolata is meghalad, akkor a VNet csak a 10.1.11.0/24 útvonal hirdetményét fogja látni, csak a ExpressRoute 2 használatával. Ezért a készenléti áramkör a meghibásodási állapotban is használatos.

### <a name="as-path-prepend"></a>Elérési út

A következő ábra a ExpressRoute útvonal kijelölésének az AS Path előtaggal való használatának befolyásolását szemlélteti. A diagramon az útvonal-hirdetmény a ExpressRoute 1 értéknél a eBGP alapértelmezett viselkedését jelzi. Az útvonalon a 2. ExpressRoute-on keresztüli útválasztási hirdetményben a helyszíni hálózat ASN-je az útvonalon is előtagértéke. Ha ugyanazt az útvonalat több ExpressRoute-áramkör is fogadja, a eBGP Route kiválasztási folyamata során a VNet a legrövidebb elérési úttal rendelkező útvonalat részesíti előnyben. 

[![5]][5]

Ha a ExpressRoute 1 kapcsolata is meghalad, akkor a VNet csak a ExpressRoute 2 használatával fog megjelenni a 10.1.11.0/24 útvonal hirdetménye. Ennek következtében a hosszabb elérési út nem lesz releváns. Ezért a készenléti áramkört ebben a meghibásodási állapotban fogjuk használni.

Ha az Azure-t a különböző módszerek egyikével szeretné használni, akkor a helyszíni hálózatnak is előnyt kell biztosítania az Azure-beli kötött forgalomra vonatkozó ExpressRoute útvonalon, hogy elkerülje az aszimmetrikus folyamatokat. A helyi hálózat általában úgy van felhasználva, hogy a helyszíni hálózatot a többi ExpressRoute-áramkör előnyére használja. A helyi preferencia egy belső BGP-(iBGP-) metrika. A BGP útvonal a legmagasabb helyi preferencia értékkel van előnyben részesíteni.

> [!IMPORTANT]
> Ha bizonyos ExpressRoute-áramköröket készenléti állapotként használ, aktívan kell kezelnie azokat, és rendszeresen tesztelni kell a feladatátvételi műveletet. 
> 

## <a name="large-distributed-enterprise-network"></a>Nagyméretű elosztott vállalati hálózat

Ha nagyméretű elosztott vállalati hálózattal rendelkezik, valószínűleg több ExpressRoute-áramkört használ. Ebből a szakaszból megtudhatja, hogyan tervezheti meg a vész-helyreállítást az aktív-aktív ExpressRoute-áramkörökkel anélkül, hogy további készenléti áramköröket kellene használnia. 

Tekintsük át a következő ábrán látható példát. A példában a contoso két helyszíni hellyel rendelkezik két különböző Azure-régióban két különböző Azure-régióban, két különböző, egymástól eltérő helyen található ExpressRoute-IaaS. 

[![6]][6]

A vész-helyreállítási felépítésének módja hatással van arra, hogy a régiók közötti (terület1/region2-location2/location1-) forgalom hogyan legyen átirányítva. Vegyünk két különböző katasztrófa-architektúrát, amely eltérő módon irányítja a régiók közötti forgalmat.

### <a name="scenario-1"></a>1\. példa

Az első forgatókönyvben tervezze meg a vész-helyreállítást úgy, hogy az Azure-régió és a helyszíni hálózat közötti összes forgalom stabil állapotban legyen a helyi ExpressRoute áramkörön. Ha a helyi ExpressRoute áramkör meghibásodik, a rendszer a távoli ExpressRoute áramkört használja az Azure és a helyszíni hálózat közötti összes adatforgalomhoz.

Az 1. forgatókönyv az alábbi ábrán látható. A diagramon a zöld vonalak a VNet1 és a helyszíni hálózatok közötti forgalom elérési útját jelölik. A kék vonalak a VNet2 és a helyszíni hálózatok közötti forgalom elérési útját jelölik. A folytonos vonalak azt jelzik, hogy a kívánt elérési út állandósult állapotban van, a szaggatott vonalak pedig a megfelelő ExpressRoute-áramkör meghibásodása esetén az állandó állapotú forgalmat bonyolító forgalom elérési útját jelzik. 

[![7]][7]

Az virtuális hálózatok befolyásolhatja a forgatókönyvet a kapcsolódási súlyozás használatával, hogy a helyszíni hálózatra irányuló hálózati forgalomhoz való kapcsolódást inkább a helyi ExpressRoute lehessen használni. A megoldás befejezéséhez biztosítania kell a szimmetrikus fordított forgalom áramlását. A iBGP-munkamenetben helyi beállításokat használhat a BGP-útválasztók között (amelyeken a ExpressRoute-áramkörök a helyszíni oldalon vannak lezárva) a ExpressRoute-áramkör előnyben részesítése érdekében. A megoldást az alábbi ábra szemlélteti. 

[![8]][8]

### <a name="scenario-2"></a>2\. példa

A 2. forgatókönyv a következő ábrán látható. A diagramon a zöld vonalak a VNet1 és a helyszíni hálózatok közötti forgalom elérési útját jelölik. A kék vonalak a VNet2 és a helyszíni hálózatok közötti forgalom elérési útját jelölik. Az állandó állapotú (a diagramon található folytonos vonalakban) a virtuális hálózatok és a helyszíni helyszínek közötti forgalom a legtöbb esetben a Microsoft gerincen keresztül zajlik, és a helyszíni helyszínek közötti kapcsolaton keresztül zajlik a ExpressRoute.

[![9]][9]

A megoldást az alábbi ábra szemlélteti. Ahogy az ábrán látható, a forgatókönyvet a VNet útvonal kiválasztásának befolyásolására szolgáló, pontosabb útvonal (1. lehetőség) vagy az elérési út (2. lehetőség) használatával is elvégezheti. Az Azure-beli kötött forgalomra vonatkozó helyszíni hálózati útvonalak kiválasztásának befolyásolásához a helyszíni hely közötti összekapcsolást kevésbé előnyösebb módon kell konfigurálni. Howe az összekapcsolási hivatkozást az előnyben részesített konfigurációtól függően a helyszíni hálózaton használt útválasztási protokolltól függ. A iBGP vagy a metrika helyi beállításait a IGP (OSPF vagy a) használatával használhatja.

[![10]][10]


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben azt beszéltünk, hogyan lehet megtervezni egy ExpressRoute-áramkör magánhálózati kapcsolatának vész-helyreállítását. A következő cikkek az alkalmazások és a előtér-hozzáférési perspektívák vész-helyreállítását tárgyalják:

- [Nagyvállalati szintű vészhelyreállítás][Enterprise DR]
- [SMB-vészhelyreállítás az Azure Site Recoveryvel][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "kis-és közepes méretű helyszíni hálózati megfontolások"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "az elérési út kiválasztásának befolyásolása konkrétabb útvonalak használatával"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "a kapcsolatok súlyozásának beállítása Azure Portal használatával"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "az elérési út kiválasztásának befolyásolása a kapcsolatok súlyozásával"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "befolyásoló útvonal kiválasztása az as Path előtaggal"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "nagyméretű elosztott helyszíni hálózati megfontolás"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png. "forgatókönyv 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktív-aktív ExpressRoute-áramköri megoldás 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png. "forgatókönyv 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktív-aktív ExpressRoute-áramköri megoldás 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending