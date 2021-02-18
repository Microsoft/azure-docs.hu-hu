---
title: A Azure Load Balancer bepillantást nyerhet
description: A terheléselosztási elemzések használatával gyors és tájékozott tervezési döntéseket érhet el
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 190a9e431dedfb0f0fe6077e903174f253f5c773
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589098"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>A Azure Load Balancer figyelése és konfigurálása a bepillantást használva

A hálózatokon keresztüli Azure Monitor a funkciók függőségi vizualizációit és előre konfigurált mérőszámokat biztosít a terheléselosztó számára. Ezek a vizualizációk segítenek a tervezési döntések meghozatalában, valamint a hibák gyors honosításában, diagnosztizálásában és megoldásában.

>[!NOTE] 
>Vegye figyelembe, hogy ez a funkció előzetes verzióban érhető el, és a funkcionális függőségi nézet és az előre konfigurált irányítópult is változhat a felhasználói élmény javítása érdekében

>[!IMPORTANT]
>A standard Load Balancer szükséges a metrikák megjelenítéséhez a Load Balancer névtérből az előre konfigurált mérőszámok irányítópultján. Továbbra is láthatja a virtuális gép, a virtuálisgép-méretezési csoport és a figyelő névterek mérőszámait, de javasoljuk, hogy az éles számítási feladatokhoz a [standard szintű verzióra frissítsen](./upgrade-basic-standard.md) , hogy kihasználhassa az Load Balancer mérőszámok robusztus készletét.

## <a name="functional-dependency-view"></a>Funkcionális függőség nézet

A funkcionális függőségi nézet lehetővé teszi, hogy még a legbonyolultabb terheléselosztó-telepítőket is képképpel tudja ábrázolni. A legújabb Load Balancer-konfigurációra vonatkozó vizualizációs visszajelzésekkel a konfiguráció szem előtt tartásával is elvégezheti a frissítéseket.

Ezt a nézetet úgy érheti el, ha felkeresi az Azure-beli Load Balancer-erőforrás bepillantást nyerhet.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="A funkcionális függőségi nézet Depecition. A terheléselosztó előtérben látható, hogy a megadott szabályok segítségével csatlakozik a háttérbeli készlet tagjaihoz. A standard Load Balancer esetében a terheléselosztási szabályokból a háttérbeli készletbe tartozó sorok színkódolása az állapot-mintavételi állapot alapján történik." border="true":::

A standard Load Balancerek esetében a háttérbeli készlet erőforrásai színkóddal vannak ellátva, és a rendszer a háttér-készlet aktuális rendelkezésre állását jelzi a forgalom kiszolgálásához. A fenti topológia mellett egy idő-Wise gráf jelenik meg, amely az alkalmazás állapotának pillanatkép-nézetét jeleníti meg.

## <a name="metrics-dashboard"></a>Metrikai irányítópult

A Load Balancer betekintő paneljén kiválaszthatja a részletesebb mérőszámokat, amelyekkel megtekintheti az előre konfigurált [Azure monitor munkafüzetet](../azure-monitor/visualize/workbooks-overview.md) , amely a Load Balancer adott szempontjaira vonatkozó mérőszámokat tartalmazó vizualizációkat tartalmaz. Ez az irányítópult a Load Balancer állapotát és a kapcsolódó dokumentációra mutató hivatkozásokat jeleníti meg az oldal tetején.

Első lépésként az Áttekintés lapot mutatjuk be. Navigálhat az elérhető lapokon, amelyek a Load Balancer adott aspektusához tartozó vizualizációkat tartalmaznak. Az egyes lapokhoz tartozó explicit útmutatást az irányítópult alján találhatja meg.

A jelenleg elérhető irányítópult-lapok a következők:
* Áttekintés
* Előtér és háttér rendelkezésre állása
* Adatátviteli sebesség
* Folyamateloszlás
* Kapcsolatfigyelők
* Metrikadefiníciók 

