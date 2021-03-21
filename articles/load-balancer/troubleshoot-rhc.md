---
title: Az erőforrás-állapot, a frontend és a háttérbeli rendelkezésre állási problémák elhárítása Azure Load Balancer
description: Az elérhető metrikák használatával diagnosztizálhatja a csökkentett teljesítményű vagy nem elérhető Azure-standard Load Balancer.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 3acaaba86c9a546a0bd45b5386287908168d50d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97955620"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Erőforrás-állapot és a bejövő rendelkezésre állással kapcsolatos problémák elhárítása 

Ez a cikk egy útmutató a terheléselosztó előtérbeli IP-és háttér-erőforrásainak rendelkezésre állását befolyásoló problémák kivizsgálásához. 

A terheléselosztó állapotának meghatározásához a Load Balancer Resource Health-ellenőrzését (RHC) használja a rendszer. A szolgáltatás az adatelérési út rendelkezésre állási metrikáját **2 percenként** elemzi, hogy meghatározza, hogy a terheléselosztási végpontok, a előtérbeli IP-címek és a előtér-portok a terheléselosztási szabályokkal együtt használhatók-e.

Az alábbi táblázat ismerteti a terheléselosztó állapotának megállapításához használt RHC logikát.

| Erőforrás állapotának állapota | Description |
| --- | --- |
| Elérhető | A standard Load Balancer erőforrása kifogástalan és elérhető. |
| Csökkentett teljesítményű | A standard Load Balancer platform vagy felhasználó által kezdeményezett események hatással vannak a teljesítményre. Az adatelérési út rendelkezésre állására vonatkozó metrika 90%-osnál rosszabb, de 25%-osnál jobb állapotot jelentett legalább két percig. A teljesítmény mérsékelten befolyásolhatja a teljesítményt. 
| Nem érhető el | A standard Load Balancer erőforrás nem kifogástalan állapotú. A DataPath rendelkezésre állási metrikája kevesebb, mint 25%-os állapotot jelentett legalább két percen belül. Jelentős teljesítménybeli hatást vagy a bejövő kapcsolatok elérhetőségének hiányát tapasztalhatja. Előfordulhat, hogy a felhasználó vagy a platform eseményei nem állnak rendelkezésre. |
| Ismeretlen | A standard Load Balancer erőforrás erőforrás-állapotának állapota még nem frissült, vagy nem kapott adatelérési utat az elmúlt 10 percben. Ennek az állapotnak átmenetinek kell lennie, és a megfelelő állapot fog megjelenni, amint az adatok beérkeznek. |


## <a name="about-the-metrics-well-use"></a>Tudnivalók a használatban lévő mérőszámokról
A használandó két mérőszám az *adatelérési út rendelkezésre állása* és az *állapot mintavételi állapota* , és fontos megérteni a jelentését a helyes elemzések elvégzéséhez. 

## <a name="data-path-availability"></a>Adatútvonalak rendelkezésre állása
Az adatelérési út rendelkezésre állási metrikáját a rendszer 25 másodpercenként egy TCP ping paranccsal hozza létre minden olyan előtér-porton, amelyen be van állítva a terheléselosztás és a bejövő NAT-szabályok. Ezt a TCP-pingelést a rendszer átirányítja a kifogástalan állapotú (feloldható) háttérbeli példányokra. Ha a szolgáltatás választ kap a pingelésre, akkor sikeresnek számít, és a metrika összegét egyszer kell megismételni, ha az nem sikerül. Ennek a mérőszámnak a száma a teljes TCP-pingelés 1/100. Ezért az átlagot szeretnénk figyelembe venni, amely az adott időszakra vonatkozó összeg/darabszám átlagát mutatja. Az adatelérési út rendelkezésre állási mérőszáma az átlag alapján összesíti a TCP-pingek százalékos sikerességi arányát az előtérbeli IP-címen: port az egyes terheléselosztási és bejövő NAT-szabályokhoz.

## <a name="health-probe-status"></a>Állapotadat-mintavétel állapota
Az állapotfigyelő állapot mérőszáma az állapot-mintavételben definiált protokoll pingelésével jön létre. Ezt a pingelést a rendszer a háttérbeli készlet minden példányára és az állapot-mintavételben definiált portra továbbítja. HTTP-és HTTPS-mintavétel esetén a sikeres pingeléshez HTTP 200 OK-válasz szükséges, míg a TCP-mintavételek esetében minden válasz sikeresnek minősül. Az egyes mintavételek egymás utáni sikeressége vagy meghibásodása azt határozza meg, hogy a háttérbeli példány kifogástalan állapotú-e, és képes-e forgalmat fogadni azokhoz a terheléselosztási szabályokhoz, amelyekhez a háttér-készlet hozzá van rendelve. Az adatelérési utak rendelkezésre állásához hasonlóan az átlagos összesítést használjuk, ami azt jelzi, hogy az átlagos sikeres/összes pingelés a mintavételezési intervallumban történik. Ez az állapot-mintavételi állapot azt jelzi, hogy a háttér állapota a terheléselosztó által elkülönített módon, a háttérbeli példányok elküldése nélkül, a felhasználói felületről küldött adatforgalom nélkül.

