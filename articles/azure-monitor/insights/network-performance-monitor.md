---
title: Network Performance Monitor megoldás az Azure-ban | Microsoft Docs
description: A Network Performance Monitor az Azure-ban a hálózat teljesítményének közel valós idejű figyelését segíti a hálózati teljesítménnyel kapcsolatos szűk keresztmetszetek észlelése és megkeresése érdekében.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 92326ea4d3de896a67b2953fb612b79948044d08
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573422"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Network Performance Monitor megoldás az Azure-ban

![Network Performance Monitor szimbólum](./media/network-performance-monitor/npm-symbol.png)

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitorról az](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

A Network Performance Monitor egy felhőalapú hibrid hálózati figyelési megoldás, amely a hálózati infrastruktúra különböző pontjai közötti hálózati teljesítmény figyelését segíti. Emellett monitorozhatja a hálózat szolgáltatással és az alkalmazás végpontjaival való kapcsolatát is, illetve az Azure ExpressRoute teljesítményét is figyelheti. 

A Network Performance Monitor észleli a hálózati problémákat, például a forgalmi blackholing, az útválasztási hibákat, valamint azokat a problémákat, amelyeket a hagyományos hálózati figyelési módszerek nem képesek észlelni. A megoldás riasztásokat készít, és értesíti, amikor egy hálózati kapcsolat meghaladja a küszöbértéket. Emellett biztosítja a hálózat teljesítményével kapcsolatos problémák időbeni észlelését és leszűkíti a hiba forrásának helyszínét egy hálózati szegmensre vagy eszközre. 

A Network Performance Monitor három széles körű képességgel rendelkezik: 

* [Teljesítményfigyelő](network-performance-monitor-performance-monitor.md): megfigyelheti a hálózati kapcsolatot a Felhőbeli és a helyszíni helyeken, több adatközpontban és fiókirodákban, valamint a kritikus többrétegű alkalmazásokban vagy a szolgáltatásokban. A Teljesítményfigyelő segítségével észlelheti a hálózati problémákat, mielőtt a felhasználók panaszkodnak.

* [Szolgáltatás-kapcsolódási figyelő](network-performance-monitor-service-connectivity.md): a felhasználók és a hozzájuk tartozó szolgáltatások közötti kapcsolatot figyelheti, meghatározhatja, hogy milyen infrastruktúra van az elérési úton, és azonosítsa a hálózati szűk keresztmetszeteket. A leállásokat a felhasználók előtt ismerheti meg, és megtekintheti a problémák pontos helyét a hálózati elérési út mentén. 

    Ez a funkció segítséget nyújt a HTTP-, HTTPS-, TCP-és ICMP-alapú tesztek végrehajtásához a közel valós idejű figyeléshez, illetve a szolgáltatás rendelkezésre állásának és válaszidőának megtartásához. A hálózat hozzájárulását a csomagok elvesztésével és késésével is figyelheti. Hálózati topológiai térképpel elkülönítheti a hálózati lassulást. A csomópont és a szolgáltatás közötti hálózati elérési út mentén megjelenő problémás helyeket azonosíthatja az egyes ugrások késési adatainak használatával. A beépített tesztek segítségével megfigyelheti Microsoft 365 és a Dynamics CRM hálózati kapcsolatát az előkonfigurálás nélkül. Ezzel a képességgel bármilyen TCP-kompatibilis végponthoz, például webhelyekhez, SaaS-alkalmazásokhoz, Pásti-alkalmazásokhoz és SQL-adatbázisokhoz is figyelheti a hálózati kapcsolatot.

* [ExpressRoute-figyelő](network-performance-monitor-expressroute.md): végpontok közötti kapcsolat és teljesítmény figyelése a fiókirodák és az Azure között az Azure ExpressRoute-en keresztül.  

További információ a [Network Performance monitor](../../networking/network-monitoring-overview.md) által támogatott különböző képességekről online érhető el.
 
## <a name="supported-regions"></a>Támogatott régiók
A NPM a világ bármely részén lévő hálózatok és alkalmazások közötti kapcsolatot a következő régiók egyikében üzemeltetett munkaterületről képes figyelni:
* Észak-Európa
* Nyugat-Európa
* Észak-Svájc
* Közép-Franciaország
* Dél-Afrika északi régiója
* Közép-Kanada
* USA nyugati régiója
* USA nyugati középső régiója
* USA északi középső régiója
* USA déli középső régiója
* USA középső régiója
* USA keleti régiója
* USA 2. keleti régiója
* USA 2. nyugati régiója
* Kelet-Japán
* Délkelet-Ázsia
* Dél-Kelet-Ausztrália
* Ausztrália középső régiója
* Kelet-Ausztrália
* Dél-Egyesült Királyság
* Kelet-Ázsia
* Dél-Korea középső régiója
* Közép-India
* USA-beli kormányzati Virginia
* Egyesült Államok kormánya, Arizona
* Kelet-Kína 2


A ExpressRoute-figyelő támogatott régiói listája a [dokumentációban](../../expressroute/how-to-npm.md?utm_swu=8117)érhető el.


## <a name="set-up-and-configure"></a>Beállítás és konfigurálás

> [!NOTE]
> Azt is megteheti, hogy a Microsoft monitoring Agent (MMA) vagy a OMS Linux Agent néven ismert Log Analytics ügynök.

### <a name="install-and-configure-agents"></a>Ügynökök telepítése és konfigurálása 

Az alapfolyamatok segítségével ügynököket telepíthet a [Windows rendszerű számítógépek Összekapcsolásával Azure monitorhoz](../agents/agent-windows.md), a Linux rendszerű [számítógépeket Azure monitor (előzetes verzió)](../../virtual-machines/extensions/oms-linux.md) és [a Operations Manager Azure monitorhoz](../agents/om-agents.md)való kapcsolódáshoz.

### <a name="where-to-install-the-agents"></a>Az ügynökök telepítése 

* **Teljesítményfigyelő**: telepítsen log Analytics-ügynököt legalább egy, az egyes alhálózatokhoz csatlakoztatott csomópontra, amelyről a hálózati kapcsolatot figyelni kívánja más alhálózatokkal.

    Hálózati kapcsolat figyeléséhez telepítse az ügynököket a hivatkozás mindkét végpontján. Ha nem biztos abban, hogy a hálózat topológiáját használja, telepítse az ügynököket olyan kiszolgálókon, amelyek kritikus fontosságú számítási feladatait a hálózati teljesítmény figyelésére kívánja használni. Ha például a webkiszolgáló és az SQL-t futtató kiszolgáló közötti hálózati kapcsolatot szeretné figyelni, telepítsen egy ügynököt mindkét kiszolgálón. Az ügynökök hálózati kapcsolatot (hivatkozásokat) figyelnek a gazdagépek között, nem maguk a gazdagépek. 

* **Szolgáltatás-kapcsolódási figyelő**: telepítsen egy log Analytics-ügynököt minden olyan csomópontra, amelyről figyelni szeretné a szolgáltatás-végponttal létesített hálózati kapcsolatot. Ilyen eset például, ha a hálózati kapcsolatot szeretné figyelni az O1, O2 és O3 nevű Office-helyekről Microsoft 365. Telepítse a Log Analytics-ügynököt legalább egy csomópontra az O1, O2 és O3 nyelven. 

* **ExpressRoute-figyelő**: telepítsen legalább egy log Analytics-ügynököt az Azure Virtual Networkben. Telepítsen legalább egy ügynököt a helyszíni alhálózaton is, amely a ExpressRoute-alapú privát kapcsolaton keresztül csatlakozik.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics-ügynökök konfigurálása figyeléshez 

A Network Performance Monitor szintetikus tranzakciókat használ a forrás-és a cél-ügynökök közötti hálózati teljesítmény figyelésére. A Teljesítményfigyelő és a szolgáltatás-csatlakozási figyelő funkcióinak figyeléséhez a TCP és az ICMP protokollt is választhatja. A ExpressRoute figyelési protokollként csak a TCP érhető el. Győződjön meg arról, hogy a tűzfal engedélyezi a kiválasztott protokoll figyeléséhez használt Log Analytics ügynökök közötti kommunikációt. 

* **TCP protokoll**: Ha a TCP protokollt választja a figyeléshez, nyissa meg a tűzfal portját a Network Performance monitor és a ExpressRoute figyeléséhez használt ügynökökön, és győződjön meg arról, hogy az ügynökök csatlakozhatnak egymáshoz. Windows rendszerű gépek esetén a port megnyitásához futtassa a [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell-parancsfájlt a rendszergazdai jogosultságokkal rendelkező PowerShell-ablakban található paraméterek nélkül.
A Linux rendszerű gépek esetében a használni kívánt portNumbers manuálisan kell módosítani. 
* Navigáljon a következő elérési útra:/var/opt/Microsoft/omsagent/npm_state. 
* Fájl megnyitása: npmdregistry
* A portszám értékének módosítása ```“PortNumber:<port of your choice>”```

 Ne feledje, hogy a használt portszámoknak meg kell egyezniük a munkaterületen használt összes ügynökkel. 

A parancsfájl a megoldáshoz szükséges beállításkulcsokat hoz létre. Emellett Windows tűzfal-szabályokat is létrehoz, amelyek lehetővé teszik, hogy az ügynökök TCP-kapcsolatokat hozzanak létre egymással. A parancsfájl által létrehozott beállításkulcsok határozzák meg, hogy naplózzák-e a hibakeresési naplókat és a naplófájlok elérési útját. A parancsfájl a kommunikációhoz használt ügynök TCP-portját is meghatározza. A kulcsok értékeit a parancsfájl automatikusan beállítja. Ezeket a kulcsokat ne módosítsa manuálisan. Alapértelmezés szerint a 8084-es port van megnyitva. Egyéni portot úgy használhat, hogy a portszám paramétert a parancsfájlhoz adja. Ugyanazt a portot használja az összes olyan számítógépen, amelyen a parancsfájl fut. 

   >[!NOTE]
   > A parancsfájl csak a Windows tűzfalat konfigurálja helyileg. Ha hálózati tűzfallal rendelkezik, győződjön meg arról, hogy az Network Performance Monitor által használt TCP-portra irányuló forgalmat engedélyezi.

   >[!NOTE]
   > Nem kell futtatnia a [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell-parancsfájlt a szolgáltatás-kapcsolódási figyelőhöz.

    

* **ICMP protokoll**: Ha a figyeléshez az ICMP protokollt választja, a következő tűzfalszabályok használatával megbízhatóan használhatja az ICMP-t:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>A megoldás konfigurálása 

1. Adja hozzá a Network Performance Monitor megoldást a munkaterülethez az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview). Használhatja a [Solutions Gallery Azure monitor megoldások hozzáadása](./solutions.md)című témakörben ismertetett eljárást is. 
2. Nyissa meg Log Analytics munkaterületét, és válassza az **Áttekintés** csempét. 
3. Válassza ki a **Network Performance monitor** csempét az üzenet *megoldásához további konfiguráció szükséges*.

   ![Network Performance Monitor csempe](media/network-performance-monitor/npm-config.png)

4. A **telepítés** lapon megtekintheti log Analytics ügynökök telepítését és az ügynökök figyelésre való konfigurálásának lehetőségét a **közös beállítások** nézetben. Ahogy korábban kifejtettük, ha Log Analytics ügynököket telepített és konfigurált, válassza a **telepítési** nézetet a használni kívánt képesség konfigurálásához. 

   **Teljesítményfigyelő**: válassza ki a szintetikus tranzakciókhoz használandó protokollt az **alapértelmezett** Teljesítményfigyelő szabályban, majd válassza a **Mentés & folytatás** lehetőséget. Ez a protokoll csak a rendszer által létrehozott alapértelmezett szabály számára van kiválasztva. Minden alkalommal ki kell választania a protokollt, amikor a Teljesítményfigyelő szabályát explicit módon hozza létre. Bármikor áthelyezheti az **alapértelmezett** szabály beállításait a **Teljesítményfigyelő** lapon (ez a nap-0 konfiguráció befejezése után jelenik meg), majd később módosíthatja a protokollt. Ha nem szeretné, hogy a Teljesítményfigyelő képes legyen, letilthatja az alapértelmezett szabályt az **alapértelmezett** szabály beállításainál a **Teljesítményfigyelő** lapon.

   ![Teljesítményfigyelő nézet](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Szolgáltatás-kapcsolódási figyelő**: a funkció beépített előre konfigurált teszteket biztosít a Microsoft 365 és a Dynamics 365 hálózati kapcsolatának figyeléséhez az ügynököktől. Válassza ki a figyelni kívánt Microsoft 365-és Dynamics 365-szolgáltatásokat. Ehhez jelölje be a jelölőnégyzeteket. A figyelni kívánt ügynökök kiválasztásához válassza az **ügynökök hozzáadása** lehetőséget. Ha nem szeretné használni ezt a funkciót, vagy később szeretné beállítani, ne válassza a semmit, és válassza a **mentés & folytatás** lehetőséget.

   ![Szolgáltatás-csatlakozási figyelő nézet](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute-figyelő**: válassza a **felderítés most** lehetőséget a log Analytics-munkaterülettel társított Azure-előfizetésben található virtuális hálózatokhoz csatlakozó összes ExpressRoute-kapcsolat felderítéséhez. 

   ![ExpressRoute-figyelő nézet](media/network-performance-monitor/npm-express-route.png)

   A felderítés befejezése után a felderített áramkörök és a társítások egy táblázatban jelennek meg. 

   ![Network Performance Monitor konfigurációs lap](media/network-performance-monitor/npm-private-peerings.png)
    
Az ilyen áramkörök és a társítások figyelése kezdetben letiltott állapotban van. Válassza ki a figyelni kívánt erőforrásokat, és konfigurálja a figyelést a jobb oldali részletek nézetből. A konfiguráció mentéséhez kattintson a **Mentés** gombra. További információt a "ExpressRoute-figyelés konfigurálása" című cikkben talál. 

A telepítés befejezése után 30 percet vesz igénybe az adatok feltöltése. Míg a megoldás összesíti az adatokat a hálózatról, az üzenet *megoldásához további konfigurációra van szükség* a Network Performance monitor **Áttekintés** csempén. Az adatok összegyűjtése és indexelése után az **áttekintő** csempék megváltoznak, és egy összefoglalóban tájékoztatják a hálózati állapotáról. Ezután szerkesztheti azon csomópontok figyelését, amelyeken a Log Analytics ügynökök telepítve vannak, valamint a környezetből felderített alhálózatokat is.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Alhálózatok és csomópontok figyelési beállításainak szerkesztése 

A konfiguráció lap **alhálózatok** lapján minden olyan alhálózat szerepel, amelyen legalább egy ügynök telepítve van. 


Adott alhálózatok figyelésének engedélyezése vagy letiltása:

1. Jelölje be vagy törölje a jelet az **alhálózati azonosító** melletti jelölőnégyzetből. Ezt követően győződjön meg arról, hogy a **figyeléshez való használat** van kiválasztva vagy törölve a megfelelő módon. Több alhálózatot is kijelölhet vagy törölhet. Ha le van tiltva, az alhálózatok nincsenek figyelve, és a rendszer frissíti az ügynököket, hogy leállítsa a többi ügynök pingelését. 
2. Válassza ki a figyelni kívánt csomópontokat egy adott alhálózaton. Válassza ki az alhálózatot a listából, és helyezze át a szükséges csomópontokat a nem figyelt és figyelt csomópontokat tartalmazó listák között. Egyéni leírást adhat hozzá az alhálózathoz.
3. A konfiguráció mentéséhez kattintson a **Mentés** gombra. 

#### <a name="choose-nodes-to-monitor"></a>Figyelni kívánt csomópontok kiválasztása

A **csomópontok** lapon az összes olyan csomópont szerepel, amelyen telepítve van az ügynök. 

1. Válassza ki vagy törölje azokat a csomópontokat, amelyek figyelését vagy leállítását meg szeretné szüntetni. 
2. A **figyeléshez** válassza a használat lehetőséget, vagy törölje a jelölést, ha szükséges. 
3. Kattintson a **Mentés** gombra. 


Konfigurálja a kívánt képességeket:

- [Teljesítménymonitorozás](network-performance-monitor-performance-monitor.md#configuration)
- [Szolgáltatáskapcsolati monitor](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute-figyelő](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Adatgyűjtés részletei
A veszteség-és késési adatok gyűjtéséhez Network Performance Monitor TCP SYN-SYNACK-ACK kézfogási csomagokat használ, ha a TCP protokollt választja. A Network Performance Monitor ICMP ECHO ICMP ECHO választ használ, ha az ICMP protokollt választja. A nyomkövetési útvonal a topológiai információk beolvasására is használatos.

Az alábbi táblázat az adatgyűjtés módszereit és a Network Performance Monitor adatok gyűjtésének egyéb részleteit mutatja be.

| Platform | Közvetlen ügynök | System Center Operations Manager ügynök | Azure Storage | Operations Manager kötelező? | A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-kézfogások/ICMP ECHO-üzenetek 5 másodpercenként, 3 percenként küldött adatküldés |
 

 
A megoldás szintetikus tranzakciókat használ a hálózat állapotának felméréséhez. Log Analytics a hálózati Exchange TCP-csomagok vagy az ICMP echo különböző pontjain telepített ügynökök egymással. Azt határozza meg, hogy az ügynökök TCP-csomagokat használnak-e, vagy az ICMP echo a figyeléshez kiválasztott protokolltól függ. A folyamat során az ügynökök megismerhetik az időkorlátot és a csomagok elvesztését, ha van ilyen. Időnként minden ügynök nyomkövetési útvonalat is végrehajt más ügynököknek, hogy megkeresse a hálózatban lévő különböző útvonalakat, amelyeket tesztelni kell. Ezeknek az adatoknak a használatával az ügynökök lefoglalhatják a hálózati késést és a csomagok elvesztésének számadatait. A tesztek öt másodpercenként ismétlődnek. Az ügynökök körülbelül három percre összesítik az adatokat, mielőtt feltöltik őket a Azure Monitor Log Analytics munkaterületére.



>[!NOTE]
> Bár az ügynökök gyakran kommunikálnak egymással, nem jelentenek jelentős hálózati forgalmat a tesztek végrehajtása során. Az ügynökök csak a TCP SYN-SYNACK-ACK kézfogási csomagok alapján határozzák meg a veszteséget és a késést. Az adatcsomagok cseréje nem történik meg. A folyamat során az ügynökök csak szükség esetén kommunikálnak egymással. Az ügynök kommunikációs topológiája a hálózati forgalom csökkentése érdekében van optimalizálva.

## <a name="use-the-solution"></a>A megoldás használata 

### <a name="network-performance-monitor-overview-tile"></a>Network Performance Monitor áttekintés csempe 

A Network Performance Monitor megoldás engedélyezése után a megoldás csempéje az **Áttekintés** oldalon gyors áttekintést nyújt a hálózat állapotáról. 

 ![Network Performance Monitor áttekintés csempe](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Network Performance Monitor irányítópult 

* A legfontosabb **hálózati állapot eseményei**: ez az oldal a rendszer legutóbbi állapotának és riasztásának listáját, valamint az események aktív állapotának időpontját tartalmazza. Az esemény vagy riasztás akkor jön létre, ha a figyelési szabály kiválasztott metrika (veszteség, késés, válaszidő vagy sávszélesség kihasználtsága) értéke meghaladja a küszöbértéket. 

* ExpressRoute-figyelő: ez az oldal a megoldás által **figyelt** különböző ExpressRoute-társítási kapcsolatok állapotának összefoglalóit tartalmazza. A **topológia** csempéje a hálózati elérési utak számát mutatja a hálózatban figyelt ExpressRoute-áramköreken keresztül. Válassza ezt a csempét a **topológia** nézethez való ugráshoz.

* **Szolgáltatás-kapcsolódási figyelő**: ez az oldal a különböző létrehozott tesztek állapotának összegzését tartalmazza. A **topológia** csempe a figyelt végpontok számát jeleníti meg. Válassza ezt a csempét a **topológia** nézethez való ugráshoz.

* **Teljesítményfigyelő**: ez az oldal a megoldás által figyelt **hálózati** kapcsolatok és **alhálózati** kapcsolatok állapotának összegzését tartalmazza. A **topológia** csempe a hálózatban figyelt hálózati elérési utak számát jeleníti meg. Válassza ezt a csempét a **topológia** nézethez való ugráshoz. 

* **Gyakori lekérdezések**: ez az oldal olyan keresési lekérdezéseket tartalmaz, amelyek közvetlenül beolvassák a nyers hálózat figyelési szolgáltatásait. Ezeket a lekérdezéseket kiindulási pontként használhatja a testreszabott jelentéskészítéshez használható saját lekérdezések létrehozásához. 

   ![Network Performance Monitor irányítópult](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Részletes részletezés 

A megoldás irányítópultján különféle hivatkozásokat is kiválaszthat, amelyekkel mélyebben megtekintheti az összes fontos területét. Ha például egy riasztás vagy egy nem megfelelő állapotú hálózati hivatkozás jelenik meg az irányítópulton, válassza ki azt a további vizsgálathoz. A lap felsorolja az adott hálózati kapcsolat alhálózati kapcsolatait. Megtekintheti az egyes alhálózati kapcsolatok elvesztésének, késésének és állapotának állapotát. Gyorsan megtudhatja, hogy melyik alhálózati kapcsolat okozza a problémákat. Válassza a **csomópontok megtekintése** lehetőséget a nem megfelelő állapotú alhálózat hivatkozásához tartozó összes csomópont megjelenítéséhez. Ezután megtekintheti az egyes csomópontok közötti hivatkozásokat, és megkeresheti a nem megfelelő állapotú csomópontokra mutató hivatkozásokat. 

Válassza a **topológia megtekintése** lehetőséget a forrás-és a cél-csomópontok közötti útvonalakon a hop-by-ugrás topológiájának megtekintéséhez. A nem megfelelő állapotú útvonalak piros színnel jelennek meg. Megtekintheti az egyes ugrások által biztosított késést, így gyorsan azonosíthatja a problémát a hálózat egy adott részében.

 

### <a name="network-state-recorder-control"></a>Hálózati állapot-rögzítő vezérlő

Az egyes nézetek egy adott időpontban jelenítik meg a hálózati állapot pillanatképét. Alapértelmezés szerint a legutóbbi állapot látható. Az oldal tetején található sáv azt az időpontot jeleníti meg, amelyben az állapot megjelenik. Ha egy korábbi időpontban szeretné megtekinteni a hálózati állapot pillanatképét, válassza a **műveletek** lehetőséget. A legutóbbi állapot megtekintésekor engedélyezheti vagy letilthatja bármelyik oldal automatikus frissítését. 

 ![Hálózati állapot-rögzítő](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend-diagramok 

Minden részletezési szinten megtekintheti a vonatkozó mérőszám trendjét. Ez lehet a veszteség, a késés, a válaszidő vagy a sávszélesség kihasználtsága. A trend időintervallumának módosításához használja a diagram tetején található idő vezérlőelemet. 

A trend-diagramok a teljesítmény-mérőszámok teljesítményének történelmi perspektíváit mutatják be. Bizonyos hálózati problémák átmeneti jellegűek, és a hálózat jelenlegi állapotával nehezen foghatók. A problémák gyorsan felmerülhetnek, és eltűnnek, mielőtt valaki észreveszi, hogy csak egy későbbi időpontban kell megjelennie. Az ilyen átmeneti problémák az alkalmazás-rendszergazdák számára is nehézkesek lehetnek. A problémák gyakran a megmagyarázhatatlan növekedéssel jelennek meg az alkalmazás válaszideje során, még akkor is, ha az összes alkalmazás-összetevő zökkenőmentesen fut. 

Az ilyen típusú problémák könnyen észlelhetők, ha egy trend diagramot keres. A probléma hirtelen tüskeként jelenik meg a hálózati késés vagy a csomagok elvesztése során. A probléma kivizsgálásához a hálózati állapot-rögzítő vezérlőelem használatával tekintse meg a hálózati pillanatképet és a topológiát a probléma bekövetkezésének időpontjában.

 
![Trend-diagramok](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topológiai Térkép 

Network Performance Monitor megjeleníti a forrás és a cél végpont közötti útvonalak ugrások közötti topológiáját egy interaktív topológiai térképen. A topológiai Térkép megtekintéséhez válassza a **topológia** csempét a megoldás irányítópultján. Kiválaszthatja a **topológia megtekintése** hivatkozást is a részletező lapokon. 

A topológiai Térkép azt jeleníti meg, hogy hány útvonal van a forrás és a cél között, és hogy milyen útvonalakon történik az adatcsomagok végrehajtása. Az egyes hálózati ugrások által biztosított késés is látható. Az összes elérési út, amelynek késése meghaladja a küszöbértéket (a megfelelő figyelési szabályban beállított), vörös színnel jelenik meg. 

Ha kijelöl egy csomópontot, vagy az egérmutatót a topológiai Térkép fölé viszi, megjelenik a csomópont tulajdonságai, például a teljes tartománynév és az IP-cím. Válasszon egy ugrást az IP-címének megtekintéséhez. A zavaró hálózati ugrást úgy azonosíthatja, hogy felvette a hozzá tartozó késleltetést. Az egyes útvonalak szűréséhez használja az összecsukható művelet ablaktáblán található szűrőket. A hálózati topológiák egyszerűbbé tételéhez rejtse el a közbenső ugrásokat a művelet ablaktábla csúszkájának használatával. A topológiai Térkép nagyítása vagy kicsinyítése az egér görgője segítségével végezhető el. 

A térképen látható topológia 3. rétegbeli topológia, és nem tartalmaz 2. rétegbeli eszközt és kapcsolatot. 

 
![Topológiai Térkép](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Lekérdezések naplózása Azure Monitor

Az Network Performance Monitor-irányítópulton és-részletezésen keresztül elérhetővé tett összes adat natív módon, a [napló lekérdezésekben](../logs/log-query-overview.md)is elérhető. A tárházban lévő adatok interaktív elemzését elvégezheti, és különböző forrásokból származó adatokhoz kapcsolhatók. Létrehozhat egyéni riasztásokat és nézeteket is, és exportálhatja az adatfájlokat az Excelbe, Power BIba vagy egy megosztható hivatkozásba. Az irányítópult **gyakori lekérdezések** területén néhány hasznos lekérdezés található, amelyeket kiindulási pontként használhat a saját lekérdezések és jelentések létrehozásához. 

## <a name="alerts"></a>Riasztások

Network Performance Monitor a [Azure monitor](../alerts/alerts-overview.md)riasztási képességeit használja.

Ez azt jelenti, hogy minden értesítés a [műveleti csoportok](../alerts/action-groups.md)használatával lesz kezelve.  

Ha Ön egy NPM-felhasználó, Log Analytics használatával hozza létre a riasztást: 
1. Ekkor megjelenik egy hivatkozás, amely átirányítja a Azure Portalra. Kattintson rá a portál eléréséhez.
2. Kattintson a Network Performance Monitor megoldás csempére. 
3. Navigáljon a konfiguráláshoz.  
4. Válassza ki azt a tesztet, amelyhez riasztást szeretne létrehozni, és kövesse az alábbi lépéseket.

Ha Ön egy NPM-felhasználó, Azure Portal használatával hozza létre a riasztást:  
1. Dönthet úgy is, hogy közvetlenül adja meg az e-mail-címet, vagy választhatja a riasztások létrehozását a műveleti csoportok használatával.
2. Ha úgy dönt, hogy közvetlenül adja meg az e-mail-címet, a rendszer létrehoz egy **NPM e-mail-ActionGroup** nevű műveleti csoportot, és hozzáadja az e-mail-azonosítót az adott műveleti csoporthoz.
3. Ha a műveleti csoportok használata mellett dönt, ki kell választania egy korábban létrehozott műveleti csoportot. Itt megtudhatja, hogyan hozhat létre egy műveleti csoportot [.](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) 
4. A riasztás sikeres létrehozása után használhatja a riasztások kezelése hivatkozást a riasztások kezeléséhez. 

Minden alkalommal, amikor riasztást hoz létre, a NPM egy lekérdezés-alapú napló-riasztási szabályt hoz létre Azure Monitorban. A lekérdezés alapértelmezés szerint 5 percenként aktiválódik. Az Azure monitor nem számítja fel a létrehozott első 250-es naplózási riasztási szabályokat, és az 250-es napló riasztási szabályának korlátja fölötti riasztási szabályok számlázása a [Azure monitor díjszabási oldalon, a riasztások díjszabása](https://azure.microsoft.com/pricing/details/monitor/)szerint történik.
Az értesítéseket a [Azure monitor díjszabási lapjának értesítési díjszabása](https://azure.microsoft.com/pricing/details/monitor/)szerint külön számoljuk el.


## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információk [online állapotban](network-performance-monitor-pricing-faq.md)vannak.

## <a name="provide-feedback"></a>Visszajelzés küldése 

* **UserVoice:** Ötleteit felteheti Network Performance Monitor olyan funkciókkal kapcsolatban, amelyeken dolgozni szeretne. Látogasson el a [UserVoice oldalára](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Csatlakozzon a kohorszhoz:** Mindig érdeklik, hogy az új ügyfelek csatlakozzanak a kohorszhoz. Ennek részeként korai hozzáférést kap az új funkciókhoz, és lehetőséget nyújt a Network Performance Monitor javítására. Ha érdekli a csatlakozás, töltse ki ezt a [gyors kérdőívet](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Következő lépések 
További információ a [Teljesítményfigyelőről](network-performance-monitor-performance-monitor.md), a [szolgáltatás-csatlakozási figyelőről](network-performance-monitor-performance-monitor.md)és a [ExpressRoute-figyelőről](network-performance-monitor-expressroute.md). 
