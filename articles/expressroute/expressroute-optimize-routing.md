---
title: 'Azure ExpressRoute: Útválasztás optimalizálása'
description: Ez az oldal részletesen ismerteti, hogyan optimalizálható az útválasztás, ha több olyan ExpressRoute-kapcsolatcsoporttal rendelkezik, amely összeköti a Microsoftot a vállalati hálózatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/11/2019
ms.author: duau
ms.openlocfilehash: f35f1d390762d3f83176d7b36db8959dc5ed0157
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204877"
---
# <a name="optimize-expressroute-routing"></a>Az ExpressRoute-útválasztás optimalizálása
Ha több ExpressRoute-kapcsolatcsoporttal rendelkezik, több útvonalon csatlakozhat a Microsofthoz. Ennek eredményeképpen előfordulhat, hogy az útválasztás nem lesz optimális – azaz a forgalom hosszabb úton jut el a Microsofthoz, illetve a Microsofttól az Ön hálózatába. Minél hosszabb a hálózati útvonal, annál nagyobb a késés. A késés közvetlen hatással van az alkalmazások teljesítményére és a felhasználói élményre. Ez a cikk ezt a problémát mutatja be, és ismerteti, hogyan optimalizálható az útválasztás a standard útválasztási technológiák segítségével.

## <a name="path-selection-on-microsoft-and-public-peerings"></a>Elérési út kiválasztása a Microsoft és a nyilvános hálózatokon
Fontos annak biztosítása, hogy a Microsoft vagy a nyilvános hálózat használatakor a forgalom a kívánt útvonalon haladjon át, ha egy vagy több ExpressRoute-áramkört használ, valamint internetes Exchange (IX) vagy internetszolgáltató (ISP) használatával az internetre mutató útvonalakat. A BGP a legjobb útvonal-kiválasztási algoritmust használja számos tényező alapján, beleértve a leghosszabb előtag-egyezést (LPM). Annak biztosítása érdekében, hogy az Azure-ra irányuló forgalom a Microsofton vagy a nyilvános közvetítésen keresztül haladjon át a ExpressRoute útvonalon, az ügyfeleknek a *helyi preferencia* attribútumot kell alkalmazniuk ahhoz, hogy az elérési út mindig előnyben részesített a ExpressRoute. 

> [!NOTE]
> Az alapértelmezett helyi beállítás általában 100. A magasabb helyi beállítások előnyben részesítettek. 
>
>

Vegye figyelembe a következő példát:

![Diagram, amely a ExpressRoute 1. probléma – az optimálisnál rosszabb útválasztást jeleníti meg az ügyféltől a Microsoft felé](./media/expressroute-optimize-routing/expressroute-localPreference.png)

A fenti példában a ExpressRoute elérési utak előnyben részesítése a következő módon konfigurálja a helyi beállításokat. 

**Cisco IOS-XE konfiguráció R1 perspektívából:**

- R1 (konfiguráció) #route-map előnyben részesített – ExR engedélyezése 10
- R1 (konfiguráció-útvonal-térkép) #set local-preferencia 150

- R1 (konfiguráció) #router BGP 345
- R1 (config-router) #neighbor 1.1.1.2 távoli – 12076
- R1 (config-router) #neighbor 1.1.1.2 aktiválása
- R1 (config-router) #neighbor 1.1.1.2 útvonal – Térkép preferálása – ExR a következőben:

**Junos konfiguráció R1 perspektívából:**

- user@R1# set protokollok BGP csoport ibgp típus belső
- user@R1# set protokollok BGP csoport ibgp local-preferencia 150



## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Optimálisnál rosszabb útválasztás az ügyféltől a Microsoft felé
Vizsgáljuk meg az útválasztási problémát egy példán keresztül. Tegyük fel, hogy van két irodája az Egyesült Államokban, egy Los Angelesben és egy New Yorkban. Az irodák egy nagykiterjedésű hálózaton (WAN) keresztül csatlakoznak, amely lehet a saját gerinchálózata vagy a szolgáltatója IP-alapú VPN hálózata. Két ExpressRoute-kapcsolatcsoporttal rendelkezik, egy az USA nyugati régiójában és egy az USA keleti régiójában, amelyek szintén a WAN hálózathoz csatlakoznak. Nyilvánvaló, hogy két útvonalon keresztül csatlakozhat a Microsoft hálózatához. Most tegyük fel, hogy az USA nyugati régiójában és az USA keleti régiójában is rendelkezik egy Azure üzemelő példánnyal (például az Azure App Service-szel). Az a szándéka, hogy a Los Angeles-i felhasználóit az USA nyugati Azure-régiójához, a New York-i felhasználóit pedig az USA keleti Azure-régiójához csatlakoztassa, mivel a szolgáltatás-rendszergazdája azt hirdeti az egyes irodákban lévő felhasználóknak, hogy a közelebbi Azure szolgáltatáshoz férjenek hozzá az optimális élmény érdekében. Sajnos ez a terv csak a keleti parti felhasználók esetében működik jól, a nyugati parti felhasználók esetében nem. A probléma oka a következő. Mindkét ExpressRoute-kapcsolatcsoporton meghirdetjük az USA keleti Azure-régiójához tartozó előtagot (23.100.0.0/16) és az USA nyugati Azure-régiójához tartozó előtagot (13.100.0.0/16) is. Ha nem tudja, hogy melyik előtag melyik régióból származik, nem tudja különböző módon kezelni őket. A WAN hálózat azt hiheti, hogy mindkét előtag közelebb van az USA keleti régiójához, mint az USA nyugati régiójához, és ezért mindkét iroda felhasználóit az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoporthoz irányíthatja. Ennek eredménye, hogy sok elégedetlen felhasználó lesz a Los Angeles-i irodában.