>[!IMPORTANT]
>Az állapot mintavételi állapotának mintavételezése egy percen keresztül történik. Ez kisebb ingadozásokat eredményezhet egy máskülönben állandó értékben. Ha például két háttér-példány létezik, az egyik mintavétel és egy leállt, az állapot mintavételi szolgáltatása 7 mintát rögzíthet az egészséges példányhoz, és 6 a nem kifogástalan állapotú példányhoz. Ez egy, a 50-as számú, a 46,15-es, egy perces intervallumot eredményező, korábban állandó értékre mutat. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Csökkentett teljesítményű és nem elérhető terheléselosztó diagnosztizálása
Az [Erőforrás-állapot című cikkben](load-balancer-standard-diagnostics.md#resource-health-status)leírtaknak megfelelően a csökkentett teljesítményű Load Balancer az egyik, amely 25%-os és 90%-os adatelérési út közötti rendelkezésre állást mutat, és egy nem elérhető terheléselosztó az adatelérési út kevesebb mint 25%-át, kétperces időtartamon belül. Ugyanezekkel a lépésekkel vizsgálhatja meg a hibát, amelyet az állapot-mintavételi állapot vagy a konfigurált adatelérési útvonalak elérhetőségi riasztásai észleltek. Megvizsgáljuk azt az esetet, amikor bevizsgáltuk az erőforrás állapotát, és megtaláljuk, hogy a terheléselosztó nem érhető el a 0%-os adatelérési utak elérhetőségével – a szolgáltatás nem áll le.

Első lépésként a Load Balancer-információk panel részletes mérőszámait láthatjuk. Ezt a terheléselosztó erőforrás-paneljén vagy a Resource Health-üzenetben szereplő hivatkozáson keresztül teheti meg.  Következő lépésként navigáljon a frontend és a háttér rendelkezésre állása lapra, és tekintse át az időszaknak azt a harminc perces időpontját, amikor a csökkentett teljesítményű vagy elérhetetlen állapot történt. Ha azt látjuk, hogy az adatelérési út rendelkezésre állása 0% volt, tudjuk, hogy probléma merült fel a terheléselosztás és a bejövő NAT-szabályok forgalmának megakadályozása, és láthatja, hogy mennyi ideig tartott a hatás. 

A következő helyen meg kell vizsgálnunk az állapot mintavételi állapotának mérőszámát annak megállapításához, hogy az adatelérési út nem érhető el, mert nem áll rendelkezésre kifogástalan háttér-példányok a forgalom kiszolgálásához. Ha a terheléselosztás és a bejövő szabályok közül legalább egy kifogástalan háttér-példánnyal rendelkezik, tudjuk, hogy az adatelérési útjaink nem állnak rendelkezésre. Ez a forgatókönyv egy Azure platformmal kapcsolatos problémát jelez, míg ritka, ne izgulj, ha úgy találja, hogy a rendszer automatikusan riasztást küld a csapatnak, hogy gyorsan megoldja az összes platformmal kapcsolatos problémát.

## <a name="diagnose-health-probe-failures"></a>Állapot-mintavételi hibák diagnosztizálása
Tegyük fel, hogy ellenőrizzük az állapot mintavételi állapotát, és megtudhatjuk, hogy az összes példány nem kifogástalan állapotú. Ez a megállapítás azt ismerteti, hogy az adatelérési út miért nem érhető el, mivel a forgalom nem mehet el. A gyakori konfigurációs hibák kiszűréséhez a következő ellenőrzőlistát kell átlépnie:
* Ellenőrizze az erőforrások CPU-kihasználtságát, és ellenőrizze, hogy a példányok valóban kifogástalan állapotban vannak-e
  * Ezt a következőt tekintheti meg 
* Ha HTTP-vagy HTTPS-mintavételt használ, ellenőrizze, hogy az alkalmazás kifogástalan és rugalmas-e
  * Annak ellenőrzése, hogy az alkalmazás közvetlenül elérhető-e a háttér-példányhoz társított magánhálózati IP-cím vagy példány-szintű nyilvános IP-cím használatával.
* Tekintse át a háttér-erőforrásokra alkalmazott hálózati biztonsági csoportokat. Győződjön meg arról, hogy nincsenek magasabb prioritású szabályok, mint a AllowAzureLoadBalancerInBound, amely letiltja az állapot-mintavételt
  * Ezt úgy teheti meg, hogy felkeresi a háttérbeli virtuális gépek hálózati paneljét vagy Virtual Machine Scale Sets
  * Ha ezt a NSG problémát tapasztalja, helyezze át a meglévő engedélyezési szabályt, vagy hozzon létre egy új, magas prioritású szabályt a AzureLoadBalancer-forgalom engedélyezéséhez
* Keresse meg az operációs rendszert. Győződjön meg arról, hogy a virtuális gépek figyelik a mintavételi portot, és tekintse át az operációs rendszerük tűzfalának szabályait annak biztosítása érdekében, hogy ne blokkolja az IP-168.63.129.16 származó mintavételi forgalmat.
  * Megtekintheti a figyelési portokat a netstat-a Windows-parancssor vagy a netstat-l Linux-terminálon való futtatásával
* Ne helyezzen tűzfal-NVA virtuális gépet a terheléselosztó backend-készletében, a [felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md#user-defined) használatával irányítsa át a forgalmat a háttérbeli példányokra a tűzfalon keresztül.
* Győződjön meg arról, hogy a megfelelő protokollt használja, ha a HTTP használatával próbál meg egy nem HTTP-alkalmazáshoz figyelő portot használni, a mintavétel sikertelen lesz.

Ha már elvégezte ezt az ellenőrzőlistát, és még mindig észlelte az állapot-mintavételi hibákat, előfordulhat, hogy ritka platform-problémák léptek fel a mintavételi szolgáltatásban a példányok esetében. Ebben az esetben az Azure visszatért, és automatikusan riasztást küld a csapatnak, hogy gyorsan megoldja az összes platformmal kapcsolatos problémát.

## <a name="next-steps"></a>Következő lépések

* [További információ a Azure Load Balancer Health mintavételről](load-balancer-custom-probe-overview.md)
* [További információ a Azure Load Balancer mérőszámokról](load-balancer-standard-diagnostics.md)
