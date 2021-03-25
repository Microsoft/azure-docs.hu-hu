---
title: Adatfeldolgozási idő naplózása Azure Monitorban | Microsoft Docs
description: Ismerteti azokat a különböző tényezőket, amelyek befolyásolják a naplózási adatok Azure Monitorban való gyűjtésének késését.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 49122421f04ee6eef8828ca305cfb235aceee3fb
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035693"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Naplóadatok feldolgozási ideje az Azure Monitorban
A Azure Monitor egy nagy léptékű adatszolgáltatás, amely több ezer ügyfelet szolgál ki havonta több, mint havi terabájt adatküldéssel. A naplózási adatok begyűjtése után elérhetővé tételével kapcsolatban gyakran merül fel kérdések. Ez a cikk a késést befolyásoló különféle tényezőket ismerteti.

## <a name="typical-latency"></a>Jellemző késés
A késés azt az időpontot jelenti, amikor az adatgyűjtés a figyelt rendszeren történik, valamint a Azure Monitor elemzéshez elérhetővé tételének időpontja. A naplózási adatmennyiség betöltésének tipikus késése 20 másodperc és 3 perc között lehet. Egy adott adat késése azonban az alábbiakban ismertetett különféle tényezőktől függően változhat.


## <a name="factors-affecting-latency"></a>Késést befolyásoló tényezők
Egy adott adathalmaz teljes betöltési ideje a következő magas szintű területekre osztható fel. 

- Ügynök időpontja – az események felderítésének ideje, összegyűjtése, majd elküldése Azure Monitor naplók betöltési pontjának naplójába. A legtöbb esetben ezt a folyamatot egy ügynök kezeli. A hálózat további késleltetést eredményezhet.
- Folyamatidő – Az az idő, amely alatt a feldolgozási folyamat feldolgozza a naplóbejegyzést. Ez magában foglalja az esemény tulajdonságainak elemzését és a számított információk esetleges hozzáadását.
- Indexelési idő – a naplófájl betöltésére fordított idő Azure Monitor big data-tárolóba.

Az alábbiakban ismertetjük az ebben a folyamatban bevezetett különböző késések részleteit.

### <a name="agent-collection-latency"></a>Az ügynökgyűjtemény késése
Az ügynökök és a felügyeleti megoldások különböző stratégiák használatával gyűjtenek adatokat egy virtuális gépről, ami hatással lehet a késésre. Néhány konkrét példa az alábbiakat tartalmazza:

- A Windows-események, a syslog-események és a teljesítmény-mérőszámok azonnal összegyűjthetők. A Linux-teljesítményszámlálók 30 másodperces időközönként lettek lekérdezve.
- Az IIS-naplók és az egyéni naplók gyűjtése az időbélyeg módosítása után történik. IIS-naplók esetén ezt az [IIS-kiszolgálón konfigurált rollover ütemezés](../agents/data-sources-iis-logs.md)befolyásolja. 
- Active Directory replikációs megoldás ötévente végzi el az értékelést, míg a Active Directory Assessment megoldás hetente értékeli az Active Directory-infrastruktúrát. Az ügynök csak az értékelés befejezésekor fogja gyűjteni ezeket a naplókat.

### <a name="agent-upload-frequency"></a>Az ügynök feltöltési gyakorisága
Annak biztosítása érdekében, hogy az Log Analytics-ügynök könnyű legyen, az ügynök pufferei naplózzák és rendszeresen feltöltik őket a Azure Monitorba. A feltöltés gyakorisága 30 másodperc és 2 perc között változik az adatok típusától függően. A legtöbb adat 1 percenként van feltöltve. 

### <a name="network"></a>Network (Hálózat)
Előfordulhat, hogy a hálózati feltételek negatív hatással vannak az adatmennyiség késésére Azure Monitor naplók betöltési pontjának eléréséhez.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Azure-tevékenységek naplói, erőforrás-naplók és metrikák
Az Azure-beli adatmennyiség további időt vehet igénybe, hogy elérhetővé váljon Azure Monitor naplók betöltési pontja feldolgozásra:

