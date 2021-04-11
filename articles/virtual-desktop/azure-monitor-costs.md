---
title: Windows rendszerű virtuális asztali díjszabási becslések figyelése – Azure
description: A Windows rendszerű virtuális asztali Azure Monitor használatának költségeinek és díjszabásának becslése.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 5bd89a734a20c913bacca1f5531aa76d76418c80
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448219"
---
# <a name="estimate-azure-monitor-costs"></a>Azure Monitor költségek becslése

Azure Monitor a naplók egy olyan szolgáltatás, amely összegyűjti, indexeli és tárolja a környezete által generált adatokat. Emiatt a Azure Monitor díjszabási modellje a Log Analytics munkaterület által naponta gigabájtban bevitt és feldolgozott (vagy "betöltött") adatmennyiségen alapul. Egy Log Analytics munkaterület díja nem csupán az összegyűjtött adatok mennyisége alapján történik, hanem azt is, hogy melyik Azure-fizetési csomagot választotta ki, és hogy mennyi ideig kívánja tárolni a környezet által létrehozott adatokat.

Ez a cikk ismerteti a következő dolgokat, amelyek segítenek megérteni, hogy a Azure Monitor hogyan működik a díjszabásban:

- Az adatfeldolgozási és a tárolási költségek előzetes becslése a funkció engedélyezése előtt
- A betöltés és a tárolás mérése és szabályozása a költségek csökkentése érdekében a funkció használatakor