![1. ExpressRoute-probléma – az optimálisnál rosszabb útválasztás az ügyféltől a Microsoft felé](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Megoldás: BGP-közösségek használata
Az útválasztás mindkét irodához való optimalizálásához ismernie kell, hogy melyik előtag származik az USA nyugati Azure-régiójából és melyik az USA keleti Azure-régiójából. Ezt az információt a [BGP-közösség értékeivel](expressroute-routing.md) kódoljuk. Minden egyes Azure-régióban egyedi BGP-közösségi értéket rendeltünk, például "12076:51004" az USA keleti régiójában, "12076:51006" az USA nyugati régiójában. Most, hogy tudja, melyik előtag melyik Azure-régióból származik, konfigurálhatja, hogy melyik ExpressRoute-kapcsolatcsoportot részesíti előnyben. Mivel az útválasztási információ cseréje a BGP segítségével történik, a BGP helyi preferenciaértékével befolyásolhatja az útválasztást. A példában magasabb helyi preferenciaértéket rendelhet hozzá a 13.100.0.0/16 előtaghoz az USA nyugati régiójában, mint az USA keleti régiójában, és hasonlóképpen magasabb helyi preferenciaértéket rendelhet hozzá a 23.100.0.0/16 előtaghoz az USA keleti régiójában, mint az USA nyugati régiójában. Ez a konfiguráció biztosítja, hogy ha a Microsoft felé mindkét útvonal elérhető, a Los Angelesben lévő felhasználók az USA nyugati régiójában lévő ExpressRoute-kapcsolatcsoport segítségével csatlakoznak az USA nyugati Azure-régiójához, míg a New Yorkban lévő felhasználók az USA keleti régiójában lévő ExpressRoute-ot használják az USA keleti Azure-régiójához való csatlakozásra. Az útválasztás minkét oldalon optimalizálva van. 

![1. ExpressRoute-eset megoldása – BGP-közösségek használata](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Ugyanez a technika alkalmazható helyi preferenciaértékkel az ügyféltől az Azure virtuális hálózat felé irányuló útválasztásra. Nem címkézzük a BGP-közösség értékét az Azure-ból a hálózatára irányuló előtagokon. Mivel azonban tudja, hogy melyik üzemelő virtuálishálózat-példány van közel melyik irodához, ennek megfelelően konfigurálhatja az útválasztóit egy ExpressRoute-kapcsolatcsoportot előnyben részesítve a többivel szemben.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Optimálisnál rosszabb útválasztás a Microsofttól az ügyfél felé
Itt látható egy másik példa, amelyben a Microsofttól induló kapcsolatok hosszabb útvonalat vesznek igénybe a hálózat eléréséhez. Ebben az esetben helyszíni Exchange-kiszolgálókat és az Exchange Online-t használja egy [hibrid környezetben](/exchange/exchange-hybrid). Az irodák egy WAN hálózathoz csatlakoznak. Mindkét irodában meghirdeti a helyszíni kiszolgálók előtagjait a Microsoft számára a két ExpressRoute-kapcsolatcsoporton keresztül. Az Exchange Online bizonyos esetekben, például a postaládák áttelepítésekor kapcsolatot kezdeményez a helyszíni kiszolgálókkal. Sajnos a Los Angeles-i irodához irányuló kapcsolat az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoporton keresztül van átirányítva, mielőtt az áthaladna az egész kontinensen vissza a nyugati partra. A probléma oka hasonló az első esetéhez. A Microsoft hálózata információ hiányában nem tudja megállapítani, hogy melyik ügyfélelőtag van közel az USA keleti régiójához, és melyik az USA nyugati régiójához. Emiatt a rossz útvonalat választja a Los Angeles-i irodához.

![2. ExpressRoute-eset– az optimálisnál rosszabb útválasztás a Microsofttól az ügyfél felé](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Megoldás: AS PATH előtag-beillesztés
A problémára két megoldás létezik. Az első, hogy egyszerűen a Los Angeles-i iroda helyszíni előtagját (177.2.0.0/31) hirdeti meg az USA nyugati régiójában lévő ExpressRoute-kapcsolatcsoporton, és a New York-i iroda helyszíni előtagját (177.2.0.2/31) hirdeti meg az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoporton. Ennek eredményeképpen a Microsoftnak csak egy útvonal áll rendelkezésére, amelyen az egyes irodákhoz csatlakozhat. Nincs félreérthetőség, és az útválasztás optimalizálva van. Ezzel a kialakítással kell kidolgoznia egy feladatátvételi stratégiát. Abban az esetben, ha a Microsoft felé az ExpressRoute-on keresztül haladó útvonal megszakad, biztosítania kell, hogy az Exchange Online továbbra is csatlakozni tudjon a helyszíni kiszolgálókhoz. 

A második megoldás az, hogy továbbra is meghirdeti mindkét előtagot mindkét ExpressRoute-kapcsolatcsoporton, és emellett egy tippet ad arról, hogy melyik előtag melyik irodához van közelebb. Mivel támogatjuk a BGP AS PATH előtag-beillesztést, konfigurálhatja az előtaghoz tartozó AS PATH előtag-beillesztést, és befolyásolhatja az útválasztást. Ebben a példában meghosszabbíthatja az USA keleti régiójában az 172.2.0.0/31 előtag AS PATH értékét, aminek hatására az USA nyugati régiójában lévő ExpressRoute-kapcsolatcsoportot fogjuk előnyben részesíteni az ehhez az előtaghoz irányuló forgalom esetében (mivel a hálózatunk úgy fogja látni, hogy az előtaghoz tartozó útvonal rövidebb a nyugati parton). Hasonlóképpen meghosszabbíthatja az USA nyugati régiójában az 172.2.0.2/31 előtag AS PATH értékét, aminek hatására az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoportot fogjuk előnyben részesíteni. Az útválasztás mindkét iroda esetében optimalizálva van. Ezzel a kialakítással, ha az egyik ExpressRoute-kör megszakad, az Exchange Online továbbra is elérheti az Ön hálózatát a másik ExpressRoute-kapcsolatcsoporton és a WAN hálózaton keresztül. 

> [!IMPORTANT]
> Az AS PATH (privát as) számot a Microsoft-társon keresztül fogadott előtagokhoz a (z) szolgáltatásban, ha privát AS-számot használ. A Microsoft-partnerek útválasztásának befolyásolásához a AS PATH (nyilvános) és az AS (nyilvános) számot kell hozzáfűzni.
> 
> 

![2. ExpressRoute-eset megoldása – AS PATH előtag-beillesztés](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Bár az itt felsorolt példák a Microsoftra és a Nyilvános társviszony-létesítésre vonatkoznak, ugyanezek a funkciók a magánhálózati társviszony-létesítéshez is támogatottak. Emellett egyetlen ExpressRoute-kapcsolatcsoporton belül működik az AS Path előtag-beillesztés is, így befolyásolva az elsődleges és a másodlagos útvonalak kiválasztását.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Az optimálisnál rosszabb útválasztás a virtuális hálózatok között
Az ExpressRoute használatával engedélyezheti a virtuális hálózatról virtuális hálózatra (más néven „VNetre”) irányuló kommunikációt, ha egy ExpressRoute-kapcsolatcsoporthoz kapcsolja azokat. Amikor több ExpressRoute-kapcsolatcsoporthoz kapcsolja a hálózatokat, az optimálisnál rosszabb útválasztás történhet a VNetek között. Vegyünk egy példát. Két ExpressRoute-kapcsolatcsoporttal rendelkezik, az egyik az USA nyugati régiójában, a másik az USA keleti régiójában található. Mindkét régióban két VNet van. A webkiszolgálói az egyik VNeten vannak üzembe helyezve, az alkalmazáskiszolgálói pedig a másikon. A redundancia miatt a régiókban lévő két VNetet a helyi ExpressRoute-kapcsolatcsoporthoz és a távoli ExpressRoute-kapcsolatcsoporthoz is hozzákapcsolja. Ahogy az alábbiakban láthatja, mindegyik VNetből két út vezet a másik VNethez. A VNetek nem tudják, melyik ExpressRoute-kapcsolatcsoport helyi és melyik távoli. Ennek következtében, mivel Azonos költségű több útvonalas útválasztást (ECMP) végeznek a VNetek közötti forgalom terheléskiegyenlítése érdekében, néhány adatforgalom a hosszabb útvonalat választja, és a távoli ExpressRoute-kapcsolatcsoportra van irányítva.

![3. ExpressRoute-eset – az optimálisnál rosszabb útválasztás a virtuális hálózatok között](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Megoldás: nagyobb súly hozzárendelése a helyi kapcsolathoz
A megoldás egyszerű. Mivel tudja, hogy hol vannak a VNetek és a kapcsolatcsoportok, megmondhatja nekünk, hogy az egyes VNetek mely útvonalat részesítsék előnyben. Ebben a példában nagyobb súlyt rendel a helyi kapcsolathoz, mint a távoli kapcsolathoz (a példakonfigurációt [itt](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection) találja). Amikor egy VNet több kapcsolaton fogadja a másik VNet előtagját, a nagyobb súlyú kapcsolatot részesíti előnyben, hogy az előtaghoz küldeni kívánt adatforgalmat elküldje.

![3. ExpressRoute-eset megoldása – nagy súly hozzárendelése a helyi kapcsolathoz](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> A VNetről a helyszíni hálózatra irányuló útválasztást is befolyásolhatja, ha több ExpressRoute-kapcsolatcsoportja van. Ehhez konfigurálja egy kapcsolat súlyát ahelyett, hogy AS PATH előtag-beillesztést használna, amelyet a fenti második eset ismertet. Mindegyik előtag esetében először mindig a kapcsolat súlyát vesszük figyelembe az AS PATH hossza előtt a forgalom elküldési módjának meghatározásához.
>
>