- Az erőforrás-naplók jellemzően 30-90 másodpercet vesznek fel az Azure-szolgáltatástól függően. Néhány Azure-szolgáltatás (pontosabban Azure SQL Database és az Azure Virtual Network) jelenleg 5 perces időközönként jelentést készít a naplókról. Ez a folyamat tovább fejleszthető. Tekintse meg az [alábbi lekérdezést](#checking-ingestion-time) a késésnek a környezetben való vizsgálatához
- Az Azure platform metrikái további 3 percet is igénybe vehetnek, hogy Azure Monitor naplók betöltési pontján legyenek exportálva.
- A műveletnapló-információk további 10-15 percet is igénybe vehetnek, ha a rendszer örökölt integrációt használ. Javasoljuk, hogy az előfizetési szintű diagnosztikai beállítások használatával beolvassa a tevékenység naplóit Azure Monitor naplókba, amelyek további 30 másodperces késést okoznak.

### <a name="management-solutions-collection"></a>Felügyeleti megoldások gyűjteménye
Egyes megoldások nem gyűjtik az adatokat az ügynöktől, és olyan gyűjteményi módszert használnak, amely további késleltetést mutat be. Egyes megoldások rendszeres időközönként gyűjtenek adatokat a közel valós idejű gyűjtés megkísérlése nélkül. Bizonyos példák a következők:

- Microsoft 365 a megoldás a felügyeleti tevékenység API használatával kérdezi le a tevékenység naplóit, amely jelenleg nem biztosít közel valós idejű késési garanciát.
- A megoldás napi gyakorisággal gyűjti össze a Windows Analytics-megoldásokat (Update Compliance például).

A gyűjtemény gyakoriságának meghatározásához tekintse meg az egyes megoldások dokumentációját.

### <a name="pipeline-process-time"></a>Folyamat – feldolgozási idő

Ha a betöltési ponton elérhető, az adatmennyiség további 30-60 másodpercet vesz igénybe a lekérdezéshez.

Miután betöltötte a naplóbejegyzések betöltését a Azure Monitori folyamatba (ahogy az a [_TimeReceived](./log-standard-columns.md#_timereceived) tulajdonságban van meghatározva), a bérlők elkülönítésének biztosításához, valamint az adatok nem elvesztésének megtételéhez a rendszer az ideiglenes tárhelyre írja őket. Ez a folyamat általában 5-15 másodpercet vesz igénybe. Egyes felügyeleti megoldások súlyosabb algoritmusokat implementálnak az adatösszesítéshez és az elemzések kinyeréséhez, mivel az adatátviteli szolgáltatás a ben. A hálózati teljesítmény figyelése például 3 perces intervallumokban összesíti a bejövő adatokat, ami gyakorlatilag 3 perces késéssel jár. Egy másik folyamat, amely a késést adja meg, az egyéni naplókat kezelő folyamat. Bizonyos esetekben előfordulhat, hogy a folyamat néhány percet vesz igénybe az ügynök által a fájlokból gyűjtött naplók számára.

### <a name="new-custom-data-types-provisioning"></a>Új egyéni adattípusok kiépítés
Amikor új egyéni adattípust hoz létre egy [Egyéni naplóból](../agents/data-sources-custom-logs.md) vagy az adatgyűjtő [API](../logs/data-collector-api.md)-ból, a rendszer létrehoz egy dedikált tároló-tárolót. Ez egy egyszeri többletterhelés, amely csak ezen adattípus első megjelenésekor jelentkezik.

### <a name="surge-protection"></a>Túlfeszültség-védelem
Azure Monitor elsődleges prioritása annak biztosítása, hogy az ügyféladatok ne legyenek elveszve, így a rendszer beépített védelmet biztosít az adathullámok számára. Ilyenek például a pufferek, amelyekkel biztosítható, hogy a rendszer még az óriási terhelés mellett is működőképes maradjon. A normál betöltés alatt ezek a vezérlőelemek kevesebb mint egy percet vesznek igénybe, de szélsőséges körülmények között és meghibásodás esetén jelentős időt vehetnek igénybe, miközben az adat biztonságos.

### <a name="indexing-time"></a>Indexelési idő
Az elemzések és a speciális keresési funkciók között minden big data platformhoz beépített egyenleg áll rendelkezésre, amely nem biztosít azonnali hozzáférést az adateléréshez. Azure Monitor lehetővé teszi, hogy több milliárd rekordon futtasson hatékony lekérdezéseket, és néhány másodpercen belül eredményeket kapjon. Ez azért lehetséges, mert az infrastruktúra a betöltés során jelentősen átalakítja az adatmennyiséget, és az egyedi kompakt struktúrákban tárolja azt. A rendszer csak akkor távolítja el az adatmennyiséget, ha ahhoz elég, hogy létrehozza ezeket a struktúrákat. Ezt csak akkor kell elvégezni, ha a log rekord megjelenik a keresési eredmények között.

Ez a folyamat jelenleg körülbelül 5 percet vesz igénybe, ha kevés az adatmennyiség, de kevesebb idő van a magasabb adatforgalomra. Ez megszokás tűnik, de ez a folyamat lehetővé teszi a késések optimalizálását a nagy mennyiségű éles munkaterhelések esetében.



## <a name="checking-ingestion-time"></a>Betöltési idő ellenőrzése
A betöltési idő különböző körülmények között eltérő lehet. A naplózási lekérdezések segítségével azonosíthatja a környezet adott viselkedését. A következő táblázat azt ismerteti, hogyan határozható meg a rekord különböző időpontja, amikor a rendszer létrehozta és elküldje Azure Monitor.

| Lépés | Tulajdonság vagy függvény | Megjegyzések |
|:---|:---|:---|
| Rekord létrehozva az adatforrásban | [TimeGenerated](./log-standard-columns.md#timegenerated-and-timestamp) <br>Ha az adatforrás nem állítja be ezt az értéket, akkor a _TimeReceived-val megegyező időpontra lesz beállítva. |
| Azure Monitor betöltési végpont által fogadott rekord | [_TimeReceived](./log-standard-columns.md#_timereceived) | Ez a mező nem a tömeges feldolgozásra van optimalizálva, és nem használható nagy adatkészletek szűrésére. |
| A munkaterületen tárolt és a lekérdezésekhez elérhető rekord | [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) | A ingestion_time () használata ajánlott, ha csak olyan rekordokat kell szűrnie, amelyek egy adott időablakban vannak betöltve. Ilyen esetben javasolt a TimeGenerated-szűrő nagyobb tartománnyal való hozzáadása is. |

### <a name="ingestion-latency-delays"></a>Betöltési késés késése
Egy adott rekord késését mérhetővé teheti, ha összehasonlítja a [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) függvény eredményét a _TimeGenerated_ tulajdonsággal. Ezeket az adatmennyiségeket különböző összesítésekkel lehet használni, hogy megtudja, hogyan viselkedik a betöltési késés. Vizsgálja meg a betöltési idő néhány százalékos arányát, hogy nagy mennyiségű adatot kapjon. 

Például a következő lekérdezés megmutatja, hogy mely számítógépeken volt a legmagasabb betöltési idő az előző 8 órában: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Az előző percentilis-ellenőrzések hasznosak a késés általános trendjeinek megkereséséhez. Ha egy rövid távú csúcsot szeretne meghatározni a késésben, a maximális () érték használata `max()` hatékonyabb lehet.

Ha egy adott számítógép betöltési idejét egy adott időszakon belül szeretné részletezni, használja a következő lekérdezést, amely a diagramon az elmúlt nap adatait is megjeleníti: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
A következő lekérdezés használatával jelenítheti meg a számítógép betöltési idejét az azon ország/régió szerint, amelyben az IP-címeik alapulnak: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Az ügynökből származó különböző adattípusok eltérő betöltési késéssel rendelkezhetnek, így a korábbi lekérdezések más típusokkal is használhatók. A különböző Azure-szolgáltatások betöltési idejének vizsgálatához használja a következő lekérdezést: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Nem válaszoló erőforrások 
Bizonyos esetekben előfordulhat, hogy egy erőforrás leállítja az adatok küldését. Annak megismeréséhez, hogy egy erőforrás adatokat küld-e, vagy sem, tekintse meg a legutóbbi rekordot, amelyet a standard _TimeGenerated_ mezőben azonosíthat.  

A _szívverés_ táblázat segítségével ellenőrizhető a virtuális gép rendelkezésre állása, mivel az ügynök percenként egyszer elküldje a szívverést. A következő lekérdezéssel listázhatja azokat az aktív számítógépeket, amelyek nemrég jelentettek szívverést: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Következő lépések
* A Azure Monitor [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/en-us/support/legal/sla/monitor/v1_3/) beolvasása.
