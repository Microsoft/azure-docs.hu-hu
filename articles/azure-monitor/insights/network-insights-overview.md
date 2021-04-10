---
title: Azure Monitor hálózatokhoz
description: A hálózatok Azure Monitor áttekintése, amely az összes telepített hálózati erőforrás állapotának és metrikáinak átfogó áttekintését teszi lehetővé konfiguráció nélkül.
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: db94a376112b3d715da9095acb05c0b4fd4ea02f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605800"
---
# <a name="azure-monitor-for-networks"></a>Azure Monitor hálózatokhoz

A hálózatok Azure Monitor az összes telepített hálózati erőforrás [állapotának](../../service-health/resource-health-checks-resource-types.md) és [metrikáinak](../essentials/metrics-supported.md) átfogó áttekintését teszi lehetővé anélkül, hogy konfigurációt kellene igényelni. Emellett hozzáférést biztosít a hálózati figyelési funkciókhoz, például a [kapcsolati figyelőhöz](../../network-watcher/connection-monitor-overview.md), [a hálózati biztonsági csoportok (NSG) adatforgalmának naplózásához](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)és [Traffic Analytics](../../network-watcher/traffic-analytics.md). És más hálózati [diagnosztikai](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) funkciókat is biztosít.

A hálózatok Azure Monitor a monitorozás ezen kulcsfontosságú összetevői köré szerveződik:
- [Hálózati állapot és mérőszámok](#networkhealth)
- [Kapcsolódás](#connectivity)
- [Adatforgalom](#traffic)
- [Diagnosztikai eszközkészlet](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Hálózati állapot és mérőszámok

A hálózatok Azure Monitor **áttekintése** lapon egyszerűen megjeleníthetők a hálózati erőforrások leltára, valamint a Resource Health és a riasztások. Négy kulcsfontosságú funkcionális területre oszlik: keresés és szűrés, erőforrás-állapot és mérőszámok, riasztások és függőségi nézetek.

[![Képernyőkép az Áttekintés lapról](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Keresés és szűrés
Az erőforrás állapota és riasztások nézetet a szűrők, például az **előfizetés**, az **erőforráscsoport** és a **típus** használatával szabhatja testre.

A keresőmező segítségével megkeresheti az erőforrásokat és a hozzájuk kapcsolódó erőforrásokat. Egy nyilvános IP-cím például egy Application gatewayhez van társítva. A nyilvános IP DNS-nevének keresése a nyilvános és a társított Application Gateway-átjárót is megadja:

[![A hálózati keresési eredmények Azure Monitorét bemutató képernyőkép.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Erőforrás állapota és mérőszámai
A következő példában az egyes csempék egy erőforrástípust jelölnek. A csempe az összes kiválasztott előfizetésben üzembe helyezett erőforrástípus példányainak számát jeleníti meg. Az erőforrás állapotát is megjeleníti. Ebben a példában 105 ER és VPN-kapcsolatok vannak telepítve. 103 kifogástalan, és a 2 nem érhető el.

![Képernyőfelvétel: a Azure Monitor a hálózatokhoz tartozó erőforrás-állapotot és mérőszámokat mutatja.](media/network-insights-overview/resource-health.png)

Ha a nem elérhető ER és VPN kapcsolatokat választja, akkor a metrika nézet jelenik meg: 

![A hálózatokra Azure Monitor metrika nézetét bemutató képernyőkép.](media/network-insights-overview/metric-view.png)

Bármelyik elemet kiválaszthatja a rács nézetben. Válassza ki az **állapot** oszlopban látható ikont az erőforrás állapotának lekéréséhez a kapcsolódáshoz. Válassza ki az értéket a **riasztás** oszlopban a kapcsolódáshoz tartozó riasztások és metrikák lapra való ugráshoz. 

### <a name="alerts"></a>Riasztások
Az oldal jobb oldalán lévő **riasztási** mező a kiválasztott erőforrásokhoz generált összes riasztást megjeleníti az összes előfizetésben. Válassza ki a riasztások számát, és lépjen a részletes riasztások oldalra.

### <a name="dependency-view"></a>Függőség nézet
A függőség nézet segítségével megjelenítheti az erőforrások konfigurálásának módját. A függőség nézet jelenleg az Azure Application Gateway, az Azure Virtual WAN és a Azure Load Balancer számára érhető el. Application Gateway esetében például elérheti a függőségi nézetet úgy, hogy a metrikák rács nézetében kijelöli a Application Gateway erőforrás nevét. Ugyanezt megteheti a virtuális WAN és a Load Balancer esetében is.

![Sreenshot, amely a hálózatok Application Gateway nézetét jeleníti meg Azure Monitor.](media/network-insights-overview/application-gateway.png)

A Application Gateway függőségi nézete egyszerűsített képet nyújt arról, hogy az előtér-IP-címek hogyan kapcsolódnak a figyelőkhöz, a szabályokhoz és a háttér-készlethez. A csatlakoztatott vonalak színkóddal rendelkeznek, és további részleteket biztosítanak a háttér-készlet állapota alapján. A nézet az összes kapcsolódó háttér-készletre vonatkozó Application Gateway mérőszámok és mérőszámok részletes nézetét is tartalmazza, például a virtuálisgép-méretezési csoport és a VM-példányok esetében.

[![A hálózatok Azure Monitor függőségi nézetét bemutató képernyőkép.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

A függőségi diagram egyszerű navigációt biztosít a konfigurációs beállításokhoz. Kattintson a jobb gombbal a háttér-készletre más információk eléréséhez. Ha például a háttérrendszer-készlet egy virtuális gép, akkor a kapcsolódási problémák azonosításához közvetlenül hozzáférhet a virtuálisgép-megállapításokhoz és az Azure Network Watcher-kapcsolat hibaelhárításához:

![A hálózatokban Azure Monitor a függőség nézet menüt megjelenítő képernyőkép.](media/network-insights-overview/dependency-view-menu.png)

A függőség nézet keresési és szűrési sávjának segítségével egyszerűen kereshet a gráfon. Ha például az előző példában a **AppGWTestRule** keres, a nézet a AppGWTestRule-n keresztül csatlakozó összes csomópontra le lesz méretezve:

![Képernyőkép, amely a hálózatok Azure Monitor keresésére mutat példát.](media/network-insights-overview/search-example.png)

A különböző szűrők segítségével a méretezést egy adott útvonalra és állapotra szűkítheti. Válassza például a csak a nem kifogástalan **állapotú** elemet az állapot listából, hogy megjelenítse az összes olyan szegélyt, amelynél az **állapot állapota sérült** .

Válassza a **részletes mérőszámok megtekintése** lehetőséget egy előre konfigurált munkafüzet megnyitásához, amely részletes mérőszámokat tartalmaz az Application Gateway, az összes háttér-készlet erőforrásai és az előtér-IP-címek számára. 

## <a name="connectivity"></a><a name="connectivity"></a>Connectivity

A **kapcsolat lapon egyszerűen megjeleníthetők a** [kapcsolat figyelője](../../network-watcher/connection-monitor-overview.md) és a kapcsolat figyelője (klasszikus) használatával konfigurált tesztek a kiválasztott előfizetésekhez.

![A hálózatok Azure Monitor kapcsolat lapját bemutató képernyőkép.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

A tesztek a **források** és a **Célhelyek** csempéi szerint vannak csoportosítva, és megjelenítik az egyes tesztek elérhetőségi állapotát. A elérhető beállítások egyszerű hozzáférést biztosítanak a REACH-feltételekhez tartozó konfigurációkhoz a sikertelen ellenőrzések alapján (%) és RTT (MS). Az értékek beállítása után az egyes tesztelési frissítések állapota a kiválasztási feltételek alapján történik.

[![A hálózatokon Azure Monitor kapcsolati teszteket bemutató képernyőkép.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

A metrika nézetet a forrás vagy a cél csempével is megnyithatja:

[![A hálózati Azure Monitor kapcsolati metrikáit bemutató képernyőkép.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


Bármelyik elemet kiválaszthatja a rács nézetben. Válassza ki a kapcsolati figyelő portált a **elérhetőség** oszlopban, és tekintse meg a hop-by-hop topológiát és az azonosított problémákat befolyásoló kapcsolatot. Válassza ki az értéket a **riasztás** oszlopban a riasztások pontra való ugráshoz. Válassza ki az **ellenőrzések sikertelen százaléka** és az **időpontok (MS)** oszlopaiban található diagramokat, hogy megnyissa a metrikák lapot a kiválasztott kapcsolódási figyelőhöz.

Az oldal jobb oldalán lévő **riasztási** mező az összes előfizetésben konfigurált csatlakozási tesztekhez létrehozott riasztásokat jeleníti meg. Válassza ki a riasztások számát, és lépjen a részletes riasztások oldalra.

## <a name="traffic"></a><a name="traffic"></a>Adatforgalom
A **Traffic (forgalom** ) lapon elérhetők a NSG [Traffic Analytics](../../network-watcher/traffic-analytics.md) - [flow naplóihoz](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) konfigurált összes NSG, valamint az előfizetések kiválasztott készletéhez, hely szerint csoportosítva. Az ezen a lapon megadott keresési funkció lehetővé teszi a keresett IP-címhez konfigurált NSG azonosítását. Bármilyen IP-címet kereshet a környezetében. A csempézett regionális nézet az összes NSG jeleníti meg, valamint a NSG folyamat naplóit és a Traffic Analytics konfigurációs állapotát.

[![A hálózatok Azure Monitor Traffic (forgalom) lapját bemutató képernyőkép.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Ha a bármely régió csempét választja, megjelenik egy rács nézet. A Grid NSG-naplókat és Traffic Analyticseket biztosít egy könnyen olvasható és konfigurálható nézetben:  

[![Képernyőkép, amely megjeleníti a forgalmi régió nézetet a hálózatok Azure Monitor.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

Bármelyik elemet kiválaszthatja a rács nézetben. A **Flowlog konfigurációs állapota** oszlopban válassza a ikont a NSG-folyamat naplójának és Traffic Analytics konfigurációjának szerkesztéséhez. Válassza ki az értéket a **riasztás** oszlopban, hogy a kiválasztott NSG konfigurált forgalmi riasztásokhoz lépjen. Ehhez hasonlóan a **Traffic Analytics munkaterületet** választva is megnyithatja a Traffic Analytics nézetet.  

Az oldal jobb oldalán lévő **riasztási** mező az összes előfizetésben Traffic Analytics munkaterület-alapú riasztást jeleníti meg. Válassza ki a riasztások számát, és lépjen a részletes riasztások oldalra.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnosztikai eszközkészlet
A diagnosztikai eszközkészlet hozzáférést biztosít a hálózat hibaelhárításához rendelkezésre álló diagnosztikai funkciókhoz. Ezt a legördülő listát használhatja olyan szolgáltatások eléréséhez, mint a [csomagok rögzítése](../../network-watcher/network-watcher-packet-capture-overview.md), a [VPN-hibaelhárítás](../../network-watcher/network-watcher-troubleshoot-overview.md), a [kapcsolat hibaelhárítása](../../network-watcher/network-watcher-connectivity-overview.md), a [következő ugrás](../../network-watcher/network-watcher-next-hop-overview.md)és az [IP-folyamat ellenőrzése](../../network-watcher/network-watcher-ip-flow-verify-overview.md):

![A diagnosztikai eszközkészlet fület megjelenítő képernyőkép.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="onboarded-resources"></a>Beépített erőforrások 

A beépített erőforrások beépített munkafüzetekkel és függőségi nézetekkel rendelkeznek. A jelenleg beépített erőforrások a következők: Virtual WAN, Application Gateway, Load Balancer és ExpressRoute.

## <a name="troubleshooting"></a>Hibaelhárítás 
Általános hibaelhárítási útmutatót a dedikált munkafüzet-alapú információk [hibaelhárítási cikkében](troubleshoot-workbooks.md)talál.
Ez a szakasz segít diagnosztizálni és elhárítani néhány olyan gyakori problémát, amely akkor fordulhat elő, ha Azure Monitort használ a hálózatokhoz. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Hogyan a teljesítménnyel kapcsolatos problémákat vagy hibákat?

A hálózatokkal Azure Monitor azonosított hálózatkezeléssel kapcsolatos problémák elhárításának megismeréséhez tekintse meg a hibás erőforrás hibaelhárítási dokumentációját. 

Íme néhány hivatkozás a gyakran használt szolgáltatások hibaelhárítási cikkeihez. A szolgáltatásokkal kapcsolatos további hibaelhárítási cikkekért tekintse meg a szolgáltatás tartalomjegyzékének hibaelhárítási szakaszában található további cikkeket.
* [Azure Virtual Network](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Miért nem látom az összes kiválasztott előfizetés erőforrásait?

A hálózatok Azure Monitor egyszerre csak öt előfizetéshez tud erőforrásokat megjeleníteni. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-for-networks"></a>Hogyan módosításokat hajthat végre, vagy vizualizációkat adhat hozzá a hálózatokhoz Azure Monitorhoz?

A változtatáshoz válassza a **szerkesztési mód** lehetőséget a munkafüzet módosításához. Ezután mentheti a módosításokat új munkafüzetként, amely egy kijelölt előfizetéshez és erőforráscsoporthoz van kötve.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Mi az az idő, amikor a munkafüzetek bármelyik részét rögzíteni szeretném?

A hálózatok Azure Monitor az **automatikus** időkeretet használja, így az időkeret a kiválasztott időtartományon alapul.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Mi az az időintervallum, amikor egy munkafüzet bármely része rögzítve van?

Az időtartomány az irányítópult beállításaitól függ.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-for-networks"></a>Mi a teendő, ha más típusú vagy saját vizualizációkat szeretnék látni? Hogyan módosíthatom Azure Monitor a hálózatokon?

A szerkesztési mód használatával bármely oldalon vagy részletes mérőszámban megjelenített munkafüzetet szerkeszthet. Ezután új munkafüzetként mentheti a módosításokat.

## <a name="next-steps"></a>Következő lépések

- További információ a hálózati figyelésről: [Mi az az Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md)
- Ismerje meg a forgatókönyveket, amelyekkel a munkafüzetek támogatottak, hogyan hozhatók létre jelentések és testreszabhatók a meglévő jelentések, és így tovább: [interaktív jelentések létrehozása Azure monitor munkafüzetek](../visualize/workbooks-overview.md) használatával
