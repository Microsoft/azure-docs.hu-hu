---
title: Teljesítményfigyelő Network Performance Monitor
description: A Network Performance Monitor Teljesítményfigyelő funkciója segíti a hálózati kapcsolatok figyelését a hálózat különböző pontjai között. A Felhőbeli üzemelő példányokat, a helyszíni helyszíneket, több adatközpontot és fiókirodát, valamint a kritikus többrétegű alkalmazásokat és a-szolgáltatásokat is figyelheti.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 6502d4259aa383c1466967d01282cecfbb8d1ded
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832963"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Network Performance Monitor megoldás: teljesítményfigyelés

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitorról az](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

A [Network Performance monitor](network-performance-monitor.md) Teljesítményfigyelő funkciója segíti a hálózati kapcsolatok figyelését a hálózat különböző pontjai között. A Felhőbeli üzemelő példányokat, a helyszíni helyszíneket, több adatközpontot és fiókirodát, valamint a kritikus többrétegű alkalmazásokat és a-szolgáltatásokat is figyelheti. A Teljesítményfigyelő használatával észlelheti a hálózati problémákat, mielőtt a felhasználók panaszkodnak. A legfontosabb előnyök a következők lehetnek: 

- A különböző alhálózatokon és a riasztások beállításakor figyelje a veszteséget és a késést.
- Figyelje a hálózaton lévő összes elérési utat (a redundáns elérési utakat is beleértve).
- Az átmeneti és az időponthoz tartozó hálózati problémák elhárítása, amelyeket nehéz replikálni.
- Határozza meg a hálózat adott szegmensét, amely a teljesítmény romlásának felel meg.
- A hálózat állapotának figyelése anélkül, hogy SNMP-t kellene használnia.


![Hálózati teljesítményfigyelő](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguráció
A Network Performance Monitor konfigurációjának megnyitásához nyissa meg a [Network Performance monitor megoldást](network-performance-monitor.md), és válassza a **Konfigurálás** lehetőséget.

![A Network Performance Monitor konfigurálása](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Új hálózatok létrehozása

A Network Performance Monitor egy hálózata az alhálózatok logikai tárolója. Ez segít a hálózati infrastruktúra figyelésének megszervezésében igényei szerint. Létrehozhat egy rövid névvel rendelkező hálózatot, és hozzáadhat alhálózatokat az üzleti logikája szerint. Létrehozhat például egy London nevű hálózatot, és hozzáadhatja az összes alhálózatot a londoni adatközpontban. Vagy létrehozhat egy *ContosoFrontEnd* nevű hálózatot, és hozzáadhatja ezt a hálózatot a contoso nevű alhálózathoz, amely az alkalmazás kezelőfelületét szolgálja ki. A megoldás automatikusan létrehoz egy alapértelmezett hálózatot, amely a környezetben felderített összes alhálózatot tartalmazza. 

Amikor hálózatot hoz létre, hozzá kell adnia egy alhálózatot. Ezt követően az alhálózat el lesz távolítva az alapértelmezett hálózatból. Ha töröl egy hálózatot, a rendszer az összes alhálózatát automatikusan visszaadja az alapértelmezett hálózatnak. Az alapértelmezett hálózat tárolóként működik az összes olyan alhálózat esetében, amely nem szerepel a felhasználó által megadott hálózatban. Az alapértelmezett hálózat nem szerkeszthető vagy törölhető. Mindig a rendszeren marad. Annyi egyéni hálózatot hozhat létre, amennyire csak szüksége van. A legtöbb esetben a szervezet alhálózatai több hálózaton vannak rendezve. Hozzon létre egy vagy több hálózatot az alhálózatok az üzleti logikához való csoportosításához.

Új hálózat létrehozása:


1. Válassza a **hálózatok** fület.
1. Válassza a **hálózat hozzáadása** lehetőséget, majd írja be a hálózat nevét és leírását. 
2. Válasszon ki egy vagy több alhálózatot, majd válassza a **Hozzáadás** lehetőséget. 
3. A konfiguráció mentéséhez kattintson a **Mentés** gombra. 


### <a name="create-monitoring-rules"></a>Figyelési szabályok létrehozása 

A Teljesítményfigyelő állapotot állít elő, ha a két alhálózat vagy két hálózat közötti hálózati kapcsolatok teljesítményének küszöbértékét megszegik. A rendszer automatikusan tudja megtanulni ezeket a küszöbértékeket. Egyéni küszöbértékeket is megadhat. A rendszer automatikusan létrehoz egy alapértelmezett szabályt, amely egy állapotfigyelő eseményt állít elő, ha a hálózati vagy alhálózati kapcsolatok bármelyik párja vagy a késése megszegi a rendszer által megismert küszöbértéket. Ez a folyamat segít a megoldásnak megfigyelni a hálózati infrastruktúrát, amíg nem hozott létre explicit módon figyelési szabályokat. Ha az alapértelmezett szabály engedélyezve van, az összes csomópont szintetikus tranzakciókat küld az összes többi, a figyelésre engedélyezett csomópontnak. Az alapértelmezett szabály kis hálózatok esetén hasznos. Ilyen eset például, ha kis számú, a Service-t futtató kiszolgálóval rendelkezik, és biztosítani szeretné, hogy az összes kiszolgáló kapcsolatot létesítsen egymással.

>[!NOTE]
> Javasoljuk, hogy tiltsa le az alapértelmezett szabályt, és hozzon létre egyéni figyelési szabályokat, különösen olyan nagyméretű hálózatokkal, amelyekben nagy számú csomópontot használ a figyeléshez. Az egyéni figyelési szabályok csökkenthetik a megoldás által generált forgalmat, és segítenek a hálózat figyelésének szervezésében.

Az üzleti logikának megfelelően hozzon létre figyelési szabályokat. Ilyen eset például, ha két Office-hely hálózati kapcsolatának teljesítményét szeretné figyelni a központba. Csoportosítsa az Office hely1 összes alhálózatát az O1 hálózatban. Ezután csoportosítsa az összes alhálózatot az Office site2 az O2 hálózatban. Végül pedig csoportosítsa az összes alhálózatot a hálózati H hálózatban. hozzon létre két figyelési szabályt – egyet az O1 és a H, valamint a másikat az O2 és a H között. 

Egyéni figyelési szabályok létrehozása:

1. A **figyelés** lapon válassza a **szabály hozzáadása** lehetőséget, és írja be a szabály nevét és leírását.
2. Válassza ki a listából a figyeléshez használandó hálózati vagy alhálózati kapcsolatokat. 
3. Válassza ki azt a hálózatot, amely a hálózat legördülő listából a kívánt alhálózatokat tartalmazza. Ezután válassza ki az alhálózatokat a megfelelő alhálózat legördülő listából. Ha egy hálózati kapcsolat összes alhálózatát figyelni szeretné, válassza az **összes alhálózat** lehetőséget. Hasonlóképpen válassza ki a többi kívánt alhálózatot. Ha ki szeretné zárni a megadott alhálózati kapcsolatok figyelését a kiválasztott beállítások közül, válassza a **kivétel hozzáadása** elemet. 
4. Válasszon az ICMP és a TCP protokollok közül a szintetikus tranzakciók végrehajtásához. 
5. Ha nem szeretne állapot-eseményeket létrehozni a kiválasztott elemekhez, törölje a jelet **az állapot-figyelés engedélyezése a szabály által érintett hivatkozásokra**. 
6. Válassza a figyelési feltételek lehetőséget. Az állapot-események generálásához szükséges egyéni küszöbértékek megadásához adja meg a küszöbértékeket. Ha a feltétel értéke meghaladja a kiválasztott hálózati vagy alhálózati pár küszöbértékét, a rendszer egy állapottal kapcsolatos eseményt generál. 
7. A konfiguráció mentéséhez kattintson a **Mentés** gombra. 

A figyelési szabály mentését követően a **riasztás létrehozása** lehetőség kiválasztásával integrálhatja ezt a szabályt a Alert Management. A rendszer automatikusan létrehoz egy riasztási szabályt a keresési lekérdezéssel. A rendszer a többi szükséges paramétert automatikusan kitölti. A riasztási szabályok használatával e-mail-alapú riasztásokat fogadhat, a Network Performance Monitoron belüli meglévő riasztások mellett is. A riasztások a runbookok-mel is indíthatnak javító műveleteket, vagy webhookok használatával integrálhatók a meglévő Service Management-megoldásokkal. A riasztási beállítások szerkesztéséhez válassza a **riasztás kezelése** lehetőséget. 

Most már több Teljesítményfigyelő szabályt is létrehozhat, vagy áthelyezheti a megoldás irányítópultját a funkció használatára.

### <a name="choose-the-protocol"></a>Válassza ki a protokollt

A Network Performance Monitor szintetikus tranzakciókat használ a hálózati teljesítmény metrikáinak kiszámításához, például a csomagok elvesztését és a kapcsolat késését. A koncepció jobb megismeréséhez vegye fontolóra Network Performance Monitor ügynök csatlakoztatását egy hálózati kapcsolat egyik végéhez. Ez a Network Performance Monitor ügynök mintavételi csomagokat küld egy második Network Performance Monitor ügynökhöz, amely a hálózat egy másik végéhez csatlakozik. A második ügynök válasz csomagokat választ. A folyamat néhányszor ismétlődik. A válaszok számának és az egyes válaszok fogadásának időtartamának mérésével az első Network Performance Monitor ügynök értékeli a kapcsolat késését és a csomagok elejtését. 

A csomagok formátumát, méretét és sorát a megfigyelési szabályok létrehozásakor választott protokoll határozza meg. A csomagok protokollja alapján a köztes hálózati eszközök, például az útválasztók és a kapcsolók is feldolgozhatják ezeket a csomagokat. Ennek következtében a protokoll választása befolyásolja az eredmények pontosságát. A protokoll választása azt is meghatározza, hogy a Network Performance Monitor megoldás telepítése után kell-e manuális lépéseket végrehajtania. 

Network Performance Monitor a szintetikus tranzakciók végrehajtásához az ICMP és a TCP protokollok közül választhat. Ha a szintetikus tranzakciós szabály létrehozásakor az ICMP lehetőséget választja, a Network Performance Monitor ügynök ICMP ECHO-üzeneteket használ a hálózati késés és a csomagok elvesztésének kiszámításához. Az ICMP ECHO ugyanazt az üzenetet használja, amelyet a hagyományos ping segédprogram küld. Ha a TCP protokollt használja protokollként, Network Performance Monitor ügynökök TCP SYN csomagokat küldenek a hálózaton keresztül. Ezt a lépést a TCP-kézfogás befejezése követi, és a rendszer az első csomagok használatával eltávolítja a csatlakozást. 

A protokoll kiválasztása előtt vegye figyelembe a következő információkat: 

* **Több hálózati útvonal felderítése.** A TCP pontosabb a több útvonal felfedése esetén, és az egyes alhálózatokon kevesebb ügynökre van szükség. Egy vagy két TCP-t használó ügynök például képes észlelni az alhálózatok közötti összes redundáns útvonalat. Hasonló eredmények eléréséhez több, ICMP protokollt használó ügynökre van szüksége. Ha az ICMP használatával több útvonal található két alhálózat között, akkor a forrás-vagy a cél alhálózatban több mint 5N-ügynökre van szükség.

* **Az eredmények pontossága.** Az útválasztók és kapcsolók általában alacsonyabb prioritást rendelnek hozzá az ICMP ECHO-csomagokhoz a TCP-csomagokhoz képest. Bizonyos helyzetekben, ha a hálózati eszközök nagy terheléssel vannak betöltve, a TCP által lekért adatvesztések az alkalmazások által tapasztalt veszteséget és késést tükrözik. Ennek az az oka, hogy az alkalmazás nagy része TCP-forgalomban van. Ilyen esetekben az ICMP kevésbé pontos eredményeket biztosít a TCP-hez képest. 

* **Tűzfal-konfiguráció.** A TCP protokoll megköveteli, hogy a TCP-csomagokat a rendszer egy célport számára küldje el. Network Performance Monitor ügynökök által használt alapértelmezett port 8084. Az ügynökök konfigurálásakor módosíthatja a portot. Győződjön meg arról, hogy a hálózati tűzfalak vagy a hálózati biztonsági csoport (NSG) szabályai (az Azure-ban) engedélyezik a forgalmat a porton. Emellett meg kell győződnie arról, hogy a helyi tűzfal azon a számítógépen, amelyen az ügynökök telepítve vannak, úgy van konfigurálva, hogy engedélyezzék a forgalmat ezen a porton. A Windows rendszerű számítógépeken a tűzfalszabályok konfigurálásához PowerShell-parancsfájlok használhatók, de a hálózati tűzfalat manuálisan kell konfigurálnia. Ezzel szemben az ICMP nem a port használatával működik. A legtöbb nagyvállalati forgatókönyvben az ICMP-forgalom a tűzfalakon keresztül engedélyezhető, hogy a hálózati diagnosztikai eszközöket, például a ping segédprogramot használja. Ha egy másik gép pingelését hajtja végre, akkor a tűzfalak manuális konfigurálása nélkül is használhatja az ICMP protokollt.

>[!NOTE] 
> Egyes tűzfalak letilthatják az ICMP-t, ami a biztonsági információk és az eseménykezelő rendszer nagy számú eseményének eredményét eredményezheti. Győződjön meg arról, hogy a választott protokollt nem blokkolja hálózati tűzfal vagy NSG. Ellenkező esetben a Network Performance Monitor nem tudja figyelni a hálózati szegmenst. Javasoljuk, hogy a TCP protokollt használja a figyeléshez. Használjon ICMP-t olyan helyzetekben, ahol nem használhatja a TCP-t, például: 
>
> - Windows ügyfél-alapú csomópontokat használ, mivel a TCP RAW szoftvercsatornák használata nem engedélyezett a Windows-ügyfeleken.
> - A hálózati tűzfal vagy NSG blokkolja a TCP-t.
> - Nem tudja, hogyan kell váltani a protokollt.

Ha az üzembe helyezés során az ICMP használatát választotta, akkor az alapértelmezett figyelési szabály szerkesztésével bármikor átválthat a TCP-re.

1. Nyissa **meg a Hálózatfigyelő**   >  ****   >  **konfigurálása**   >  **figyelőt**. Ezután válassza az **alapértelmezett szabály** lehetőséget. 
2. Görgessen a **protokoll** szakaszhoz, és válassza ki a használni kívánt protokollt. 
3. A beállítás alkalmazásához válassza a **Mentés** lehetőséget. 

Még ha az alapértelmezett szabály egy adott protokollt is használ, létrehozhat új szabályokat egy másik protokollal. Akár olyan szabályokat is létrehozhat, amelyekben bizonyos szabályok az ICMP protokollt használják, mások pedig TCP-t használnak. 

## <a name="walkthrough"></a>Útmutatás 

Most tekintse meg az állapotot kiváltó esemény egyszerű vizsgálatát.

A megoldás irányítópultján a Health esemény azt mutatja, hogy a hálózati kapcsolat nem megfelelő állapotú. A probléma kivizsgálásához válassza ki a **figyelni kívánt hálózati kapcsolatokat** .

A részletezés oldalon látható, hogy az **DMZ2-DMZ1** hálózati kapcsolat nem kifogástalan. Válassza a hálózati kapcsolat **alhálózati hivatkozásainak megtekintése** lehetőséget. 


A részletezés oldalon a **DMZ2-DMZ1** hálózati kapcsolat összes alhálózati kapcsolata látható. Mindkét alhálózati kapcsolat esetében a késés túllépte a küszöbértéket, ami miatt a hálózati kapcsolat nem megfelelő állapotú. Az alhálózati kapcsolatok késési trendje is látható. A gráf időkijelölési vezérlőjét használva a szükséges időtartományra koncentrálhat. Megtekintheti a nap azon időpontját, amikor a késés elérte a csúcspontját. A probléma kivizsgálásához keressen rá később a naplókra. További részletezéshez válassza a **csomópont-hivatkozások megtekintése** lehetőséget. 
 
 ![Alhálózati kapcsolatok lapja](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Az előző oldalhoz hasonlóan az adott alhálózat hivatkozásának részletezési lapja az összetevő csomópontjának hivatkozásait sorolja fel. Az előző lépésben hasonló műveleteket is végrehajthat. A két csomópont közötti topológia megtekintéséhez válassza a **topológia megtekintése** lehetőséget. 
 
 ![Csomópont-hivatkozások lap](media/network-performance-monitor-performance-monitor/node-links.png) 

A két kiválasztott csomópont közötti összes útvonal fel van rajzolva a topológiai térképen. A topológiai Térkép két csomópontja közötti útvonalak ugrások közötti topológiáját jelenítheti meg. Egyértelmű képet ad arról, hogy hány útvonal létezik a két csomópont között, és hogy milyen útvonalakat tart az adatcsomagok. A hálózati teljesítmény szűk keresztmetszetei piros színnel jelennek meg. Hibás hálózati vagy hibás hálózati eszköz kereséséhez tekintse meg a topológiai Térkép vörös elemeit. 

 ![Topológiai irányítópult topológiai térképpel](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

A **művelet** ablaktáblán áttekintheti a veszteséget, a késést és a ugrások számát az egyes útvonalakon. A nem megfelelő állapotú elérési utak részleteinek megtekintéséhez használja a görgetősávot. A szűrők segítségével kiválaszthatja az elérési utakat a nem megfelelő állapotú ugrással, hogy csak a kijelölt elérési utak topológiája legyen ábrázolva. A topológiai térkép nagyításához vagy kicsinyítéséhez használja az egér kerekét. 

A következő ábrán a probléma területének kiváltó oka a hálózat adott szakasza a vörös elérési utakon és ugrásokban jelenik meg. Válasszon ki egy csomópontot a topológiai térképen a csomópont tulajdonságainak megjelenítéséhez, amely tartalmazza a teljes tartománynevet és az IP-címet. Egy ugrás kiválasztásakor megjelenik az ugrás IP-címe. 
 
![Topológiai Térkép a csomópont tulajdonságaival kiválasztva](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Következő lépések
[Keresési naplók](../log-query/log-query-overview.md) a hálózati teljesítményadatok részletes rekordjainak megtekintéséhez.
