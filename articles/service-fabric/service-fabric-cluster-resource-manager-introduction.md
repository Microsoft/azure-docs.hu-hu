---
title: A Service Fabric fürterőforrás-kezelő bemutatása
description: Ismerkedjen meg a Service Fabric fürterőforrás-kezelővel, amellyel kezelheti az alkalmazás szolgáltatásait.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75563326"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>A Service Fabric fürterőforrás-kezelő bemutatása
Az informatikai rendszerek és a online szolgáltatások hagyományosan felügyelik a konkrét fizikai vagy virtuális gépeket az adott szolgáltatásokhoz vagy rendszerekhez. A szolgáltatásokat rétegekként tervezték. A "web" és az "adat", illetve a "Storage" szintű csomag is szerepel. Az alkalmazások üzenetkezelési szinttel rendelkeznek, ahol be-és kifelé irányuló kérések, valamint a gyorsítótárazásra kijelölt gépek összessége is rendelkezésre áll. Az egyes rétegek vagy számítási feladatok egyes típusai speciális gépekhez vannak hozzárendelve: az adatbázishoz külön gépek vannak hozzárendelve, a webkiszolgálók. Ha egy adott számítási feladat miatt a gépek túl melegen futnak, akkor az adott szinthez hasonló konfigurációval rendelkező gépeket adott hozzá. A számítási feladatok azonban nem méretezhetők át könnyen – különösen az adatréteggel, amely általában nagyobb gépekkel rendelkező gépeket helyettesít. Nem nehéz. Ha egy gép nem sikerült, az általános alkalmazás egy része alacsonyabb kapacitással futott, amíg a gép vissza nem állítható. Még mindig elég egyszerű (ha nem feltétlenül jó).

Most azonban a szolgáltatás és a szoftver architektúrája megváltozott. Sokkal gyakoribb, hogy az alkalmazások kibővített kialakítást fogadtak. A tárolókkal vagy a szolgáltatásokkal (vagy mindkettővel) rendelkező alkalmazások közösek. Most, hogy továbbra is csak néhány gép fut, nem csupán egyetlen munkaterhelést futtatnak. Akár több különböző munkaterhelést is futtatnak egyszerre. Mostantól több tucat különböző típusú szolgáltatás létezik (a teljes gép vagyonának hiánya), akár több száz különböző példányban. Minden elnevezett példányhoz egy vagy több példány vagy replika tartozik a magas rendelkezésre álláshoz (HA). A munkaterhelések méretétől függően, és hogy mennyire elfoglaltak, több száz vagy akár több ezer gépet is megtalálhat. 

A környezet hirtelen kezelése nem olyan egyszerű, mint néhány, egyetlen típusú számítási feladathoz rendelt gép kezelése. A kiszolgálók virtuálisak, és már nem rendelkeznek nevekkel (az összesre kiváltott a [háziállatok](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) és a szarvasmarhák közötti gondolkodás). A konfiguráció kevésbé fontos a gépekről és a szolgáltatásokról. A számítási feladatok egyetlen példánya számára kijelölt hardverek nagyrészt a múltban is megtalálhatók. A szolgáltatások olyan kisméretű elosztott rendszerek lettek, amelyek több kisebb mennyiségű termékre terjednek ki.

Mivel az alkalmazás már nem a monolitok több rétegre kiterjedő sorozata, mostantól számos további kombinációval találkozhat. Ki dönti el, hogy milyen típusú számítási feladatok futtathatók a hardveren, vagy hány? Mely számítási feladatok működnek jól ugyanazon a hardveren, és milyen ütközést okoznak? Ha a gép leáll, Honnan tudhatja, hogy mi fut ott ezen a gépen? Kinek kell meggyőződnie arról, hogy a számítási feladat újra elindul? Megvárja, hogy a (virtuális?) számítógép visszakerüljön, vagy a számítási feladatok automatikusan átadják a feladatokat más gépeknek, és továbbra is futnak? Emberi beavatkozásra van szükség? Mi a helyzet a frissítésekkel ebben a környezetben?

Az ebben a környezetben dolgozó fejlesztőknek és kezelőknek segítségre lesz szükségük az összetettség kezeléséhez. A feldolgozói binge és az emberek összetettségének megszüntetése valószínűleg nem a megfelelő válasz, tehát mi a teendőnk?

## <a name="introducing-orchestrators"></a>A hangszerelók bemutatása
A "Orchestrator" egy olyan szoftver általános kifejezése, amely segít a rendszergazdáknak az ilyen típusú környezetek kezelésében. A rendszerindító a kérelmekben szereplő összetevők, például "szeretném, ha a szolgáltatás öt példányban fut a saját környezetben". Megpróbálják, hogy a környezet megfeleljen a kívánt állapotnak, függetlenül attól, hogy mi történik.

A rendszerszervezők (nem emberi), mi a teendő, ha egy gép meghibásodik, vagy a munkaterhelés valamilyen váratlan ok miatt leáll. A legtöbb hangszerelő nem csupán a hibákkal foglalkozik. Más szolgáltatások, amelyek új központi telepítéseket kezelnek, frissítéseket kezelnek, és kezelik az erőforrások felhasználását és irányítását. Az összes szervező alapvetően a konfiguráció bizonyos kívánt állapotának fenntartásához szükséges a környezetben. Szeretné tudni, hogy milyen Orchestrator van szüksége, és hogy a nagy mennyiségű emelést végezze el. Az Aurora a Mezos, a Docker Datacenter, a Docker Swarm, a Kubernetes és a Service Fabric. A rendszer aktívan fejleszti az üzemi környezetekben található valós számítási feladatok igényét. 

