---
title: Network Performance Monitor megoldás az Azure Log Analyticsban | Microsoft Docs
description: A Network Performance Monitor ExpressRoute-figyelő funkciójának használatával figyelheti a végpontok közötti kapcsolatot és a teljesítményt a fiókirodák és az Azure között az Azure ExpressRoute-en keresztül.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 0b005e46e9f9a740fee153e063bebf108c22b303
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725696"
---
# <a name="expressroute-monitor"></a>ExpressRoute-figyelő

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitorról az](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

A [Network Performance monitor](network-performance-monitor.md) Azure ExpressRoute monitor funkciójának használatával megfigyelheti a végpontok közötti kapcsolatot és a teljesítményt a fiókirodák és az Azure között, az Azure ExpressRoute-en keresztül. A legfontosabb előnyök a következők: 

- Az előfizetéshez társított ExpressRoute-áramkörök automatikus észlelése.
- A sávszélesség-használat, a veszteségek és a késések nyomon követése az áramkörön, a ExpressRoute és az Azure Virtual Network szintjén.
- A ExpressRoute-áramkörök hálózati topológiájának felderítése.

![ExpressRoute-figyelő](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfiguráció 
A Network Performance Monitor konfigurációjának megnyitásához nyissa meg a [Network Performance monitor megoldást](network-performance-monitor.md) , és válassza a **Konfigurálás** lehetőséget.

### <a name="configure-network-security-group-rules"></a>A hálózati biztonsági csoport szabályainak konfigurálása 
Az Azure-ban a Network Performance Monitor-on keresztül történő figyelésre használt kiszolgálók esetében konfigurálja a hálózati biztonsági csoport (NSG) szabályait úgy, hogy engedélyezzék a TCP-forgalmat a Network Performance Monitor által a szintetikus tranzakciók esetében használt porton. Az alapértelmezett port a 8084. Ez a konfiguráció lehetővé teszi, hogy az Azure-beli virtuális gépeken telepített Log Analytics-ügynök kommunikáljon egy helyszíni figyelő ügynökkel. 

További információ a NSG: [hálózati biztonsági csoportok](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> A lépés folytatása előtt telepítse a helyszíni kiszolgáló ügynököt és az Azure Server Agent ügynököt, és futtassa a EnableRules.ps1 PowerShell-parancsfájlt. 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute-társítási kapcsolatok felderítése 
 
1. Válassza ki a **ExpressRoute-társak** nézetet.
2. A **felderítés most** lehetőség kiválasztásával felderítheti az Azure log Analytics-munkaterülettel társított Azure-előfizetésben található virtuális hálózatokhoz csatlakozó összes ExpressRoute.

    >[!NOTE]
    > A megoldás jelenleg csak privát ExpressRoute. 

    >[!NOTE]
    > A rendszer csak az ezzel a Log Analytics munkaterülettel társított előfizetéshez tartozó virtuális hálózatokhoz csatlakozó privát partnereket deríti fel. Ha a ExpressRoute a munkaterülethez kapcsolt előfizetésen kívüli virtuális hálózatokhoz csatlakozik, hozzon létre egy Log Analytics munkaterületet az előfizetésekben. Ezt követően a Network Performance Monitor használatával figyelheti ezeket a partnereket. 

    ![ExpressRoute-figyelő konfigurációja](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   A felderítés befejezése után a felderített magánhálózati kapcsolatok a táblázatban láthatók. Ezek a társítások figyelése kezdetben letiltott állapotban van. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>A ExpressRoute-társítási kapcsolatok figyelésének engedélyezése 

1. Válassza ki a figyelni kívánt privát társ-összevonási kapcsolatot.
2. A jobb oldali ablaktáblán jelölje be a társítás **figyelése** jelölőnégyzetet. 
3. Ha a kapcsolódáshoz szeretné létrehozni az állapotfigyelő eseményeket, válassza **az állapot figyelésének engedélyezése ehhez a** társításhoz lehetőséget. 
4. Válassza a figyelési feltételek lehetőséget. A küszöbértékek megadásával egyéni küszöbértékeket állíthat be az állapot-események generálásához. Ha a feltétel értéke a társítási kapcsolathoz megadott küszöbérték fölé esik, a rendszer egy állapottal kapcsolatos eseményt generál. 
5. Az **ügynökök hozzáadása** lehetőség kiválasztásával válassza ki azokat a figyelési ügynököket, amelyeket használni szeretne a társ-kapcsolódás figyeléséhez. Győződjön meg arról, hogy a kapcsolatok mindkét végpontján ügynököket ad hozzá. Ehhez a társításhoz csatlakoztatott virtuális hálózatban legalább egy ügynökre van szükség. Ehhez a társításhoz is szükség van legalább egy helyszíni ügynökre. 
6. A konfiguráció mentéséhez kattintson a **Mentés** gombra. 

   ![ExpressRoute-figyelési konfiguráció](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Miután engedélyezte a szabályokat, és kijelöli az értékeket és az ügynököket, várjon 30 – 60 percet, hogy az értékek feltöltve legyenek, a **ExpressRoute-figyelési** csempék pedig megjelenjenek. Amikor megjelenik a figyelés csempék, a ExpressRoute-áramköröket és a kapcsolatok erőforrásait Network Performance Monitor figyeli. 

>[!NOTE]
> Ez a funkció megbízhatóan működik az új lekérdezési nyelvre frissített munkaterületeken.

## <a name="walkthrough"></a>Útmutatás 

Az Network Performance Monitor irányítópult áttekintést nyújt a ExpressRoute-áramkörök és a társ-létesítési kapcsolatok állapotáról. 

![Network Performance Monitor irányítópult](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Áramkörök listája 

Az összes figyelt ExpressRoute-áramkör listájának megtekintéséhez válassza a ExpressRoute-áramkörök csempét. Kiválaszthat egy áramkört, megtekintheti az állapotot, a csomagok elvesztésével, a sávszélesség kihasználtságával és a késéssel kapcsolatos trend-diagramokat. A diagramok interaktívak. Kiválaszthat egy egyéni időablakot a diagramok ábrázolásához. Húzza az egeret a diagram egyik területére a nagyításhoz, és tekintse meg a részletes adatpontokat. 

![ExpressRoute-áramkörök listája](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>A veszteség, a késés és az átviteli sebesség trendjei 

A sávszélesség kihasználtsága, a késés és a veszteségek diagramjai interaktívak. Az egér-vezérlőelemek használatával a diagramok bármelyik szakaszára nagyíthat. Emellett megtekintheti a sávszélességet, a késést és az adatvesztést más intervallumok esetén is. A **műveletek** gomb bal felső részén válassza a  **dátum/idő** lehetőséget. 

![ExpressRoute késés](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Társítások listája 

Ha szeretné, hogy a virtuális hálózatok közötti összes kapcsolat összekapcsolva legyen, válassza ki a **privát társak** csempét az irányítópulton. Itt választhatja ki a virtuális hálózati kapcsolatokat, és megtekintheti az állapotát, a csomagok elvesztésének, a sávszélesség kihasználtságának és a késésnek a trend-diagramokat. 

![ExpressRoute-társítások](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Áramköri topológia 

Az áramköri topológia megtekintéséhez válassza a **topológia** csempét. Ezzel a művelettel a kijelölt kör vagy társítás topológiájának nézete látható. A topológia diagramja biztosítja a hálózat minden szegmensének késését, és a 3. rétegbeli ugrásokat a diagram egy csomópontja jelképezi. Egy ugrás kiválasztásával további részleteket talál a hop-ról. A helyszíni ugrásokat tartalmazó láthatóság szintjének növeléséhez mozgassa a csúszkát a **szűrők** területen. A csúszka balra vagy jobbra való mozgatásával növelheti vagy csökkentheti a topológiai gráfban lévő ugrások számát. Az egyes szegmensek késése látható, ami lehetővé teszi a nagy késleltetésű szegmensek gyorsabb elkülönítését a hálózaton.

![ExpressRoute-topológia](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Áramkör részletes topológiás nézete 

Ebben a nézetben láthatók a virtuális hálózati kapcsolatok. 

![ExpressRoute virtuális hálózati kapcsolatok](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnosztika 

Network Performance Monitor segítséget nyújt több áramköri kapcsolati probléma diagnosztizálásához. Az alábbi problémák némelyike alább látható.

Az értesítési kódokat láthatja, és riasztásokat állíthat be rajtuk a **LogAnalytics**-on keresztül. A **NPM diagnosztika** lapon az összes aktivált diagnosztikai üzenet leírását láthatja.

| Értesítési kód (naplók) | Leírás |
| --- | --- |
| 5501 | A ExpressRoute áramkör másodlagos kapcsolata nem haladhat át |
| 5502 | A ExpressRoute áramkör elsődleges kapcsolata nem haladhat át |
| 5503 | Nem található áramkör a munkaterülethez kapcsolt előfizetéshez | 
| 5508 | Nem sikerült megállapítani, hogy a forgalom áthalad-e az elérési utakhoz tartozó áramkör (ek) en keresztül |
| 5510 | A forgalom nem halad át a kívánt áramkörön | 
| 5511 | A forgalom nem halad át a kívánt virtuális hálózaton | 

**Az áramkör nem áll le.** Network Performance Monitor értesíti, amint a helyszíni erőforrások és az Azure-beli virtuális hálózatok közötti kapcsolat megszakad. Ez az értesítés segítséget nyújt az előjelzéses műveletek elvégzéséhez a felhasználói Eszkalációk fogadása és az állásidő csökkentése érdekében.

![A ExpressRoute áramkör nem érhető el](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**A forgalom nem áramlik a kívánt áramkörön keresztül.** Network Performance Monitor értesíti, ha a forgalom nem áramlik át a kívánt ExpressRoute áramkörön. Ez a probléma akkor fordulhat elő, ha az áramkör nem működik, és a forgalom a biztonsági mentési útvonalon halad át. Akkor is előfordulhat, ha útválasztási probléma van. Ezen információk segítségével proaktív módon kezelheti az útválasztási házirendek konfigurációs problémáit, és gondoskodhat a legoptimálisabb és biztonságos útvonal használatáról. 

 

**Az elsődleges áramkörön keresztül nem áramló forgalom.** Network Performance Monitor értesíti, ha a forgalom a másodlagos ExpressRoute áramkörön keresztül áramlik. Bár ebben az esetben nem tapasztal semmilyen kapcsolódási problémát, proaktív módon elháríthatja az elsődleges áramkörrel kapcsolatos problémákat, így jobban felkészült. 

 
![ExpressRoute forgalmi folyamat](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Csökkenés a csúcsérték kihasználtsága miatt.** A sávszélesség-kihasználtsági trendet összekapcsolhatja a késési trendtel annak megállapítása érdekében, hogy az Azure munkaterhelés-romlása a sávszélesség kihasználtságának csúcsa vagy sem. Ezt követően elvégezheti a műveleteket.

![ExpressRoute sávszélesség-kihasználtsága](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Következő lépések
[Keresési naplók](../logs/log-query-overview.md) a hálózati teljesítményadatok részletes rekordjainak megtekintéséhez.