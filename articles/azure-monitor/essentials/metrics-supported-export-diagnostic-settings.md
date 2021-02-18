---
title: A metrikák exportálási viselkedése NULL és nulla értékkel
description: NULL vagy nulla értékek egyeztetése a metrikák exportálásakor, valamint egy mutató egy olyan listára, amely nem exportálható a metrikák listájára.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: 8f503623031b7071c5e0a206d078175e16a7e919
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617108"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>A diagnosztikai beállításokon keresztül exportálható Azure Monitor-platformmetrikák

A Azure Monitor alapértelmezés szerint a [platform metrikáit](../essentials/data-platform-metrics.md) biztosítja konfiguráció nélkül. Számos lehetőséget kínál a platform metrikáinak interakcióra, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Lásd: [mérőszámok – támogatott](../platform/metrics-supported.md) a Azure monitor konszolidált metrikus folyamatával jelenleg elérhető platform-metrikák teljes listájához. A metrikák lekérdezéséhez és eléréséhez használja az [2018-01-01 API-Version](/rest/api/monitor/metricdefinitions). Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>A metrikák nem exportálhatók diagnosztikai beállítások használatával

Az ezen a helyen használt tartalom át lett helyezve [Azure monitor mérőszámok támogatott listájára](../platform/metrics-supported.md#exporting-platform-metrics-to-other-locations).

A mérőszámok diagnosztikai beállításokon keresztüli exportálásának korlátai vannak. Az összes metrika exportálható a REST API használatával. 

## <a name="exported-zero-vs-null-values"></a>Exportált nulla vs NULL értékek 

A metrikák eltérő viselkedéssel rendelkeznek, ha 0 vagy NULL értéket kell kezelni.  Egyes mérőszámok 0. jelentés, ha nem szerezhetők be adatok, például a http-hibák metrikái. A többi metrika NULL értéket tár, ha nem szereznek be adatokat, mert jelezheti, hogy az erőforrás offline állapotban van. A mérőszámok a [szaggatott vonalként](metrics-troubleshoot.md#chart-shows-dashed-line)megjelenített null értékekkel való ábrázolásakor a különbség látható. 

Ha a platform metrikái a diagnosztikai beállítások használatával exportálhatók, azok megfelelnek a metrika viselkedésének. Tehát NULLák exportálhatók, ha az erőforrás nem küld adatokat.  A 0 értéket csak akkor exportálják, ha a mögöttes erőforrás valóban kiadta őket. 

Ha töröl egy erőforráscsoportot vagy egy adott erőforrást, az érintett erőforrásokból származó metrikai adatokat a rendszer többé nem küldi el a diagnosztikai beállítás exportálási helyeire. Ez azt eredményezi, hogy az Event Hubs, az Azure Storage-fiókokban és a Log Analytics-munkaterületeken már nem jelenik meg.

### <a name="metrics-that-used-to-export-zero-for-null"></a>A null értékű nulla exportálására használt metrikák

2020. június 1. előtt az alábbi metrikák a "0" kiadását **használták** , amikor nem történt adat. Ezeket a nullákat később olyan alsóbb rétegbeli rendszerekre lehet exportálni, mint a Log Analytics és az Azure Storage.  Ez a viselkedés zavart okozott a valós "0s" (erőforrás által kibocsátott) és az "0s" (NULLs) értelmezése között, és így a viselkedés úgy módosult, hogy megfeleljen az előző szakaszban említett alapul szolgáló metrika értékének. 

A változás a nyilvános és a privát felhőkben történt.

A módosítás nem befolyásolta a következő tapasztalatok valamelyikének viselkedését: 
   - A platform erőforrás-naplói a diagnosztikai beállítások használatával lettek exportálva
   - Metrikák ábrázolási Metrikaböngésző
   - A platform metrikáinak riasztása
 
A következő lista felsorolja azokat a metrikákat, amelyek viselkedése megváltozott. 

| ResourceType                    | Metric               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Nem engedélyezett átjáró-kérelmek (elavult)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Összes átjáró kérelme (elavult)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  Sikeres átjáró-kérelmek (elavult)  | 
| Microsoft.ApiManagement/service | Kérelmek |  Kérelmek  | 
| Microsoft.ApiManagement/service | OtherRequests |  Egyéb átjáró-kérelmek (elavult)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Sikertelen átjáró-kérelmek (elavult)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Sikertelen EventHub események  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Összes EventHub esemény  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  EventHub-események mérete  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Időtúllépés a EventHub eseményeinél  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Szabályozott EventHub események  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Sikeres EventHub események  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  EventHub-események visszautasítva  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Eldobott EventHub események  | 
| Microsoft.ApiManagement/service | Időtartam |  Az átjárók kéréseinek teljes időtartama  | 
| Microsoft.ApiManagement/service | BackendDuration |  A háttérbeli kérelmek időtartama  | 
| Microsoft. DBforMariaDB/kiszolgálók | storage_used |  Felhasznált tárterület  | 
| Microsoft. DBforMariaDB/kiszolgálók | storage_percent |  Tárolási százalék  | 
| Microsoft. DBforMariaDB/kiszolgálók | storage_limit |  Tárolási korlát  | 
| Microsoft. DBforMariaDB/kiszolgálók | serverlog_storage_usage |  Kiszolgáló naplójának tárolója  | 
| Microsoft. DBforMariaDB/kiszolgálók | serverlog_storage_percent |  Kiszolgáló naplójának tárolási százaléka  | 
| Microsoft. DBforMariaDB/kiszolgálók | serverlog_storage_limit |  Kiszolgáló naplójának tárolási korlátja  | 
| Microsoft. DBforMariaDB/kiszolgálók | seconds_behind_master |  Replikálás késése másodpercben  | 
| Microsoft. DBforMariaDB/kiszolgálók | network_bytes_ingress |  Bejövő hálózat  | 
| Microsoft. DBforMariaDB/kiszolgálók | network_bytes_egress |  Kimenő hálózat  | 
| Microsoft. DBforMariaDB/kiszolgálók | memory_percent |  Memória százaléka  | 
| Microsoft. DBforMariaDB/kiszolgálók | io_consumption_percent |  IO-százalék  | 
| Microsoft. DBforMariaDB/kiszolgálók | cpu_percent |  CPU-százalék  | 
| Microsoft. DBforMariaDB/kiszolgálók | connections_failed |  Sikertelen kapcsolatok  | 
| Microsoft. DBforMariaDB/kiszolgálók | backup_storage_used |  Felhasznált biztonsági mentési tár  | 
| Microsoft. DBforMariaDB/kiszolgálók | active_connections |  Aktív kapcsolatok  | 
| Microsoft. DBforMySQL/kiszolgálók | storage_used |  Felhasznált tárterület  | 
| Microsoft. DBforMySQL/kiszolgálók | storage_percent |  Tárolási százalék  | 
| Microsoft. DBforMySQL/kiszolgálók | storage_limit |  Tárolási korlát  | 
| Microsoft. DBforMySQL/kiszolgálók | serverlog_storage_usage |  Kiszolgáló naplójának tárolója  | 
| Microsoft. DBforMySQL/kiszolgálók | serverlog_storage_percent |  Kiszolgáló naplójának tárolási százaléka  | 
| Microsoft. DBforMySQL/kiszolgálók | serverlog_storage_limit |  Kiszolgáló naplójának tárolási korlátja  | 
| Microsoft. DBforMySQL/kiszolgálók | seconds_behind_master |  Replikálás késése másodpercben  | 
| Microsoft. DBforMySQL/kiszolgálók | network_bytes_ingress |  Bejövő hálózat  | 
| Microsoft. DBforMySQL/kiszolgálók | network_bytes_egress |  Kimenő hálózat  | 
| Microsoft. DBforMySQL/kiszolgálók | memory_percent |  Memória százaléka  | 
| Microsoft. DBforMySQL/kiszolgálók | io_consumption_percent |  IO-százalék  | 
| Microsoft. DBforMySQL/kiszolgálók | cpu_percent |  CPU-százalék  | 
| Microsoft. DBforMySQL/kiszolgálók | connections_failed |  Sikertelen kapcsolatok  | 
| Microsoft. DBforMySQL/kiszolgálók | backup_storage_used |  Felhasznált biztonsági mentési tár  | 
| Microsoft. DBforMySQL/kiszolgálók | active_connections |  Aktív kapcsolatok  | 
| Microsoft. Devices/Account | digitaltwins. telemetria. csomópontok |  Digitális Twins-csomópont telemetria helyőrzője  | 
| Microsoft. Devices/IotHubs | twinQueries. success |  Sikeres Twin-lekérdezések  | 
| Microsoft. Devices/IotHubs | twinQueries.resultSize |  Dupla lekérdezések eredményének mérete  | 
| Microsoft. Devices/IotHubs | twinQueries. hiba |  Sikertelen dupla lekérdezések  | 
| Microsoft. Devices/IotHubs | feladatok. queryJobs. sikeres |  Sikeres feladatok lekérdezése  | 
| Microsoft. Devices/IotHubs | feladatok. queryJobs. hiba |  Sikertelen feladatok lekérdezése  | 
| Microsoft. Devices/IotHubs | feladatok. listJobs. sikeres |  Sikeres hívások a feladatok listázásához  | 
| Microsoft. Devices/IotHubs | feladatok. listJobs. hiba |  Sikertelen hívások a feladatok listázásához  | 
| Microsoft. Devices/IotHubs | feladatok. sikertelen |  Sikertelen feladatok  | 
| Microsoft. Devices/IotHubs | feladatok. createTwinUpdateJob. sikeres |  A kettős frissítési feladatok sikeres létrehozása  | 
| Microsoft. Devices/IotHubs | feladatok. createTwinUpdateJob. hiba |  A kettős frissítési feladatok sikertelen létrehozása  | 
| Microsoft. Devices/IotHubs | feladatok. createDirectMethodJob. sikeres |  Metódus-Meghívási feladatok sikeres létrehozása  | 
| Microsoft. Devices/IotHubs | feladatok. createDirectMethodJob. hiba |  Nem sikerült létrehozni a metódus Meghívási feladatait  | 
| Microsoft. Devices/IotHubs | feladatok. kész |  Befejezett feladatok  | 
| Microsoft. Devices/IotHubs | feladatok. cancelJob. sikeres |  Sikeres feladatok törlése  | 
| Microsoft. Devices/IotHubs | feladatok. cancelJob. hiba |  Sikertelen feladatok megszakítása  | 
| Microsoft. Devices/IotHubs | EventGridLatency |  Event Grid késés (előzetes verzió)  | 
| Microsoft. Devices/IotHubs | EventGridDeliveries |  Event Grid kézbesítések (előzetes verzió)  | 
| Microsoft. Devices/IotHubs | Devices. totalDevices |  Összes eszköz (elavult)  | 
| Microsoft. Devices/IotHubs | Devices. connectedDevices. allProtocol |  Csatlakoztatott eszközök (elavult)   | 
| Microsoft. Devices/IotHubs | deviceDataUsageV2 |  Az eszköz összes adatfelhasználása (előzetes verzió)  | 
| Microsoft. Devices/IotHubs | deviceDataUsage |  Az eszköz összes adatfelhasználása  | 
| Microsoft. Devices/IotHubs | dailyMessageQuotaUsed |  A felhasznált üzenetek teljes száma  | 
| Microsoft. Devices/IotHubs | D2C. Twin. Update. success |  Sikeres dupla frissítések az eszközökről  | 
| Microsoft. Devices/IotHubs | D2C. Twin. Update. size |  Az eszközökből származó kettős frissítések mérete  | 
| Microsoft. Devices/IotHubs | D2C. Twin. Update. failure |  Sikertelen dupla frissítések az eszközökről  | 
| Microsoft. Devices/IotHubs | D2C. Twin. Read. success |  Sikeres dupla olvasások az eszközökről  | 
| Microsoft. Devices/IotHubs | D2C. Twin. Read. size |  Az eszközökről érkező kettős olvasások válaszának mérete  | 
| Microsoft. Devices/IotHubs | D2C. Twin. Read. failure |  Sikertelen dupla olvasások az eszközökről  | 
| Microsoft. Devices/IotHubs | D2C. telemetria. beáramló. sikeres |  Telemetria üzenetek elküldése  | 
| Microsoft. Devices/IotHubs | D2C. telemetria. beáramló. sendThrottle |  Szabályozási hibák száma  | 
| Microsoft. Devices/IotHubs | D2C. telemetria. beáramló. allProtocol |  Telemetria üzenetek küldése  | 
| Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. sikeres |  Útválasztás: telemetria üzenetek kézbesítése  | 
| Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. árva |  Útválasztás: árva telemetria üzenetek   | 
| Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. érvénytelen |  Útválasztás: Inkompatibilis telemetria-üzenetek  | 
| Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. tartalék |  Útválasztás: tartalékként továbbított üzenetek  | 
| Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. eldobott |  Útválasztás: telemetria üzenetek elvetve   | 
| Microsoft. Devices/IotHubs | D2C. endpoints. látencia. Storage |  Útválasztás: üzenetek késése a tároláshoz  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. serviceBusTopics |  Útválasztás: Service Bus témakör üzenetének késése  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. serviceBusQueues |  Útválasztás: Service Bus üzenetsor üzenet-késése  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. eventHubs |  Útválasztás: az Event hub üzenetének késése  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. beépített. események |  Útválasztás: üzenetek/események üzenetének késése  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. Storage. Bytes |  Útválasztás: a tárolóba szállított adatmennyiség  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. Storage. Blobok |  Útválasztás: tárolóba szállított Blobok  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. Storage |  Útválasztás: a tárolóba küldött üzenetek  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. serviceBusTopics |  Útválasztás: Service Bus témakörbe küldött üzenetek  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. serviceBusQueues |  Útválasztás: Service Bus üzenetsor számára továbbított üzenetek  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. eventHubs |  Útválasztás: az Event hub számára továbbított üzenetek  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. beépített. események |  Útválasztás: üzenetek/események küldésére küldött üzenetek  | 
| Microsoft. Devices/IotHubs | konfigurációk |  Konfigurációs mérőszámok  | 
| Microsoft. Devices/IotHubs | C2DMessagesExpired |  C2D-üzenetek lejárt (előzetes verzió)  | 
| Microsoft. Devices/IotHubs | C2D. Twin. Update. success |  Sikeres dupla frissítések a háttérből  | 
| Microsoft. Devices/IotHubs | C2D. Twin. Update. size |  Dupla frissítések mérete a háttérből  | 
| Microsoft. Devices/IotHubs | C2D. Twin. Update. failure |  Sikertelen dupla frissítések a háttérből  | 
| Microsoft. Devices/IotHubs | C2D. Twin. Read. success |  Sikeres dupla olvasások a háttérből  | 
| Microsoft. Devices/IotHubs | C2D. Twin. Read. size |  Dupla olvasások válaszának mérete a háttérből  | 
| Microsoft. Devices/IotHubs | C2D. Twin. Read. failure |  Sikertelen dupla olvasások a háttérből  | 
| Microsoft. Devices/IotHubs | C2D. Methods. success |  Közvetlen metódusok sikeres meghívása  | 
| Microsoft. Devices/IotHubs | C2D. Methods. responseSize |  A közvetlen metódus-meghívások válaszának mérete  | 
| Microsoft. Devices/IotHubs | C2D. Methods. requestSize |  A közvetlen metódus meghívásának mérete  | 
| Microsoft. Devices/IotHubs | C2D. Methods. failure |  Sikertelen közvetlen metódusok meghívása  | 
| Microsoft. Devices/IotHubs | C2D. commands. kimenő. elutasítás. sikeres |  C2D-üzenetek elutasítva  | 
| Microsoft. Devices/IotHubs | C2D. commands. kimenő. Complete. success |  C2D-üzenetek kézbesítésének befejezése  | 
| Microsoft. Devices/IotHubs | C2D. commands. kimenő. elhagyása. sikeres |  C2D üzenetek elhagyva  | 
| Microsoft. Devices/provisioningServices | RegistrationAttempts |  Regisztrációs kísérletek  | 
| Microsoft. Devices/provisioningServices | DeviceAssignments |  Hozzárendelt eszközök  | 
| Microsoft. Devices/provisioningServices | AttestationAttempts |  Igazolási kísérletek  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Kérelmek összes egysége  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Összes kérelem  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo kérelmek  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Mongo-kérelem díja  | 
| Microsoft. EventGrid/tartományok | PublishSuccessLatencyInMs |  Sikeres közzétételi késés  | 
| Microsoft. EventGrid/tartományok | PublishSuccessCount |  Közzétett események  | 
| Microsoft. EventGrid/tartományok | PublishFailCount |  Sikertelen események közzététele  | 
| Microsoft. EventGrid/tartományok | MatchedEventCount |  Egyeztetett események  | 
| Microsoft. EventGrid/tartományok | DroppedEventCount |  Eldobott események  | 
| Microsoft. EventGrid/tartományok | DeliverySuccessCount |  Kézbesítési események  | 
| Microsoft. EventGrid/tartományok | DeadLetteredCount |  Kézbesítetlen levelek eseményei  | 
| Microsoft. EventGrid/eventSubscriptions | MatchedEventCount |  Egyeztetett események  | 
| Microsoft. EventGrid/eventSubscriptions | DroppedEventCount |  Eldobott események  | 
| Microsoft. EventGrid/eventSubscriptions | DeliverySuccessCount |  Kézbesítési események  | 
| Microsoft. EventGrid/eventSubscriptions | DeadLetteredCount |  Kézbesítetlen levelek eseményei  | 
| Microsoft. EventGrid/extensionTopics | UnmatchedEventCount |  Páratlan események  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Sikeres közzétételi késés  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessCount |  Közzétett események  | 
| Microsoft. EventGrid/extensionTopics | PublishFailCount |  Sikertelen események közzététele  | 
| Microsoft. EventGrid/témakörök | UnmatchedEventCount |  Páratlan események  | 
| Microsoft. EventGrid/témakörök | PublishSuccessLatencyInMs |  Sikeres közzétételi késés  | 
| Microsoft. EventGrid/témakörök | PublishSuccessCount |  Közzétett események  | 
| Microsoft. EventGrid/témakörök | PublishFailCount |  Sikertelen események közzététele  | 
| Microsoft. EventHub/fürtök | OutgoingMessages |  Kimenő üzenetek  | 
| Microsoft. EventHub/fürtök | OutgoingBytes |  Kimenő bájtok.  | 
| Microsoft. EventHub/fürtök | IncomingRequests |  Bejövő kérelmek  | 
| Microsoft. EventHub/fürtök | IncomingMessages |  Bejövő üzenetek  | 
| Microsoft. EventHub/fürtök | IncomingBytes |  Bejövő bájtok.  | 
| Microsoft. EventHub/névterek | SVRBSY |  Kiszolgáló által foglalt hibák (elavult)  | 
| Microsoft. EventHub/névterek | SUCCREQ |  Sikeres kérések (elavult)  | 
| Microsoft. EventHub/névterek | OUTMSGS |  Kimenő üzenetek (elavult) (elavult)  | 
| Microsoft. EventHub/névterek | OutgoingMessages |  Kimenő üzenetek  | 
| Microsoft. EventHub/névterek | OutgoingBytes |  Kimenő bájtok.  | 
| Microsoft. EventHub/névterek | MISCERR |  Egyéb hibák (elavult)  | 
| Microsoft. EventHub/névterek | INTERer |  Belső kiszolgálói hibák (elavult)  | 
| Microsoft. EventHub/névterek | INREQS |  Bejövő kérelmek (elavult)  | 
| Microsoft. EventHub/névterek | INMSGS |  Bejövő üzenetek (elavult) (elavult)  | 
| Microsoft. EventHub/névterek | IncomingRequests |  Bejövő kérelmek  | 
| Microsoft. EventHub/névterek | IncomingMessages |  Bejövő üzenetek  | 
| Microsoft. EventHub/névterek | IncomingBytes |  Bejövő bájtok.  | 
| Microsoft. EventHub/névterek | FAILREQ |  Sikertelen kérelmek (elavult)  | 
| Microsoft. EventHub/névterek | EHOUTMSGS |  Kimenő üzenetek (elavult)  | 
| Microsoft. EventHub/névterek | EHOUTMBS |  Kimenő bájtok (elavult) (elavult)  | 
| Microsoft. EventHub/névterek | EHOUTBYTES |  Kimenő bájtok (elavult)  | 
| Microsoft. EventHub/névterek | EHINMSGS |  Bejövő üzenetek (elavult)  | 
| Microsoft. EventHub/névterek | EHINMBS |  Bejövő bájtok (elavult) (elavult)  | 
| Microsoft. EventHub/névterek | EHINBYTES |  Bejövő bájtok (elavult)  | 
| Microsoft. EventHub/névterek | EHAMSGS |  Archivált üzenetek (elavult)  | 
| Microsoft. EventHub/névterek | EHAMBS |  Archivált üzenetek átviteli sebessége (elavult)  | 
| Microsoft. EventHub/névterek | EHABL |  Archivált várakozó üzenetek (elavult)  | 
| Microsoft. HDInsight/fürtök | NumActiveWorkers |  Aktív feldolgozók száma  | 
| Microsoft. HDInsight/fürtök | GatewayRequests |  Átjáróra vonatkozó kérelmek  | 
| Microsoft. HDInsight/fürtök | CategorizedGatewayRequests |  Kategorizált átjáró-kérelmek  | 
| Microsoft. bepillantások/AutoscaleSettings | ScaleActionsInitiated |  Elindított skálázási műveletek  | 
| Microsoft. bepillantások/összetevők | nyomkövetés/darabszám |  Hívásláncok  | 
| Microsoft. bepillantások/összetevők | performanceCounters/requestsPerSecond |  HTTP-kérelmek gyakorisága  | 
| Microsoft. bepillantások/összetevők | performanceCounters/requestsInQueue |  HTTP-kérelmek az alkalmazás-várólistában  | 
| Microsoft. bepillantások/összetevők | performanceCounters/exceptionsPerSecond |  Kivételek aránya  | 
| Microsoft. bepillantások/összetevők | Oldalmegtekintések/darabszám |  Lapok nézetei  | 
| Microsoft. bepillantások/összetevők | kivételek/darabszám |  Kivételek  | 
| Microsoft. Kusto/fürtök | StreamingIngestResults |  Streamelés betöltési eredménye  | 
| Microsoft. Kusto/fürtök | StreamingIngestDuration |  Streamelés betöltési időtartama  | 
| Microsoft. Kusto/fürtök | StreamingIngestDataRate |  Streamelés adatbetöltési sebessége  | 
| Microsoft. Kusto/fürtök | SteamingIngestRequestRate |  Streamelési betöltési kérelmek gyakorisága  | 
| Microsoft. Kusto/fürtök | QueryDuration |  Lekérdezés időtartama  | 
| Microsoft. Kusto/fürtök | KeepAlive |  Életben tartása  | 
| Microsoft. Kusto/fürtök | IngestionVolumeInMB |  Betöltési mennyiség (MB)  | 
| Microsoft. Kusto/fürtök | IngestionUtilization |  Betöltési kihasználtság  | 
| Microsoft. Kusto/fürtök | IngestionResult |  Betöltés eredménye  | 
| Microsoft. Kusto/fürtök | IngestionLatencyInSeconds |  Betöltési késleltetés (másodperc)  | 
| Microsoft. Kusto/fürtök | ExportUtilization |  Exportálás kihasználtsága  | 
| Microsoft. Kusto/fürtök | EventsProcessedForEventHubs |  Feldolgozott események (Event/IoT hubok esetében)  | 
| Microsoft. Kusto/fürtök | CPU |  CPU  | 
| Microsoft. Kusto/fürtök | ContinuousExportResult |  Folyamatos exportálás eredménye  | 
| Microsoft. Kusto/fürtök | ContinuousExportPendingCount |  Folyamatos exportálás függőben lévő darabszáma  | 
| Microsoft. Kusto/fürtök | ContinuousExportNumOfRecordsExported |  Folyamatos exportálás – számú exportált rekord  | 
| Microsoft. Kusto/fürtök | ContinuousExportMaxLatenessMinutes |  Folyamatos exportálás maximális késői percben  | 
| Microsoft. Kusto/fürtök | CacheUtilization |  Gyorsítótár kihasználtsága  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Kiváltott események indítása  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSucceeded |  Sikeres eseményindítók   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersStarted |  Elindított eseményindítók   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSkipped |  Kihagyott eseményindítók  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFired |  Kilőtt eseményindítók   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFailed |  Sikertelen eseményindítók   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersCompleted |  Befejezett eseményindítók   | 
| Microsoft. Logic/integrationServiceEnvironments | RunThrottledEvents |  Szabályozott események futtatása  | 
| Microsoft. Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Indítási leszabályozású események futtatása  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsSucceeded |  Sikeres futtatások  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsStarted |  Elindított futtatások  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsFailed |  Sikertelen futtatások  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCompleted |  Befejezett futtatások  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCancelled |  Megszakított futtatások  | 
| Microsoft. Logic/integrationServiceEnvironments | RunFailurePercentage |  Sikertelen futtatások százalékos aránya  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionThrottledEvents |  Művelet által szabályozott események  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSucceeded |  Sikeres műveletek   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsStarted |  Elindított műveletek   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSkipped |  Kihagyott műveletek   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsFailed |  Sikertelen műveletek   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsCompleted |  Befejezett műveletek   | 
| Microsoft. Logic/munkafolyamatok | TriggerThrottledEvents |  Kiváltott események indítása  | 
| Microsoft. Logic/munkafolyamatok | TriggersSucceeded |  Sikeres eseményindítók   | 
| Microsoft. Logic/munkafolyamatok | TriggersStarted |  Elindított eseményindítók   | 
| Microsoft. Logic/munkafolyamatok | TriggersSkipped |  Kihagyott eseményindítók  | 
| Microsoft. Logic/munkafolyamatok | TriggersFired |  Kilőtt eseményindítók   | 
| Microsoft. Logic/munkafolyamatok | TriggersFailed |  Sikertelen eseményindítók   | 
| Microsoft. Logic/munkafolyamatok | TriggersCompleted |  Befejezett eseményindítók   | 
| Microsoft. Logic/munkafolyamatok | TotalBillableExecutions |  Számlázandó végrehajtások összesen  | 
| Microsoft. Logic/munkafolyamatok | RunThrottledEvents |  Szabályozott események futtatása  | 
| Microsoft. Logic/munkafolyamatok | RunStartThrottledEvents |  Indítási leszabályozású események futtatása  | 
| Microsoft. Logic/munkafolyamatok | RunsSucceeded |  Sikeres futtatások  | 
| Microsoft. Logic/munkafolyamatok | RunsStarted |  Elindított futtatások  | 
| Microsoft. Logic/munkafolyamatok | RunsFailed |  Sikertelen futtatások  | 
| Microsoft. Logic/munkafolyamatok | RunsCompleted |  Befejezett futtatások  | 
| Microsoft. Logic/munkafolyamatok | RunsCancelled |  Megszakított futtatások  | 
| Microsoft. Logic/munkafolyamatok | RunFailurePercentage |  Sikertelen futtatások százalékos aránya  | 
| Microsoft. Logic/munkafolyamatok | BillingUsageStorageConsumption |  Számlázási használat a tárolási felhasználás végrehajtásához  | 
| Microsoft. Logic/munkafolyamatok | BillingUsageStorageConsumption |  Számlázási használat a tárolási felhasználás végrehajtásához  | 
| Microsoft. Logic/munkafolyamatok | BillingUsageStandardConnector |  A standard szintű összekötők végrehajtásának számlázási használata  | 
| Microsoft. Logic/munkafolyamatok | BillingUsageStandardConnector |  A standard szintű összekötők végrehajtásának számlázási használata  | 
| Microsoft. Logic/munkafolyamatok | BillingUsageNativeOperation |  A natív művelet végrehajtásához használt számlázási használat  | 
| Microsoft. Logic/munkafolyamatok | BillingUsageNativeOperation |  A natív művelet végrehajtásához használt számlázási használat  | 
| Microsoft. Logic/munkafolyamatok | BillableTriggerExecutions |  Számlázható trigger-végrehajtások  | 
| Microsoft. Logic/munkafolyamatok | BillableActionExecutions |  Számlázható műveletek végrehajtása  | 
| Microsoft. Logic/munkafolyamatok | ActionThrottledEvents |  Művelet által szabályozott események  | 
| Microsoft. Logic/munkafolyamatok | ActionsSucceeded |  Sikeres műveletek   | 
| Microsoft. Logic/munkafolyamatok | ActionsStarted |  Elindított műveletek   | 
| Microsoft. Logic/munkafolyamatok | ActionsSkipped |  Kihagyott műveletek   | 
| Microsoft. Logic/munkafolyamatok | ActionsFailed |  Sikertelen műveletek   | 
| Microsoft. Logic/munkafolyamatok | ActionsCompleted |  Befejezett műveletek   | 
| Microsoft. Network/frontdoors | WebApplicationFirewallRequestCount |  Webalkalmazási tűzfalra vonatkozó kérelmek száma  | 
| Microsoft. Network/frontdoors | TotalLatency |  Teljes késés  | 
| Microsoft. Network/frontdoors | ResponseSize |  Válasz mérete  | 
| Microsoft. Network/frontdoors | RequestSize |  Kérelem mérete  | 
| Microsoft. Network/frontdoors | RequestCount |  Kérelmek száma  | 
| Microsoft. Network/frontdoors | BillableResponseSize |  Számlázható válasz mérete  | 
| Microsoft. Network/frontdoors | BackendRequestLatency |  Háttérbeli kérelmek késése  | 
| Microsoft. Network/frontdoors | BackendRequestCount |  Háttérbeli kérelmek száma  | 
| Microsoft. Network/frontdoors | BackendHealthPercentage |  Háttér állapotának százalékos aránya  | 
| Microsoft. Network/trafficManagerProfiles | QpsByEndpoint |  Visszaadott végponti lekérdezések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | ütemezett. függőben |  Függőben lévő ütemezett értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. frissítés |  Regisztrációs frissítési műveletek  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. Get |  Regisztrálási olvasási műveletek  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. Delete |  Regisztrációs törlési műveletek  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. Create |  Regisztráció-létrehozási műveletek  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. All |  Regisztrációs műveletek  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. wrongtoken |  WNS-hitelesítési hibák (hibás token)  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. tokenproviderunreachable |  WNS-hitelesítési hibák (nem érhető el)  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. szabályozva |  WNS-szabályozású értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. sikeres |  WNS sikeres értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. pnserror |  WNS hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidtoken |  WNS-hitelesítési hibák (érvénytelen token)  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidnotificationsize |  A WNS érvénytelen értesítési méretet észlelt  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidnotificationformat |  WNS – érvénytelen értesítési formátum  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidcredentials |  WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. expiredchannel |  WNS lejárt csatorna hibája  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. Dropped |  WNS eldobott értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. channelthrottled |  WNS csatorna szabályozása  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. channeldisconnected |  WNS csatorna leválasztva  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. badchannel |  WNS hibás csatorna hiba  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. authenticationerror |  WNS-hitelesítési hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. szabályozva |  MPNS-szabályozású értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. sikeres |  MPNS sikeres értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. pnserror |  MPNS hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. invalidnotificationformat |  MPNS – érvénytelen értesítési formátum  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. invalidcredentials |  MPNS – érvénytelen hitelesítő adatok  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. Dropped |  MPNS eldobott értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. channeldisconnected |  MPNS csatorna leválasztva  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. badchannel |  MPNS hibás csatorna hiba  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. authenticationerror |  MPNS-hitelesítési hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. wrongchannel |  GCM rossz csatorna hibája  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. szabályozva |  GCM-szabályozású értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. sikeres |  GCM sikeres értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. pnserror |  GCM hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidnotificationsize |  A GCM érvénytelen értesítési méretet észlelt  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidnotificationformat |  GCM – érvénytelen értesítési formátum  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidcredentials |  GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. expiredchannel |  GCM lejárt csatorna hibája  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. badchannel |  GCM hibás csatorna hiba  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. authenticationerror |  GCM-hitelesítési hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. sikeres |  APNS sikeres értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. pnserror |  APNS hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. invalidnotificationsize |  A APNS érvénytelen értesítési méretet észlelt  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. invalidcredentials |  APNS-hitelesítési hibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. expiredchannel |  APNS lejárt csatorna hibája  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. badchannel |  APNS hibás csatorna hiba  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. sikeres |  Sikeres értesítések  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. pnserror |  Külső értesítési rendszerhibák  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. invalidpayload |  Hasznos adatok  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. channelerror |  Csatorna hibái  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | notificationhub. leküldések |  Minden kimenő értesítés  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | telepítési. upsert |  Telepítési műveletek létrehozása vagy frissítése  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | Installation. patch |  Javítások telepítési műveletei  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | telepítés. Get |  Telepítési műveletek beolvasása  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | telepítés. Törlés |  Telepítési műveletek törlése  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | a telepítés. All |  Telepítési felügyeleti műveletek  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ütemezett. Mégse |  Ütemezett leküldéses értesítések megszakítva  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ütemezett |  Ütemezett leküldéses értesítések elküldve  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ALL. requests |  Minden bejövő kérelem  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ALL. failedrequests |  Minden bejövő sikertelen kérelem  | 
| Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő |  Bejövő üzenetek  | 
| Microsoft. OperationalInsights/munkaterületek | Szívverés |  Szívverés  | 
| Microsoft. Relay/névterek | BytesTransferred |  BytesTransferred  | 
| Microsoft. ServiceBus/névterek | OutgoingMessages |  Kimenő üzenetek  | 
| Microsoft. ServiceBus/névterek | IncomingRequests |  Bejövő kérelmek  | 
| Microsoft. ServiceBus/névterek | IncomingMessages |  Bejövő üzenetek  | 
| Microsoft. SignalRService/szignáló | UserErrors |  Felhasználói hibák  | 
| Microsoft. SignalRService/szignáló | SystemErrors |  Rendszerhibák  | 
| Microsoft. SignalRService/szignáló | OutboundTraffic |  Kimenő forgalom  | 
| Microsoft. SignalRService/szignáló | MessageCount |  Üzenetek száma  | 
| Microsoft. SignalRService/szignáló | InboundTraffic |  Bejövő forgalom  | 
| Microsoft. SignalRService/szignáló | ConnectionCount |  Kapcsolatok száma  | 
| Microsoft. SQL/managedInstances | avg_cpu_percent |  Átlagos CPU-százalék  | 
| Microsoft. SQL/kiszolgálók | dtu_used |  Használt DTU  | 
| Microsoft. SQL/kiszolgálók | dtu_consumption_percent |  DTU-kihasználtság (%)  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | xtp_storage_percent |  In-Memory OLTP-tárolási százalék  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | workers_percent |  Munkavégzők százalékos aránya  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | sessions_percent |  Munkamenetek százalékos aránya  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | physical_data_read_percent |  Adat IO kihasználtsága (%)  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | log_write_percent |  Naplózási IO-százalék  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dwu_used |  Használt DWU  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dwu_consumption_percent |  DWU százalékos aránya  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dtu_used |  Használt DTU  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dtu_consumption_percent |  DTU-kihasználtság (%)  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | holtpont |  Holtpontok  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | cpu_used |  Felhasznált CPU  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | cpu_percent |  Processzorhasználat (%)  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | connection_successful |  Sikeres kapcsolatok  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | connection_failed |  Sikertelen kapcsolatok  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | cache_hit_percent |  Gyorsítótár találati százaléka  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | blocked_by_firewall |  A tűzfal blokkolja  | 
| Microsoft. SQL/kiszolgálók/elasticPools | xtp_storage_percent |  In-Memory OLTP-tárolási százalék  | 
| Microsoft. SQL/kiszolgálók/elasticPools | workers_percent |  Munkavégzők százalékos aránya  | 
| Microsoft. SQL/kiszolgálók/elasticPools | sessions_percent |  Munkamenetek százalékos aránya  | 
| Microsoft. SQL/kiszolgálók/elasticPools | physical_data_read_percent |  Adat IO kihasználtsága (%)  | 
| Microsoft. SQL/kiszolgálók/elasticPools | log_write_percent |  Naplózási IO-százalék  | 
| Microsoft. SQL/kiszolgálók/elasticPools | eDTU_used |  használt eDTU  | 
| Microsoft. SQL/kiszolgálók/elasticPools | dtu_consumption_percent |  DTU-kihasználtság (%)  | 
| Microsoft. SQL/kiszolgálók/elasticPools | cpu_percent |  Processzorhasználat (%)  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Összes előtér  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Kisméretű App Service-csomag feldolgozói  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Kérelmek |  Kérelmek  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Memória százaléka  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Közepes App Service-csomag feldolgozói  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Nagy App Service-csomag feldolgozói  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Http-várólista hossza  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http5xx |  Http-kiszolgálói hibák  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http4xx |  Http-4xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http401 |  HTTP 401  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http3xx |  Http-3xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http2xx |  Http-2xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Lemezvezérlő-várólista hossza  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | CpuPercentage |  Processzorhasználat (%)  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | BytesSent |  Kimenő adatvesztés  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | BytesReceived |  A-ben tárolt adatértékek  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Átlagos válaszidő  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | ActiveRequests |  Aktív kérések  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersUsed |  Használt feldolgozók  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersTotal |  Alkalmazottak összesen  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersAvailable |  Rendelkezésre álló munkavégzők  | 
| Microsoft. Web/hostingEnvironments/workerPools | MemoryPercentage |  Memória százaléka  | 
| Microsoft. Web/hostingEnvironments/workerPools | CpuPercentage |  Processzorhasználat (%)  | 
| Microsoft. Web/kiszolgálófarmok | TcpTimeWait |  TCP-idő várakozása  | 
| Microsoft. Web/kiszolgálófarmok | TcpSynSent |  Eljuttatott TCP SYN  | 
| Microsoft. Web/kiszolgálófarmok | TcpSynReceived |  TCP SYN kapott  | 
| Microsoft. Web/kiszolgálófarmok | TcpLastAck |  TCP Last ACK  | 
| Microsoft. Web/kiszolgálófarmok | TcpFinWait2 |  TCP fin WAIT 2  | 
| Microsoft. Web/kiszolgálófarmok | TcpFinWait1 |  TCP fin WAIT 1  | 
| Microsoft. Web/kiszolgálófarmok | TcpEstablished |  A TCP létrejött  | 
| Microsoft. Web/kiszolgálófarmok | TcpClosing |  TCP-zárás  | 
| Microsoft. Web/kiszolgálófarmok | TcpCloseWait |  TCP-bezárási várakozás  | 
| Microsoft. Web/kiszolgálófarmok | MemoryPercentage |  Memória százaléka  | 
| Microsoft. Web/kiszolgálófarmok | HttpQueueLength |  Http-várólista hossza  | 
| Microsoft. Web/kiszolgálófarmok | DiskQueueLength |  Lemezvezérlő-várólista hossza  | 
| Microsoft. Web/kiszolgálófarmok | CpuPercentage |  Processzorhasználat (%)  | 
| Microsoft. Web/kiszolgálófarmok | BytesSent |  Kimenő adatvesztés  | 
| Microsoft. Web/kiszolgálófarmok | BytesReceived |  A-ben tárolt adatértékek  | 
| Microsoft. Web/Sites | TotalAppDomainsUnloaded |  Összes kitöltött alkalmazás-tartomány  | 
| Microsoft. Web/Sites | TotalAppDomains |  Alkalmazás összes tartománya  | 
| Microsoft. Web/Sites | Szálak |  Szálak száma  | 
| Microsoft. Web/Sites | RequestsInApplicationQueue |  Kérelmek az alkalmazás-várólistán  | 
| Microsoft. Web/Sites | Kérelmek |  Kérelmek  | 
| Microsoft. Web/Sites | PrivateBytes |  Saját bájtok  | 
| Microsoft. Web/Sites | MemoryWorkingSet |  Memória munkakészlete  | 
| Microsoft. Web/Sites | IoWriteOperationsPerSecond |  IO írási műveletek másodpercenként  | 
| Microsoft. Web/Sites | IoWriteBytesPerSecond |  IO írási bájtok másodpercenként  | 
| Microsoft. Web/Sites | IoReadOperationsPerSecond |  I/o-olvasási műveletek másodpercenként  | 
| Microsoft. Web/Sites | IoReadBytesPerSecond |  IO olvasási bájtok másodpercenként  | 
| Microsoft. Web/Sites | IoOtherOperationsPerSecond |  IO egyéb műveletek másodpercenként  | 
| Microsoft. Web/Sites | IoOtherBytesPerSecond |  IO – egyéb bájtok másodpercenként  | 
| Microsoft. Web/Sites | HttpResponseTime |  Válaszidő  | 
| Microsoft. Web/Sites | Http5xx |  Http-kiszolgálói hibák  | 
| Microsoft. Web/Sites | Http4xx |  Http-4xx  | 
| Microsoft. Web/Sites | Http406 |  Http 406  | 
| Microsoft. Web/Sites | Http404 |  Http 404  | 
| Microsoft. Web/Sites | Http403 |  Http 403  | 
| Microsoft. Web/Sites | Http401 |  HTTP 401  | 
| Microsoft. Web/Sites | Http3xx |  Http-3xx  | 
| Microsoft. Web/Sites | Http2xx |  Http-2xx  | 
| Microsoft. Web/Sites | Http101 |  Http 101  | 
| Microsoft. Web/Sites | HealthCheckStatus |  Állapot-ellenőrzési állapot  | 
| Microsoft. Web/Sites | Kezeli |  Leírók száma  | 
| Microsoft. Web/Sites | Gen2Collections |  2. generációs Garbage gyűjtemények  | 
| Microsoft. Web/Sites | Gen1Collections |  1. generációs Garbage gyűjtemények  | 
| Microsoft. Web/Sites | Gen0Collections |  0. generációs Garbage-gyűjtemények  | 
| Microsoft. Web/Sites | FunctionExecutionUnits |  Függvények végrehajtási egységei  | 
| Microsoft. Web/Sites | FunctionExecutionCount |  Függvény végrehajtásának száma  | 
| Microsoft. Web/Sites | CurrentAssemblies |  Aktuális szerelvények  | 
| Microsoft. Web/Sites | CpuTime |  CPU-idő  | 
| Microsoft. Web/Sites | BytesSent |  Kimenő adatvesztés  | 
| Microsoft. Web/Sites | BytesReceived |  A-ben tárolt adatértékek  | 
| Microsoft. Web/Sites | AverageResponseTime |  Átlagos válaszidő  | 
| Microsoft. Web/Sites | AverageMemoryWorkingSet |  Memória átlagos munkakészlete  | 
| Microsoft. Web/Sites | AppConnections |  Kapcsolatok  | 
| Microsoft. Web/Sites/Slots | TotalAppDomainsUnloaded |  Összes kitöltött alkalmazás-tartomány  | 
| Microsoft. Web/Sites/Slots | TotalAppDomains |  Alkalmazás összes tartománya  | 
| Microsoft. Web/Sites/Slots | Szálak |  Szálak száma  | 
| Microsoft. Web/Sites/Slots | RequestsInApplicationQueue |  Kérelmek az alkalmazás-várólistán  | 
| Microsoft. Web/Sites/Slots | Kérelmek |  Kérelmek  | 
| Microsoft. Web/Sites/Slots | PrivateBytes |  Saját bájtok  | 
| Microsoft. Web/Sites/Slots | MemoryWorkingSet |  Memória munkakészlete  | 
| Microsoft. Web/Sites/Slots | IoWriteOperationsPerSecond |  IO írási műveletek másodpercenként  | 
| Microsoft. Web/Sites/Slots | IoWriteBytesPerSecond |  IO írási bájtok másodpercenként  | 
| Microsoft. Web/Sites/Slots | IoReadOperationsPerSecond |  I/o-olvasási műveletek másodpercenként  | 
| Microsoft. Web/Sites/Slots | IoReadBytesPerSecond |  IO olvasási bájtok másodpercenként  | 
| Microsoft. Web/Sites/Slots | IoOtherOperationsPerSecond |  IO egyéb műveletek másodpercenként  | 
| Microsoft. Web/Sites/Slots | IoOtherBytesPerSecond |  IO – egyéb bájtok másodpercenként  | 
| Microsoft. Web/Sites/Slots | HttpResponseTime |  Válaszidő  | 
| Microsoft. Web/Sites/Slots | Http5xx |  Http-kiszolgálói hibák  | 
| Microsoft. Web/Sites/Slots | Http4xx |  Http-4xx  | 
| Microsoft. Web/Sites/Slots | Http406 |  Http 406  | 
| Microsoft. Web/Sites/Slots | Http404 |  Http 404  | 
| Microsoft. Web/Sites/Slots | Http403 |  Http 403  | 
| Microsoft. Web/Sites/Slots | Http401 |  HTTP 401  | 
| Microsoft. Web/Sites/Slots | Http3xx |  Http-3xx  | 
| Microsoft. Web/Sites/Slots | Http2xx |  Http-2xx  | 
| Microsoft. Web/Sites/Slots | Http101 |  Http 101  | 
| Microsoft. Web/Sites/Slots | HealthCheckStatus |  Állapot-ellenőrzési állapot  | 
| Microsoft. Web/Sites/Slots | Kezeli |  Leírók száma  | 
| Microsoft. Web/Sites/Slots | Gen2Collections |  2. generációs Garbage gyűjtemények  | 
| Microsoft. Web/Sites/Slots | Gen1Collections |  1. generációs Garbage gyűjtemények  | 
| Microsoft. Web/Sites/Slots | Gen0Collections |  0. generációs Garbage-gyűjtemények  | 
| Microsoft. Web/Sites/Slots | FunctionExecutionUnits |  Függvények végrehajtási egységei  | 
| Microsoft. Web/Sites/Slots | FunctionExecutionCount |  Függvény végrehajtásának száma  | 
| Microsoft. Web/Sites/Slots | CurrentAssemblies |  Aktuális szerelvények  | 
| Microsoft. Web/Sites/Slots | CpuTime |  CPU-idő  | 
| Microsoft. Web/Sites/Slots | BytesSent |  Kimenő adatvesztés  | 
| Microsoft. Web/Sites/Slots | BytesReceived |  A-ben tárolt adatértékek  | 
| Microsoft. Web/Sites/Slots | AverageResponseTime |  Átlagos válaszidő  | 
| Microsoft. Web/Sites/Slots | AverageMemoryWorkingSet |  Memória átlagos munkakészlete  | 
| Microsoft. Web/Sites/Slots | AppConnections |  Kapcsolatok  | 
| Microsoft. SQL/kiszolgálók/adatbázisok | cpu_percent | Processzorhasználat (%) | 
| Microsoft. SQL/kiszolgálók/adatbázisok | physical_data_read_percent | Adat IO kihasználtsága (%) | 
| Microsoft. SQL/kiszolgálók/adatbázisok | log_write_percent | Naplózási IO-százalék | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dtu_consumption_percent | DTU-kihasználtság (%) | 
| Microsoft. SQL/kiszolgálók/adatbázisok | connection_successful | Sikeres kapcsolatok | 
| Microsoft. SQL/kiszolgálók/adatbázisok | connection_failed | Sikertelen kapcsolatok | 
| Microsoft. SQL/kiszolgálók/adatbázisok | blocked_by_firewall | A tűzfal blokkolja | 
| Microsoft. SQL/kiszolgálók/adatbázisok | holtpont | Holtpontok | 
| Microsoft. SQL/kiszolgálók/adatbázisok | xtp_storage_percent | In-Memory OLTP-tárolási százalék | 
| Microsoft. SQL/kiszolgálók/adatbázisok | workers_percent | Munkavégzők százalékos aránya | 
| Microsoft. SQL/kiszolgálók/adatbázisok | sessions_percent | Munkamenetek százalékos aránya | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dtu_used | Használt DTU | 
| Microsoft. SQL/kiszolgálók/adatbázisok | cpu_used | Felhasznált CPU | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dwu_consumption_percent | DWU százalékos aránya | 
| Microsoft. SQL/kiszolgálók/adatbázisok | dwu_used | Használt DWU | 
| Microsoft. SQL/kiszolgálók/adatbázisok | cache_hit_percent | Gyorsítótár találati százaléka | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_allocation_relative_to_system_percent | Munkaterhelés-csoport kiosztása rendszerszázalékkal | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_allocation_relative_to_wlg_effective_cap_percent | Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_active_queries | Munkaterhelési csoport aktív lekérdezései | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_queued_queries | Munkaterhelési csoport várólistán lévő lekérdezések | 
| Microsoft. SQL/kiszolgálók/adatbázisok | active_queries | Aktív lekérdezések | 
| Microsoft. SQL/kiszolgálók/adatbázisok | queued_queries | Várólistán lévő lekérdezések | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_active_queries_timeouts | Munkaterhelés-csoport lekérdezési időtúllépései | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_queued_queries_timeouts | Munkaterhelési csoport várólistán lévő lekérdezési időtúllépései | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_effective_min_resource_percent | Effektív minimális erőforrás százaléka | 
| Microsoft. SQL/kiszolgálók/adatbázisok | wlg_effective_cap_resource_percent | Érvényes Cap-erőforrás százaléka | 
| Microsoft. SQL/kiszolgálók/elasticPools | cpu_percent | Processzorhasználat (%) | 
| Microsoft. SQL/kiszolgálók/elasticPools | physical_data_read_percent | Adat IO kihasználtsága (%) | 
| Microsoft. SQL/kiszolgálók/elasticPools | log_write_percent | Naplózási IO-százalék | 
| Microsoft. SQL/kiszolgálók/elasticPools | dtu_consumption_percent | DTU-kihasználtság (%) | 
| Microsoft. SQL/kiszolgálók/elasticPools | workers_percent | Munkavégzők százalékos aránya | 
| Microsoft. SQL/kiszolgálók/elasticPools | sessions_percent | Munkamenetek százalékos aránya | 
| Microsoft. SQL/kiszolgálók/elasticPools | eDTU_used | használt eDTU | 
| Microsoft. SQL/kiszolgálók/elasticPools | xtp_storage_percent | In-Memory OLTP-tárolási százalék | 
| Microsoft. SQL/kiszolgálók | dtu_consumption_percent | DTU-kihasználtság (%) | 
| Microsoft. SQL/kiszolgálók | dtu_used | Használt DTU | 
| Microsoft. SQL/managedInstances | avg_cpu_percent | Átlagos CPU-százalék | 