## <a name="orchestration-as-a-service"></a>Szolgáltatásként való előkészítés
A fürterőforrás-kezelő a rendszerösszetevő, amely kezeli a Service Fabric. A fürterőforrás-kezelő feladata három részre oszlik:

1. Szabályok érvényesítése
2. A környezet optimalizálása
3. Más folyamatokkal való segítségnyújtás

### <a name="what-it-isnt"></a>Mi nem
A hagyományos N szintű alkalmazásokban mindig van egy [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Ez általában hálózati Load Balancer (NLB) vagy egy alkalmazás-Load Balancer (ALB), attól függően, hogy hol ült a hálózati veremben. Néhány terheléselosztó olyan hardver-alapú, mint a F5's BigIP-ajánlat, mások olyan szoftverek, mint a Microsoft hálózati terheléselosztása. Más környezetekben például HAProxy, Nginx, Istio vagy megbízottat lehet látni ebben a szerepkörben. Ezekben az architektúrákban a terheléselosztás feladata, hogy biztosítsa az állapot nélküli munkaterhelések fogadását (nagyjából) ugyanazon mennyiségű munkát. A terhelés kiegyensúlyozására szolgáló stratégiák eltérőek. Egyes balancerek minden hívást egy másik kiszolgálóra küldenek. Mások a munkamenetek rögzítését/stickiét adták meg. A fejlettebb balancerek tényleges terhelési becsléssel vagy jelentéssel irányítják a hívást a várt és a gép aktuális terhelése alapján.

A hálózati balancerek vagy az üzenetküldési útválasztók megpróbálták biztosítani, hogy a webes/feldolgozói szintek nagyjából egyensúlyban maradnak. Az adatszint kiegyensúlyozására szolgáló stratégiák különbözőek voltak, és az adattárolási mechanizmustól függnek. Az adatréteg terheléselosztása az adathorizontálisan, a gyorsítótárazás, a felügyelt nézetek, a tárolt eljárások és az egyéb tároló-specifikus mechanizmusok alapján történik.

Néhány ilyen stratégia érdekes, de a Service Fabric fürterőforrás-kezelő nem minden más, mint a hálózati terheléselosztó vagy a gyorsítótár. A hálózati Load Balancer a rendszerfelületek közötti adatforgalom elosztásával egyensúlyt teremt. A Service Fabric fürterőforrás-kezelő más stratégiát tartalmaz. Alapvetően Service Fabric helyezi át a *szolgáltatásokat* , hogy a lehető legalkalmasabbak legyenek, és a forgalom vagy a terhelés várható legyen. Előfordulhat például, hogy a szolgáltatásokat olyan csomópontokra helyezi át, amelyek jelenleg túl hidegek, mert a nem sokat működnek a szolgáltatások. Előfordulhat, hogy a csomópontok túl hidegek, mert a jelen lévő szolgáltatások máshol lettek törölve vagy áthelyezve. Egy másik példaként a fürterőforrás-kezelő el is helyezhet egy szolgáltatást a gépről. Lehet, hogy a gép frissítése folyamatban van, vagy túlterhelt, mert a rajta futó szolgáltatások által felhasznált csúcs van. Másik lehetőségként előfordulhat, hogy a szolgáltatás erőforrás-követelményei megnőttek. Ennek eredményeképpen a gépen nem áll rendelkezésre elegendő erőforrás a Futtatás folytatásához. 

Mivel a fürterőforrás-kezelő felelős a szolgáltatások áthelyezéséhez, egy másik szolgáltatáskészlet-készletet tartalmaz, mint amit a hálózati terheléselosztó talál. Ennek az az oka, hogy a hálózati terheléselosztó olyan hálózati forgalmat biztosít, ahol a szolgáltatások már léteznek, még akkor is, ha az adott hely nem ideális a szolgáltatás futtatására. A Service Fabric fürterőforrás-kezelője alapvetően különböző stratégiákat alkalmaz, amelyekkel biztosítható, hogy a fürt erőforrásai hatékonyan legyenek kihasználva.

## <a name="next-steps"></a>Következő lépések
- A fürterőforrás-kezelő architektúrájának és információinak folyamatával kapcsolatos információkért tekintse meg [ezt a cikket.](service-fabric-cluster-resource-manager-architecture.md)
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni a metrikákkal kapcsolatban, tekintse meg ezt a cikket a [Service Fabric-fürt leírásában](service-fabric-cluster-resource-manager-cluster-description.md)
- A szolgáltatások konfigurálásával kapcsolatos további információkért [tekintse meg a szolgáltatások konfigurálását](service-fabric-cluster-resource-manager-configure-services.md) ismertető témakört.
- A metrikák azt jelentik, hogyan kezeli a Service Fabric fürterőforrás-kezelő a fürtben a felhasználást és a kapacitást. A metrikákkal és azok konfigurálásával kapcsolatos további információkért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-metrics.md)
- A fürterőforrás-kezelő a Service Fabric felügyeleti képességeivel működik. Ha többet szeretne megtudni az adott integrációról, olvassa el [ezt a cikket](service-fabric-cluster-resource-manager-management-integration.md)
- Ha szeretné megtudni, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [terhelés kiegyensúlyozásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
