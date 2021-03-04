---
title: Példák a Azure Monitor log lekérdezésre | Microsoft Docs
description: Példák a Azure Monitor a Kusto lekérdezési nyelvét használó naplózási lekérdezésekre.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 56866d8e255f15a93aaa9133f9cda9de4fdd9fd5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030800"
---
# <a name="azure-monitor-log-query-examples"></a>Példák a Azure Monitor log lekérdezésekre
Ez a cikk különféle példákat tartalmaz a [Kusto lekérdezési nyelvét](/azure/kusto/query/) használó [lekérdezések](./log-query-overview.md) különböző típusú naplófájljainak lekérésére Azure monitorból. Az adatösszesítés és az elemzés különböző módszerekkel történik, így ezek a minták a saját igényeihez esetlegesen használt különböző stratégiák azonosítására használhatók.  

A példákban használt különböző kulcsszavak részleteit a [Kusto nyelvi dokumentációjában](/azure/kusto/query/) tekintheti meg. Áttekintheti a [lekérdezéseket](./get-started-queries.md) , ha új Azure monitor.

## <a name="events"></a>Események

### <a name="search-application-level-events-described-as-cryptographic"></a>"Titkosítási"-ként leírt alkalmazás szintű események keresése
Ez a példa az **Events (események** ) táblában keres olyan rekordokat, amelyekben az **Eseménynapló** _alkalmazás_ -és **RenderedDescription** _titkosítási_ adatokat tartalmaz. Az elmúlt 24 órában nyilvántartott rekordokat tartalmazza.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Az átadással kapcsolatos események keresése
Az átadást _megemlítő_ rekordok keresése a táblák **esemény** -és **SecurityEvents** .

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Szívverés

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Az adatokat küldő számítógépek számának hetente több mint hét nézetét ábrázoló diagram

Az alábbi példa a szívverést küldő különálló számítógépek számát, hetente.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Elavult számítógépek keresése

A következő példa megkeresi az elmúlt nap során aktív számítógépeket, de az elmúlt órában nem küldött szívveréseket.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>A legújabb szívverési rekord beszerzése számítógépes IP-címen

Ez a példa az összes számítógép IP-címének utolsó szívverési rekordját adja vissza.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Védett állapotüzenetek egyeztetése szívverési rekordokkal

Ez a példa a kapcsolódó védelmi állapotokra vonatkozó rekordokat és szívverési rekordokat talál, amelyek a számítógép és az idő szerint egyeznek.
Figyelje meg, hogy a Time mező a legközelebbi percre van kerekítve. A futásidejű raktárhely számítását a következőre használtuk: `round_time=bin(TimeGenerated, 1m)` .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Kiszolgáló rendelkezésre állási aránya

A kiszolgáló rendelkezésre állási arányának kiszámítása szívverési rekordok alapján. A rendelkezésre állás a "legalább 1 szívverés/óra" értékkel van meghatározva.
Tehát, ha egy kiszolgáló 100 óra 98-es rendelkezésre áll, a rendelkezésre állási arány 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Több adattípus

### <a name="chart-the-record-count-per-table"></a>Diagram – a rekordok száma táblázat szerint
Az alábbi példa az elmúlt öt órában az összes tábla összes rekordját gyűjti, és megszámolja, hogy hány rekord volt az egyes táblákban. Az eredmények egy idődiagramját jelennek meg.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Az elmúlt órában gyűjtött összes napló száma típus szerint
Az alábbi példa az elmúlt órában megjelenő összes jelentést keresi, és az egyes táblák rekordjait **típus** szerint számolja. Az eredmények sáv diagramon jelennek meg.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Az Azure Diagnostics-rekordok száma kategóriánként
Ez a példa az összes Azure Diagnostics-rekordot megszámolja mindegyik egyedi kategóriához.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Véletlenszerű rekord beolvasása minden egyes egyedi kategóriához
Ebben a példában egyetlen véletlenszerű Azure Diagnostics-rekordot kap minden egyes egyedi kategóriához.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>A legújabb rekord/kategória beszerzése
Ez a példa minden egyes egyedi kategóriában beolvassa az Azure Diagnostics legújabb rekordját.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Hálózatfigyelés

### <a name="computers-with-unhealthy-latency"></a>Sérült késéssel rendelkező számítógépek
Ez a példa olyan különálló számítógépek listáját hozza létre, amelyek nem megfelelő állapotú késéssel rendelkeznek.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Teljesítmény

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>A memória és a processzor összekapcsolásához csatlakoztassa a számítógépekhez tartozó Perf-rekordokat
Ez a példa egy adott számítógép **perf** -rekordjait kapcsolja össze, és két idődiagramot hoz létre, az átlagos CPU-t és a maximális memóriát.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Teljesítményfigyelő CPU-kihasználtsági gráfja számítógépenként
Ez a példa a _contoso_-vel KEZDŐDŐ számítógépek CPU-kihasználtságának kiszámítását és diagramokat számítja ki.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Védelmi állapot

### <a name="computers-with-non-reporting-protection-status-duration"></a>Nem jelentéskészítési védelmi állapottal rendelkező számítógépek
Ez a példa azokat a számítógépeket sorolja fel, amelyek védelmi állapota _nem jelentett_  , és az adott állapotban lévő időtartam.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Védett állapotüzenetek egyeztetése szívverési rekordokkal
Ez a példa a kapcsolódó védelmi állapot rekordokat és a szívverési rekordokat is megkeresi a számítógép és az idő alapján.
Az időmezőt a rendszer a legközelebbi percre kerekíti a **bin** használatával.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Biztonsági rekordok

