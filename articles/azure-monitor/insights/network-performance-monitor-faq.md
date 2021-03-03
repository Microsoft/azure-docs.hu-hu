---
title: Gyakori kérdések – Network Performance Monitor megoldás az Azure-ban | Microsoft Docs
description: Ez a cikk az Azure-Network Performance Monitorokkal kapcsolatos gyakori kérdéseket rögzíti. Network Performance Monitor (NPM) segít a hálózatok teljesítményének közel valós idejű figyelésében, valamint a hálózati teljesítmény szűk keresztmetszetének észlelésében és megkeresésében.
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 909cbd6174fe7eceaa8b53b5ba44fe72990b56d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708050"
---
# <a name="network-performance-monitor-solution-faq"></a>Network Performance Monitor megoldás – gyakori kérdések

![Network Performance Monitor szimbólum](media/network-performance-monitor-faq/npm-symbol.png)

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitorról az](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

Ez a cikk az Azure-beli Network Performance Monitor (NPM) kapcsolatos gyakori kérdéseket (GYIK) rögzíti

A [Network Performance monitor](../../networking/network-monitoring-overview.md) egy felhőalapú [hibrid hálózati figyelési](./network-performance-monitor-performance-monitor.md) megoldás, amely a hálózati infrastruktúra különböző pontjai közötti hálózati teljesítmény figyelését segíti. Emellett a [szolgáltatás-és alkalmazás-végpontok](./network-performance-monitor-service-connectivity.md) hálózati kapcsolatának figyelésére és [Az Azure-ExpressRoute teljesítményének figyelésére](./network-performance-monitor-expressroute.md)is lehetőséget nyújt. 

A Network Performance Monitor észleli a hálózati problémákat, például a forgalmi blackholing, az útválasztási hibákat, valamint azokat a problémákat, amelyeket a hagyományos hálózati figyelési módszerek nem képesek észlelni. A megoldás riasztásokat készít, és értesíti, amikor egy hálózati kapcsolat meghaladja a küszöbértéket. Emellett biztosítja a hálózat teljesítményével kapcsolatos problémák időbeni észlelését és leszűkíti a hiba forrásának helyszínét egy hálózati szegmensre vagy eszközre. 

További információ a [Network Performance monitor](../../networking/network-monitoring-overview.md) által támogatott különböző képességekről online érhető el.

## <a name="set-up-and-configure-agents"></a>Ügynökök beállítása és konfigurálása

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Milyen platformokra vonatkozó követelmények vonatkoznak a NPM figyeléséhez használt csomópontokra?
Az alábbiakban az NPM különböző képességeire vonatkozó platformokra vonatkozó követelmények láthatók:

- A NPM Teljesítményfigyelő és szolgáltatás-csatlakozási figyelő képességei a Windows Servert és a Windows asztali számítógépeket, illetve az ügyfél operációs rendszereit is támogatják. A Windows Server operációs rendszer támogatott verziói a 2008 SP1 vagy újabb verzió. A Windows rendszerű asztali számítógépek/ügyfél-verziók a következők: Windows 10, Windows 8,1, Windows 8 és Windows 7. 
- A NPM ExpressRoute-figyelő funkciója csak a Windows Server (2008 SP1 vagy újabb) operációs rendszert támogatja.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Használhatok Linux rendszerű gépeket figyelési csomópontként a NPM-ben?
A hálózatok a Linux-alapú csomópontok használatával történő figyelésének képessége már általánosan elérhető. [Itt](../../virtual-machines/extensions/oms-linux.md)érheti el az ügynököt. 

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Mik a NPM által a figyeléshez használt csomópontok méretére vonatkozó követelmények?
Ahhoz, hogy a NPM megoldás a csomópontokon futó virtuális gépeken a hálózatok figyelésére fusson, a csomópontoknak legalább 500 MB memóriával és egy mag kell rendelkezniük. Nem kell külön csomópontokat használnia a NPM futtatásához. A megoldás olyan csomópontokon is futhat, amelyeken más munkaterhelések futnak. A megoldás képes a figyelési folyamat leállítására, ha az több mint 5%-os CPU-t használ.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>A NPM használatához csatlakoztatni kell a saját csomópontokat közvetlen ügynökként vagy System Center Operations Manageron keresztül?
A Teljesítményfigyelő és a szolgáltatás-csatlakozási figyelő funkciói támogatják a [közvetlen ügynökként csatlakoztatott](../agents/agent-windows.md) csomópontokat, és [Operations Manageron keresztül csatlakoznak](../agents/om-agents.md).

Az ExpressRoute-figyelési funkció esetében az Azure-csomópontokat csak közvetlen ügynökként kell csatlakoztatni. Az Operations Manageron keresztül csatlakoztatott Azure-csomópontok nem támogatottak. A helyszíni csomópontok esetében a közvetlen ügynökként és Operations Manager-n keresztül csatlakozó csomópontok ExpressRoute-áramkör figyelésére is használhatók.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Melyik protokollt kell választani a TCP és az ICMP között a figyeléshez?
Ha Windows Server-alapú csomópontokkal figyeli a hálózatot, javasoljuk, hogy a TCP protokollt használja figyelési protokollként, mivel ez nagyobb pontosságot biztosít. 

Az ICMP használata a Windows rendszerű asztali számítógépekhez és az ügyfél operációs rendszer alapú csomópontjaihoz ajánlott. Ez a platform nem teszi lehetővé, hogy a TCP-adat a nyers szoftvercsatornán legyen elküldve, amelyet a NPM a hálózati topológia felderítésére használ.

Az egyes protokollok relatív előnyeiről [itt](./network-performance-monitor-performance-monitor.md#choose-the-protocol)talál további információt.

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hogyan állíthatok be egy csomópontot a figyelés támogatásához a TCP protokoll használatával?
Ahhoz, hogy a csomópont támogassa a figyelést a TCP protokoll használatával: 
* Győződjön meg arról, hogy a csomópont platformja Windows Server (2008 SP1 vagy újabb).
* Futtassa [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell-parancsfájlt a csomóponton. További részletekért tekintse meg az [utasításokat](./network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) .


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hogyan változtathatom meg a NPM által használt TCP-portot a figyeléshez?
A NPM által a figyeléshez használt TCP-portot a [EnableRules.ps1](https://aka.ms/npmpowershellscript) parancsfájl futtatásával módosíthatja. Meg kell adnia a paraméterként használni kívánt portszámot. Ha például engedélyezni szeretné a TCP-t a 8060-as porton, futtassa a parancsot `EnableRules.ps1 8060` . Győződjön meg arról, hogy ugyanazt a TCP-portot használja a figyeléshez használt összes csomóponton.

A parancsfájl csak a Windows tűzfalat konfigurálja helyileg. Ha a hálózati tűzfal vagy a hálózati biztonsági csoport (NSG) szabályai vannak, győződjön meg arról, hogy engedélyezik a NPM által használt TCP-portra irányuló forgalmat.

### <a name="how-many-agents-should-i-use"></a>Hány ügynököt használok?
Minden figyelni kívánt alhálózathoz legalább egy ügynököt kell használnia.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Legfeljebb hány ügynököt használhatok, vagy hibaüzenetet látok. elérte a konfigurációs korlátot?
A NPM az IP-címek számát 5000 IP-címekre korlátozza munkaterületen. Ha egy csomópont IPv4-és IPv6-címeket is tartalmaz, akkor ez a csomópont 2 IP-címének számít. Ezért a 5000 IP-cím korlátja határozza meg az ügynökök számának felső határát. Törölheti az inaktív ügynököket a NPM csomópontok lapján >> a configure (Konfigurálás) lehetőségre. A NPM az összes olyan IP-cím előzményeit is fenntartja, amely az ügynököt futtató virtuális géphez lett társítva, és mindegyik külön IP-címmel van elfoglalva, amely a felső határ 5000 IP-címeihez járul hozzá. A munkaterülethez tartozó IP-címek felszabadításához használhatja a csomópontok lapot a nem használt IP-címek törléséhez.

## <a name="monitoring"></a>Figyelés

### <a name="how-are-loss-and-latency-calculated"></a>A veszteségek és a késések kiszámítása
A forrásoldali ügynökök TCP SYN-kérelmeket küldenek (ha a TCP protokoll figyelésre van kiválasztva) vagy ICMP ECHO-kéréseket (ha az ICMP protokoll figyelésre van kiválasztva) a cél IP-címhez rendszeres időközönként, így biztosítva, hogy a forrás-cél IP-kombinációhoz tartozó összes útvonal le legyen fedve. Az egyes elérési utak elvesztésének és késésének kiszámításához a fogadott csomagok százalékos arányát és az oda-és bejárási időt kell mérni. Ezeket az adatokat a lekérdezési időköz és az összes elérési út alapján összesíti a rendszer az adott lekérdezési időközhöz tartozó IP-kombinációhoz tartozó veszteségek és késések összesített értékeinek beszerzéséhez.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Milyen gyakorisággal küldi el a forrás ügynök a csomagokat a figyelés céljára?
A Teljesítményfigyelő és a ExpressRoute figyelési képességei esetében a forrás 5 másodpercenként küld csomagokat, és rögzíti a hálózati méréseket. Ezeket az adatokat 3 perces lekérdezési intervallumban összesítjük a veszteségek és a késések átlagos és maximális értékeinek kiszámításához. A szolgáltatás-kapcsolódási figyelő képességhez a csomagok hálózati mérésre való küldésének gyakoriságát a felhasználó által az adott teszthez megadott gyakoriság határozza meg a teszt konfigurálása során.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hány csomagot küld a rendszer a figyeléshez?
A forrás-ügynök által a lekérdezésben a célhelyre küldött csomagok száma adaptív, és a tulajdonosi algoritmus határozza meg, amely különböző hálózati topológiák esetében eltérő lehet. A forrás – cél IP-kombináció közötti hálózati elérési utak száma több, mint a küldött csomagok száma. A rendszer biztosítja, hogy a forrás – cél IP-kombináció közötti összes útvonal le legyen fedve.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hogyan észleli a NPM a hálózati topológiát a forrás és a cél között?
A NPM a traceroute alapján szabadalmaztatott algoritmust használ a forrás és a cél közötti összes útvonal és ugrás felderítésére.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>A NPM biztosítja az útválasztási és a váltási szintű adatokat 
Bár a NPM képes észlelni az összes lehetséges útvonalat a forrás-ügynök és a cél között, nem biztosítja, hogy az adott munkaterhelések által küldött csomagok milyen útvonalat hoztak létre. A megoldás segítségével azonosíthatja az elérési utakat és a mögöttes hálózati ugrásokat, amelyek a vártnál több késést adnak hozzá.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Miért nem megfelelő az elérési utak némelyike?
Különböző hálózati elérési utak létezhetnek a forrás és a cél IP-címei között, és minden elérési út eltérő lehet a veszteségek és a késések értékével. A NPM a nem kifogástalan állapotú (piros színnel jelölt) elérési utakat jelzi, hogy a veszteségek és/vagy késések értéke nagyobb legyen, mint a figyelési konfigurációban beállított megfelelő küszöbérték.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Mit jelent a piros színű Ugrás a hálózati topológia térképen?
Ha egy ugrás vörös színű, azt jelenti, hogy az egy nem megfelelő állapotú elérési út része. A NPM csak a nem kifogástalan állapotú elérési utakat jelöli meg, nem különíti el az egyes elérési utak állapotát. A zavaró ugrások azonosításához megtekintheti az ugrások közötti késést, és elkülönítheti azokat, amelyek a vártnál több időt vesznek igénybe.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hogyan működik a teljesítményproblémák lokalizálása a Teljesítményfigyelőben?
A NPM egy valószínűségi mechanizmust használ a meghibásodási valószínűségek az egyes hálózati elérési utakhoz, hálózati szegmensekhez és az összetevők hálózati ugrásokhoz való hozzárendeléséhez azon nem kifogástalan elérési utak száma alapján, amelyek részét képezik. Mivel a hálózati szegmensek és a komló több nem megfelelő állapotú elérési út részévé válik, a hozzájuk tartozó hibák valószínűsége növekszik. Ez az algoritmus akkor működik a legjobban, ha sok olyan csomóponttal rendelkezik, amelynek NPM-ügynöke csatlakozik egymáshoz, mivel ez növeli az adatpontokat a hiba valószínűségének kiszámításához.

### <a name="how-can-i-create-alerts-in-npm"></a>Hogyan hozhatok létre riasztásokat a NPM-ben?
Jelenleg a NPM felhasználói felületéről érkező riasztások létrehozása egy ismert probléma miatt meghiúsul. [Manuálisan hozzon létre riasztásokat](../alerts/alerts-log.md).

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>A riasztások alapértelmezett Log Analytics lekérdezései
Teljesítményfigyelő lekérdezése

```kusto
NetworkMonitoring
 | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
```

Szolgáltatás-kapcsolódási figyelő lekérdezése

```kusto
NetworkMonitoring
 | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
```

ExpressRoute-figyelő lekérdezések: áramkörök lekérdezése

```kusto
NetworkMonitoring
 | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"
```

Magánhálózati társviszony-létesítés

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"
```

Microsoftos társhálózat-létesítés

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"
```

Gyakori lekérdezés

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy")
```

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Az útválasztók és a kiszolgálók önálló eszközökként is NPM?
A NPM csak az alapul szolgáló hálózati ugrások (kapcsolók, útválasztók, kiszolgálók stb.) IP-címét és állomásnevét azonosítja a forrás és a cél IP-címei között. Emellett meghatározza az azonosított ugrások közötti késést is. Nem figyeli külön a mögöttes ugrásokat.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Használható NPM az Azure és az AWS közötti hálózati kapcsolat figyelésére?
Igen. A részletekért tekintse meg az [Azure, az AWS és a helyszíni hálózatok figyelése a NPM használatával című](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor) cikket.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>A ExpressRoute sávszélesség-használat bejövő vagy kimenő?
A sávszélesség-használat a bejövő és a kimenő sávszélesség teljes száma. Ez a bit/mp értékben van kifejezve.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Lekérhetjük a bejövő és kimenő sávszélességre vonatkozó információkat a ExpressRoute?
Az elsődleges és a másodlagos sávszélesség bejövő és kimenő értékeit is rögzítheti.

MS-társi szintű információk esetében használja az alábbi lekérdezést a naplóbeli keresésben

```kusto
NetworkMonitoring
 | where SubType == "ERMSPeeringUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond 
```

A privát peering szintű információkhoz használja az alábbi lekérdezést a naplóbeli keresésben

```kusto
NetworkMonitoring
 | where SubType == "ERVNetConnectionUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond
```

Az áramköri szintű információk esetében használja az alábbi lekérdezést a naplóbeli keresésben

```kusto
NetworkMonitoring
 | where SubType == "ERCircuitTotalUtilization"
 | project CircuitName, BitsInPerSecond, BitsOutPerSecond
```

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Mely régiók támogatottak a NPM teljesítményének monitorozásához?
A NPM a világ bármely részén lévő hálózatok közötti kapcsolat figyelésére a [támogatott régiók](./network-performance-monitor.md#supported-regions) egyikében üzemeltetett munkaterületről

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Mely régiók támogatottak a NPM szolgáltatás kapcsolódási figyelője számára?
A NPM a világ bármely részén a szolgáltatásokhoz való kapcsolódást a [támogatott régiók](./network-performance-monitor.md#supported-regions) egyikében üzemeltetett munkaterületről tudja figyelni.

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Mely régiók támogatottak a NPM ExpressRoute-figyelője számára?
A NPM bármely Azure-régióban található ExpressRoute-áramköröket nyomon követheti. A NPM való bevezetéshez Log Analytics munkaterületre lesz szükség, amelyet a [támogatott régiók](../../expressroute/how-to-npm.md) egyikében kell üzemeltetni.

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Miért nem azonosított ugrások vannak megjelölve a hálózati topológia nézetben?
A NPM a traceroute módosított verzióját használja a forrás ügynök és a cél közötti topológia felderítésére. Az azonosítatlan ugrás azt jelenti, hogy a hálózati ugrás nem válaszolt a forrásoldali ügynök traceroute-kérelmére. Ha három egymást követő hálózati ugrás nem válaszol az ügynök traceroute-re, a megoldás megjelöli a nem válaszoló ugrásokat azonosítatlan, és nem próbál további ugrásokat felderíteni.

Előfordulhat, hogy egy ugrás az alábbi forgatókönyvek közül egy vagy több esetében nem válaszol a traceroute-re:

* Az útválasztók úgy lettek konfigurálva, hogy ne tárják fel identitásukat.
* A hálózati eszközök nem engedélyezik ICMP_TTL_EXCEEDED forgalmat.
* A tűzfal blokkolja a ICMP_TTL_EXCEEDED választ a hálózati eszközről.

Ha a végpontok egyike az Azure-ban található, a traceroute azonosítatlan ugrásokat jelenít meg, mivel az Azure-infrastruktúra nem fedi fel az identitást a traceroute számára. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Riasztásokat kapok a nem kifogástalan állapotú tesztekhez, de nem látok magas értékeket a NPM elvesztése és késési gráfjában. Hogyan a nem megfelelő állapotú elemek ellenőrzését?
A NPM riasztást küld, ha a forrás és a cél között a végpontok közötti késés a közöttük lévő bármely útvonal küszöbértékét keresztezi. Egyes hálózatok több elérési úttal rendelkeznek, amelyek ugyanahhoz a forráshoz és célhoz kapcsolódnak. A NPM egy riasztást vált ki, a sérült elérési út nem megfelelő. A gráfokban látható veszteség és késés az összes útvonal átlagos értéke, ezért előfordulhat, hogy nem jeleníti meg egyetlen útvonal pontos értékét. Annak megismeréséhez, hogy a küszöbérték hogyan lett megszegve, keresse meg a riasztás "altípus" oszlopát. Ha a problémát egy elérési út okozta, az altípus értéke NetworkPath lesz (a Teljesítményfigyelő tesztek esetében), a EndpointPath (a Service connectivity monitor tesztek esetében) és a ExpressRoutePath (ExpressRotue-figyelő tesztek esetében). 

A keresendő lekérdezési útvonal sérült:

```kusto
NetworkMonitoring
 | where ( SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
 | project SubType, LossHealthState, LatencyHealthState, MedianLatency
```

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Miért jelenik meg a teszt állapota, de a topológia nem 
A NPM különböző időközönként figyeli a végpontok közötti adatvesztést, a késést és a topológiát. A veszteséget és a késést 5 másodpercenként, és három percenként összesítjük (a Teljesítményfigyelő és az Express Route monitor esetében), míg a topológia kiszámítása 10 percenként egyszer a traceroute használatával történik. Például 3:44 és 4:04 között a topológia háromszor frissíthető (3:44, 3:54, 4:04), a veszteséget és a késést azonban hét alkalommal frissítik (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). A 3:54-at generált topológia a 3:56, 3:59 és 4:02 értéknél kiszámított veszteséget és késést fogja megjeleníteni. Tegyük fel, hogy riasztást kap arról, hogy az ER-áramkör állapota 3:59. Jelentkezzen be a NPM-be, és próbálja meg beállítani a topológiai időt 3:59-re. A NPM a 3:54-kor generált topológiát fogja megjeleníteni. A hálózat utolsó ismert topológiájának megismeréséhez hasonlítsa össze a TimeProcessed (a veszteségek és a késések kiszámításának időpontját) és a TracerouteCompletedTime (a topológia kiszámításának időpontját). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Mi a különbség a NetworkMonitoring tábla E2EMedianLatency és AvgHopLatencyList mezői között
A E2EMedianLatency a TCP ping tesztek eredményeinek összesítése után 3 percenként frissülő késés, míg a AvgHopLatencyList a traceroute alapján 10 percenként frissül. A E2EMedianLatency kiszámításának pontos időpontjának megismeréséhez használja a TimeProcessed mezőt. Ha szeretné megismerni, hogy a traceroute hogyan fejeződött be és frissítették a AvgHopLatencyList, használja a TracerouteCompletedTime mezőt

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Miért különböznek a HopLatencyValues a hop-by-hop késési számoktól 
A HopLatencyValues forrása a végpont.
Például: ugrások – A, B, C. AvgHopLatency-10, 15, 20. Ez azt jelenti, hogy A forrás a késés = 10, a forrás – B késés = 15, a forrás – C késés pedig 20. A felhasználói felület a-B ugrások késését 5-ként számítja ki a topológiában.

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A megoldás 100%-os csökkenést mutat, de kapcsolat van a forrás és a cél között.
Ez akkor fordulhat elő, ha a gazdagép tűzfala vagy a köztes tűzfal (hálózati tűzfal vagy Azure NSG) blokkolja a forrás-ügynök és a cél közötti kommunikációt a NPM figyeléséhez használt porton (alapértelmezés szerint a port 8084, ha az ügyfél nem módosította ezt).

* Annak ellenőrzéséhez, hogy a gazdagép tűzfala nem blokkolja-e a kommunikációt a szükséges porton, tekintse meg a forrás és a cél csomópontok állapotát a következő nézetből: Network Performance Monitor-> Configuration-> csomópontok. 
  Ha nem kifogástalan állapotú, tekintse meg az utasításokat, és végezze el a megfelelő lépéseket. Ha a csomópontok állapota Kifogástalan, lépjen a b lépésre. az alábbiakban.
* Annak ellenőrzéséhez, hogy a közbenső hálózati tűzfal vagy az Azure NSG nem blokkolja-e a szükséges porton keresztüli kommunikációt, használja a külső gyártótól származó PsPing segédprogramot az alábbi utasítások használatával:
  * a psping segédprogram letölthető [innen](/sysinternals/downloads/psping) 
  * Futtassa a következő parancsot a forrás csomópontról.
    * psping-n 15 \<destination node IPAddress\> :P ortnumber alapértelmezett NPM 8084 portot használ. Ha explicit módon módosította ezt a EnableRules.ps1 parancsfájl használatával, adja meg az Ön által használt egyéni portszámot). Ez a ping az Azure-gépről a helyi környezetbe
* Ellenőrizze, hogy a pingek sikeresek-e. Ha nem, akkor azt jelzi, hogy egy közbenső hálózati tűzfal vagy az Azure NSG blokkolja a port forgalmát.
* Most futtassa a parancsot a cél csomópontról a forrás csomópont IP-címére.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Az A csomópont veszteséget okoz, de a B csomópontból nem.
Mivel az A és B közötti hálózati elérési utak a B és A közötti hálózati útvonalaktól eltérőek lehetnek, a veszteségek és késések különböző értékei figyelhetők meg.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Miért nem észlelhetők a ExpressRoute-áramkörök és a társ-összekapcsolási kapcsolatok?
A NPM most felfedi a ExpressRoute-áramköröket és az összes olyan előfizetésben lévő kapcsolatot, amelyhez a felhasználónak hozzáférése van. Válassza ki az összes olyan előfizetést, ahol az expressz útvonal-erőforrások össze vannak kapcsolva, és engedélyezze a figyelést minden felderített erőforráshoz A NPM megkeresi a kapcsolatok objektumait, amikor felvesz egy privát társítást, ezért ellenőrizze, hogy van-e társítva VNET a társával. A NPM nem ismeri fel az olyan áramköröket és társításokat, amelyek egy másik bérlőn találhatók a Log Analytics munkaterületen.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Az ER-figyelő funkció diagnosztikai üzenettel rendelkezik, "a forgalom nem halad át semmilyen áramkörön". Ez mit jelent?

Előfordulhat, hogy a helyszíni és az Azure-csomópontok közötti kapcsolat kifogástalan, de a forgalom nem halad át a NPM által figyelt ExpressRoute-áramkörön. 

Ez a következő esetekben fordulhat elő:

* Az ER áramkör nem érhető el.
* Az útválasztási szűrők úgy vannak konfigurálva, hogy elsőbbséget biztosítanak más útvonalaknak (például egy VPN-kapcsolatnak vagy egy másik ExpressRoute áramkörnek) a kívánt ExpressRoute áramkörön. 
* A ExpressRoute áramkör figyelési konfigurációban való figyelésére kiválasztott helyszíni és Azure-csomópontok nem rendelkeznek kapcsolattal egymással a kívánt ExpressRoute áramkörön keresztül. Győződjön meg arról, hogy a figyelni kívánt ExpressRoute-áramkörön a megfelelő csomópontok vannak kiválasztva.

### <a name="why-does-expressroute-monitor-report-my-circuitpeering-as-unhealthy-when-it-is-available-and-passing-data"></a>Miért jelent a ExpressRoute-figyelő a saját áramkörét/társát a nem kifogástalan állapotú, amikor az elérhető, és továbbítja az adattovábbítást.
A ExpressRoute-figyelő összehasonlítja az ügynökök/szolgáltatás által jelentett hálózati teljesítménnyel kapcsolatos értékeket (veszteségek, késések és sávszélesség-kihasználtság) a konfiguráció során beállított küszöbértékekkel. Ha egy áramkör esetében a jelentett sávszélesség meghaladja a konfigurációban beállított küszöbértéket, az áramkör nem kifogástalan állapotú van megjelölve. A társítások esetében, ha a jelentett veszteség, késés vagy sávszélesség kihasználtsága nagyobb, mint a konfigurációban beállított küszöbérték, a társítás sérültként van megjelölve. A NPM nem használ mérőszámokat vagy bármilyen más adattípust az állapot eldöntéséhez.

### <a name="why-does-expressroute-monitorbandwidth-utilization-report-a-value-different-from-metrics-bits-inout"></a>Miért jelent a ExpressRoute Monitor'bandwidth-kihasználtsága olyan értéket, amely eltér a metrikai bitek és a kimenő értékek között
A ExpressRoute-figyelő esetében a sávszélesség-kihasználtság az elmúlt 20 percben a bejövő és a kimenő sávszélesség átlaga, amelyet a rendszer bit/mp-ben fejez ki. Az expressz útvonal metrikái esetében a bit be/ki érték percenkénti adatpontok. A mindkettőhöz használt adatkészlet belsőleg azonos, de az Összesítés a NPM és az ER mérőszámok között változik. A szemcsés, a percenkénti figyelés és a gyors riasztások esetében javasoljuk, hogy a riasztásokat közvetlenül az ER mérőszámokon állítsa be.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>A ExpressRoute-áramkör figyelésének konfigurálása közben az Azure-csomópontok nem észlelhetők.
Ez akkor fordulhat elő, ha az Azure-csomópontok Operations Manageron keresztül csatlakoznak. A ExpressRoute-figyelő funkció csak azokat az Azure-csomópontokat támogatja, amelyek közvetlen ügynökként vannak csatlakoztatva.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Nem tudom felderíteni a ExpressRoute-áramköröket a OMS-portálon
Bár a NPM mind a Azure Portal, mind a OMS-portálon használható, a ExpressRoute-figyelő funkció áramkör-felderítése csak a Azure Portalon keresztül működik. Miután a rendszer felderíti az áramkört a Azure Portalon keresztül, használhatja a két portál bármelyikét. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>A szolgáltatás-kapcsolódási figyelő képességben a szolgáltatás válaszideje, a hálózati veszteség, valamint a késés a NA-ként jelenik meg.
Ez akkor fordulhat elő, ha egy vagy több igaz:

* A szolgáltatás nem érhető el.
* A szolgáltatáshoz való hálózati kapcsolat ellenőrzéséhez használt csomópont nem érhető el.
* A teszt konfigurációjában megadott cél helytelen.
* A csomópontnak nincs hálózati kapcsolata.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>A szolgáltatás-kapcsolódási figyelő funkcióban érvényes szolgáltatási válaszidő jelenik meg, de a hálózati veszteség, valamint a késés is NA-ként jelenik meg.
 Ez akkor fordulhat elő, ha egy vagy több igaz:

* Ha a szolgáltatáshoz való hálózati kapcsolat ellenőrzéséhez használt csomópont egy Windows-ügyfélszámítógép, vagy a célként megadott szolgáltatás blokkolja az ICMP-kéréseket, vagy a hálózati tűzfal blokkolja a csomóponttól származó ICMP-kérelmeket.
* A hálózati mérések elvégzése jelölőnégyzet üres a tesztelési konfigurációban.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>A szolgáltatás-kapcsolódási figyelő képességben a szolgáltatás válaszideje NA, de a hálózati veszteség, valamint a késés érvényes
Ez akkor fordulhat elő, ha a célként megadott szolgáltatás nem webalkalmazás, de a teszt webes tesztként van konfigurálva. Szerkessze a teszt konfigurációját, és válassza ki a teszt típusát a web helyett hálózatként.

## <a name="miscellaneous"></a>Különböző veszélyes anyagok és tárgyak

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Hatással van-e a figyelésre használt csomópont teljesítményére?
A NPM folyamat úgy van beállítva, hogy leálljon, ha a gazdagép CPU-erőforrásainak több mint 5%-át használja. Ezzel biztosíthatja, hogy a csomópontokat a szokásos számítási feladatokhoz a teljesítmény befolyásolása nélkül tudja használni.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>A NPM szerkesztheti a figyelési szabályokat?
A NPM csak helyi Windows tűzfal-szabályt hoz létre azon csomópontokon, amelyeken a EnableRules.ps1 PowerShell-parancsfájl fut, hogy az ügynökök TCP-kapcsolatokat hozzanak létre egymással a megadott porton. A megoldás nem módosítja a hálózati tűzfal vagy a hálózati biztonsági csoport (NSG) szabályait.

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hogyan ellenőrizhetem a figyeléshez használt csomópontok állapotát?
A figyeléshez használt csomópontok állapotát a következő nézetből tekintheti meg: Network Performance Monitor-> Configuration-> csomópontok. Ha egy csomópont nem kifogástalan állapotú, megtekintheti a hiba részleteit, és elvégezheti a javasolt műveletet.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>NPM a jelentés késési számait a másodpercenként?
A NPM felkerekíti a késési számokat a felhasználói felületen és ezredmásodpercben. Ugyanazokat az adatokat a rendszer magasabb részletességgel tárolja (esetenként akár négy tizedesjegy is).

## <a name="next-steps"></a>Következő lépések

- További információ a Network Performance Monitorről az [Azure-beli Network Performance monitor megoldásra](./network-performance-monitor.md)való hivatkozással.