### <a name="overview-tab"></a>Áttekintés lap
Az Áttekintés lap egy kereshető rácsot tartalmaz, amely tartalmazza az adatelérési út teljes rendelkezésre állását és az állapot mintavételi állapotát a Load Balancerhoz csatolt összes előtérbeli IP-cím esetében. Ezek a mérőszámok jelzik, hogy a előtérbeli IP-cím válaszol-e, és hogy a háttérbeli készlet számítási példányai külön-e a bejövő kapcsolatokra.

Ezen a lapon megtekintheti az összes előtérbeli IP-cím összesített adatátviteli sebességét, így azt is megismerheti, hogy a rendszer a várható forgalmi szinteket állítja elő és fogadja-e. A lap alján található útmutatás a megfelelő lapon jelenik meg, ha bármilyen szabálytalan értéket lát.

### <a name="frontend-and-backend-availability-tab"></a>A frontend és a háttér rendelkezésre állása lap
A frontend és a háttér rendelkezésre állási lapja az adatelérési út átviteli sebességét és az állapot-mintavételi állapot mérőszámait mutatja be néhány hasznos nézetben. Az első grafikonon az összesített érték látható, így megállapítható, hogy van-e probléma. A többi gráf a különböző dimenziók által felosztott metrikákat jeleníti meg, így a bejövő rendelkezésre állási problémák forrásainak elhárítását és azonosítását is lehetővé teszi.

A gráfok megtekintésére szolgáló munkafolyamatot az oldal alján tekintheti meg, és a különböző tünetek gyakori okai. 

### <a name="data-throughput-tab"></a>Adatátviteli lap
Az adatátvitel lapon áttekintheti a bejövő és kimenő adatátviteli sebességét annak megállapításához, hogy a forgalmi minták a várt módon működnek-e. Megjeleníti a beérkező és kimenő adatátviteli sebességet az előtéri IP-cím és a frontend-port alapján, így azonosíthatja, hogy a futó szolgáltatások hogyan működnek önállóan.

### <a name="flow-distribution"></a>Folyamateloszlás
A folyamat terjesztése lapon megtekintheti és kezelheti a háttérbeli példányok által fogadott és gyártott folyamatok számát. A folyamat a bejövő és kimenő forgalom forgalmának arányát és a folyamatok darabszámát, valamint az egyes virtuális gépek és a virtuálisgép-méretezési csoport példányainak fogadására szolgáló hálózati forgalmat jeleníti meg. 

Ezek a nézetek visszajelzést adhatnak arról, hogy a Load Balancer-konfiguráció vagy a forgalmi szokások kiegyensúlyozatlan forgalmat eredményeznek-e. Ha például a munkamenet-affinitás konfigurálva van, és egyetlen ügyfél aránytalanul sok kérést tesz ki. Azt is megtudhatja, hogy a [virtuális](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) gép méretétől függ-e.

### <a name="connection-monitors"></a>Kapcsolatfigyelők
A kapcsolati figyelők lapon megtekintheti az összes konfigurált [kapcsolati figyelő](../network-watcher/connection-monitor.md)  globális térképének kerekítési késleltetését. Ezek a vizualizációk hasznos információkat nyújtanak a szigorú késési követelményekkel rendelkező szolgáltatásokhoz. A követelmények teljesítése érdekében további regionális központi telepítéseket kell felvennie, vagy át kell térnie a [régiók közötti](./cross-region-overview.md) terheléselosztási modellre

### <a name="metric-definitions"></a>Metrikadefiníciók
A metrika-definíciók lapon a [többdimenziós metrikák című cikkben](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)látható összes információ szerepel.

## <a name="next-steps"></a>Következő lépések
* Tekintse át az irányítópultot, és küldjön visszajelzést az alábbi hivatkozással, ha van valami, ami javítható
* [Tekintse át a metrikák dokumentációját, amelyből megtudhatja, hogyan számítja ki az egyes metrikákat](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [A Load Balancerhoz tartozó kapcsolatok figyelők létrehozása](../network-watcher/connection-monitor.md)
* [Saját munkafüzetek létrehozásához](../azure-monitor/visualize/workbooks-overview.md)kattintson a részletes mérőszámok irányítópultjának Szerkesztés gombjára.