>[!NOTE]
> A jelen cikkben felsorolt összes méret és díjszabás csak példákat mutat be a becslés működésének bemutatására. A Azure Monitor Log Analytics díjszabási modell és az Azure-régió alapján végzett pontosabb értékelésért tekintse meg a [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Adatfeldolgozási és tárolási költségek becslése

Javasoljuk, hogy a Log Analytics munkaterületen naplóként írt előre definiált adatkészletet használjon. A következő példában megtekintjük a számlázható adatértékeket az alapértelmezett konfigurációban

A Windows rendszerű virtuális asztali Azure Monitor számára előre definiált adatkészletek a következők:

- Teljesítményszámlálók a munkamenet-gazdagépekről
- Windows-eseménynaplók a munkamenet-gazdagépekről
- Windows rendszerű virtuális asztali diagnosztika a szolgáltatási infrastruktúrából

Az adatfeldolgozási és tárolási költségek a környezet méretétől, állapotától és használattól függenek. Az ebben a cikkben ismertetett becslések alapján kiszámíthatja, hogy a várhatóan használt költségek köre a [virtuális gépek méretezési irányelvei](/remote/remote-desktop-services/virtual-machine-recs)alapján, a lehető legkevesebb adatfeldolgozási és tárolási költségek kiszámítására szolgál.

A példában a könnyű használatú virtuális gép a következő összetevőket tartalmazza:

- 4 vCPU, 1 lemez
- napi 16 munkamenet
- Egy átlagos munkamenet időtartama 2 óra (120 perc)
- 100 folyamat/munkamenet

A példánkban szereplő energiafogyasztási virtuális gép a következő összetevőket tartalmazza:

- 6 vCPU, 1 lemez
- 6 munkamenet/nap
- Munkamenetek átlagos időtartama 4 óra (240 perc)
- 200 folyamat/munkamenet

## <a name="estimating-performance-counter-ingestion"></a>Teljesítményszámláló betöltésének becslése

A teljesítményszámlálók bemutatják, hogyan működnek a rendszererőforrások. A teljesítményszámláló-adatok betöltése a környezet méretétől és a használattól függ. A legtöbb esetben a teljesítményszámlálók a Windows rendszerű virtuális asztali Azure Monitor esetében az adatfeldolgozás 80-99%-át teszik ki.

Mielőtt elkezdi a becslést, fontos tisztában lennie azzal, hogy az egyes teljesítményszámlálók adott gyakorisággal küldenek adatokat. Az alapértelmezett mintavételezési sebesség percenként (a beállításokban is szerkeszthető), de ez a sebesség a számlálótól függően eltérő szorzótényezők esetén lesz alkalmazva. Az alábbi tényezők befolyásolják a sebességet:

- A virtuális gép (VM) faktor esetében minden számláló az alapértelmezett mintavételezési arány percenként küldi az adatokat a környezetben, a virtuális gép futása közben. A számlálók által naponta küldött rekordok számát megbecsülheti, ha a környezetében lévő virtuális gépek száma percenként megszorozza az alapértelmezett mintavételezési arányt, majd ezt a számot a virtuális gép átlagos napi futási idejének szorzatával szorozza meg.

   Összegezve:

   Alapértelmezett mintavételi sebesség percenként × CPU-magok száma a virtuális gép SKU-ban × virtuális gépek száma másodpercenként átlagosan napi futási idő = a naponta elküldésre kerülő rekordok száma

- A/CPU-faktor esetében minden számláló az alapértelmezett mintavételezési arány percenként, a környezet minden egyes virtuális gépén vCPU, miközben a virtuális gép fut. Megbecsülheti, hogy a számlálók hány rekordot küldenek naponta, ha a virtuális gép SKU-ban lévő CPU-magok száma alapján megszorozza az alapértelmezett mintavételezési arányt, majd ezt a számot a virtuális gép által futtatott percek számával és a környezetben lévő virtuális gépek számával szorozza meg.

   Összegezve:
   
   Alapértelmezett mintavételi sebesség percenként × CPU-magok száma a virtuális gép SKU-ban × a virtuális gép által futtatott percek száma × virtuális gépek száma = a másodpercenként elküldésre kerülő rekordok száma

- A lemezes faktorok esetében minden számláló az alapértelmezett mintavételi sebességgel küldi az adatokat a környezetben található minden egyes lemez esetében. Azoknak a rekordoknak a száma, amelyeket ezek a számlálók naponta küldenek, a virtuálisgép-SKU-ban lévő lemezek számával megszorozva a virtuális gép SKU-ban lévő lemezekkel 60, majd a virtuális gép átlagos aktív órájának szorzatával.

   Összegezve:

   Alapértelmezett mintavételi sebesség percenként × lemezek száma a VM SKU-ban × 60 perc/óra × másodpercenkénti számú VM × átlagos virtuális gép napi futási idő = a naponta elküldésre kerülő rekordok száma

- A munkamenetenkénti faktor esetében minden számláló az alapértelmezett mintavételezési arányban továbbítja az adatokat a környezet minden egyes munkamenetéhez, miközben a munkamenet csatlakoztatva van. Megbecsülheti, hogy a számlálók hány rekordot küldenek naponta. az alapértelmezett mintavételezési arány percenkénti megszorzásával a napi munkamenetek átlagos száma és az átlagos munkamenet időtartama alapján.

   Összegezve:

   Alapértelmezett mintavételi sebesség percenként × munkamenetek száma × átlagos munkamenet időtartama = naponta eljuttatott rekordok száma

- A folyamaton belüli tényező esetében minden számláló a környezet minden egyes munkamenetében az egyes folyamatok alapértelmezett díjszabása szerint küld adatokat. Megbecsülheti, hogy a számlálók hány rekordot küldenek naponta, ha az alapértelmezett mintavételezési arányt percenként szorozza a napi munkamenetek átlagos számával, majd szorozza meg a munkamenetek átlagos időtartamával és a folyamatok átlagos számával.
  
   Összegezve:

   Alapértelmezett mintavételezési sebesség percenként × munkamenetek száma × átlagos munkamenet időtartama × munkamenetek átlagos száma (munkamenetenként) = a másodpercenként elküldésre kerülő rekordok száma

A következő táblázat a Windows rendszerű virtuális asztali gépekhez Azure Monitor 20 teljesítményszámlálók listáját tartalmazza, és azok alapértelmezett díjait:

| Számláló neve | Alapértelmezett mintavételi sebesség | Gyakorisági tényező |
|--------------|---------------------|------------------|
| Logikai lemez (C:) \\ % szabad terület | 60 másodperc  | /Lemez             |
| Logikai lemez (C:) \\ átlagos Lemezvezérlő-várólista hossza   | 30 másodperc    | /Lemez             |
| Logikai lemez (C:) \\ átlagos műveleti idő (mp/átvitel)  | 60 másodperc       | /Lemez             |
| Logikai lemez (C:) a \\ lemez aktuális várólistájának hossza  | 30 másodperc      | /Lemez             |
| Memória ( \* ) \\ elérhető MB-ban | 30 másodperc    | /Virtuális gép  |
| Memória ( \* ) \\ laphibák másodpercenként | 30 másodperc   | /Virtuális gép  |
| Memória ( \* ) \\ lapok másodpercenként | 30 másodperc   | /Virtuális gép  |
| Memória ( \* ) \\ %-ban előjegyzett bájtok használatban | 30 másodperc    | /Virtuális gép  |
| Fizikai \* lemez () átlagos lemezvezérlő- \\ várólista hossza | 30 másodperc      | /Lemez             |
| Fizikai \* lemez () \\ átlagos olvasási idő (mp/olvasás) | 30 másodperc  | /Lemez             |
| Fizikai lemez () átlagos műveleti idő ( \* \\ mp/átvitel) | 30 másodperc  | /Lemez             |
| Fizikai lemez ( \* ) \\ átlagos írási idő (mp/írás) | 30 másodperc | /Lemez             |
| Processzor adatai (_Total) \\ processzoridő%-ban | 30 másodperc | /Mag/CPU         |
| Terminal Services ( \* ) \\ aktív munkamenetek          | 60 másodperc | /Virtuális gép  |
| Terminal Services ( \* ) \\ inaktív munkamenetek        | 60 másodperc | /Virtuális gép  |
| Terminal Services ( \* ) \\ összes munkamenete | 60 másodperc | /Virtuális gép  |
| Felhasználói bevitel késleltetése folyamat szerint ( \* ) \\ maximális bemeneti késleltetés         | 30 másodperc | /Folyamat          |
| Felhasználói bemeneti késleltetés/munkamenet ( \* ) \\ maximális bemeneti késleltetése        | 30 másodperc | /Munkamenet          |
| Távoli hálózat ( \* ) \\ jelenlegi TCP-RTT | 30 másodperc | /Virtuális gép  |
| Távoli hálózat ( \* ) \\ jelenlegi UDP-sávszélesség     | 30 másodperc | /Virtuális gép  |

Ha az egyes rekordok méretét 200 bájtra becsüljük, egy példa, amely az alapértelmezett mintavételi sebességen alapuló, könnyű számítási feladatot futtató virtuális gép számára napi másodpercenként körülbelül 90 MB teljesítményszámláló-adatmennyiséget küld. Eközben egy Power munkaterhelés-t futtató virtuális gép körülbelül 130 MB teljesítményszámláló-adat küldését küldi el egy virtuális gépenként. A rekord mérete és a környezet használata azonban eltérő lehet, így a központi telepítés által használt napi megabájtok eltérőek lehetnek.

A bemeneti késleltetési teljesítményszámlálók részletes ismertetését lásd: [felhasználói bemeneti késleltetési](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)teljesítményszámlálók.

## <a name="estimating-windows-event-log-ingestion"></a>A Windows-Eseménynapló betöltésének becslése

A Windows-eseménynaplók olyan adatforrások, amelyeket Log Analytics ügynökök gyűjtenek a Windows rendszerű virtuális gépeken. Eseményeket gyűjthet a szabványos naplókból, például a rendszerből és az alkalmazásból, valamint a figyelni kívánt alkalmazások által létrehozott egyéni naplókból.

A Windows rendszerű virtuális asztali Azure Monitor alapértelmezett Windows-eseményei:

- Alkalmazás
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
- Rendszer
- Microsoft-FSLogix – alkalmazások/működési
- Microsoft-FSLogix-alkalmazások/rendszergazda

A Windows-események akkor lesznek elküldve, ha az esemény feltételei teljesülnek a környezetben. A kifogástalan állapotú gépek kevesebb eseményt küldenek, mint a nem megfelelő állapotú gépek. Mivel az események száma nem előre jelezhető, a becslések szerint napi egy 1 000 és 10 000 esemény/virtuális gép/nap közötti időtartamot használunk. Ha például az ebben a példában szereplő összes Event Record méretet 1 500 bájtra becsüljük, akkor ez a megadott környezet esetében körülbelül 2 – 15 megabájt/nap.

További információ a Windows-eseményekről: [Windows-események rekordjainak tulajdonságai](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Diagnosztika betöltésének becslése

A diagnosztikai szolgáltatás a felhasználói és rendszergazdai műveletekhez is létrehozza a tevékenység naplóit.

Ezek a tevékenységek a diagnosztikai számláló nyomon követésére szolgáló naplók neve:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

A szolgáltatás diagnosztikai adatokat küld, amikor a környezet megfelel a rekord létrehozásához szükséges feltételeknek. Mivel a diagnosztikai rekordok száma nem előre jelezhető, a becslések szerint napi egy 500 – 1000 eseményt használunk a virtuális gépenként.

Ha például a példában szereplő összes diagnosztikai rekord méretét 200 bájtra becsüljük, akkor a teljes betöltött adat napi 1 MB-nál kevesebb lesz.

Ha többet szeretne megtudni a műveletnapló-kategóriákról, tekintse meg a [Windows rendszerű virtuális asztali diagnosztika](diagnostics-log-analytics.md)című témakört.

## <a name="estimating-total-costs"></a>Az összes költség becslése

Végezetül becsülje meg a teljes díjat. Ebben a példában tegyük fel, hogy az előző szakaszban szereplő példa alapján a következő eredményeket vesszük fel:

| Adatforrás        | Becsült napi méret (megabájtban)   |
|-------------------------------------|------------------------------------------|
| Teljesítményszámlálók   | 90-130 |
| Események    | 2-15 |
| Windows rendszerű virtuális asztali diagnosztika | \< 1 |

Ebben a példában a Windows rendszerű virtuális asztali Azure Monitor teljes betöltött adata naponta 92 – 145 megabájt/másodperc. Más szóval 31 naponként minden virtuális gép nagyjából 3 – 5 gigabájt adatot tölt be.

A [log Analytics díjszabásra](https://azure.microsoft.com/pricing/details/monitor/)vonatkozó alapértelmezett utólagos elszámolású modell használatával megbecsülheti a Azure monitor adatgyűjtési és-tárolási költségeket havonta. Az adatfeldolgozástól függően érdemes megfontolni a kapacitás foglalási modelljét is Log Analytics díjszabáshoz.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Az adatfeldolgozás kezelése a költségek csökkentése érdekében

Ez a szakasz bemutatja, hogyan mérhető és kezelhető az adatfeldolgozás a költségek csökkentése érdekében.

A jogosultságok és engedélyek a munkafüzethez való kezelésével kapcsolatos további információkért lásd: [hozzáférés-vezérlés](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>Az adatpontok eltávolítása hatással lesz a Windows rendszerű virtuális asztali Azure Monitor a megfelelő vizualizációra.

### <a name="log-analytics-settings"></a>Log Analytics beállítások

Néhány javaslat a Log Analytics beállításainak optimalizálására az adatfeldolgozás kezeléséhez:

- A Windows rendszerű virtuális asztali erőforrások kijelölt Log Analytics munkaterületének használatával biztosíthatja, hogy a Log Analytics csak a Windows rendszerű virtuális asztali környezetben lévő virtuális gépekhez tartozó teljesítményszámlálókat és eseményeket Gyűjtse.
- Módosítsa a Log Analytics tárolási beállításait a költségek kezeléséhez. Csökkentheti a megőrzési időtartamot, kiértékelheti, hogy a rögzített tárolási árképzési csomag költséghatékonyabb-e, vagy hogy mekkora mennyiségű adatot kell betartania a nem kifogástalan állapotú telepítés hatásának korlátozására. További információért lásd: [Azure monitor naplók használatának és költségeinek kezelése](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Felesleges adatmennyiség eltávolítása

Az alapértelmezett konfigurációnk az egyetlen olyan adathalmaz, amelyet a Windows rendszerű virtuális asztali Azure Monitorhoz ajánlunk. Mindig lehetősége van további adatpontok hozzáadására és megtekintésére a gazdagép-diagnosztika: böngésző vagy egyéni diagramok létrehozásához, azonban a hozzáadott adatmennyiség növeli a Log Analytics költségeket. Ezek a költségmegtakarítás érdekében eltávolíthatók.

### <a name="measure-and-manage-your-performance-counter-data"></a>Teljesítményszámláló-adat mérése és kezelése

A valódi figyelési költségek a környezet méretétől, a használattól és az állapottól függenek. A Log Analytics munkaterületen található adatfeldolgozás mértékének megismeréséhez tekintse meg a betöltött [napló adatkötetének ismertetése](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)című témakört.

A munkamenet-gazdagépek által használt teljesítményszámlálók valószínűleg a Windows rendszerű virtuális asztali Azure Monitor legjelentősebb forrása a betöltött adatmennyiségnek. Egy Log Analytics munkaterület következő egyéni lekérdezési sablonja nyomon követheti az elmúlt nap során a teljesítményszámláló által betöltött gyakoriságot és megabájtot:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Ügyeljen arra, hogy a sablon helyőrző értékeit cserélje le a környezet által használt értékekre, ellenkező esetben a lekérdezés nem fog működni.

Ez a lekérdezés az összes olyan teljesítményszámlálókat megjeleníti, amelyet engedélyezett a környezetben, nem csak a Windows rendszerű virtuális asztalok alapértelmezett Azure Monitor. Ez az információ segít megismerni, hogy mely területek célozzák meg a költségek csökkentését, például a számláló gyakoriságának csökkentését vagy teljes eltávolítását.

A teljesítményszámlálók eltávolításával is csökkentheti a költségeket. További információ a teljesítményszámlálók eltávolításáról vagy a meglévő számlálók szerkesztéséről a gyakoriság csökkentése érdekében: [teljesítményszámlálók konfigurálása](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Windows-eseménynaplók kezelése

A Windows-események nem valószínű, hogy az adatfeldolgozás során egy tüske kerül be, amikor az összes gazdagép kifogástalan állapotú. A nem kifogástalan állapotú gazdagépek növelhetik a naplóba eljuttatott események számát, de az információk kritikus fontosságúak lehetnek a gazdagép problémáinak kijavításához. Javasoljuk, hogy őrizze meg őket. A Windows-eseménynaplók kezelésével kapcsolatos további információkért lásd: [Windows-eseménynaplók konfigurálása](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Diagnosztika kezelése

A Windows rendszerű virtuális asztali diagnosztika az adattárolási költségek kevesebb mint 1%-át teszi ki, ezért nem ajánlott eltávolítani őket. A Windows rendszerű virtuális asztali diagnosztika kezeléséhez [használja a log Analytics a diagnosztikai szolgáltatáshoz](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Következő lépések

További információ a Windows rendszerű virtuális asztali Azure Monitor a következő cikkekben található:

- Az [üzembe helyezés figyeléséhez használja a Windows rendszerű virtuális asztali Azure monitor](azure-monitor.md).
- A [Szószedet](azure-monitor-glossary.md) segítségével további információkat tudhat meg a feltételekről és fogalmakról.
- Ha probléma merül fel, segítségért tekintse meg a [hibaelhárítási útmutatót](troubleshoot-azure-monitor.md) .
- A figyelési költségek kezelésével kapcsolatos további információkért tekintse meg a [figyelési használatot és a becsült költségeket Azure monitor](../azure-monitor/usage-estimated-costs.md) .