### <a name="count-security-events-by-activity-id"></a>Biztonsági események száma tevékenység azonosítója szerint


Ez a példa a **tevékenység** oszlopának rögzített struktúrájára támaszkodik: \<ID\> - \<Name\> .
Elemzi a **tevékenység** értékét két új oszlopra, és megszámolja az egyes **tevékenységazonosító** előfordulását.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Az engedélyekkel kapcsolatos biztonsági események száma
Ebben a példában a **securityEvent** rekordok száma látható, amelyekben a **tevékenység** oszlop a teljes kifejezésre _vonatkozó engedélyeket_ tartalmazza. A lekérdezés az elmúlt 30 percben létrehozott rekordokra vonatkozik.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Olyan fiókok keresése, amelyek nem tudnak biztonsági észleléssel bejelentkezni a számítógépekről
Ez a példa megkeresi és megszámolja azokat a fiókokat, amelyek nem tudnak bejelentkezni a biztonsági észlelést észlelő számítógépekről.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Elérhetők a biztonsági adataim?
Az adatfeltárás megkezdése gyakran az adatrendelkezésre állás-ellenőrzési szolgáltatással kezdődik. Ez a példa a **SecurityEvent** -rekordok számát mutatja az elmúlt 30 percben.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Elemzési tevékenység neve és azonosítója
Az alábbi két példa a **tevékenység** oszlop rögzített struktúrájára támaszkodik: \<ID\> - \<Name\> . Az első példa az **elemzési** operátort használja az értékek két új oszlophoz való hozzárendeléséhez: **tevékenységazonosító** és **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Ez a példa a **felosztott** operátort használja különálló értékek tömb létrehozásához.
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicit hitelesítő adatok feldolgozása
Az alábbi példa egy tortadiagramot mutat be, amely az elmúlt héten explicit hitelesítő adatokat használt.

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Leggyakrabban futó folyamatok

Az alábbi példa egy idővonalat mutat be az öt leggyakoribb folyamathoz az elmúlt három napban.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Ismétlődő sikertelen bejelentkezési kísérletek megkeresése ugyanazzal a fiókkal különböző IP-címekről

Az alábbi példa megkeresi a sikertelen bejelentkezési kísérleteket ugyanazzal a fiókkal az elmúlt hat órában több mint öt különböző IP-címről.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Azon felhasználói fiókok keresése, amelyek nem tudnak bejelentkezni 
Az alábbi példa azokat a felhasználói fiókokat azonosítja, amelyek az elmúlt nap során ötnél többször nem voltak bejelentkezve, és amikor utoljára próbáltak bejelentkezni.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

A **JOIN** és a **let** utasítások segítségével ellenőrizhető, hogy ugyanazok a gyanús fiókok később sikeresen be tudnak-e jelentkezni.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Használat

Az `Usage` adattípus segítségével nyomon követheti a betöltött adatmennyiséget megoldás vagy adattípus alapján. A betöltött adatmennyiségeket a [számítógép](./manage-cost-storage.md#data-volume-by-computer) vagy az [Azure-előfizetés, az erőforráscsoport vagy az erőforrás](./manage-cost-storage.md#data-volume-by-azure-resource-resource-group-or-subscription)alapján is megvizsgálhatja.

#### <a name="data-volume-by-solution"></a>Adatmennyiség megoldásonként

A számlázható adatmennyiségnek az előző hónapban a megoldással való megtekintésére használt lekérdezés (az utolsó részleges nap kivételével) a következő:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Vegye figyelembe, hogy a záradék `where IsBillable = true` kiszűri az adattípusokat bizonyos olyan megoldásokból, amelyekhez nincs betöltési díj.  Emellett a záradéka `TimeGenerated` csak annak biztosítása érdekében, hogy a Azure Portal lekérdezési élménye az alapértelmezett 24 órában is visszatekinthető legyen. A használati adatok típusának használatakor, `StartTime` valamint `EndTime` az eredmények megjelenítéséhez használt időgyűjtők. 

#### <a name="data-volume-by-type"></a>Adatmennyiség típus szerint

További részletezéssel tekintheti meg az adattípussal kapcsolatos adattrendeket:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Vagy megtekintheti az előző hónapra vonatkozó táblát megoldás és típus szerint.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> A használati adattípus egyes mezői, miközben még mindig a séma része, elavultak, és az értékek már nem lesznek feltöltve. Ezek a **számítógépek** , valamint a betöltéssel kapcsolatos mezők (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** és **AverageProcessingTimeMs**).

## <a name="updates"></a>Frissítések

### <a name="computers-still-missing-updates"></a>A számítógépek még mindig hiányoznak a frissítések
Ez a példa azoknak a számítógépeknek a listáját jeleníti meg, amelyek néhány nappal ezelőtt hiányoznak egy vagy több kritikus frissítésből, és még mindig hiányoznak a frissítések.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Következő lépések

- A nyelvre vonatkozó részletekért tekintse meg a [Kusto nyelvi referenciáját](/azure/kusto/query) .
- Végigvezeti a [Azure monitorban található naplók írására szolgáló leckén](./get-started-queries.md).