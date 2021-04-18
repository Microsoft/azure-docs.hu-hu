---
title: Azure Monitor metrikák erőforrástípus szerint
description: Az egyes erőforrástípushoz elérhető metrikák listája Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/15/2021
ms.author: robb
ms.openlocfilehash: 1091d103428315a065dd1ff9800ce2ad16632df0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600012"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

> [!NOTE]
> Ez a lista nagyrészt automatikusan jön létre. A listában a GitHubon keresztül végzett módosítások figyelmeztetés nélkül írhatóak át. A végleges frissítésekkel kapcsolatos részletekért forduljon a cikk szerzőjéhez.

Azure Monitor a metrikák többféleképpen is használhatók, például a portálon való diagramolást, a REST API-kapcsolaton keresztüli hozzáféréseket, illetve a PowerShell vagy a parancssori felület használatával való lekérdezést. 

Ez a cikk az összes olyan platformmetrika teljes listáját tartalmazza, amely jelenleg elérhető Azure Monitor összesített metrika-folyamatával. Előfordulhat, hogy a cikk tetején található dátum után módosított vagy hozzáadott metrikák még nem jelennek meg alább. A metrikák listájának programozott lekérdezéséhez és eléréséhez használja a [2018-01-01 API-verziót.](/rest/api/monitor/metricdefinitions) A listán nem található egyéb metrikák elérhetők lehetnek a portálon vagy örökölt API-k használatával.

A metrikák erőforrás-szolgáltatók és erőforrástípus szerint vannak rendezve. A szolgáltatások, az erőforrás-szolgáltatók és a hozzájuk tartozó típusok listáját lásd: [Erőforrás-szolgáltatók Azure-szolgáltatásokhoz.](../../azure-resource-manager/management/azure-services-resource-providers.md)  

## <a name="exporting-platform-metrics-to-other-locations"></a>Platformmetrikák exportálása más helyekre

A platformmetrikákat kétféleképpen exportálhatja az Azure Monitor-folyamatból más helyekre.
1. A [metrikák REST API](/rest/api/monitor/metrics/list)
2. A [diagnosztikai beállítások használatával](../essentials/diagnostic-settings.md) irányíthatja a platformmetrikákat a 
    - Azure Storage
    - Azure Monitor naplók (és így a Log Analytics)
    - Event Hubs– így jutnak el a nem a Microsofttól különböző rendszerekre 

A metrikák útválasztásának legegyszerűbb módja a diagnosztikai beállítások használata, de van néhány korlátozás: 

- **Néhány nem** exportálható – Minden metrika exportálható a REST API használatával, de némelyik nem exportálható a diagnosztikai beállítások használatával a Azure Monitor miatt. Az alábbi *táblázatokban az Exportable via Diagnostic Settings* (Exportálható diagnosztikai beállításokon keresztül) oszlopban látható, hogy mely metrikák exportálhatók így.  

- **Többdimenziós metrikák** – A többdimenziós metrikák küldése más helyekre diagnosztikai beállításokon keresztül jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel. *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.

## <a name="guest-os-and-host-os-metrics"></a>Vendég operációs rendszer és gazdagép operációs rendszerének metrikai

> [!WARNING]
> Az Azure Virtual Machines, Service Fabric és Cloud Services operációs rendszerben futó vendég operációs rendszer (vendég operációs rendszer) metrikai **NEM listában** szerepelnek. A vendég operációs rendszer metrikákat a vendég operációs rendszeren vagy annak részeként futó egy vagy több ügynökön keresztül kell összegyűjteni.  A vendég operációs rendszer metrika olyan teljesítményszámlálókat tartalmaz, amelyek nyomon követik a vendég processzorhasználatát vagy a memóriahasználatot, és mindkettőt gyakran használják automatikus skálázáshoz vagy riasztáshoz. 
>
> **A gazdagép operációs rendszerének metrikai elérhetők és alább vannak felsorolva.** Nem azonosak. A gazda operációs rendszer metrikák a vendég operációs rendszer munkamenetét üzemeltető Hyper-V-munkamenethez kapcsolódnak. 

> [!TIP]
> Az ajánlott eljárás a Azure Diagnostics-bővítmény használata és konfigurálása [arra,](../agents/diagnostics-extension-overview.md) hogy a vendég operációs rendszer teljesítménymetrikákat küldjön ugyanabba Azure Monitor metrikaadatbázisba, ahol a platformmetrikák tárolva vannak. A bővítmény a vendég operációs rendszer metrikákat az egyéni [metrics API-n keresztüli útvonalakon keresztül használja.](../essentials/metrics-custom-overview.md) Ezután diagramokat, riasztásokat és egyéb módon felhasználhatja a vendég operációs rendszer metrikákat, például a platformmetrikákat. Másik lehetőségként a Log Analytics-ügynökkel küldhet vendég operációsrendszer-metrikákat a Azure Monitor Log Analyticsbe. Itt a metrikák és a nem metrikaadatok együttesen is lekérdezhetőek. 

További fontos információkért lásd: [Monitoring Agents Overview (A figyelési ügynökök áttekintése).](../agents/agents-overview.md)

## <a name="table-formatting"></a>Táblaformázás

> [!IMPORTANT] 
> Ez a legújabb frissítés egy új oszlopot ad hozzá, és ábécésorrendbe rendezi a metrikákat. Az összeadási információk azt jelentik, hogy az alábbi táblázatok alján a böngészőablak szélességétől függően vízszintes görgetősáv is lehet. Ha úgy véli, hogy hiányoznak az információk, a görgetősáv használatával láthatja a táblázat teljes egészét.
## <a name="microsoftaadiamazureadmetrics"></a>microsoft.ametricam/azureADMetrics

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|Darabszám|Átlag|azureADMetrics típusú metrika|Nincsenek dimenziók|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice (AcurrentPrice tisztítása)|Yes|Memória: Aktuális ár tisztítása|Darabszám|Átlag|A memória jelenlegi ára ($/bájt/idő) 1000-re normalizálva.|ServerResourceType (Kiszolgálóforrástípus)|
|CleanerMemoryNonshrinkable|Yes|Memória: A tisztítási memória nem kezelhető|Bájt|Átlag|A memória mennyisége bájtban, a háttértisztítás által nem véglegesen kiürítve.|ServerResourceType (Kiszolgálóforrástípus)|
|CleanerMemoryShrinkable|Yes|Memória: A tisztítási memória zsugorodhat|Bájt|Átlag|A memória mennyisége bájtban, a háttértisztítás által kiürítve.|ServerResourceType (Kiszolgálóforrástípus)|
|CommandPoolBusyThreads|Yes|Szálak: A parancskészlet foglalt szálak|Darabszám|Átlag|A parancsszálkészletben foglalt szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|CommandPoolIdleThreads|Yes|Szálak: Parancskészlet üresjárati szálai|Darabszám|Átlag|A parancsszálkészletben található tétlen szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|CommandPoolJobQueueLength|Yes|Parancskészlet-feladat várólistájának hossza|Darabszám|Átlag|A parancsszálkészlet várólistán lévő feladatok száma.|ServerResourceType (Kiszolgálóforrástípus)|
|CurrentConnections (Aktuális kapcsolat)|Yes|Kapcsolat: Aktuális kapcsolatok|Darabszám|Átlag|A létrehozott ügyfélkapcsolatok aktuális száma.|ServerResourceType (Kiszolgálóforrástípus)|
|CurrentUserSessions (AktuálisuserSessions)|Yes|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrehozott felhasználói munkamenetek aktuális száma.|ServerResourceType (Kiszolgálóforrástípus)|
|LongParsingBusyThreads|Yes|Szálak: Foglalt szálak hosszú elemzési ideje|Darabszám|Átlag|A hosszú elemzőszálkészletben foglalt szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|LongParsingIdleThreads (LongParsingIdleThreads)|Yes|Szálak: Tétlen szálak hosszú elemzési ideje|Darabszám|Átlag|A hosszú elemzőszálkészletben található tétlen szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|LongParsingJobQueueLength|Yes|Szálak: A feladat várakozási sorának hossza hosszú|Darabszám|Átlag|A hosszú elemzési szálkészlet várólistán lévő feladatok száma.|ServerResourceType (Kiszolgálóforrástípus)|
|mashup_engine_memory_metric|Yes|M-motor memóriája|Bájt|Átlag|Memóriahasználat adategyesítési motorfolyamatok szerint|ServerResourceType (Kiszolgálóforrástípus)|
|mashup_engine_private_bytes_metric|Yes|M Engine Private Bytes|Bájt|Átlag|Privát bájtok használata adategyesítési motorfolyamatok szerint.|ServerResourceType (Kiszolgálóforrástípus)|
|mashup_engine_qpu_metric|Yes|M Engine QPU|Darabszám|Átlag|QPU-használat adategyesítési motorfolyamatok szerint|ServerResourceType (Kiszolgálóforrástípus)|
|mashup_engine_virtual_bytes_metric|Yes|M Engine virtuális bájtok|Bájt|Átlag|Virtuális bájtok használata adategyesítési motorfolyamatok szerint.|ServerResourceType (Kiszolgálóforrástípus)|
|memory_metric|Yes|Memória|Bájt|Átlag|Memory. 0–25 GB tartomány S1-hez, 0–50 GB S2 és 0–100 GB S4-hez|ServerResourceType (Kiszolgálóforrástípus)|
|memory_thrashing_metric|Yes|Memóriaakadozás|Százalék|Átlag|Átlagos memóriaakozás.|ServerResourceType (Kiszolgálóforrástípus)|
|MemoryLimitHard (Memórialimithard)|Yes|Memória: A memóriakorlát nem|Bájt|Átlag|A konfigurációs fájlból származó merevlemez-memóriakorlát.|ServerResourceType (Kiszolgálóforrástípus)|
|MemoryLimitHigh (Memórialimit–magas)|Yes|Memória: Magas memóriakorlát|Bájt|Átlag|Magas memóriakorlát a konfigurációs fájlból.|ServerResourceType (Kiszolgálóforrástípus)|
|MemoryLimitLow (Memórialimitlow)|Yes|Memória: Memóriakorlát – alacsony|Bájt|Átlag|Alacsony memóriakorlát a konfigurációs fájlból.|ServerResourceType (Kiszolgálóforrástípus)|
|MemoryLimitVertiPaq|Yes|Memória: VertiPaq memóriakorlát|Bájt|Átlag|Memóriakorlát a konfigurációs fájlból.|ServerResourceType (Kiszolgálóforrástípus)|
|MemoryUsage (Memória)Usage (Memória)|Yes|Memória: Memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználata a tisztítási memória árának kiszámításához. Egyenlő a Process\PrivateBytes értékekkel és a memória által leképezett adatok méretével, figyelmen kívül hagyva az xVelocity memóriaelemzési motor (VertiPaq) által az xVelocity motor memóriakorlátján túl leképezett vagy lefoglalt memóriát.|ServerResourceType (Kiszolgálóforrástípus)|
|private_bytes_metric|Yes|Saját bájtok|Bájt|Átlag|Privát bájtok.|ServerResourceType (Kiszolgálóforrástípus)|
|ProcessingPoolBusyIOJobThreads|Yes|Szálak: Foglalt I/O-feladatszálak feldolgozása a készletben|Darabszám|Átlag|A feldolgozásiszál-készletben I/O-feladatokat futtató szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|ProcessingPoolBusyNonIOThreads|Yes|Szálak: A feldolgozókészlet foglalt, nem I/O-szálak|Darabszám|Átlag|A feldolgozószálkészletben nem I/O-feladatokat futtató szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|ProcessingPoolIdleIOJobThreads|Yes|Szálak: Készlet tétlen I/O-feladatszálának feldolgozása|Darabszám|Átlag|A feldolgozószálkészletben található I/O-feladatokhoz szükséges tétlen szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|ProcessingPoolIdleNonIOThreads|Yes|Szálak: Készlet üresjárati nem I/O-szálának feldolgozása|Darabszám|Átlag|A feldolgozószál-készletben a nem I/O-feladatokhoz dedikált tétlen szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|ProcessingPoolIOJobQueueLength|Yes|Szálak: A feldolgozási készlet I/O-feladat várólistájának hossza|Darabszám|Átlag|A feldolgozási szálkészlet várólistájának I/O-feladatok száma.|ServerResourceType (Kiszolgálóforrástípus)|
|ProcessingPoolJobQueueLength|Yes|Készlet feladat-várólistahosszának feldolgozása|Darabszám|Átlag|Nem I/O-feladatok száma a feldolgozási szálkészlet várólistán.|ServerResourceType (Kiszolgálóforrástípus)|
|qpu_metric|Yes|QPU|Darabszám|Átlag|QPU. 0–100 tartomány S1,0–200 S2 és 0–400 S4 értékhez|ServerResourceType (Kiszolgálóforrástípus)|
|QueryPoolBusyThreads|Yes|A lekérdezéskészlet foglalt szálai|Darabszám|Átlag|A lekérdezési szálkészletben foglalt szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|QueryPoolIdleThreads|Yes|Szálak: A lekérdezéskészlet tétlen szálai|Darabszám|Átlag|A feldolgozásiszál-készletben található I/O-feladatok tétlen szálai száma.|ServerResourceType (Kiszolgálóforrástípus)|
|QueryPoolJobQueueLength|Yes|Szálak: Lekérdezéskészlet-feladat várólistájának hossza|Darabszám|Átlag|A lekérdezési szálkészlet üzenetsorában lévő feladatok száma.|ServerResourceType (Kiszolgálóforrástípus)|
|Kvóta|Yes|Memória: Kvóta|Bájt|Átlag|Aktuális memóriakvóta, bájtban. A memóriakvóta más néven memória-engedély vagy memóriafoglalás.|ServerResourceType (Kiszolgálóforrástípus)|
|QuotaBlocked (Kvóta blokkolva)|Yes|Memória: Kvóta blokkolva|Darabszám|Átlag|Az egyéb memóriakvóták felszabadításáig blokkolt kvótakérések aktuális száma.|ServerResourceType (Kiszolgálóforrástípus)|
|RowsConvertedPerSec|Yes|Feldolgozás: Másodpercenként konvertált sorok|CountPerSecond (Másodpercek száma)|Átlag|A feldolgozás során konvertált sorok száma.|ServerResourceType (Kiszolgálóforrástípus)|
|RowsReadPerSec|Yes|Feldolgozás: Másodpercenként beolvasott sorok|CountPerSecond (Másodpercek száma)|Átlag|Az összes relációs adatbázisból beolvasott sorok száma.|ServerResourceType (Kiszolgálóforrástípus)|
|RowsWrittenPerSec|Yes|Feldolgozás: Másodpercenként írt sorok|CountPerSecond (Másodpercek száma)|Átlag|A feldolgozás során írt sorok sebessége.|ServerResourceType (Kiszolgálóforrástípus)|
|ShortParsingBusyThreads|Yes|Szálak: Foglalt szálak röviden elemezve|Darabszám|Átlag|A rövid elemzési szálkészletben foglalt szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|ShortParsingIdleThreads|Yes|Szálak: Tétlen szálak röviden elemezve|Darabszám|Átlag|A rövid elemzési szálkészletben található tétlen szálak száma.|ServerResourceType (Kiszolgálóforrástípus)|
|ShortParsingJobQueueLength (ShortParsingJobQueueLength)|Yes|Szálak: Az elemzési feladat várólistájának rövid hossza|Darabszám|Átlag|A rövid elemzési szálkészlet várólistán lévő feladatok száma.|ServerResourceType (Kiszolgálóforrástípus)|
|SuccessfullConnectionsPerSec|Yes|Sikeres kapcsolatok másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|A sikeres kapcsolat-befejezések sebessége.|ServerResourceType (Kiszolgálóforrástípus)|
|TotalConnectionFailures|Yes|Csatlakozási hibák összesen|Darabszám|Átlag|Sikertelen csatlakozási kísérletek összesen.|ServerResourceType (Kiszolgálóforrástípus)|
|TotalConnectionRequests|Yes|Csatlakozási kérelmek összesen|Darabszám|Átlag|Összes csatlakozási kérelem. Ezek érkezések.|ServerResourceType (Kiszolgálóforrástípus)|
|VertiPaqNonpaged|Yes|Memória: VertiPaq Nonpaged|Bájt|Átlag|A munkakészletben zárolt memória bájtja a memóriamotor számára.|ServerResourceType (Kiszolgálóforrástípus)|
|VertiPaqPaged|Yes|Memória: VertiPaq Paged|Bájt|Átlag|Memóriaadatokhoz használt lapszámba bájtok memóriahasználata.|ServerResourceType (Kiszolgálóforrástípus)|
|virtual_bytes_metric|Yes|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType (Kiszolgálóforrástípus)|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BackendDuration (Háttér-Duráció)|Yes|Háttérkérések időtartama|Ezredmásodperc|Átlag|A háttérkérések időtartama ezredmásodpercben|Hely, Állomásnév|
|Kapacitás|Yes|Kapacitás|Százalék|Átlag|Az ApiManagement szolgáltatás kihasználtsági metrika|Hely|
|Időtartam|Yes|Átjárókérések teljes időtartama|Ezredmásodperc|Átlag|Az átjárókérések teljes időtartama ezredmásodpercben|Hely, Állomásnév|
|EventHubDroppedEvents|Yes|Eldobott EventHub-események|Darabszám|Összesen|Az üzenetsor méretkorlátja miatt kihagyott események száma|Hely|
|EventHubRejectedEvents|Yes|Elutasított EventHub-események|Darabszám|Összesen|Elutasított EventHub-események száma (helytelen konfiguráció vagy jogosulatlan)|Hely|
|EventHubSuccessfulEvents|Yes|Sikeres EventHub-események|Darabszám|Összesen|Sikeres EventHub-események száma|Hely|
|EventHubThrottledEvents|Yes|Szabályozásos EventHub-események|Darabszám|Összesen|A szabályozásos EventHub-események száma|Hely|
|EventHubTimedoutEvents|Yes|Időkorreklált EventHub-események|Darabszám|Összesen|Időkorreklált EventHub-események száma|Hely|
|EventHubTotalBytesSent|Yes|EventHub-események mérete|Bájt|Összesen|EventHub-események teljes mérete bájtban|Hely|
|EventHubTotalEvents|Yes|EventHub-események összesen|Darabszám|Összesen|Az EventHubra küldött események száma|Hely|
|EventHubTotalFailedEvents|Yes|Sikertelen EventHub-események|Darabszám|Összesen|Sikertelen EventHub-események száma|Hely|
|FailedRequests|Yes|Sikertelen átjárókérések (elavult)|Darabszám|Összesen|Hibák száma az átjárókérések között – Többdimenziós kérelemmetrikák használata a GatewayResponseCodeCategory dimenzióval|Hely, Állomásnév|
|NetworkConnectivity|Yes|Az erőforrások hálózati kapcsolati állapota (előzetes verzió)|Darabszám|Átlag|A függő erőforrástípusok hálózati kapcsolati állapota a API Management szolgáltatásból|Hely, Erőforrástípus|
|OtherRequests (Egyébrequests)|Yes|Egyéb átjárókérések (elavult)|Darabszám|Összesen|Egyéb átjárókérések száma – Használjon inkább többdimenziós kérelemmetrikát a GatewayResponseCodeCategory dimenzióval|Hely, Állomásnév|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Átjárókérési metrikák több dimenzióval|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests (Sikeresrequests)|Yes|Sikeres átjárókérések (elavult)|Darabszám|Összesen|Sikeres átjárókérések száma – Használjon inkább többdimenziós kérelemmetrikát a GatewayResponseCodeCategory dimenzióval|Hely, Állomásnév|
|TotalRequests|Yes|Összes átjárókérés (elavult)|Darabszám|Összesen|Átjárókérések száma – Használjon inkább többdimenziós kérelemmetrikát a GatewayResponseCodeCategory dimenzióval|Hely, Állomásnév|
|UnauthorizedRequests|Yes|Jogosulatlan átjárókérések (elavult)|Darabszám|Összesen|Nem engedélyezett átjárókérések száma – Használjon inkább többdimenziós kérelemmetrikát a GatewayResponseCodeCategory dimenzióval|Hely, Állomásnév|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Darabszám|Darabszám|A bejövő HTTP-kérések teljes száma.|StatusCode, Hitelesítés|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Darabszám|Átlag|Http-kérés késése.|StatusCode, Hitelesítés|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Darabszám|Darabszám|A http-kérések szabályozása.|Nincsenek dimenziók|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|aktív-időzítő-szám|Yes|aktív-időzítő-szám|Darabszám|Átlag|A jelenleg aktív időzítők száma|Üzembe helyezés, AppName, Pod|
|alloc-rate|Yes|alloc-rate|Bájt|Átlag|A felügyelt halomban lefoglalt bájtok száma|Üzembe helyezés, AppName, Pod|
|AppCpuUsage (AppCpuUsage)|Yes|Alkalmazás CPU-használata |Százalék|Átlag|Az alkalmazás legutóbbi CPU-használata|Üzembe helyezés, AppName, Pod|
|szerelvények száma|Yes|szerelvények száma|Darabszám|Átlag|Betöltött szerelvények száma|Üzembe helyezés, AppName, Pod|
|cpu-használat|Yes|cpu-használat|Százalék|Átlag|% az idő, amikor a folyamat felhasználta a CPU-t|Üzembe helyezés, AppName, Pod|
|aktuális kérések|Yes|aktuális kérések|Darabszám|Átlag|A feldolgozás alatt áll kérelmek teljes száma a folyamat élettartama során|Üzembe helyezés, AppName, Pod|
|kivételszám|Yes|kivételszám|Darabszám|Összesen|Kivételek száma|Üzembe helyezés, AppName, Pod|
|sikertelen kérelmek|Yes|sikertelen kérelmek|Darabszám|Átlag|A sikertelen kérelmek teljes száma a folyamat élettartama során|Üzembe helyezés, AppName, Pod|
|gc-heap-size|Yes|gc-heap-size|Darabszám|Átlag|A GC által jelentett halommemóta teljes mérete (MB)|Üzembe helyezés, AppName, Pod|
|gen-0-gc-count|Yes|gen-0-gc-count|Darabszám|Átlag|0. generációs GCS-k száma|Üzembe helyezés, AppName, Pod|
|gen-0-size|Yes|gen-0-size|Bájt|Átlag|Gen 0 halommemóta mérete|Üzembe helyezés, AppName, Pod|
|gen-1-gc-count|Yes|gen-1-gc-count|Darabszám|Átlag|1. generációs GCS-k száma|Üzembe helyezés, AppName, Pod|
|gen-1-size|Yes|gen-1-size|Bájt|Átlag|Gen 1 halommemóta mérete|Üzembe helyezés, AppName, Pod|
|gen-2-gc-count|Yes|gen-2-gc-count|Darabszám|Átlag|2. generációs GCS-k száma|Üzembe helyezés, AppName, Pod|
|gen-2-size|Yes|gen-2-size|Bájt|Átlag|Gen 2 halommemóta mérete|Üzembe helyezés, AppName, Pod|
|jvm.gc.live.data.size|Yes|jvm.gc.live.data.size|Bájt|Átlag|A régi generációs memóriakészlet mérete a teljes GC után|Üzembe helyezés, AppName, Pod|
|jvm.gc.max.data.size|Yes|jvm.gc.max.data.size|Bájt|Átlag|A régi generációs memóriakészlet maximális mérete|Üzembe helyezés, AppName, Pod|
|jvm.gc.memory.allocated|Yes|jvm.gc.memory.allocated|Bájt|Maximum|Növekményes növekmény a fiatal generációs memóriakészlet méretének egyik GC-ről a következő előttire való növeléséhez|Üzembe helyezés, AppName, Pod|
|jvm.gc.memory.promoted|Yes|jvm.gc.memory.promoted|Bájt|Maximum|A régi generációs memóriakészlet méretének pozitív növekedése a GC előtt és a GC után|Üzembe helyezés, AppName, Pod|
|jvm.gc.pause.total.count|Yes|jvm.gc.pause.total.count|Darabszám|Összesen|GC szüneteltetési száma|Üzembe helyezés, AppName, Pod|
|jvm.gc.pause.total.time|Yes|jvm.gc.pause.total.time|Ezredmásodperc|Összesen|GC Szüneteltetés teljes ideje|Üzembe helyezés, AppName, Pod|
|jvm.memory.committed|Yes|jvm.memory.committed|Bájt|Átlag|A JVM-hez rendelt memória bájtban|Üzembe helyezés, AppName, Pod|
|jvm.memory.max|Yes|jvm.memory.max|Bájt|Maximum|A memóriakezeléshez használható maximális memóriamennyiség bájtban|Üzembe helyezés, AppName, Pod|
|jvm.memory.used|Yes|jvm.memory.used|Bájt|Átlag|Bájtban használt alkalmazásmemória|Üzembe helyezés, AppName, Pod|
|loh-size|Yes|loh-size|Bájt|Átlag|LOH-halommemóta mérete|Üzembe helyezés, AppName, Pod|
|monitor-lock-contention-count|Yes|monitor-lock-contention-count|Darabszám|Átlag|A figyelő zárolásának megkétszeresedése|Üzembe helyezés, AppName, Pod|
|process.cpu.usage|Yes|process.cpu.usage|Százalék|Átlag|A JVM-folyamat legutóbbi CPU-használata|Üzembe helyezés, AppName, Pod|
|kérések másodpercenként|Yes|kérések sebessége|Darabszám|Átlag|Kérelmek sebessége|Üzembe helyezés, AppName, Pod|
|system.cpu.usage|Yes|system.cpu.usage|Százalék|Átlag|A legutóbbi CPU-használat a teljes rendszerre|Üzembe helyezés, AppName, Pod|
|threadpool-completed-items-count|Yes|threadpool-completed-items-count|Darabszám|Átlag|ThreadPool befejezve munkaelemek száma|Üzembe helyezés, AppName, Pod|
|threadpool-queue-length|Yes|threadpool-queue-length|Darabszám|Átlag|ThreadPool munkaelemek várakozási sorának hossza|Üzembe helyezés, AppName, Pod|
|threadpool-thread-count|Yes|threadpool-thread-count|Darabszám|Átlag|Szálkészletszálak száma|Üzembe helyezés, AppName, Pod|
|time-in-gc|Yes|time-in-gc|Százalék|Átlag|Százalékos idő a GC-ban az utolsó GC óta|Üzembe helyezés, AppName, Pod|
|tomcat.global.error|Yes|tomcat.global.error|Darabszám|Összesen|Tomcat globális hiba|Üzembe helyezés, AppName, Pod|
|tomcat.global.received|Yes|tomcat.global.received|Bájt|Összesen|Tomcat összes fogadott bájtja|Üzembe helyezés, AppName, Pod|
|tomcat.global.request.avg.time|Yes|tomcat.global.request.avg.time|Ezredmásodperc|Átlag|Tomcat-kérelmek átlagos ideje|Üzembe helyezés, AppName, Pod|
|tomcat.global.request.max|Yes|tomcat.global.request.max|Ezredmásodperc|Maximum|Tomcat-kérelem maximális ideje|Üzembe helyezés, AppName, Pod|
|tomcat.global.request.total.count|Yes|tomcat.global.request.total.count|Darabszám|Összesen|Tomcat-kérelmek teljes száma|Üzembe helyezés, AppName, Pod|
|tomcat.global.request.total.time|Yes|tomcat.global.request.total.time|Ezredmásodperc|Összesen|Tomcat-kérelem teljes ideje|Üzembe helyezés, AppName, Pod|
|tomcat.global.sent|Yes|tomcat.global.sent|Bájt|Összesen|Tomcat összes elküldött bájtja|Üzembe helyezés, AppName, Pod|
|tomcat.sessions.active.current|Yes|tomcat.sessions.active.current|Darabszám|Összesen|Tomcat-munkamenet aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat.sessions.active.max|Yes|tomcat.sessions.active.max|Darabszám|Összesen|Tomcat-munkamenetek maximális aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat.sessions.alive.max|Yes|tomcat.sessions.alive.max|Ezredmásodperc|Maximum|Tomcat-munkamenet maximális életben helyi ideje|Üzembe helyezés, AppName, Pod|
|tomcat.sessions.created|Yes|tomcat.sessions.created|Darabszám|Összesen|Létrehozott Tomcat-munkamenetek száma|Üzembe helyezés, AppName, Pod|
|tomcat.sessions.expired|Yes|tomcat.sessions.expired|Darabszám|Összesen|A Tomcat-munkamenetek száma lejárt|Üzembe helyezés, AppName, Pod|
|tomcat.sessions.rejected|Yes|tomcat.sessions.rejected|Darabszám|Összesen|Tomcat-munkamenet elutasított száma|Üzembe helyezés, AppName, Pod|
|tomcat.threads.config.max|Yes|tomcat.threads.config.max|Darabszám|Összesen|Tomcat config maximális szálszáma|Üzembe helyezés, AppName, Pod|
|tomcat.threads.current|Yes|tomcat.threads.current|Darabszám|Összesen|Tomcat aktuális szálszáma|Üzembe helyezés, AppName, Pod|
|összes kérelem|Yes|összes kérelem|Darabszám|Átlag|Kérelmek teljes száma a folyamat élettartama során|Üzembe helyezés, AppName, Pod|
|working-set (munkakészlet)|Yes|working-set (munkakészlet)|Darabszám|Átlag|A folyamat által használt munkakészlet mennyisége (MB)|Üzembe helyezés, AppName, Pod|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Összes feladat|Darabszám|Összesen|A feladatok teljes száma|Runbook, Állapot|
|TotalUpdateDeploymentMachineRuns|Yes|Frissítéstelepítési gépek teljes futtatása|Darabszám|Összesen|A szoftverfrissítések központi telepítésére használt gépek teljes futtatása egy szoftverfrissítés központi telepítésének futtatásakor|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Frissítéstelepítések teljes futtatása|Darabszám|Összesen|Szoftverfrissítések központi telepítésének teljes futtatása|SoftwareUpdateConfigurationName, Status|


## <a name="microsoftavsprivateclouds"></a>Microsoft.AVS/privateClouds

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|KapacitásLatest (Kapacitás)|Yes|Adattár lemezének teljes kapacitása|Bájt|Átlag|Az adattárban lévő lemez teljes kapacitása|dsname (dsname)|
|DiskUsedPercentage|Yes| Felhasznált adattárlemez százalékos aránya|Százalék|Átlag|Az adattárban rendelkezésre álló lemezek százalékos aránya|dsname (dsname)|
|EffectiveCpuAverage|Yes|Százalékos processzorhasználat|Százalék|Átlag|A fürtben használt CPU-erőforrások százalékos aránya|clustername (fürtnév)|
|EffectiveMemAverage (Hatékony kezelés)|Yes|Átlagos tényleges memória|Bájt|Átlag|A gépmemória teljes rendelkezésre álló mennyisége a fürtben|clustername (fürtnév)|
|Többletterhelés-takarékosság|Yes|Átlagos memóriaterhelés|Bájt|Átlag|A virtualizálási infrastruktúra által felhasznált fizikai memória gazdagépe|clustername (fürtnév)|
|TotalMbAverage|Yes|Átlagos teljes memória|Bájt|Átlag|Fürt összes memóriája|clustername (fürtnév)|
|Használattakarékos üzemmód|Yes|Átlagos memóriahasználat|Százalék|Átlag|Memóriahasználat a teljes konfigurált vagy rendelkezésre álló memória százalékában|clustername (fürtnév)|
|UsedLatest (Felhasznált felhasznált)|Yes|Felhasznált adattárlemez|Bájt|Átlag|Az adattárban használt lemez teljes mennyisége|dsname (dsname)|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Bat/batchAccounts létrehozása

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CoreCount|No|Dedikált magok száma|Darabszám|Összesen|A batch-fiókban található dedikált magok teljes száma|Nincsenek dimenziók|
|CreatingNodeCount (Csomópontszám létrehozása)|No|Csomópontszám létrehozása|Darabszám|Összesen|A létrehozott csomópontok száma|Nincsenek dimenziók|
|IdleNodeCount|No|Tétlen csomópontok száma|Darabszám|Összesen|Tétlen csomópontok száma|Nincsenek dimenziók|
|JobDeleteCompleteEvent|Yes|Feladat törlése kész események|Darabszám|Összesen|A sikeresen törölt feladatok teljes száma.|jobId (feladatazonosító)|
|JobDeleteStartEvent|Yes|Feladat törlésének indítási eseményei|Darabszám|Összesen|A törlésre kért feladatok teljes száma.|jobId (feladatazonosító)|
|JobDisableCompleteEvent|Yes|Feladat letiltása – Befejezett események|Darabszám|Összesen|A sikeresen letiltott feladatok teljes száma.|jobId (feladatazonosító)|
|JobDisableStartEvent|Yes|Feladat indítási eseményeinek letiltása|Darabszám|Összesen|A letiltani kért feladatok teljes száma.|jobId (feladatazonosító)|
|JobStartEvent (Feladatindítási és -vent)|Yes|Feladat-indítási események|Darabszám|Összesen|A sikeresen elindított feladatok teljes száma.|jobId (feladatazonosító)|
|JobTerminateCompleteEvent|Yes|Feladat lemondhatja a befejezett eseményeket|Darabszám|Összesen|A sikeresen lekért feladatok teljes száma.|jobId (feladatazonosító)|
|JobTerminateStartEvent|Yes|Feladat lemondhatja az indítási eseményeket|Darabszám|Összesen|A lekért feladatok teljes száma.|jobId (feladatazonosító)|
|LeavingPoolNodeCount (Várólistacsomópontszám elhagyása)|No|Készletcsomópontszám elhagyása|Darabszám|Összesen|A készletet elhagyó csomópontok száma|Nincsenek dimenziók|
|LowPriorityCoreCount|No|Alacsonyprioritású magok száma|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincsenek dimenziók|
|OfflineNodeCount|No|Offline csomópontok száma|Darabszám|Összesen|Offline csomópontok száma|Nincsenek dimenziók|
|PoolCreateEvent (Készletlétrehozás)|Yes|Készlet létrehozása események|Darabszám|Összesen|A létrehozott készletek teljes száma|poolId (készletazonosító)|
|PoolDeleteCompleteEvent|Yes|Készlet törlése kész események|Darabszám|Összesen|A befejezett készlet törlések teljes száma|poolId (készletazonosító)|
|PoolDeleteStartEvent|Yes|Készlet törlésének indítási eseményei|Darabszám|Összesen|Az elindított készlet törlésének teljes száma|poolId (készletazonosító)|
|PoolResizeCompleteEvent|Yes|Készlet átméretezése kész események|Darabszám|Összesen|A befejezett készletátméretezések teljes száma|poolId (készletazonosító)|
|PoolResizeStartEvent|Yes|Készlet átméretezésének indítási eseményei|Darabszám|Összesen|Az elindított készletátméretezések teljes száma|poolId (készletazonosító)|
|PreemptedNodeCount (Előtagcsomópont száma)|No|Előre meghatározott csomópontok száma|Darabszám|Összesen|A kivonatolt csomópontok száma|Nincsenek dimenziók|
|ANodeCount újraindítása|No|Csomópontok száma újraindítása|Darabszám|Összesen|Újraindítási csomópontok száma|Nincsenek dimenziók|
|ReimagingNodeCount|No|Csomópontok számának újraképezése|Darabszám|Összesen|A csomópontok újraképezésének száma|Nincsenek dimenziók|
|RunningNodeCount (Csomópontszám futtatása)|No|Futó csomópontok száma|Darabszám|Összesen|Futó csomópontok száma|Nincsenek dimenziók|
|StartingNodeCount (Csomópontszám indítása)|No|Csomópontok kezdő száma|Darabszám|Összesen|Induló csomópontok száma|Nincsenek dimenziók|
|StartTaskFailedNodeCount|No|Indítási tevékenység sikertelen csomópontszáma|Darabszám|Összesen|Azon csomópontok száma, amelyeken meghiúsult az indítási feladat|Nincsenek dimenziók|
|TaskCompleteEvent|Yes|Tevékenység kész eseményei|Darabszám|Összesen|A befejezett tevékenységek teljes száma|poolId, jobId|
|TaskFailEvent|Yes|Feladat meghiúsult eseményei|Darabszám|Összesen|A meghiúsult állapotban befejezett tevékenységek teljes száma|poolId, jobId|
|TaskStartEvent (Feladatindítási és -vent)|Yes|Tevékenység-indítási események|Darabszám|Összesen|Az elindított tevékenységek teljes száma|poolId, jobId|
|TotalLowPriorityNodeCount|No|Low-Priority csomópontok száma|Darabszám|Összesen|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincsenek dimenziók|
|TotalNodeCount (Csomópontok száma összesen)|No|Dedikált csomópontok száma|Darabszám|Összesen|A Batch-fiókban található dedikált csomópontok teljes száma|Nincsenek dimenziók|
|UnusableNodeCount|No|Nem használható csomópontok száma|Darabszám|Összesen|Nem használható csomópontok száma|Nincsenek dimenziók|
|WaitingForStartTaskNodeCount|No|Várakozás az indítási tevékenység csomópontszámra|Darabszám|Összesen|Az indítási tevékenység befejezésére várakozó csomópontok száma|Nincsenek dimenziók|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Yes|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Yes|Aktív csomópontok|Darabszám|Átlag|Futó csomópontok száma|Forgatókönyv, ClusterName|
|Tétlen magok|Yes|Tétlen magok|Darabszám|Átlag|Tétlen magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Yes|Tétlen csomópontok|Darabszám|Átlag|Tétlen csomópontok száma|Forgatókönyv, ClusterName|
|A feladat befejeződött|Yes|Feladat befejezve|Darabszám|Összesen|A befejezett feladatok száma|Forgatókönyv, ClusterName, ResultType|
|Elküldött feladat|Yes|Elküldött feladat|Darabszám|Összesen|Elküldött feladatok száma|Forgatókönyv, ClusterName|
|Magok elhagyása|Yes|Magok elhagyása|Darabszám|Átlag|A magok elhagyása|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Yes|Csomópontok elhagyása|Darabszám|Átlag|A meghagyó csomópontok száma|Forgatókönyv, ClusterName|
|Preempted Cores|Yes|Preempted Cores|Darabszám|Átlag|A preemptált magok száma|Forgatókönyv, ClusterName|
|Előre meghatározott csomópontok|Yes|Előre meghatározott csomópontok|Darabszám|Átlag|A preemptált csomópontok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtságának százalékos aránya|Yes|Kvóta kihasználtságának százalékos aránya|Darabszám|Átlag|A felhasznált kvóta százalékos aránya|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Magok teljes száma|Yes|Magok teljes száma|Darabszám|Átlag|Az összes mag száma|Forgatókönyv, ClusterName|
|Összes csomópont|Yes|Összes csomópont|Darabszám|Átlag|Az összes csomópont száma|Forgatókönyv, ClusterName|
|Nem használható magok|Yes|Nem használható magok|Darabszám|Átlag|Nem használható magok száma|Forgatókönyv, ClusterName|
|Nem használható csomópontok|Yes|Nem használható csomópontok|Darabszám|Átlag|Nem használható csomópontok száma|Forgatókönyv, ClusterName|

## <a name="microsoftbingaccounts"></a>microsoft.bing/accounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BlockedCalls (Letiltotthívások)|Yes|Letiltott hívások|Darabszám|Összesen|A sebesség- vagy kvótakorlátot túllépő hívások száma|ApiName, ServingRegion, StatusCode|
|ClientErrors (Ügyfélerrorok)|Yes|Ügyfélhibák|Darabszám|Összesen|Ügyfélhiba esetén a hívások száma (4xx HTTP-állapotkód)|ApiName, ServingRegion, StatusCode|
|DataIn (Adatok)|Yes|Adatok a következőben:|Bájt|Összesen|Bejövő kérelem tartalomhossza bájtban|ApiName, ServingRegion, StatusCode|
|DataOut|Yes|Adatok ki|Bájt|Összesen|Kimenő válasz Tartalom hossza bájtban|ApiName, ServingRegion, StatusCode|
|Késés|Yes|Késés|Ezredmásodperc|Átlag|Késés ezredmásodpercben|ApiName, ServingRegion, StatusCode|
|ServerErrors (Kiszolgálói kiszolgálók)|Yes|Kiszolgálóhibák|Darabszám|Összesen|Kiszolgálóhiba esetén a hívások száma (5xx HTTP-állapotkód)|ApiName, ServingRegion, StatusCode|
|SuccessfulCalls (Sikereshívások)|Yes|Sikeres hívások|Darabszám|Összesen|Sikeres hívások száma (2xx HTTP-állapotkód)|ApiName, ServingRegion, StatusCode|
|TotalCalls (Összeshívás)|Yes|Összes hívás|Darabszám|Összesen|Hívások teljes száma|ApiName, ServingRegion, StatusCode|
|TotalErrors (Összesítők)|Yes|Összes hiba|Darabszám|Összesen|A hibával jelzett hívások száma (HTTP-állapotkód: 4xx vagy 5xx)|ApiName, ServingRegion, StatusCode|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|Darabszám|Átlag|A feldolgozott tranzakciók száma.|Csomópont, csatorna, típus, állapot|
|ChaincodeExecuteTimeouts (ÜzletlánckódexecuteTimeouts)|Yes|ChaincodeExecuteTimeoutsDisplayName|Darabszám|Átlag|Azon lánckód-végrehajtások száma (Init vagy Invoke), amelyek időkorrektáltak.|Csomópont, lánckód|
|ChaincodeLaunchFailures|Yes|ChaincodeLaunchFailuresDisplayName|Darabszám|Átlag|A sikertelen lánckód-indítások száma.|Csomópont, lánckód|
|ChaincodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|Darabszám|Átlag|Az időkorrekta üzletlánckód-indítások száma.|Csomópont, lánckód|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Darabszám|Átlag|A befejezett chaincode-kódkód-kérések száma.|Csomópont, típus, csatorna, lánckód, sikeres|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Darabszám|Átlag|A kapott chaincode-kódkód-kérések száma.|Csomópont, típus, csatorna, lánckód|
|ClusterCommEgressQueueCapacity|Yes|ClusterCommEgressQueueCapacityDisplayName|Darabszám|Átlag|A kigressziós üzenetsor kapacitása.|Csomópont, gazdagép, msg_type, csatorna|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Darabszám|Átlag|A bejövő forgalom várólistájának hossza.|Csomópont, gazdagép, msg_type, csatorna|
|ClusterCommEgressQueueWorkers|Yes|ClusterCommEgressQueueWorkersDisplayName|Darabszám|Átlag|A bejövő üzenetsor-dolgozók száma.|Csomópont, csatorna|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|Darabszám|Átlag|Streamek száma más csomópontokra.|Csomópont, csatorna|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Darabszám|Átlag|A más csomópontokkal létesítendő TLS-kapcsolatok száma.|Csomópont|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Darabszám|Átlag|A többi csomópontról származó streamek száma.|Csomópont|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Darabszám|Átlag|Eldobott üzenetek száma.|Csomópont, gazdagép, csatorna|
|ConnectionAccepted (Kapcsolati jog)|Yes|Elfogadott kapcsolatok|Darabszám|Összesen|Elfogadott kapcsolatok|Csomópont|
|ConnectionActive (Kapcsolat aktív)|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Csomópont|
|ConnectionHandled (Kapcsolat kezelt)|Yes|Kezelt kapcsolatok|Darabszám|Összesen|Kezelt kapcsolatok|Csomópont|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Darabszám|Átlag|A csatorna aktív csomópontjainak száma.|Csomópont, csatorna|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Darabszám|Átlag|A csatorna csomópontjainak száma.|Csomópont, csatorna|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Darabszám|Átlag|A legutóbb lekötött blokk blokkszáma.|Csomópont, csatorna|
|ConsensusEtcdraftConfigProposalsReceived|Yes|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Darabszám|Átlag|A konfigurációs típusú tranzakciókra vonatkozó javaslatok teljes száma.|Csomópont, csatorna|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|Darabszám|Átlag|Az aktuális csomópont vezetői állapota: 1, ha a vezető más 0.|Csomópont, csatorna|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Darabszám|Átlag|A vezető változásainak száma a folyamat kezdete óta.|Csomópont, csatorna|
|ConsensusEtcdraftNormalProposalsReceived|Yes|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Darabszám|Átlag|A normál típusú tranzakciókra vonatkozó javaslatok teljes száma.|Csomópont, csatorna|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Darabszám|Átlag|A javaslathibák száma.|Csomópont, csatorna|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Darabszám|Átlag|A legújabb pillanatkép blokkszáma.|Csomópont, csatorna|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Darabszám|Átlag|A kötegek átlagos mérete a témakörökbe küldött bájtban.|Csomópont, témakör|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Darabszám|Átlag|A témakörök átlagos tömörítési aránya (százalékban kifejezve).|Csomópont, témakör|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Darabszám|Átlag|Bájt/másodpercben leolvassa a közvetítőket.|Node, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Darabszám|Átlag|A legutóbb lekötött blokk blokk-metaadataiban megadott eltolás.|Csomópont, csatorna|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Darabszám|Átlag|Közvetítőkbe írt bájt/másodperc.|Node, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Darabszám|Átlag|A témaköröknek küldött rekordok másodpercenkénti száma.|Csomópont, témakör|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Darabszám|Átlag|A témaköröknek küldött kérésenkénti rekordok átlagos száma.|Csomópont, témakör|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Darabszám|Átlag|A kérések átlagos késése ms-ban a közvetítők számára.|Node, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Darabszám|Átlag|A közvetítőknek küldött kérések másodpercenkénti száma.|Node, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Darabszám|Átlag|A közvetítők kérésének átlagos mérete bájtban.|Node, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Darabszám|Átlag|A közvetítőknek küldött kérések másodpercenkénti száma.|Node, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Darabszám|Átlag|A közvetítők átlagos válaszmérete bájtban.|Node, broker_id|
|CpuUsagePercentageInDouble|Yes|Processzorhasználat százalékos aránya|Százalék|Maximum|Processzorhasználat százalékos aránya|Csomópont|
|DeliverBlocksSent|Yes|DeliverBlocksSentDisplayName|Darabszám|Átlag|A kézbesítési szolgáltatás által küldött blokkok száma.|Csomópont, csatorna, szűrt, data_type|
|DeliverRequestsCompleted|Yes|DeliverRequestsCompletedDisplayName|Darabszám|Átlag|A befejezett kézbesítési kérelmek száma.|Csomópont, csatorna, szűrt, data_type, sikeres|
|DeliverRequestsReceived|Yes|DeliverRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott kézbesítési kérelmek száma.|Csomópont, csatorna, szűrt, data_type|
|DeliverStreamsBezárás|Yes|DeliverStreamsClosedDisplayName|Darabszám|Átlag|A szolgáltatás számára lezárt GRPC-streamek száma.|Csomópont|
|DeliverStreamsOpened|Yes|DeliverStreamsOpenedDisplayName|Darabszám|Átlag|A szolgáltatáshoz megnyitott GRPC-streamek száma.|Csomópont|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|Darabszám|Átlag|A sikertelen chaincode-példányok vagy -frissítések száma.|Csomópont, csatorna, lánckód|
|EndorserDuplicateTransactionFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|Darabszám|Átlag|A duplikált tranzakcióazonosító miatt meghiúsult javaslatok száma.|Csomópont, csatorna, lánckód|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|Darabszám|Átlag|A sikertelen ajánlások száma.|Csomópont, csatorna, lánckód, chaincodeerror|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|Darabszám|Átlag|Az ACL-ellenőrzések meghiúsult javaslatainak száma.|Csomópont, csatorna, lánckód|
|EndorserProposalSimulationFailures|Yes|EndorserProposalSimulationFailuresDisplayName|Darabszám|Átlag|A sikertelen ajánlatszimulációk száma.|Csomópont, csatorna, lánckód|
|EndorserProposalsReceived|Yes|EndorserProposalsReceivedDisplayName|Darabszám|Átlag|A fogadott javaslatok száma.|Csomópont|
|EndorserProposalValidationFailures|Yes|EndorserProposalValidationFailuresDisplayName|Darabszám|Átlag|A kezdeti érvényesítést sikertelenül elkért javaslatok száma.|Csomópont|
|EndorserSuccessfulProposals|Yes|EndorserSuccessfulProposalsDisplayName|Darabszám|Átlag|A sikeres javaslatok száma.|Csomópont|
|FabricVersion|Yes|FabricVersionDisplayName|Darabszám|Átlag|A Háló aktív verziója.|Csomópont, verzió|
|CommMessagesReceived|Yes|CommMessagesReceivedDisplayName|Darabszám|Átlag|A fogadott üzenetek száma.|Csomópont|
|CommMessagesSent|Yes|CommMessagesSentDisplayName|Darabszám|Átlag|Az elküldött üzenetek száma.|Csomópont|
|CommOverflowCount|Yes|CommOverflowCountDisplayName|Darabszám|Átlag|A kimenő üzenetsor puffertúlcsordulásának száma.|Csomópont|
|ValamintLeaderElectionLeader|Yes|LettLeaderElectionLeaderDisplayName|Darabszám|Átlag|A társ vezető (1) vagy követő (0).|Csomópont, csatorna|
|FogamembershipTotalPeersKnown|Yes|FogamembershipTotalPeersKnownDisplayName|Darabszám|Átlag|Összes ismert társ.|Csomópont, csatorna|
|PayloadBufferSize|Yes|PayloadBufferSizeDisplayName|Darabszám|Átlag|A hasznos adatpuffer mérete.|Csomópont, csatorna|
|StateHeight (Új-StateHeight)|Yes|StateHeightDisplayName|Darabszám|Átlag|Aktuális főkönyv magassága.|Csomópont, csatorna|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Darabszám|Átlag|GRPC-kapcsolatok lezárva. Az Open mínusz zárt a kapcsolatok aktív száma.|Csomópont|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Darabszám|Átlag|GRPC-kapcsolatok megnyitva. Az Open mínusz zárt a kapcsolatok aktív száma.|Csomópont|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|Darabszám|Átlag|A fogadott streamüzenetek száma.|Csomópont, szolgáltatás, metódus|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|Darabszám|Átlag|Az elküldött streamüzenetek száma.|Csomópont, szolgáltatás, metódus|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Darabszám|Átlag|A befejezett streamkérések száma.|Csomópont, szolgáltatás, metódus, kód|
|GrpcServerUnaryRequestsReceived|Yes|GrpcServerUnaryRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott egyenletlen kérelmek száma.|Csomópont, szolgáltatás, metódus|
|IOReadBytes|Yes|I/O-olvasási bájtok|Bájt|Összesen|I/O-olvasási bájtok|Csomópont|
|IOWriteBytes|Yes|IO-írási bájtok|Bájt|Összesen|IO-írási bájtok|Csomópont|
|LedgerBlockchainHeight|Yes|LedgerBlockchainHeightDisplayName|Darabszám|Átlag|A lánc magassága blokkokban.|Csomópont, csatorna|
|LedgerTransactionCount (Tranzakció tranzakciószáma)|Yes|LedgerTransactionCountDisplayName|Darabszám|Átlag|A feldolgozott tranzakciók száma.|Csomópont, csatorna, transaction_type, üzletlánckód, validation_code|
|LoggingEntriesChecked|Yes|LoggingEntriesCheckedDisplayName|Darabszám|Átlag|Az aktív naplózási szintnek megfelelő naplóbejegyzések száma.|Csomópont, szint|
|LoggingEntriesWritten (Naplók felülírva)|Yes|LoggingEntriesWrittenDisplayName|Darabszám|Átlag|A megírt naplóbejegyzések száma.|Csomópont, szint|
|MemoryLimit (Memórialimit)|Yes|Memóriakorlát|Bájt|Átlag|Memóriakorlát|Csomópont|
|MemoryUsage (Memória)Usage (Memória)|Yes|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryUsagePercentageInDouble|Yes|Memóriahasználat százalékos aránya|Százalék|Átlag|Memóriahasználat százalékos aránya|Csomópont|
|PendingTransactions (Függőben lévő műveletek)|Yes|Függőben lévő tranzakciók|Darabszám|Átlag|Függőben lévő tranzakciók|Csomópont|
|ProcessedBlocks|Yes|Feldolgozott blokkok|Darabszám|Összesen|Feldolgozott blokkok|Csomópont|
|ProcessedTransactions (Feldolgozotttranzakciók)|Yes|Feldolgozott tranzakciók|Darabszám|Összesen|Feldolgozott tranzakciók|Csomópont|
|QueuedTransactions (Üzenetsorba ékelt üzenetek)|Yes|Várólistán lévő tranzakciók|Darabszám|Átlag|Várólistán lévő tranzakciók|Csomópont|
|RequestHandled (Kérés által kezelt)|Yes|Kezelt kérések|Darabszám|Összesen|Kezelt kérések|Csomópont|
|StorageUsage|Yes|Tárterület-használat|Bájt|Átlag|Tárterület-használat|Csomópont|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|RequestLatency (Kérelem lekérése)|Yes|Kérések késése|Ezredmásodperc|Összesen|A kiszolgáló által a kérés feldolgozásához szükséges idő|Működés, hitelesítés, protokoll, adatközpont|
|RequestsTraffic|Yes|Forgalom kérése|Százalék|Darabszám|A kérések száma|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Gyorsítótár-találatok (példányalapú)|Darabszám|Összesen||ShardId, Port, Elsődleges|
|allcachemisses|Yes|Gyorsítótár-tévesztés (példányalapú)|Darabszám|Összesen||ShardId, Port, Elsődleges|
|allcacheRead|Yes|Gyorsítótár-olvasás (példányalapú)|BájtokPerSecond|Maximum||ShardId, Port, Elsődleges|
|allcacheWrite|Yes|Gyorsítótár írása (példányalapú)|BájtperSecond (bájt/másodperc)|Maximum||ShardId, Port, Elsődleges|
|allconnectedclients (összes kapcsolódóclient)|Yes|Csatlakoztatott ügyfelek (példányalapú)|Darabszám|Maximum||ShardId, Port, Elsődleges|
|allevictedkeys|Yes|Kieső kulcsok (példányalapú)|Darabszám|Összesen||ShardId, Port, Elsődleges|
|allexpiredkeys|Yes|Lejárt kulcsok (példányalapú)|Darabszám|Összesen||ShardId, Port, Elsődleges|
|allgetcommands|Yes|Lekért (példányalapú)|Darabszám|Összesen||ShardId, Port, Elsődleges|
|alloperationsPerSecond|Yes|Műveletek száma másodpercenként (példányalapú)|Darabszám|Maximum||ShardId, Port, Elsődleges|
|allserverLoad|Yes|Kiszolgálóterhelés (példányalapú)|Százalék|Maximum||ShardId, Port, Elsődleges|
|allsetcommands|Yes|Készletek (példányalapú)|Darabszám|Összesen||ShardId, Port, Elsődleges|
|alltotalcommandsprocessed|Yes|Összes művelet (példányalapú)|Darabszám|Összesen||ShardId, Port, Elsődleges|
|alltotalkeys (összes kulcs)|Yes|Összes kulcs (példányalapú)|Darabszám|Maximum||ShardId, Port, Elsődleges|
|allusedmemory|Yes|Felhasznált memória (példányalapú)|Bájt|Maximum||ShardId, Port, Elsődleges|
|allusedmemorypercentage|Yes|Felhasznált memória százalékos aránya (példányalapú)|Százalék|Maximum||ShardId, Port, Elsődleges|
|allusedmemoryRss|Yes|Felhasznált memória RSS (példányalapú)|Bájt|Maximum||ShardId, Port, Elsődleges|
|cachehits|Yes|Gyorsítótár-találatok|Darabszám|Összesen||ShardId (Szegmensazonosító)|
|cachehits0|Yes|Gyorsítótár-találatok (0. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits1|Yes|Gyorsítótár-találatok (1. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits2|Yes|Gyorsítótár-találatok (2. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits3|Yes|Gyorsítótár-találatok (3. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cacheemets4|Yes|Gyorsítótár-találatok (4. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits5|Yes|Gyorsítótár-találatok (5. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits6|Yes|Gyorsítótár-találatok (6. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits7|Yes|Gyorsítótár-találatok (7. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits8|Yes|Gyorsítótár-találatok (8. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachehits9|Yes|Gyorsítótár-találatok (9. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cacheLatency|Yes|Gyorsítótár késése – mikroszekundum (előzetes verzió)|Darabszám|Átlag||ShardId (Szegmensazonosító)|
|cachemisses|Yes|Gyorsítótár-tévesztések|Darabszám|Összesen||ShardId (Szegmensazonosító)|
|cachemisses0|Yes|Gyorsítótár-tévesztés (0. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses1|Yes|Gyorsítótár-tévesztés (1. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses2|Yes|Gyorsítótár-tévesztés (2. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses3|Yes|Gyorsítótár-tévesztés (3. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses4|Yes|Gyorsítótár-tévesztés (4. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses5|Yes|Gyorsítótár-tévesztés (5. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses6|Yes|Gyorsítótár-tévesztés (6. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses7|Yes|Gyorsítótár-tévesztés (7. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses8|Yes|Gyorsítótár-tévesztés (8. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemisses9|Yes|Gyorsítótár-tévesztés (9. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|cachemissrate|Yes|Gyorsítótár-tévesztések aránya|Százalék|cachemissrate||ShardId (Szegmensazonosító)|
|cacheRead (gyorsítótár beolvasása)|Yes|Gyorsítótár-olvasás|BájtokPerSecond|Maximum||ShardId (Szegmensazonosító)|
|cacheRead0|Yes|Gyorsítótár olvasása (0. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheRead1|Yes|Gyorsítótár-olvasás (1. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheRead2|Yes|Gyorsítótár-olvasás (2. szegmens)|BájtperSecond (bájt/másodperc)|Maximum||Nincsenek dimenziók|
|cacheRead3|Yes|Gyorsítótár-olvasás (3. szegmens)|Bájt/másodperc|Maximum||Nincsenek dimenziók|
|cacheRead4|Yes|Gyorsítótár-olvasás (4. szegmens)|Bájt/másodperc|Maximum||Nincsenek dimenziók|
|cacheRead5|Yes|Gyorsítótár-olvasás (5. szegmens)|Bájt/másodperc|Maximum||Nincsenek dimenziók|
|cacheRead6|Yes|Gyorsítótár-olvasás (6. szegmens)|BájtperSecond (bájt/másodperc)|Maximum||Nincsenek dimenziók|
|cacheRead7|Yes|Gyorsítótár-olvasás (7. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheRead8|Yes|Gyorsítótár-olvasás (8. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheRead9|Yes|Gyorsítótár-olvasás (9. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheWrite|Yes|Gyorsítótár-írás|BájtokPerSecond|Maximum||ShardId (Szegmensazonosító)|
|cacheWrite0|Yes|Gyorsítótár írása (0. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheWrite1|Yes|Gyorsítótár írása (1. szegmens)|Bájt/másodperc|Maximum||Nincsenek dimenziók|
|cacheWrite2|Yes|Gyorsítótár írása (2. szegmens)|Bájt/másodperc|Maximum||Nincsenek dimenziók|
|cacheWrite3|Yes|Gyorsítótár írása (3. szegmens)|Bájt/másodperc|Maximum||Nincsenek dimenziók|
|cacheWrite4|Yes|Gyorsítótár írása (4. szegmens)|Bájt/másodperc|Maximum||Nincsenek dimenziók|
|cacheWrite5|Yes|Gyorsítótár írása (5. szegmens)|BájtperSecond (bájt/másodperc)|Maximum||Nincsenek dimenziók|
|cacheWrite6|Yes|Gyorsítótár írása (6. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheWrite7|Yes|Gyorsítótár írása (7. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheWrite8|Yes|Gyorsítótár írása (8. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|cacheWrite9|Yes|Gyorsítótár írása (9. szegmens)|BájtokPerSecond|Maximum||Nincsenek dimenziók|
|connectedclients (csatlakoztatottclientek)|Yes|Csatlakoztatott ügyfelek|Darabszám|Maximum||ShardId (Szegmensazonosító)|
|connectedclients0|Yes|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients1|Yes|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients2|Yes|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients3|Yes|Csatlakoztatott ügyfelek (3. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients4|Yes|Csatlakoztatott ügyfelek (4. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients5|Yes|Csatlakoztatott ügyfelek (5. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients6|Yes|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients7|Yes|Csatlakoztatott ügyfelek (7. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients8|Yes|Csatlakoztatott ügyfelek (8. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|connectedclients9|Yes|Csatlakoztatott ügyfelek (9. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|Hibák|Yes|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|evictedkeys|Yes|Kizárt kulcsok|Darabszám|Összesen||ShardId (Szegmensazonosító)|
|evictedkeys0|Yes|Kieső kulcsok (0. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys1|Yes|Kieső kulcsok (1. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys2|Yes|Kieső kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys3|Yes|Kieső kulcsok (3. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys4|Yes|Kieső kulcsok (4. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys5|Yes|Kieső kulcsok (5. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys6|Yes|Kieső kulcsok (6. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys7|Yes|Kieső kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys8|Yes|Kieső kulcsok (8. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|evictedkeys9|Yes|Kieső kulcsok (9. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys|Yes|Lejárt kulcsok|Darabszám|Összesen||ShardId (Szegmensazonosító)|
|expiredkeys0|Yes|Lejárt kulcsok (0. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys1|Yes|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys2|Yes|Lejárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys3|Yes|Lejárt kulcsok (3. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys4|Yes|Lejárt kulcsok (4. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys5|Yes|Lejárt kulcsok (5. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys6|Yes|Lejárt kulcsok (6. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys7|Yes|Lejárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys8|Yes|Lejárt kulcsok (8. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys9|Yes|Lejárt kulcsok (9. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands|Yes|Lekérések|Darabszám|Összesen||ShardId (Szegmensazonosító)|
|getcommands0|Yes|Lekért (0. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands1|Yes|Lekért (1. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands2|Yes|Lekért (2. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands3|Yes|Lekért (3. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands4|Yes|Lekért (4. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands5|Yes|Lekért (5. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands6|Yes|Lekért (6. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands7|Yes|Lekért (7. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands8|Yes|Lekért (8. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands9|Yes|Lekért (9. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|operationsPerSecond|Yes|Műveletek száma másodpercenként|Darabszám|Maximum||ShardId (Szegmensazonosító)|
|operationsPerSecond0|Yes|Műveletek másodpercenként (0. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond1|Yes|Műveletek másodpercenként (1. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond2|Yes|Műveletek másodpercenként (2. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond3|Yes|Műveletek másodpercenként (3. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond4|Yes|Műveletek száma másodpercenként (4. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond5|Yes|Műveletek másodpercenként (5. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond6|Yes|Műveletek másodpercenként (6. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond7|Yes|Műveletek másodpercenként (7. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond8|Yes|Műveletek száma másodpercenként (8. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|operationsPerSecond9|Yes|Műveletek száma másodpercenként (9. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|percentProcessorTime|Yes|CPU|Százalék|Maximum||ShardId (Szegmensazonosító)|
|percentProcessorTime0|Yes|CPU (0. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime1|Yes|CPU (1. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime2|Yes|CPU (2. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime3|Yes|CPU (3. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime4|Yes|CPU (4. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime5|Yes|CPU (5. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime6|Yes|CPU (6. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime7|Yes|CPU (7. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime8|Yes|CPU (8. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|percentProcessorTime9|Yes|CPU (9. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad (kiszolgálóterhelés)|Yes|Kiszolgáló terhelése|Százalék|Maximum||ShardId (Szegmensazonosító)|
|serverLoad0|Yes|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad1|Yes|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad2|Yes|Kiszolgáló terhelése (2. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad3|Yes|Kiszolgáló terhelése (3. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad4|Yes|Kiszolgáló terhelése (4. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad5|Yes|Kiszolgáló terhelése (5. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad6|Yes|Kiszolgáló terhelése (6. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad7|Yes|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad8|Yes|Kiszolgáló terhelése (8. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|serverLoad9|Yes|Kiszolgáló terhelése (9. szegmens)|Százalék|Maximum||Nincsenek dimenziók|
|setcommands|Yes|Halmazok|Darabszám|Összesen||ShardId (Szegmensazonosító)|
|setcommands0|Yes|Készletek (0. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands1|Yes|Készletek (1. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands2|Yes|Készletek (2. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands3|Yes|Készletek (3. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands4|Yes|Készletek (4. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands5|Yes|Készletek (5. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands6|Yes|Készletek (6. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands7|Yes|Készletek (7. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands8|Yes|Készletek (8. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|setcommands9|Yes|Készletek (9. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed|Yes|Műveletek összesen|Darabszám|Összesen||ShardId (Szegmensazonosító)|
|totalcommandsprocessed0|Yes|Összes művelet (0. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed1|Yes|Összes művelet (1. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed2|Yes|Összes művelet (2. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed3|Yes|Összes művelet (3. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed4|Yes|Összes művelet (4. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed5|Yes|Összes művelet (5. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed6|Yes|Összes művelet (6. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed7|Yes|Összes művelet (7. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed8|Yes|Összes művelet (8. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed9|Yes|Összes művelet (9. szegmens)|Darabszám|Összesen||Nincsenek dimenziók|
|totalkeys (összes kulcs)|Yes|Kulcsok összesen|Darabszám|Maximum||ShardId (Szegmensazonosító)|
|totalkeys0|Yes|Összes kulcs (0. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys1|Yes|Összes kulcs (1. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys2|Yes|Kulcsok összesen (2. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys3|Yes|Kulcsok összesen (3. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys4|Yes|Összes kulcs (4. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys5|Yes|Összes kulcs (5. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys6|Yes|Összes kulcs (6. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys7|Yes|Összes kulcs (7. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys8|Yes|Összes kulcs (8. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|totalkeys9|Yes|Összes kulcs (9. szegmens)|Darabszám|Maximum||Nincsenek dimenziók|
|usedmemory|Yes|Felhasznált memória|Bájt|Maximum||ShardId (Szegmensazonosító)|
|usedmemory0|Yes|Felhasznált memória (0. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory1|Yes|Felhasznált memória (1. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory2|Yes|Felhasznált memória (2. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory3|Yes|Felhasznált memória (3. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory4|Yes|Felhasznált memória (4. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory5|Yes|Felhasznált memória (5. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory6|Yes|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory7|Yes|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory8|Yes|Felhasznált memória (8. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemory9|Yes|Felhasznált memória (9. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemorypercentage|Yes|Felhasznált memória százalékos aránya|Százalék|Maximum||ShardId (Szegmensazonosító)|
|usedmemoryRss|Yes|Felhasznált memória RSS|Bájt|Maximum||ShardId (Szegmensazonosító)|
|usedmemoryRss0|Yes|Felhasznált memória RSS (0. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss1|Yes|Felhasznált memória RSS (1. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss2|Yes|Felhasznált memória RSS (2. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss3|Yes|Felhasznált memória RSS (3. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss4|Yes|Felhasznált memória RSS (4. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss5|Yes|Felhasznált memória RSS (5. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss6|Yes|Felhasznált memória RSS (6. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss7|Yes|Felhasznált memória RSS (7. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss8|Yes|Felhasznált memória RSS (8. szegmens)|Bájt|Maximum||Nincsenek dimenziók|
|usedmemoryRss9|Yes|Felhasznált memória RSS (9. szegmens)|Bájt|Maximum||Nincsenek dimenziók|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|cachehits|Yes|Gyorsítótár-találatok|Darabszám|Összesen||Nincsenek dimenziók|
|cacheLatency|Yes|Gyorsítótár késése – mikroszekundum (előzetes verzió)|Darabszám|Átlag||InstanceId|
|cachemisses|Yes|Gyorsítótár-tévesztések|Darabszám|Összesen||InstanceId|
|cacheRead (gyorsítótár beolvasása)|Yes|Gyorsítótár-olvasás|BájtokPerSecond|Maximum||InstanceId|
|cacheWrite|Yes|Gyorsítótár-írás|BájtokPerSecond|Maximum||InstanceId|
|connectedclients (csatlakoztatottclientek)|Yes|Csatlakoztatott ügyfelek|Darabszám|Maximum||InstanceId|
|Hibák|Yes|Hibák|Darabszám|Maximum||InstanceId, ErrorType|
|evictedkeys|Yes|Kizárt kulcsok|Darabszám|Összesen||Nincsenek dimenziók|
|expiredkeys (lejárt kulcs)|Yes|Lejárt kulcsok|Darabszám|Összesen||Nincsenek dimenziók|
|getcommands|Yes|Lekérések|Darabszám|Összesen||Nincsenek dimenziók|
|operationsPerSecond|Yes|Műveletek száma másodpercenként|Darabszám|Maximum||Nincsenek dimenziók|
|percentProcessorTime|Yes|CPU|Százalék|Maximum||InstanceId|
|serverLoad (kiszolgálóterhelés)|Yes|Kiszolgáló terhelése|Százalék|Maximum||Nincsenek dimenziók|
|setcommands|Yes|Halmazok|Darabszám|Összesen||Nincsenek dimenziók|
|totalcommandsprocessed|Yes|Műveletek összesen|Darabszám|Összesen||Nincsenek dimenziók|
|totalkeys (összes kulcs)|Yes|Kulcsok összesen|Darabszám|Maximum||Nincsenek dimenziók|
|usedmemory|Yes|Felhasznált memória|Bájt|Maximum||Nincsenek dimenziók|
|usedmemorypercentage|Yes|Felhasznált memória százalékos aránya|Százalék|Maximum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall kérelmek száma|Darabszám|Összesen|Az ügyfél által feldolgozott ügyfélkérések Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Bájt találati aránya|Százalék|Átlag|Ez a gyorsítótár által kiszolgált teljes bájtok aránya a teljes válasz bájthoz képest|Végpont|
|OriginHealthPercentage|Yes|Forrás állapotának százalékos aránya|Százalék|Átlag|Az AFDX és a háttér között sikeres állapot-mintavételek százalékos aránya.|Origin, OriginGroup|
|OriginLatency (Forrás latency)|Yes|Forrás késése|Ezredmásodperc|Átlag|Az AFDX peremhálózata által a háttérnek küldött kérésből kiszámított idő, amíg az AFDX nem kapta meg a háttértől az utolsó bájtot.|Forrás, végpont|
|OriginRequestCount|Yes|Forráskérések száma|Darabszám|Összesen|Az AFDX-től a forráshoz küldött kérelmek száma.|HttpStatus, HttpStatusGroup, Forrás, Végpont|
|Percentage4XX (Százalék4XX)|Yes|A 4XX százalékos aránya|Százalék|Átlag|Azon ügyfélkérések százalékos aránya, amelyek válaszállapot-kódja 4XX|Endpoint, ClientRegion, ClientCountry|
|Percentage5XX (Százalékos érték)|Yes|Az 5XX százalékos aránya|Százalék|Átlag|Azon ügyfélkérések százalékos aránya, amelyek válaszállapot-kódja 5XX|Endpoint, ClientRegion, ClientCountry|
|RequestCount (Kérelem száma)|Yes|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfélkérések száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|RequestSize (Kérések megszagorizálás|Yes|Kérés mérete|Bájt|Összesen|Az ügyfelektől az AFDX-nek kérésként küldött bájtok száma.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|ResponseSize (Válaszszava)|Yes|Válasz mérete|Bájt|Összesen|A HTTP/S proxy által az ügyfeleknek válaszként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|TotalLatency (Teljes levallatencia)|Yes|Teljes késés|Ezredmásodperc|Átlag|A HTTP/S proxy ügyfélkérésének bekérése és az utolsó bájt a HTTP/S proxytól való nyugtázásáig számított idő|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall kérelmek száma|Darabszám|Összesen|Az ügyfél által feldolgozott ügyfélkérések Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájt/s|No|Lemez olvasása|BájtokPerSecond|Átlag|Lemezről beolvasott átlagos bájtok száma a monitorozási időszakban.|RoleInstanceId (Szerepkör-instanceId)|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Lemez olvasási IOPS.|RoleInstanceId (Szerepkör-instanceId)|
|Lemezírási bájt/s|No|Lemezírás|BájtperSecond (bájt/másodperc)|Átlag|A lemezre írt átlagos bájtok száma a monitorozási időszakban.|RoleInstanceId (Szerepkör-instanceId)|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|LemezírásI IOPS.|RoleInstanceId (Szerepkör-instanceId)|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId (Szerepkör-instanceId)|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren kimenő bájtok száma.|RoleInstanceId (Szerepkör-instanceId)|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ök) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId (Szerepkör-instanceId)|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájt/s|No|Lemez olvasása|BájtokPerSecond|Átlag|Lemezről beolvasott átlagos bájtok száma a monitorozási időszakban.|Nincsenek dimenziók|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Lemez olvasási IOPS-érték.|Nincsenek dimenziók|
|Lemez írása (bájt/s)|No|Lemezírás|BájtokPerSecond|Átlag|A lemezre írt átlagos bájtok száma a monitorozási időszakban.|Nincsenek dimenziók|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|LemezírásI IOPS.|Nincsenek dimenziók|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincsenek dimenziók|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren kimenő bájtok száma.|Nincsenek dimenziók|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|Azon lefoglalt számítási egységek százalékos aránya, amelyek jelenleg használatban vannak a virtuális gép(ök)hez.|Nincsenek dimenziók|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kérésének végpontok közötti késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt késés, ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity (Felhasznált kapacitás)|No|Felhasznált kapacitás|Bájt|Átlag|Felhasznált fiókkapacitás|Nincsenek dimenziók|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|BlobCapacity (Blobkapacitás)|No|Blobkapacitás|Bájt|Átlag|A tárfiók tárterülete által felhasznált Blob service bájtban meg.|BlobType, szint|
|BlobCount|No|Blobok száma|Darabszám|Átlag|A tárfiók tárolóhelyének blobszáma Blob service.|BlobType, Szint|
|ContainerCount|Yes|Blobtárolók száma|Darabszám|Átlag|A tárfiók tárolóinak száma Blob service.|Nincsenek dimenziók|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|IndexCapacity (Indexkapacitás)|No|Indexkapacitás|Bájt|Átlag|A (hierarchikus) index által ADLS Gen2 tárterület mennyisége bájtban.|Nincsenek dimenziók|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kérésének végpontok közötti késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt késés, ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kihozott adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|FileCapacity (Fájlkapacitás)|No|Fájlkapacitás|Bájt|Átlag|A tárfiók File szolgáltatása által felhasznált tárterület bájtban.|Fájlmegosztás|
|FileCount (Fájlszám)|No|Fájlok száma|Darabszám|Átlag|A tárfiók File szolgáltatásában található fájlok száma.|Fájlmegosztás|
|FileShareCount (Fájlmegosztás száma)|No|Fájlmegosztások száma|Darabszám|Átlag|A tárfiók File szolgáltatásában található fájlmegosztások száma.|Nincsenek dimenziók|
|FileShareQuota|No|Fájlmegosztási kvóta mérete|Bájt|Átlag|A szolgáltatás által bájtban Azure Files tárterület felső korlátja.|Fájlmegosztás|
|FileShareSnapshotCount|No|Fájlmegosztási pillanatképek száma|Darabszám|Átlag|A tárfiók Files szolgáltatásában található megosztáson található pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|No|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A tárfiók File szolgáltatásában a pillanatképek által használt tárterület bájtban.|Fájlmegosztás|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet felé sikeresen lekért kérések végpontok közötti késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|SuccessServerLatency|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt késés, ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kihozott adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|QueueCapacity (Várólistakapacitás)|Yes|Üzenetsor kapacitása|Bájt|Átlag|A tárfiók tárterülete által felhasznált Queue szolgáltatás bájtban meg.|Nincsenek dimenziók|
|QueueCount|Yes|Üzenetsorok száma|Darabszám|Átlag|A tárfiók tárolójának üzenetsorának Queue szolgáltatás.|Nincsenek dimenziók|
|QueueMessageCount|Yes|Üzenetsor üzenetszáma|Darabszám|Átlag|A tárfiók üzenetsor-üzenetei körülbelül Queue szolgáltatás.|Nincsenek dimenziók|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet felé sikeres kérések végpontok közötti késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency (Sikeres kiszolgálólatency)|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt késés, ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kihozott adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kérésének végpontok közötti késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt késés, ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|TableCapacity (Táblakapacitás)|Yes|Táblakapacitás|Bájt|Átlag|A tárfiók Table szolgáltatása által felhasznált tárterület bájtban.|Nincsenek dimenziók|
|TableCount (Táblaszám)|Yes|Táblaszám|Darabszám|Átlag|A tábla száma a tárfiók Table szolgáltatásában.|Nincsenek dimenziók|
|TableEntityCount (Tábla száma)|Yes|Táblaentitások száma|Darabszám|Átlag|A tárfiók Table service szolgáltatásában található táblaentitások száma.|Nincsenek dimenziók|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BlockedCalls (Letiltotthívások)|Yes|Letiltott hívások|Darabszám|Összesen|A sebesség- vagy kvótakorlátot túllépő hívások száma.|ApiName, OperationName, Region|
|Karakterek által korlátozott|Yes|Betanított karakterek|Darabszám|Összesen|A betanított karakterek teljes száma.|ApiName, OperationName, Region|
|Karakterek Lefordítva|Yes|Karakterek lefordítva|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, Region|
|ClientErrors (Ügyfélerrorok)|Yes|Ügyfélhibák|Darabszám|Összesen|Ügyféloldali hibával (4xx HTTP-válaszkód) jelzett hívások száma.|ApiName, OperationName, Region|
|DataIn (Adatok)|Yes|Adatok be|Bájt|Összesen|A bejövő adatok mérete bájtban.|ApiName, OperationName, Region|
|DataOut|Yes|Adatok ki|Bájt|Összesen|A kimenő adatok mérete bájtban.|ApiName, OperationName, Region|
|Késés|Yes|Késés|Ezredmásodperc|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, Region|
|LearnedEvents (Tanultak)|Yes|Tanult események|Darabszám|Összesen|Megtanult események száma.|IsMatchBaseline, Mode, RunId|
|MatchedRewards (Megfeleltetve)|Yes|Matched Rewards|Darabszám|Összesen| A megfeleltetve rewardsok száma.|IsMatchBaseline, Mode, RunId|
|ObservedRewards (Megfigyelt irányok)|Yes|Megfigyelt rewards|Darabszám|Összesen|A megfigyelt rewardsok száma.|IsMatchBaseline, Mode, RunId|
|ProcessedCharacters (Folyamatábra)|Yes|Feldolgozott karakterek|Darabszám|Összesen|Karakterek száma.|ApiName, FeatureName, UsageChannel, Region|
|ProcessedTextRecords|Yes|Feldolgozott szövegrekordok|Darabszám|Összesen|Szövegrekordok száma.|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors (Kiszolgálói kiszolgálók)|Yes|Kiszolgálóhibák|Darabszám|Összesen|A szolgáltatás belső hibája esetén a hívások száma (HTTP-válaszkód: 5xx).|ApiName, OperationName, Region|
|SpeechSessionDuration|Yes|Beszéd-munkamenet időtartama|Másodperc|Összesen|A beszéd-munkamenet teljes időtartama másodpercben.|ApiName, OperationName, Region|
|SuccessfulCalls (Sikereshívások)|Yes|Sikeres hívások|Darabszám|Összesen|A sikeres hívások száma.|ApiName, OperationName, Region|
|TotalCalls (Összeshívás)|Yes|Összes hívás|Darabszám|Összesen|A hívások teljes száma.|ApiName, OperationName, Region|
|TotalErrors (Összesítő)|Yes|Összes hiba|Darabszám|Összesen|A hibaüzenetet kapó hívások teljes száma (HTTP-válaszkód: 4xx vagy 5xx).|ApiName, OperationName, Region|
|TotalTokenCalls|Yes|Jogkivonat-hívások összesen|Darabszám|Összesen|Jogkivonat-hívások teljes száma.|ApiName, OperationName, Region|
|TotalTransactions (Összes tranzakció)|Yes|Összes tranzakció|Darabszám|Összesen|Tranzakciók teljes száma.|Nincsenek dimenziók|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|Hitelesítési API-kérések|Darabszám|Darabszám|A hitelesítési végpontra Communication Services kérelmek száma.|Operation, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Csevegési API-kérések|Darabszám|Darabszám|A csevegési végpontra Communication Services kérelmek száma.|Operation, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|SMS API kérések|Darabszám|Darabszám|Az SMS-végpontra Communication Services kérelmek száma.|Operation, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtja|Yes|Lemez olvasási bájtja|Bájt|Összesen|Lemezről beolvasott bájtok száma a monitorozási időszakban|RoleInstanceId, RoleId|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Lemez olvasási IOPS-érték|RoleInstanceId, RoleId|
|Lemezírási bájtok|Yes|Lemezírási bájtok|Bájt|Összesen|A figyelési időszak alatt lemezre írt bájtok|RoleInstanceId, RoleId|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|LemezírásI IOPS|RoleInstanceId, RoleId|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ök) által jelenleg használt lefoglalt számítási egységek százalékos aránya|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtja|Yes|Lemez olvasási bájtja|Bájt|Összesen|Lemezről beolvasott bájtok száma a figyelési időszakban|RoleInstanceId, RoleId|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Lemez olvasási IOPS-érték|RoleInstanceId, RoleId|
|Lemezírási bájtok|Yes|Lemezírási bájtok|Bájt|Összesen|Monitorozási időszak alatt lemezre írt bájtok|RoleInstanceId, RoleId|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|LemezírásI IOPS|RoleInstanceId, RoleId|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ök) által jelenleg használt lefoglalt számítási egységek százalékos aránya|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Összetett lemez olvasási bájt/s|No|Lemez olvasási bájt/s (előzetes verzió)|Bájt|Átlag|Vegye figyelembe, hogy a monitorozási időszakban a lemezről beolvasott bájtok száma (bájt/s) ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez olvasási műveletei másodpercenként|No|Lemez olvasási műveletei másodpercenként (előzetes verzió)|Bájt|Átlag|Vegye figyelembe, hogy a lemezen a monitorozási időszakban végrehajtott olvasási I/I/S-k száma. Vegye figyelembe, hogy ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez írása (bájt/s)|No|Lemezírási bájt/s (előzetes verzió)|Bájt|Átlag|Vegye figyelembe, hogy ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat a lemezre írt bájt/mp||
|Összetett lemez írási műveletei másodpercenként|No|Lemezírási műveletek másodpercenként (előzetes verzió)|Bájt|Átlag|Vegye figyelembe, hogy a lemezen a monitorozási időszakban végrehajtott írási I/I/-k száma. Vegye figyelembe, hogy ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma. Csak B sorozatú, adatlökhető virtuális gépeken érhető el|Nincsenek dimenziók|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|Az adatlökökben elérhető kreditek teljes száma. Csak B sorozatú, adatlökhető virtuális gépeken érhető el|Nincsenek dimenziók|
|Adatlemez felhasznált sávszélességének százalékos aránya|Yes|Adatlemez felhasznált sávszélességének százalékos aránya|Százalék|Átlag|Az adatlemez percenként felhasznált sávszélességének százalékos aránya|Lun|
|Adatlemez IOPS-fogyasztásának százalékos aránya|Yes|Adatlemez IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|Az adatlemez percenként felhasznált I/O-nak százalékos aránya|Lun|
|Adatlemez maximális adatlöredék-sávszélessége|Yes|Adatlemez maximális adatlöredék-sávszélessége|Darabszám|Átlag|A másodpercenkénti maximális átviteli sebesség adatlemeze teljesítmény-átvitel esetén|Lun|
|Adatlemez maximális burst IOPS-érték|Yes|Adatlemez maximális burst IOPS-érték|Darabszám|Átlag|A maximális IOPS-adatlemez a teljesítménylokozással|Lun|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várakozási sorának mélysége (vagy várólista hossza)|Lun|
|Adatlemez másodpercenkénti olvasási bájtja|Yes|Adatlemez másodpercenkénti olvasási bájtja|BájtokPerSecond|Átlag|Egyetlen lemezről a figyelési időszakban beolvasott bájt/másodperc|Lun|
|Adatlemez olvasási műveletei másodpercenként|Yes|Adatlemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS olvasása egyetlen lemezről a monitorozási időszakban|Lun|
|Adatlemez cél sávszélessége|Yes|Adatlemez cél sávszélessége|Darabszám|Átlag|Az alapérték bájt/másodperc átviteli sebesség Adatlemeze teljesítmény-átvitel nélkül is el lehet érni|Lun|
|Adatlemez cél IOPS-adatai|Yes|Adatlemez cél IOPS-adatai|Darabszám|Átlag|Az alap IOPS-adatlemez adatlokáció nélkül is el lehet érni|Lun|
|Adatlemez felhasznált adatlökének (burst BPS) kreditek százalékos aránya|Yes|Adatlemez felhasznált adatlökének (burst BPS) kreditek százalékos aránya|Százalék|Átlag|Az adatlemez adatlökének adatlökének eddig felhasznált sávszélesség-kreditek százalékos aránya|Lun|
|Adatlemez felhasznált adatlökök io-kreditek százalékos aránya|Yes|Adatlemez felhasznált adatlökök-i/O-kreditek százalékos aránya|Százalék|Átlag|Az adatlemezek adatlökés-adatátviteli kreditek eddig felhasznált százalékos aránya|Lun|
|Adatlemez írása (bájt/s)|Yes|Adatlemez írása (bájt/s)|BájtokPerSecond|Átlag|Egyetlen lemezre írt bájt/mp a figyelési időszakban|Lun|
|Adatlemez írási műveletei másodpercenként|Yes|Adatlemez írási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS írása egyetlen lemezről monitorozási időszakban|Lun|
|Lemez olvasási bájtja|Yes|Lemez olvasási bájtja|Bájt|Összesen|Lemezről beolvasott bájtok száma a monitorozási időszakban|Nincsenek dimenziók|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Lemez olvasási IOPS-érték|Nincsenek dimenziók|
|Lemezírási bájtok|Yes|Lemezírási bájtok|Bájt|Összesen|A figyelési időszak alatt lemezre írt bájtok|Nincsenek dimenziók|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|LemezírásI IOPS|Nincsenek dimenziók|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irányban lévő aktuális folyamatok száma (a virtuális gépre irányuló forgalom)|Nincsenek dimenziók|
|Bejövő folyamatok maximális létrehozási sebessége|Yes|Bejövő folyamatok maximális létrehozási sebessége|CountPerSecond (Másodpercek száma)|Átlag|A bejövő folyamatok maximális létrehozási sebessége (a virtuális gépre irányuló forgalom)|Nincsenek dimenziók|
|Bejövő hálózat|Yes|Hálózat számlázhatóban (elavult)|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom) (elavult)|Nincsenek dimenziók|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek dimenziók|
|Kimenő hálózat|Yes|Kiszámlázható hálózat (elavult)|Bájt|Összesen|A virtuális gépek (kimenő forgalom) által az összes hálózati adapteren kiszámlázható bájtok száma (elavult)|Nincsenek dimenziók|
|Teljes hálózati összeg|Yes|Teljes hálózati összeg|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren kimenő bájtok száma (kimenő forgalom)|Nincsenek dimenziók|
|Operációsrendszer-lemez felhasznált sávszélességének százalékos aránya|Yes|Operációsrendszer-lemez felhasznált sávszélességének százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|Lun|
|Operációsrendszer-lemez IOPS-fogyasztásának százalékos aránya|Yes|Operációsrendszer-lemez IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|Az operációsrendszer-lemez percenként felhasznált I/O-nak százalékos aránya|Lun|
|Operációsrendszer-lemez maximális adatlok-sávszélessége|Yes|Operációsrendszer-lemez maximális adatlok-sávszélessége|Darabszám|Átlag|Az operációsrendszer-lemez maximális bájt/másodperces átviteli sebességének elérése teljesítmény-átvitel esetén|Lun|
|Operációsrendszer-lemez maximális burst IOPS-érték|Yes|Operációsrendszer-lemez maximális burst IOPS-érték|Darabszám|Átlag|A maximális IOPS-os operációsrendszer-lemez a teljesítménylokozással|Lun|
|OS-lemez várakozási sorának mélysége|Yes|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|Operációsrendszer-lemez üzenetsorának mélysége (vagy az üzenetsor hossza)|Nincsenek dimenziók|
|Operációsrendszer-lemez másodpercenkénti olvasási bájtja|Yes|Operációsrendszer-lemez másodpercenkénti olvasási száma (bájt/s)|BájtperSecond (bájt/másodperc)|Átlag|Az operációsrendszer-lemez figyelési időszakában egyetlen lemezről beolvasott bájt/másodperc|Nincsenek dimenziók|
|Operációsrendszer-lemez olvasási műveletei másodpercenként|Yes|Operációsrendszer-lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS olvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek dimenziók|
|Operációsrendszer-lemez cél sávszélessége|Yes|Operációsrendszer-lemez cél sávszélessége|Darabszám|Átlag|Az alapérték bájt/másodperc átviteli sebesség az operációsrendszer-lemez teljesítmény-átvitele nélkül is|Lun|
|Operációsrendszer-lemez cél IOPS-érték|Yes|Operációsrendszer-lemez cél IOPS-érték|Darabszám|Átlag|Az alap IOPS-os operációsrendszer-lemez adatlokozás nélkül is el lehet érni|Lun|
|Az operációsrendszer-lemez által használt burst BPS kreditek százalékos aránya|Yes|Az operációsrendszer-lemez által használt burst BPS kreditek százalékos aránya|Százalék|Átlag|Az operációsrendszer-lemez adatlökének eddig felhasznált sávszélesség-igényének százalékos aránya|Lun|
|Operációsrendszer-lemez által felhasznált adatlökök százalékos I/O-kreditek|Yes|Operációsrendszer-lemez által felhasznált adatlökök százalékos I/O-kreditek|Százalék|Átlag|Az operációsrendszer-lemez adatlökének eddig felhasznált I/O-kreditek százalékos aránya|Lun|
|Operációsrendszer-lemez írási bájtja (bájt/s)|Yes|Operációsrendszer-lemez írási bájtja (bájt/s)|BájtokPerSecond|Átlag|Operációsrendszer-lemez figyelési időszaka alatt egyetlen lemezre írt bájt/mp|Nincsenek dimenziók|
|Operációsrendszer-lemez írási műveletei másodpercenként|Yes|Operációsrendszer-lemez írási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek dimenziók|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok az aktuális folyamatok száma a kimenő irányban (a virtuális gépről kifelé irányuló forgalom)|Nincsenek dimenziók|
|Kimenő folyamatok maximális létrehozási sebessége|Yes|Kimenő folyamatok maximális létrehozási sebessége|CountPerSecond (Másodpercek száma)|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé irányuló forgalom)|Nincsenek dimenziók|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ök) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek dimenziók|
|Prémium szintű adatlemez gyorsítótárának olvasási találata|Yes|Prémium szintű adatlemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási találata|Lun|
|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Yes|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Lun|
|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek dimenziók|
|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Yes|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Százalék|Átlag|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazott sávszélességének kihasznált százaléka|Yes|Virtuális gép gyorsítótárazott sávszélességének kihasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez-sávszélesség százalékos aránya|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazott IOPS-fogyasztásának százalékos aránya|Yes|Virtuális gép gyorsítótárazott IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS-ének százalékos aránya|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazás nélküli sávszélességének kihasznált százaléka|Yes|Virtuális gép gyorsítótárazás nélküli sávszélességének kihasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazás nélküli lemez-sávszélesség százalékos aránya|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazás nélküli IOPS-fogyasztásának százalékos aránya|Yes|Virtuális gép gyorsítótárazás nélküli IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazás nélküli lemez IOPS-érték százalékos aránya|Nincsenek dimenziók|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma. Csak B sorozatú, adatlökhető virtuális gépeken érhető el|Nincsenek dimenziók|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|Az adatlökökben elérhető kreditek teljes száma. Csak B sorozatú, adatlökhető virtuális gépeken érhető el|Nincsenek dimenziók|
|Adatlemez felhasznált sávszélességének százalékos aránya|Yes|Adatlemez felhasznált sávszélességének százalékos aránya|Százalék|Átlag|Az adatlemez percenként felhasznált sávszélességének százalékos aránya|LUN, VMName|
|Adatlemez IOPS-fogyasztásának százalékos aránya|Yes|Adatlemez IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|Az adatlemez percenként felhasznált I/O-nak százalékos aránya|LUN, VMName|
|Adatlemez maximális adatlöredék-sávszélessége|Yes|Adatlemez maximális adatlöredék-sávszélessége|Darabszám|Átlag|A másodpercenkénti maximális átviteli sebesség adatlemeze teljesítmény-átvitel esetén|LUN, VMName|
|Adatlemez maximális burst IOPS-érték|Yes|Adatlemez maximális burst IOPS-érték|Darabszám|Átlag|A maximális IOPS-adatlemez a teljesítménylokozással|LUN, VMName|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várakozási sorának mélysége (vagy várólista hossza)|LUN, VMName|
|Adatlemez másodpercenkénti olvasási bájtja|Yes|Adatlemez másodpercenkénti olvasási bájtja|BájtperSecond (bájt/másodperc)|Átlag|Egyetlen lemezről a figyelési időszakban beolvasott bájt/másodperc|LUN, VMName|
|Adatlemez olvasási műveletei másodpercenként|Yes|Adatlemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS olvasása egyetlen lemezről a monitorozási időszakban|LUN, VMName|
|Adatlemez cél sávszélessége|Yes|Adatlemez cél sávszélessége|Darabszám|Átlag|Az alapérték bájt/másodperc átviteli sebesség Adatlemeze teljesítmény-átvitel nélkül is el lehet érni|LUN, VMName|
|Adatlemez cél IOPS-adatai|Yes|Adatlemez cél IOPS-adatai|Darabszám|Átlag|Az alap IOPS-adatlemez adatlokáció nélkül is el lehet érni|LUN, VMName|
|Adatlemez felhasznált adatlökének (burst BPS) kreditek százalékos aránya|Yes|Adatlemez felhasznált adatlökének (burst BPS) kreditek százalékos aránya|Százalék|Átlag|Az adatlemez adatlökének adatlökének eddig felhasznált sávszélesség-kreditek százalékos aránya|LUN, VMName|
|Adatlemez felhasznált adatlökök-i/O-kreditek százalékos aránya|Yes|Adatlemez felhasznált adatlökök-i/O-kreditek százalékos aránya|Százalék|Átlag|Az adatlemezek adatlökés-adatátviteli kreditek eddig felhasznált százalékos aránya|LUN, VMName|
|Adatlemez írása (bájt/s)|Yes|Adatlemez írása (bájt/s)|BájtokPerSecond|Átlag|Egyetlen lemezre írt bájt/mp a figyelési időszak alatt|LUN, VMName|
|Adatlemez írási műveletei másodpercenként|Yes|Adatlemez írási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS írása egyetlen lemezről monitorozási időszakban|LUN, VMName|
|Lemez olvasási bájtja|Yes|Lemez olvasási bájtja|Bájt|Összesen|Lemezről beolvasott bájtok száma a monitorozási időszakban|VMName|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Lemez olvasási IOPS-érték|VMName|
|Lemezírási bájtok|Yes|Lemezírási bájtok|Bájt|Összesen|A figyelési időszak alatt lemezre írt bájtok|VMName|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|LemezírásI IOPS|VMName|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irányban lévő aktuális folyamatok száma (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási sebessége|Yes|Bejövő folyamatok maximális létrehozási sebessége|CountPerSecond (Másodpercek száma)|Átlag|A bejövő folyamatok maximális létrehozási sebessége (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő hálózat|Yes|Hálózat számlázhatóban (elavult)|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom) (elavult)|VMName|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat|Yes|Kiszámlázható hálózati számlázható (elavult)|Bájt|Összesen|A virtuális gépek (kimenő forgalom) által az összes hálózati adapteren kiszámlázható bájtok száma (elavult)|VMName|
|Teljes hálózati összeg|Yes|Teljes hálózati összeg|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren kimenő bájtok száma (kimenő forgalom)|VMName|
|Operációsrendszer-lemez felhasznált sávszélességének százalékos aránya|Yes|Operációsrendszer-lemez felhasznált sávszélességének százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|LUN, VMName|
|Operációsrendszer-lemez IOPS-fogyasztásának százalékos aránya|Yes|Operációsrendszer-lemez IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|Operációsrendszer-lemez percenként felhasznált I/O-nak százalékos aránya|LUN, VMName|
|Operációsrendszer-lemez maximális burst sávszélessége|Yes|Operációsrendszer-lemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti maximális átviteli sebesség az operációsrendszer-lemezen teljesítmény-átvitel esetén|LUN, VMName|
|Operációsrendszer-lemez maximális burst IOPS-érték|Yes|Operációsrendszer-lemez maximális burst IOPS-érték|Darabszám|Átlag|A maximális IOPS-os operációsrendszer-lemez a teljesítménylokozással|LUN, VMName|
|OS-lemez várakozási sorának mélysége|Yes|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|Operációsrendszer-lemez üzenetsorának mélysége (vagy az üzenetsor hossza)|VMName|
|Operációsrendszer-lemez másodpercenkénti olvasási bájtja|Yes|Operációsrendszer-lemez másodpercenkénti olvasási száma (bájt/s)|BájtperSecond (bájt/másodperc)|Átlag|Az operációsrendszer-lemez figyelési időszakában egyetlen lemezről beolvasott bájt/másodperc|VMName|
|Operációsrendszer-lemez olvasási műveletei másodpercenként|Yes|Operációsrendszer-lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS olvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|Operációsrendszer-lemez cél sávszélessége|Yes|Operációsrendszer-lemez cél sávszélessége|Darabszám|Átlag|Az alapérték bájt/másodperc átviteli sebesség az operációs rendszer lemezén teljesítmény-átvitel nélkül is el lehet érni|LUN, VMName|
|Operációsrendszer-lemez cél IOPS-érték|Yes|Operációsrendszer-lemez cél IOPS-érték|Darabszám|Átlag|Az alap IOPS operációsrendszer-lemez adatlokozás nélkül is el lehet érni|LUN, VMName|
|OS Disk Used Burst BPS Credits Percentage|Yes|OS Disk Used Burst BPS Credits Percentage|Százalék|Átlag|Az operációsrendszer-lemez adatlökének eddig felhasznált sávszélesség-kreditek százalékos aránya|LUN, VMName|
|Operációsrendszer-lemez által felhasznált adatlökök százalékos I/O-kreditek|Yes|Az operációsrendszer-lemez által felhasznált adatlökök io-kreditek százalékos aránya|Százalék|Átlag|Az operációsrendszer-lemez adatlökének eddig felhasznált I/O-kreditek százalékos aránya|LUN, VMName|
|Operációsrendszer-lemez írása (bájt/s)|Yes|Operációsrendszer-lemez írási bájtja (bájt/s)|BájtokPerSecond|Átlag|Operációsrendszer-lemez figyelési időszaka alatt egyetlen lemezre írt bájt/mp|VMName|
|Operációsrendszer-lemez írási műveletei másodpercenként|Yes|Operációsrendszer-lemez írási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok az aktuális folyamatok száma a kimenő irányban (a virtuális gépről kifelé irányuló forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási sebessége|Yes|Kimenő folyamatok maximális létrehozási sebessége|CountPerSecond (Másodpercek száma)|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kimenő forgalom)|VMName|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ök) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Prémium szintű adatlemez gyorsítótárának olvasási találata|Yes|Prémium szintű adatlemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási találata|LUN, VMName|
|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Yes|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|LUN, VMName|
|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Yes|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Százalék|Átlag|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|VMName|
|Virtuális gép gyorsítótárazott sávszélességének kihasznált százaléka|Yes|Virtuális gép gyorsítótárazott sávszélességének kihasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez-sávszélesség százalékos aránya|VMName|
|Virtuális gép gyorsítótárazott IOPS-fogyasztásának százalékos aránya|Yes|Virtuális gép gyorsítótárazott IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS-ének százalékos aránya|VMName|
|Virtuális gép gyorsítótárazás nélküli sávszélességének kihasznált százaléka|Yes|Virtuális gép gyorsítótárazás nélküli sávszélességének kihasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazás nélküli lemez-sávszélesség százalékos aránya|VMName|
|Virtuális gép gyorsítótárazás nélküli IOPS-fogyasztásának százalékos aránya|Yes|Virtuális gép gyorsítótárazás nélküli IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált, gyorsítótárazás nélküli lemez IOPS-ének százalékos aránya|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma. Csak B sorozatú, adatlökhető virtuális gépeken érhető el|Nincsenek dimenziók|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|Az adatlökökben elérhető kreditek teljes száma. Csak B sorozatú, adatlökhető virtuális gépeken érhető el|Nincsenek dimenziók|
|Adatlemez felhasznált sávszélességének százalékos aránya|Yes|Adatlemez felhasznált sávszélességének százalékos aránya|Százalék|Átlag|Az adatlemez percenként felhasznált sávszélességének százalékos aránya|Lun|
|Adatlemez IOPS-fogyasztásának százalékos aránya|Yes|Adatlemez IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|Az adatlemez percenként felhasznált I/O-nak százalékos aránya|Lun|
|Adatlemez maximális adatlok-sávszélessége|Yes|Adatlemez maximális adatlok-sávszélessége|Darabszám|Átlag|Az adatlemez által a másodpercenkénti maximális átviteli sebesség adatlökökre vonatkozó teljesítmény-átvitele|Lun|
|Adatlemez maximális adatlok-IOPS-érték|Yes|Adatlemez maximális adatlok-IOPS-érték|Darabszám|Átlag|A maximális IOPS-adatlemez a teljesítménylokozással|Lun|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez üzenetsorának mélysége (vagy az üzenetsor hossza)|Lun|
|Adatlemez másodpercenkénti olvasási bájtja|Yes|Adatlemez másodpercenkénti olvasási bájtja|BájtokPerSecond|Átlag|Egyetlen lemezről beolvasott bájt/másodperc a figyelési időszakban|Lun|
|Adatlemez olvasási műveletei másodpercenként|Yes|Adatlemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS olvasása egyetlen lemezről a monitorozási időszak alatt|Lun|
|Adatlemez cél sávszélessége|Yes|Adatlemez cél sávszélessége|Darabszám|Átlag|Az adatlemez teljesítmény-átvitele nélkül is el lehet érni az alapérték bájt/másodpercben|Lun|
|Adatlemez cél IOPS-adatai|Yes|Adatlemez cél IOPS-adatai|Darabszám|Átlag|Az alapkonfiguráció IOPS-adatlemeze adatlokáció nélkül is el lehet érni|Lun|
|Felhasznált adatlemez – burst BPS kreditek százalékos aránya|Yes|Felhasznált adatlemez – burst BPS kreditek százalékos aránya|Százalék|Átlag|Az adatlemez adatlökének adatlökének eddig felhasznált sávszélesség-kreditek százalékos aránya|Lun|
|Adatlemez felhasznált adatlökök io-kreditek százalékos aránya|Yes|Adatlemez felhasznált adatlökök io-kreditek százalékos aránya|Százalék|Átlag|Az adatlemez-adatlökök eddig felhasznált I/O-kreditek százalékos aránya|Lun|
|Adatlemez írása (bájt/s)|Yes|Adatlemez írási bájtja (bájt/s)|BájtperSecond (bájt/másodperc)|Átlag|Egyetlen lemezre írt bájt/mp a figyelési időszakban|Lun|
|Adatlemez írási műveletei másodpercenként|Yes|Adatlemez írási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS írása egyetlen lemezről a monitorozási időszakban|Lun|
|Lemez olvasási bájtja|Yes|Lemez olvasási bájtja|Bájt|Összesen|Lemezről beolvasott bájtok száma a monitorozási időszakban|Nincsenek dimenziók|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Lemez olvasási IOPS-érték|Nincsenek dimenziók|
|Lemezírási bájtok|Yes|Lemezírási bájtok|Bájt|Összesen|A monitorozási időszakban lemezre írt bájtok|Nincsenek dimenziók|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|LemezírásI IOPS|Nincsenek dimenziók|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok az aktuális folyamatok száma a bejövő irányban (a virtuális gépre irányuló forgalom)|Nincsenek dimenziók|
|Bejövő folyamatok maximális létrehozási sebessége|Yes|Bejövő folyamatok maximális létrehozási sebessége|CountPerSecond (Másodpercek száma)|Átlag|A bejövő folyamatok maximális létrehozási sebessége (a virtuális gépre irányuló forgalom)|Nincsenek dimenziók|
|Bejövő hálózat|Yes|Hálózat számlázhatóként (elavult)|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom) (elavult)|Nincsenek dimenziók|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép(ök) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek dimenziók|
|Kimenő hálózat|Yes|Kiszámlázható hálózati számlázható (elavult)|Bájt|Összesen|A virtuális gépek (kimenő forgalom) által az összes hálózati adapteren kiszámlázható bájtok száma (elavult)|Nincsenek dimenziók|
|Teljes hálózati összeg|Yes|Teljes hálózati összeg|Bájt|Összesen|A virtuális gépek (kimenő forgalom) által az összes hálózati adapteren kimenő bájtok száma|Nincsenek dimenziók|
|Operációsrendszer-lemez felhasznált sávszélességének százalékos aránya|Yes|Operációsrendszer-lemez felhasznált sávszélességének százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|Lun|
|Operációsrendszer-lemez IOPS-fogyasztásának százalékos aránya|Yes|Operációsrendszer-lemez IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|Az operációsrendszer-lemez percenként felhasznált I/O-nak százalékos aránya|Lun|
|Operációsrendszer-lemez maximális burst sávszélessége|Yes|Operációsrendszer-lemez maximális adatlöredék-sávszélessége|Darabszám|Átlag|A másodpercenkénti maximális átviteli sebesség az operációsrendszer-lemezen teljesítmény-átvitel esetén|Lun|
|Operációsrendszer-lemez maximális burst IOPS-érték|Yes|Operációsrendszer-lemez maximális burst IOPS-érték|Darabszám|Átlag|Az IOPS-os operációsrendszer-lemez maximálisan el lehet érni a teljesítménylokozással|Lun|
|OS-lemez várakozási sorának mélysége|Yes|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|Operációsrendszer-lemez üzenetsorának mélysége (vagy várólista hossza)|Nincsenek dimenziók|
|Operációsrendszer-lemez másodpercenkénti olvasási bájtja|Yes|Operációsrendszer-lemez másodpercenkénti olvasási bájtja|BájtokPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszakában egyetlen lemezről beolvasott bájt/másodperc|Nincsenek dimenziók|
|Operációsrendszer-lemez olvasási műveletei másodpercenként|Yes|Operációsrendszer-lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|IOPS olvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek dimenziók|
|Operációsrendszer-lemez cél sávszélessége|Yes|Operációsrendszer-lemez cél sávszélessége|Darabszám|Átlag|Az alapérték bájt/másodperc átviteli sebesség az operációs rendszer lemezén teljesítmény-átvitel nélkül is el lehet érni|Lun|
|Operációsrendszer-lemez cél IOPS-érték|Yes|Operációsrendszer-lemez cél IOPS-érték|Darabszám|Átlag|Az alap IOPS operációsrendszer-lemez adatlokozás nélkül is képes elérni|Lun|
|OS Disk Used Burst BPS Credits Percentage|Yes|OS Disk Used Burst BPS Credits Percentage|Százalék|Átlag|Az operációsrendszer-lemez adatlökének eddig felhasznált sávszélesség-kreditek százalékos aránya|Lun|
|Az operációsrendszer-lemez által felhasznált adatlökök io-kreditek százalékos aránya|Yes|Az operációsrendszer-lemez által felhasznált adatlökök io-kreditek százalékos aránya|Százalék|Átlag|Az operációsrendszer-lemez adatlökének eddig felhasznált I/O-kreditek százalékos aránya|Lun|
|Operációsrendszer-lemez írási bájtja (bájt/s)|Yes|Operációsrendszer-lemez írási bájt/s|BájtperSecond (bájt/másodperc)|Átlag|Operációsrendszer-lemez figyelési időszaka alatt egyetlen lemezre írt bájt/mp|Nincsenek dimenziók|
|Operációsrendszer-lemez írási műveletei másodpercenként|Yes|Operációsrendszer-lemez írási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Írási IOPS egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek dimenziók|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok az aktuális folyamatok száma a kimenő irányban (a virtuális gépről kifelé irányuló forgalom)|Nincsenek dimenziók|
|Kimenő folyamatok maximális létrehozási sebessége|Yes|Kimenő folyamatok maximális létrehozási sebessége|CountPerSecond (Másodpercek száma)|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kimenő forgalom)|Nincsenek dimenziók|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ök) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek dimenziók|
|Prémium szintű adatlemez gyorsítótárának olvasási találata|Yes|Prémium szintű adatlemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási találata|Lun|
|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Yes|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási tévesztése|Lun|
|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek dimenziók|
|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Yes|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Százalék|Átlag|Prémium szintű operációsrendszer-lemez gyorsítótárának olvasási tévesztése|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazott sávszélességének kihasznált százaléka|Yes|Virtuális gép gyorsítótárazott sávszélességének kihasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez-sávszélesség százalékos aránya|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazott IOPS-fogyasztásának százalékos aránya|Yes|Virtuális gép gyorsítótárazott IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS-ének százalékos aránya|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazás nélküli sávszélességének kihasznált százaléka|Yes|Virtuális gép gyorsítótárazás nélküli sávszélességének kihasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazás nélküli lemez-sávszélesség százalékos aránya|Nincsenek dimenziók|
|Virtuális gép gyorsítótárazás nélküli IOPS-fogyasztásának százalékos aránya|Yes|Virtuális gép gyorsítótárazás nélküli IOPS-fogyasztásának százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazás nélküli lemez IOPS-ének százalékos aránya|Nincsenek dimenziók|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CpuUsage (Processzorhasználat)|Yes|CPU-használat|Darabszám|Átlag|Processzorhasználat az összes magon millicore-ban.|containerName (tároló neve)|
|MemoryUsage (Memória)Usage (Memória)|Yes|Memóriahasználat|Bájt|Átlag|Teljes memóriahasználat bájtban.|containerName (tároló neve)|
|NetworkBytesReceivedPerSecond|Yes|Fogadott hálózati bájtok száma másodpercenként|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|Nincsenek dimenziók|
|NetworkBytesTransmittedPerSecond|Yes|Továbbított hálózati bájtok másodpercenként|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|Nincsenek dimenziók|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime (ÜgynökkészletCPUTime)|Yes|AgentPool CPU Time|Másodperc|Összesen|AgentPool CPU Time in seconds (AgentPool CPU-idő másodpercben)|Nincsenek dimenziók|
|RunDuration (Futtatás futtatása)|Yes|Futtatás időtartama|Ezredmásodperc|Összesen|Futtatás időtartama ezredmásodpercben|Nincsenek dimenziók|
|SuccessfulPullCount (SikerespullCount)|Yes|Sikeres le pull-szám|Darabszám|Átlag|A sikeres rendszerkép-lekért képek száma|Nincsenek dimenziók|
|SuccessfulPushCount|Yes|Sikeres leküldések száma|Darabszám|Átlag|A sikeres rendszerkép-leküldések száma|Nincsenek dimenziók|
|TotalPullCount|Yes|Összes le pull-szám|Darabszám|Átlag|A rendszerkép-lekért képek teljes száma|Nincsenek dimenziók|
|TotalPushCount|Yes|Leküldések teljes száma|Darabszám|Átlag|Rendszerkép-leküldések teljes száma|Nincsenek dimenziók|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Inflight Kérések|Darabszám|Átlag|Az apiserver-n jelenleg használt, kérési alosztályonként aktuálisan használt kérések maximális száma az utolsó másodpercben|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|No|Fürt állapota|Darabszám|Átlag|Meghatározza, hogy az automatikus fürtméretozó műveletet fog-e eltenni a fürtön||
|cluster_autoscaler_scale_down_in_cooldown|No|Leskálásos leskála-hűtés|Darabszám|Átlag|Megállapítja, hogy a leskálás a legördülő menüben van-e – Ebben az időkeretben egyetlen csomópontot sem távolít el a rendszer||
|cluster_autoscaler_unneeded_nodes_count|No|Szükségtelen csomópontok|Darabszám|Átlag|A fürt auotscaler azokat a csomópontokat törlésre jelöltként jelöli meg, és végül törlődnek||
|cluster_autoscaler_unschedulable_pods_count|No|Nem ütemezhető podok|Darabszám|Átlag|A fürtön jelenleg nem ütemezhető podok száma||
|kube_node_status_allocatable_cpu_cores|No|A rendelkezésre álló processzormagok teljes száma egy felügyelt fürtben|Darabszám|Átlag|A rendelkezésre álló processzormagok teljes száma egy felügyelt fürtben||
|kube_node_status_allocatable_memory_bytes|No|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége||
|kube_node_status_condition|No|Különböző csomópont-feltételek állapotai|Darabszám|Átlag|Különböző csomópont-feltételek állapotai|feltétel, állapot, állapot2, csomópont|
|kube_pod_status_phase|No|Podok száma fázisok szerint|Darabszám|Átlag|Podok száma fázisok szerint|fázis, névtér, pod|
|kube_pod_status_ready|No|Kész állapotban van a podok száma|Darabszám|Átlag|Kész állapotban van a podok száma|névtér, pod, feltétel|
|node_cpu_usage_millicores|Yes|Processzorhasználat ezredmásodpertékben|MilliCores (MilliCores)|Átlag|A cpu-kihasználtság összesített mérése millicore-ban a fürtben|node, nodepool|
|node_cpu_usage_percentage|Yes|Processzorhasználat százalékos aránya|Százalék|Átlag|Összesített átlagos cpu-kihasználtság, százalékos arányban kifejezve a fürtben|node, nodepool|
|node_disk_usage_bytes|Yes|Felhasznált lemez (bájt)|Bájt|Átlag|Eszköz által felhasznált lemezterület bájtban|csomópont, csomópontkészlet, eszköz|
|node_disk_usage_percentage|Yes|Felhasznált lemez százalékos aránya|Százalék|Átlag|Az eszköz által felhasznált lemezterület százalékban|csomópont, csomópontkészlet, eszköz|
|node_memory_rss_bytes|Yes|Memória RSS bájtja|Bájt|Átlag|Tároló RSS-memóriája bájtban|node, nodepool|
|node_memory_rss_percentage|Yes|Memória RSS-százaléka|Százalék|Átlag|A tároló RSS-memóriája (százalékban)|node, nodepool|
|node_memory_working_set_bytes|Yes|Memória-munkakészlet bájtja|Bájt|Átlag|Tároló-munkakészlet bájtban használt memóriája|node, nodepool|
|node_memory_working_set_percentage|Yes|Memória-munkakészlet százalékos aránya|Százalék|Átlag|A tároló munkakészletének memóriája százalékban van használva|node, nodepool|
|node_network_in_bytes|Yes|Hálózat bájtban|Bájt|Átlag|Fogadott hálózat (bájt)|node, nodepool|
|node_network_out_bytes|Yes|Hálózatonkénti bájt|Bájt|Átlag|Hálózati átvitel (bájt)|node, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Sikertelen kérelmek|Darabszám|Összesen|Lekérte az egyéni erőforrás-szolgáltatókhoz elérhető naplókat|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Sikeres kérelmek|Darabszám|Összesen|Az egyéni szolgáltató sikeres kérései|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre álló kapacitás|Yes|Rendelkezésre álló kapacitás|Bájt|Átlag|A jelentéskészítési időszakban rendelkezésre álló kapacitás bájtban.|Nincsenek dimenziók|
|BytesUploadedToCloud|Yes|Feltöltött felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Eszközről a jelentéskészítési időszakban az Azure-ba feltöltött bájtok teljes száma.|Nincsenek dimenziók|
|BytesUploadedToCloudPerShare|Yes|Feltöltött felhőbeli bájtok (megosztás)|Bájt|Átlag|Az Azure-ba a jelentéskészítési időszak során egy megosztásból feltöltött bájtok teljes száma.|Megosztás|
|CloudReadThroughput (CloudReadThroughput)|Yes|Felhőalapú letöltési átviteli sebesség|Bájt/másodperc|Átlag|A felhő a jelentéskészítési időszak alatt letölti az átviteli sebességet az Azure-ba.|Nincsenek dimenziók|
|CloudReadThroughputPerShare|Yes|Felhőalapú letöltési átviteli sebesség (megosztás)|BájtokPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség egy megosztásból a jelentéskészítési időszak alatt.|Megosztás|
|CloudUploadThroughput (CloudUploadThroughput)|Yes|Felhőfeltöltés átviteli sebesség|BájtokPerSecond|Átlag|A felhőbe való feltöltés átviteli sebességét az Azure-ba a jelentéskészítési időszak alatt.|Nincsenek dimenziók|
|CloudUploadThroughputPerShare|Yes|Felhőfeltöltés átviteli sebesség (megosztás)|BájtokPerSecond|Átlag|A jelentéskészítési időszak alatt a megosztásból az Azure-ba történő feltöltés átviteli sebességét.|Megosztás|
|HyperVMemoryUtilization|Yes|Peremhálózati számítás – Memóriahasználat|Százalék|Átlag|A ram mennyisége használatban|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Peremhálózati számítás – Százalékos processzorhasználat|Százalék|Átlag|Százalékos processzorhasználat|InstanceName|
|NICReadThroughput (Hálózati adapterolvasási sebesség)|Yes|Olvasási átviteli sebesség (hálózat)|BájtperSecond (bájt/másodperc)|Átlag|Az eszköz hálózati adapterének olvasási átviteli sebességét a jelentéskészítési időszakban az átjáróban található összes kötetre vonatkozóan.|InstanceName|
|NICWriteThroughput|Yes|Írási átviteli sebesség (hálózat)|BájtperSecond (bájt/másodperc)|Átlag|Az eszköz hálózati adapterének írási átviteli sebességét a jelentéskészítési időszakban az átjáróban található összes kötetre vonatkozóan.|InstanceName|
|TotalCapacity (Teljes kapacitás)|Yes|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|Nincsenek dimenziók|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Adateszközök létrehozása|Darabszám|Maximum|Létrehozott adateszközök száma|DataAssetName (DataAssetName)|
|PipelineCount|Yes|Létrehozott folyamatok|Darabszám|Maximum|A létrehozott folyamatok száma|PipelineName (Folyamat neve)|
|ProposalCount (Ajánlat száma)|Yes|Létrehozott javaslatok|Darabszám|Maximum|A létrehozott javaslatok száma|ProposalName (Javaslat neve)|
|ScriptCount (Szkriptszám)|Yes|Létrehozott szkriptek|Darabszám|Maximum|A létrehozott szkriptek száma|ScriptName (Szkriptnév)|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRuns (Sikertelenruns)|Yes|Sikertelen futtatás|Darabszám|Összesen||pipelineName, activityName|
|Sikeresfuttatás|Yes|Sikeres futtatás|Darabszám|Összesen||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|Megszakított tevékenységfuttatás metrika|Darabszám|Összesen||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Yes|Sikertelen tevékenységfuttatás metrika|Darabszám|Összesen||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Yes|Sikeres tevékenységfuttatás metrika|Darabszám|Összesen||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Yes|Teljes gyári méret (GB egység)|Darabszám|Maximum||Nincsenek dimenziók|
|IntegrationRuntimeAvailableMemory|Yes|Rendelkezésre álló memória az integrációstime-hez|Bájt|Átlag||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Yes|Elérhető integrációskörnyezeti csomópontok száma|Darabszám|Átlag||IntegrationRuntimeName (IntegrationRuntimeName)|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Integrációskörnyezeti üzenetsor időtartama|Másodperc|Átlag||IntegrationRuntimeName (IntegrationRuntimeName)|
|IntegrationRuntimeCpuPercentage|Yes|Integrációskörnyezet CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Yes|Integrációskörnyezeti üzenetsor hossza|Darabszám|Átlag||IntegrationRuntimeName (IntegrationRuntimeName)|
|MaxAllowedFactorySizeInGbUnits|Yes|Maximálisan engedélyezett gyári méret (GB egység)|Darabszám|Maximum||Nincsenek dimenziók|
|MaxAllowedResourceCount|Yes|Engedélyezett entitások maximális száma|Darabszám|Maximum||Nincsenek dimenziók|
|PipelineCancelledRuns|Yes|Megszakított folyamatfuttatható metrikák|Darabszám|Összesen||FailureType, Name|
|PipelineElapsedTimeRuns|Yes|Eltelt idő folyamatfuttatásának metrikai|Darabszám|Összesen||RunId, Name|
|PipelineFailedRuns|Yes|Sikertelen folyamatfuttatás metrika|Darabszám|Összesen||FailureType, Name|
|PipelineSucceededRuns|Yes|Sikeres folyamatfuttatás metrika|Darabszám|Összesen||FailureType, Name|
|ResourceCount (Erőforrásszám)|Yes|Összes entitás száma|Darabszám|Maximum||Nincsenek dimenziók|
|SSISIntegrationRuntimeStartCancel|Yes|Megszakított SSIS integrációskörnyezet indítási metrika|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|SSISIntegrationRuntimeStartFailed|Yes|Sikertelen SSIS integrációskörnyezet indítási metrika|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|SSISIntegrationRuntimeStartSucceededed|Yes|Sikeres SSIS integrációskörnyezet indítási metrika|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|SSISIntegrationRuntimeStopStuck|Yes|Elakadt SSIS integration runtime stop metrikák|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|SSISIntegrationRuntimeStopSucceeded|Yes|Sikeres SSIS integration runtime stop metrikák|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|SSISPackageExecutionCancel|Yes|Megszakított SSIS-csomagvégrehajtási metrikák|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|SSISPackageExecutionFailed|Yes|Sikertelen SSIS-csomagvégrehajtási metrikák|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|SSISPackageExecutionSucceeded|Yes|Sikeres SSIS-csomagvégrehajtási metrikák|Darabszám|Összesen||IntegrationRuntimeName (IntegrationRuntimeName)|
|TriggerCancelledRuns|Yes|A megszakított eseményindító futtatja a metrikákat|Darabszám|Összesen||Név, Hibatípus|
|TriggerFailedRuns|Yes|Sikertelen eseményindító futtatja a metrikákat|Darabszám|Összesen||Név, Hibatípus|
|TriggerSucceededRuns|Yes|Sikeres trigger futtatja a metrikákat|Darabszám|Összesen||Név, Hibatípus|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled (FeladatauendedCancelled)|Yes|Megszakított AU-idő|Másodperc|Összesen|A megszakított feladatok teljes AU-ideje.|Nincsenek dimenziók|
|JobAUEndedFailure (FeladatauendedFailure)|Yes|Sikertelen AU-idő|Másodperc|Összesen|A sikertelen feladatok teljes AU-ideje.|Nincsenek dimenziók|
|JobAUEndedSuccess (FeladatauendedSuccess)|Yes|Sikeres AU-idő|Másodperc|Összesen|A sikeres feladatok teljes AU-ideje.|Nincsenek dimenziók|
|JobEndedCancelled (JobEndedCancelled)|Yes|Megszakított feladatok|Darabszám|Összesen|A megszakított feladatok száma.|Nincsenek dimenziók|
|JobEndedFailure (JobEndedFailure)|Yes|Sikertelen feladatok|Darabszám|Összesen|A sikertelen feladatok száma.|Nincsenek dimenziók|
|JobEndedSuccess (JobEndedSuccess)|Yes|Sikeres feladatok|Darabszám|Összesen|A sikeres feladatok száma.|Nincsenek dimenziók|
|JobStage (Feladatsztage)|Yes|Feladatok a fázisban|Darabszám|Összesen|Az egyes fázisok feladatszáma.|Nincsenek dimenziók|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DataRead (Adatolvasás)|Yes|Adatok olvasása|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|Nincsenek dimenziók|
|DataWritten (Adatírás)|Yes|Írt adatok|Bájt|Összesen|A fiókba írt adatok teljes mennyisége.|Nincsenek dimenziók|
|ReadRequests|Yes|Olvasási kérések|Darabszám|Összesen|A fióknak beolvasott adatkérések száma.|Nincsenek dimenziók|
|TotalStorage (Teljes költség)|Yes|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatok teljes mennyisége.|Nincsenek dimenziók|
|WriteRequests|Yes|Írási kérések|Darabszám|Összesen|A fióknak való adatírási kérések száma.|Nincsenek dimenziók|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Yes|Fogadott megosztási sikertelen pillanatképek|Darabszám|Darabszám|Sikertelen megosztásról érkezett pillanatképek száma a fiókban|Nincsenek dimenziók|
|FailedShareSynchronizations|Yes|Sikertelen megosztási pillanatképek elküldve|Darabszám|Darabszám|Sikertelen megosztási pillanatképek száma a fiókban|Nincsenek dimenziók|
|ShareCount (Megosztás száma)|Yes|Elküldött megosztások|Darabszám|Maximum|A fiókban elküldött megosztások száma|ShareName (Megosztás neve)|
|ShareSubscriptionCount|Yes|Fogadott megosztások|Darabszám|Maximum|Fogadott megosztások száma a fiókban|ShareSubscriptionName (ShareSubscriptionName)|
|SucceededShareSubscriptionSynchronizations|Yes|Sikeres megosztásról készült pillanatképek|Darabszám|Darabszám|A fiókban sikeresen fogadott megosztásról készült pillanatképek száma|Nincsenek dimenziók|
|SucceededShareSynchronizations (Sikeres Megosztásszinkronizálások)|Yes|Elküldött sikeres megosztási pillanatképek|Darabszám|Darabszám|A fiókban sikeresen elküldött megosztási pillanatképek száma|Nincsenek dimenziók|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek dimenziók|
|backup_storage_used|Yes|Használt biztonsági mentési tár|Bájt|Átlag|Használt biztonsági mentési tár|Nincsenek dimenziók|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek dimenziók|
|cpu_percent|Yes|Processzorhasználat|Százalék|Átlag|Processzorhasználat|Nincsenek dimenziók|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek dimenziók|
|memory_percent|Yes|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincsenek dimenziók|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Kihálózat aktív kapcsolatok között|Nincsenek dimenziók|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat aktív kapcsolatok között|Nincsenek dimenziók|
|seconds_behind_master|Yes|Replikáció késése másodpercben|Darabszám|Maximum|Replikáció késése másodpercben|Nincsenek dimenziók|
|serverlog_storage_limit|Yes|Kiszolgálónapló tárolási korlátja|Bájt|Maximum|Kiszolgálónapló tárolási korlátja|Nincsenek dimenziók|
|serverlog_storage_percent|Yes|Kiszolgálónapló tárolási százaléka|Százalék|Átlag|Kiszolgálónapló tárolási százaléka|Nincsenek dimenziók|
|serverlog_storage_usage|Yes|Használt kiszolgálónapló-tároló|Bájt|Átlag|Használt kiszolgálónapló-tároló|Nincsenek dimenziók|
|storage_limit|Yes|Tárhelykorlát|Bájt|Maximum|Tárhelykorlát|Nincsenek dimenziók|
|storage_percent|Yes|Tárterület százalékos aránya|Százalék|Átlag|Tárterület százalékos aránya|Nincsenek dimenziók|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek dimenziók|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Megszakított kapcsolatok|Darabszám|Összesen|Megszakított kapcsolatok|Nincsenek dimenziók|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Maximum|Aktív kapcsolatok|Nincsenek dimenziók|
|backup_storage_used|Yes|Használt biztonsági mentési tár|Bájt|Maximum|Használt biztonsági mentési tár|Nincsenek dimenziók|
|cpu_percent|Yes|Gazdagép processzorhasználata|Százalék|Maximum|Gazdagép processzorhasználata|Nincsenek dimenziók|
|io_consumption_percent|Yes|IO-százalék|Százalék|Maximum|IO-százalék|Nincsenek dimenziók|
|memory_percent|Yes|Gazdagép memória százaléka|Százalék|Maximum|Gazdagép memória százaléka|Nincsenek dimenziók|
|network_bytes_egress|Yes|Gazdagép hálózatának ki van va|Bájt|Összesen|Gazdagép hálózati bejövő forgaloma bájtban|Nincsenek dimenziók|
|network_bytes_ingress|Yes|Gazdagéphálózat a következőben:|Bájt|Összesen|Gazdagép hálózati bejövő forgaloma bájtban|Nincsenek dimenziók|
|Lekérdezések|Yes|Lekérdezések|Darabszám|Összesen|Lekérdezések|Nincsenek dimenziók|
|replication_lag|Yes|Replikáció késése másodpercben|Másodperc|Maximum|Replikáció késése másodpercben|Nincsenek dimenziók|
|storage_limit|Yes|Tárhelykorlát|Bájt|Maximum|Tárhelykorlát|Nincsenek dimenziók|
|storage_percent|Yes|Tárterület százalékos aránya|Százalék|Maximum|Tárterület százalékos aránya|Nincsenek dimenziók|
|storage_used|Yes|Felhasznált tárterület|Bájt|Maximum|Felhasznált tárterület|Nincsenek dimenziók|
|total_connections|Yes|Összes kapcsolat|Darabszám|Összesen|Összes kapcsolat|Nincsenek dimenziók|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek dimenziók|
|backup_storage_used|Yes|Használt biztonsági mentési tár|Bájt|Átlag|Használt biztonsági mentési tár|Nincsenek dimenziók|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek dimenziók|
|cpu_percent|Yes|Processzorhasználat|Százalék|Átlag|Processzorhasználat|Nincsenek dimenziók|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek dimenziók|
|memory_percent|Yes|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincsenek dimenziók|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózat aktív kapcsolatok között|Nincsenek dimenziók|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat aktív kapcsolatok között|Nincsenek dimenziók|
|seconds_behind_master|Yes|Replikáció késése másodpercben|Darabszám|Maximum|Replikáció késése másodpercben|Nincsenek dimenziók|
|serverlog_storage_limit|Yes|Kiszolgálónapló tárolási korlátja|Bájt|Maximum|Kiszolgálónapló tárolási korlátja|Nincsenek dimenziók|
|serverlog_storage_percent|Yes|Kiszolgálónapló tárolási százaléka|Százalék|Átlag|Kiszolgálónapló tárolási százaléka|Nincsenek dimenziók|
|serverlog_storage_usage|Yes|Használt kiszolgálónapló-tároló|Bájt|Átlag|Használt kiszolgálónapló-tároló|Nincsenek dimenziók|
|storage_limit|Yes|Tárhelykorlát|Bájt|Maximum|Tárhelykorlát|Nincsenek dimenziók|
|storage_percent|Yes|Tárterület százalékos aránya|Százalék|Átlag|Tárterület százalékos aránya|Nincsenek dimenziók|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek dimenziók|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek dimenziók|
|backup_storage_used|Yes|Használt biztonsági mentési tár|Bájt|Átlag|Használt biztonsági mentési tár|Nincsenek dimenziók|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek dimenziók|
|connections_succeeded|Yes|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek dimenziók|
|cpu_credits_consumed|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|Az adatbázis-kiszolgáló által felhasznált kreditek teljes száma|Nincsenek dimenziók|
|cpu_credits_remaining|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|Az adatlökökre rendelkezésre álló kreditek teljes száma|Nincsenek dimenziók|
|cpu_percent|Yes|Processzorhasználat|Százalék|Átlag|Processzorhasználat|Nincsenek dimenziók|
|disk_queue_depth|Yes|Lemez üzenetsorának mélysége|Darabszám|Átlag|Az adatlemez függőben lévő I/O-műveleteinek száma|Nincsenek dimenziók|
|iops (iops)|Yes|IOPS|Darabszám|Átlag|I/O-műveletek másodpercenként|Nincsenek dimenziók|
|maximum_used_transactionIDs|Yes|Maximális használt tranzakció-számak|Darabszám|Átlag|Maximális használt tranzakció-számak|Nincsenek dimenziók|
|memory_percent|Yes|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincsenek dimenziók|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózat aktív kapcsolatok között|Nincsenek dimenziók|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat aktív kapcsolatok között|Nincsenek dimenziók|
|read_iops|Yes|IOPS olvasása|Darabszám|Átlag|Adatlemez másodpercenkénti I/O-olvasási műveleteinek száma|Nincsenek dimenziók|
|read_throughput|Yes|Olvasási átviteli sebesség (bájt/s)|Darabszám|Átlag|Az adatlemezről másodpercenként beolvasott bájtok száma a monitorozási időszakban|Nincsenek dimenziók|
|storage_free|Yes|Szabad tárterület|Bájt|Átlag|Szabad tárterület|Nincsenek dimenziók|
|storage_percent|Yes|Tárterület százalékos aránya|Százalék|Átlag|Tárterület százalékos aránya|Nincsenek dimenziók|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek dimenziók|
|txlogs_storage_used|Yes|Tranzakciónapló használt tárolása|Bájt|Átlag|Tranzakciónapló használt tárolása|Nincsenek dimenziók|
|write_iops|Yes|IOPS írása|Darabszám|Átlag|Adatlemezek másodpercenkénti írási I/O-műveleteinek száma|Nincsenek dimenziók|
|write_throughput|Yes|Írási átviteli sebesség (bájt/s)|Darabszám|Átlag|Az adatlemezre a figyelési időszak során másodpercenként írt bájtok száma|Nincsenek dimenziók|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|ServerName|
|cpu_percent|Yes|Processzorhasználat|Százalék|Átlag|Processzorhasználat|ServerName|
|iops (iops)|Yes|IOPS|Darabszám|Átlag|I/O-műveletek száma másodpercenként|ServerName|
|memory_percent|Yes|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|ServerName|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Kihálózat aktív kapcsolatok között|ServerName|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat aktív kapcsolatok között|ServerName|
|storage_percent|Yes|Tárterület százalékos aránya|Százalék|Átlag|Tárterület százalékos aránya|ServerName|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek dimenziók|
|backup_storage_used|Yes|Használt biztonsági mentési tár|Bájt|Átlag|Használt biztonsági mentési tár|Nincsenek dimenziók|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek dimenziók|
|cpu_percent|Yes|Processzorhasználat|Százalék|Átlag|Processzorhasználat|Nincsenek dimenziók|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek dimenziók|
|memory_percent|Yes|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincsenek dimenziók|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózat aktív kapcsolatok között|Nincsenek dimenziók|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat aktív kapcsolatok között|Nincsenek dimenziók|
|pg_replica_log_delay_in_bytes|Yes|Replikák legnagyobb késése|Bájt|Maximum|Késés bájtban a legkésésebb késésű replika bájtban|Nincsenek dimenziók|
|pg_replica_log_delay_in_seconds|Yes|Replika késése|Másodperc|Maximum|Replika késése másodpercben|Nincsenek dimenziók|
|serverlog_storage_limit|Yes|Kiszolgálónapló tárolási korlátja|Bájt|Maximum|Kiszolgálónapló tárolási korlátja|Nincsenek dimenziók|
|serverlog_storage_percent|Yes|Kiszolgálónapló tárolási százaléka|Százalék|Átlag|Kiszolgálónapló tárolási százaléka|Nincsenek dimenziók|
|serverlog_storage_usage|Yes|Használt kiszolgálónapló-tároló|Bájt|Átlag|Használt kiszolgálónapló-tároló|Nincsenek dimenziók|
|storage_limit|Yes|Tárhelykorlát|Bájt|Maximum|Tárhelykorlát|Nincsenek dimenziók|
|storage_percent|Yes|Tárterület százalékos aránya|Százalék|Átlag|Tárterület százalékos aránya|Nincsenek dimenziók|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek dimenziók|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek dimenziók|
|cpu_percent|Yes|Processzorhasználat|Százalék|Átlag|Processzorhasználat|Nincsenek dimenziók|
|iops (iops)|Yes|IOPS|Darabszám|Átlag|I/O-műveletek száma másodpercenként|Nincsenek dimenziók|
|memory_percent|Yes|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincsenek dimenziók|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Kihálózat aktív kapcsolatok között|Nincsenek dimenziók|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat aktív kapcsolatok között|Nincsenek dimenziók|
|storage_percent|Yes|Tárterület százalékos aránya|Százalék|Átlag|Tárterület százalékos aránya|Nincsenek dimenziók|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek dimenziók|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|kért használati arány|Százalék|Átlag|kért használati arány|Nincsenek dimenziók|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|A C2D-üzenetek felhagytak|Darabszám|Összesen|Az eszköz által a felhőből az eszközre küldött üzenetek száma|Nincsenek dimenziók|
|c2d.commands.egress.complete.success|Yes|A C2D-üzenetek kézbesítése befejeződött|Darabszám|Összesen|Az eszköz által sikeresen befejezett, a felhőből az eszközre küldött üzenetek kézbesítésének száma|Nincsenek dimenziók|
|c2d.commands.egress.reject.success|Yes|Elutasított C2D-üzenetek|Darabszám|Összesen|Az eszköz által elutasított, felhőből az eszközre küldött üzenetek száma|Nincsenek dimenziók|
|c2d.methods.failure|Yes|Sikertelen közvetlen metódushívások|Darabszám|Összesen|Az összes sikertelen közvetlen metódushívás száma.|Nincsenek dimenziók|
|c2d.methods.requestSize|Yes|A közvetlen metódushívások kérésmérete|Bájt|Átlag|Az összes sikeres közvetlen metóduskérés átlaga, minimuma és maximuma.|Nincsenek dimenziók|
|c2d.methods.responseSize|Yes|A közvetlen metódushívások válaszmérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlaga, minimuma és maximuma.|Nincsenek dimenziók|
|c2d.methods.success|Yes|Sikeres közvetlen metódushívások|Darabszám|Összesen|Az összes sikeres közvetlen metódushívás száma.|Nincsenek dimenziók|
|c2d.twin.read.failure|Yes|Sikertelen ikereszköz-olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen, háttér által kezdeményezett ikereszköz-olvasás száma.|Nincsenek dimenziók|
|c2d.twin.read.size|Yes|A háttérből beolvasott ikereszköz válaszmérete|Bájt|Átlag|Az összes sikeresen kezdeményezett ikereszköz-olvasás átlaga, minimuma és maximuma.|Nincsenek dimenziók|
|c2d.twin.read.success|Yes|Sikeres ikereszköz-olvasások a háttérből|Darabszám|Összesen|Az összes sikeres, háttér által kezdeményezett ikereszköz-olvasás száma.|Nincsenek dimenziók|
|c2d.twin.update.failure|Yes|Sikertelen ikerfrissítések a háttérből|Darabszám|Összesen|Az összes sikertelen, háttér által kezdeményezett ikerfrissítés száma.|Nincsenek dimenziók|
|c2d.twin.update.size|Yes|Az ikerfrissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres, háttér által kezdeményezett ikerfrissítés átlagos, minimális és maximális mérete.|Nincsenek dimenziók|
|c2d.twin.update.success|Yes|Sikeres ikerfrissítések a háttérből|Darabszám|Összesen|A háttérbeli, sikeresen kezdeményezett ikerfrissítések száma.|Nincsenek dimenziók|
|C2DMessagesExpired|Yes|A C2D-üzenetek lejártak|Darabszám|Összesen|Lejárt, felhőből az eszközre küldött üzenetek száma|Nincsenek dimenziók|
|Konfigurációk|Yes|Konfigurációs metrikák|Darabszám|Összesen|A konfigurációs műveletek metrikai|Nincsenek dimenziók|
|connectedDeviceCount|Yes|Csatlakoztatott eszközök|Darabszám|Átlag|Az IoT Hubhoz csatlakoztatott eszközök száma|Nincsenek dimenziók|
|d2c.endpoints.egress.builtIn.events|Yes|Útválasztás: üzenetekhez/eseményekhez kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub az útválasztás sikeresen kézbesített üzeneteket a beépített végpontra (üzenetek/események).|Nincsenek dimenziók|
|d2c.endpoints.egress.eventHubs|Yes|Útválasztás: az eseményközpontba kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket az Event Hubs-végpontokra.|Nincsenek dimenziók|
|d2c.endpoints.egress.serviceBusQueues|Yes|Útválasztás: az üzenetsorba Service Bus üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket a Service Bus végpontjaira.|Nincsenek dimenziók|
|d2c.endpoints.egress.serviceBusTopics|Yes|Útválasztás: üzenettémakörbe Service Bus üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket a Service Bus-végpontokra.|Nincsenek dimenziók|
|d2c.endpoints.egress.storage|Yes|Útválasztás: a tárolóba kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket a tárolási végpontokra.|Nincsenek dimenziók|
|d2c.endpoints.egress.storage.blobs|Yes|Útválasztás: tárolóba kézbesített blobok|Darabszám|Összesen|Az a szám, IoT Hub a blobokat a tárolási végpontokra.|Nincsenek dimenziók|
|d2c.endpoints.egress.storage.bytes|Yes|Útválasztás: a tárolóba kézbesített adatok|Bájt|Összesen|A tárolási végpontokra IoT Hub adatok (bájtok) mennyisége.|Nincsenek dimenziók|
|d2c.endpoints.latency.builtIn.events|Yes|Útválasztás: üzenetek/események üzenetkésése|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) és a telemetriai üzenetek bejövő száma a beépített végpontba (üzenetek/események).|Nincsenek dimenziók|
|d2c.endpoints.latency.eventHubs|Yes|Útválasztás: az event hub üzenetkomikenciája|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) és az eseményközpont-végpontba bejövő üzenetek között.|Nincsenek dimenziók|
|d2c.endpoints.latency.serviceBusQueues|Yes|Útválasztás: üzenetkomikencia Service Bus üzenetsorhoz|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) és az üzenetsorvégpontba Service Bus telemetriai üzenetek bejövő száma.|Nincsenek dimenziók|
|d2c.endpoints.latency.serviceBusTopics|Yes|Útválasztás: üzenetkomikencia Service Bus témakörben|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) egy Service Bus végpontra.|Nincsenek dimenziók|
|d2c.endpoints.latency.storage|Yes|Útválasztás: üzenetk késése a tárterületen|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) és a telemetriai üzenetek egy tárolási végpontba való bejövő száma.|Nincsenek dimenziók|
|d2c.telemetry.egress.dropped|Yes|Útválasztás: telemetriai üzenetek eldobva |Darabszám|Összesen|Az üzenetek megszakadt végpontok miatti IoT Hub eldobott üzenetek száma. Ez az érték nem számolja a tartalék útvonalra küldött üzeneteket, mivel a rendszer nem kézbesíti az eldobott üzeneteket.|Nincsenek dimenziók|
|d2c.telemetry.egress.fallback|Yes|Útválasztás: a tartaléknak kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub a tartalék útvonalhoz társított végpontnak küldött üzenetek száma.|Nincsenek dimenziók|
|d2c.telemetry.egress.invalid|Yes|Útválasztás: nem kompatibilis telemetriai üzenetek|Darabszám|Összesen|Az a szám, IoT Hub az útválasztás nem tudott üzeneteket kézbesíteni a végponttal való inkompatibilitás miatt. Ez az érték nem tartalmazza az újrabeküldetieket.|Nincsenek dimenziók|
|d2c.telemetry.egress.orphaned|Yes|Útválasztás: árva telemetriai üzenetek |Darabszám|Összesen|Az üzeneteknek az útválasztás által árva IoT Hub száma, mert nem egyeznek egyetlen útválasztási sszabályokkal sem (beleértve a tartalék szabályt is). |Nincsenek dimenziók|
|d2c.telemetry.egress.success|Yes|Útválasztás: kézbesített telemetriai üzenetek|Darabszám|Összesen|Az üzenetek sikeres kézbesítésének száma az összes végpontra IoT Hub útválasztás használatával. Ha egy üzenet több végpontra van irányítva, ez az érték minden sikeres kézbesítéshez 1-sel nő. Ha egy üzenet többször is ugyanannak a végpontnak lesz kézbesítve, ez az érték minden sikeres kézbesítésnél 1-rel nő.|Nincsenek dimenziók|
|d2c.telemetry.ingress.allProtocol|Yes|Telemetriai üzenetek küldési kísérlete|Darabszám|Összesen|Az IoT Hubra megkísérelt, az eszközről a felhőbe küldött telemetriai üzenetek száma|Nincsenek dimenziók|
|d2c.telemetry.ingress.sendThrottle|Yes|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebességének szabályozása miatti szabályozási hibák száma|Nincsenek dimenziók|
|d2c.telemetry.ingress.success|Yes|Elküldött telemetriai üzenetek|Darabszám|Összesen|Az IoT Hubnak sikeresen elküldött, az eszközről a felhőbe küldött telemetriai üzenetek száma|Nincsenek dimenziók|
|d2c.twin.read.failure|Yes|Sikertelen ikereszköz-olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen ikereszköz-olvasás száma.|Nincsenek dimenziók|
|d2c.twin.read.size|Yes|Az ikereszközökről beolvasott ikereszközök válaszmérete|Bájt|Átlag|Az ikereszköz által kezdeményezett sikeres olvasások átlaga, minimális és maximálisa.|Nincsenek dimenziók|
|d2c.twin.read.success|Yes|Sikeres ikereszköz-olvasások az eszközökről|Darabszám|Összesen|Az ikereszköz által kezdeményezett sikeres olvasások száma.|Nincsenek dimenziók|
|d2c.twin.update.failure|Yes|Sikertelen ikerfrissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett összes sikertelen ikerfrissítés száma.|Nincsenek dimenziók|
|d2c.twin.update.size|Yes|Az ikereszközökről származó frissítések mérete|Bájt|Átlag|Az eszköz által kezdeményezett összes sikeres ikerfrissítés átlagos, minimális és maximális mérete.|Nincsenek dimenziók|
|d2c.twin.update.success|Yes|Sikeres ikerfrissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett összes sikeres ikerfrissítés száma.|Nincsenek dimenziók|
|dailyMessageQuotaUsed|Yes|A használt üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt összes üzenet száma|Nincsenek dimenziók|
|deviceDataUsage (eszközadatok használata)|Yes|Eszközök teljes adathasználata|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|Nincsenek dimenziók|
|deviceDataUsageV2|Yes|Eszközök teljes adathasználata (előzetes verzió)|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|Nincsenek dimenziók|
|devices.connectedDevices.allProtocol|Yes|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT Hubhoz csatlakoztatott eszközök száma|Nincsenek dimenziók|
|devices.totalEszközök|Yes|Eszközök összesen (elavult)|Darabszám|Összesen|Az IoT Hubon regisztrált eszközök száma|Nincsenek dimenziók|
|EventGridDeliveries|Yes|Event Grid kézbesítések|Darabszám|Összesen|A IoT Hub közzétett események Event Grid. A sikeres és sikertelen kérések számát az Eredmény dimenzióban használhatja. Az EventType dimenzió az esemény típusát () jeleníti https://aka.ms/ioteventgrid) meg.|Eredmény, EventType|
|EventGridLatency|Yes|Event Grid késés|Ezredmásodperc|Átlag|Az átlagos késés (ezredmásodperc) az Iot Hub-esemény keletkezését és az eseménynek a Event Grid. Ez a szám az összes eseménytípus átlaga. Az EventType dimenzióval egy adott eseménytípus késését láthatja.|EventType (Eseménytípus)|
|jobs.cancelJob.failure|Yes|Sikertelen feladat megszakítása|Darabszám|Összesen|A feladat megszakítását kezdeményező összes sikertelen hívás száma.|Nincsenek dimenziók|
|jobs.cancelJob.success|Yes|Sikeres feladattörlések|Darabszám|Összesen|A feladat megszakítását kezdeményező összes sikeres hívás száma.|Nincsenek dimenziók|
|jobs.completed|Yes|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek dimenziók|
|jobs.createDirectMethodJob.failure|Yes|Metódushívási feladatok sikertelen létrehozása|Darabszám|Összesen|A közvetlen metódushívási feladatok sikertelen létrehozásának száma.|Nincsenek dimenziók|
|jobs.createDirectMethodJob.success|Yes|Metódushívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódushívási feladatok sikeres létrehozásának száma.|Nincsenek dimenziók|
|jobs.createTwinUpdateJob.failure|Yes|Az ikerfrissítési feladatok sikertelen létrehozása|Darabszám|Összesen|Az ikerfrissítési feladatok sikertelen létrehozásának száma.|Nincsenek dimenziók|
|jobs.createTwinUpdateJob.success|Yes|Az ikerfrissítési feladatok sikeres létrehozása|Darabszám|Összesen|Az ikerfrissítési feladatok sikeres létrehozásának száma.|Nincsenek dimenziók|
|jobs.failed|Yes|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek dimenziók|
|jobs.listJobs.failure|Yes|Sikertelen hívások a feladatok listához|Darabszám|Összesen|A feladatok listához sikertelen hívások száma.|Nincsenek dimenziók|
|jobs.listJobs.success|Yes|Sikeres hívások a feladatok listához|Darabszám|Összesen|A feladatok listához sikeres hívások száma.|Nincsenek dimenziók|
|jobs.queryJobs.failure|Yes|Sikertelen feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikertelen hívásának száma.|Nincsenek dimenziók|
|jobs.queryJobs.success|Yes|Sikeres feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek dimenziók|
|RoutingDataSizeInBytesDelivered|Yes|Útválasztási kézbesítési üzenet mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT Hub által a végpontnak küldött üzenetek teljes mérete bájtban. Az EndpointName és az EndpointType dimenzióval megtekintheti a különböző végpontokra küldött üzenetek méretét bájtban. A metrika értéke minden kézbesített üzenetnél nő, beleértve azt is, hogy az üzenet több végpontnak van-e kézbesítve, vagy ha az üzenet többször is ugyanannak a végpontnak lesz kézbesítve.|EndpointType, EndpointName, RoutingSource|
|ÚtválasztásTűcikkek|Yes|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Az a szám, IoT Hub megkíséreltek üzeneteket kézbesíteni az összes végpontra útválasztás használatával. A sikeres vagy sikertelen kísérletek számának az Eredmény dimenzió használatával használhatja. A hiba okát (például érvénytelen, eldobott vagy árva) az FailureReasonCategory dimenzióval láthatja. Az EndpointName és az EndpointType dimenziók használatával azt is megértheti, hogy hány üzenet érkezett a különböző végpontokra. A metrika értéke minden kézbesítési kísérlethez 1-sel nő, beleértve azt is, hogy az üzenet több végpontra van-e kézbesítve, vagy ha az üzenet többször is ugyanannak a végpontnak lesz kézbesítve.|EndpointType, EndpointName, FailureReasonCategory, Eredmény, RoutingSource|
|RoutingDeliveryLatency|Yes|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodperc|Átlag|Az átlagos késés (ezredmásodperc) az üzenet be- és IoT Hub a végpontba bejövő telemetriai üzenetek között. Az EndpointName és az EndpointType dimenziók használatával megértheti a különböző végpontok késését.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Yes|kért használati arány|Százalék|Átlag|kért használati arány|Nincsenek dimenziók|
|totalDeviceCount|Yes|Eszközök összesen|Darabszám|Átlag|Az IoT Hubon regisztrált eszközök száma|Nincsenek dimenziók|
|twinQueries.failure|Yes|Sikertelen ikerlekérdezések|Darabszám|Összesen|Az összes sikertelen ikerlekérdezések száma.|Nincsenek dimenziók|
|twinQueries.resultSize|Yes|Iker lekérdezések eredménymérete|Bájt|Átlag|Az összes sikeres ikerlekérdezések eredményméretének átlaga, minimális és maximális mérete.|Nincsenek dimenziók|
|twinQueries.success|Yes|Sikeres ikerlekérdezések|Darabszám|Összesen|Az összes sikeres ikerlekérdezések száma.|Nincsenek dimenziók|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|A C2D-üzenetek felhagynak|Darabszám|Összesen|Az eszköz által a felhőből az eszközre küldött üzenetek száma|Nincsenek dimenziók|
|c2d.commands.egress.complete.success|Yes|A C2D-üzenetek kézbesítése befejeződött|Darabszám|Összesen|Az eszköz által sikeresen befejezett, a felhőből az eszközre küldött üzenetek kézbesítésének száma|Nincsenek dimenziók|
|c2d.commands.egress.reject.success|Yes|Elutasított C2D-üzenetek|Darabszám|Összesen|Az eszköz által elutasított, felhőből az eszközre küldött üzenetek száma|Nincsenek dimenziók|
|c2d.methods.failure|Yes|Sikertelen közvetlen metódushívások|Darabszám|Összesen|Az összes sikertelen közvetlen metódushívás száma.|Nincsenek dimenziók|
|c2d.methods.requestSize|Yes|A közvetlen metódushívások kérésmérete|Bájt|Átlag|Az összes sikeres közvetlen metódusra vonatkozó kérés átlaga, minimuma és maximuma.|Nincsenek dimenziók|
|c2d.methods.responseSize|Yes|A közvetlen metódushívások válaszmérete|Bájt|Átlag|A közvetlen metódusok összes sikeres válaszának átlaga, minimuma és maximuma.|Nincsenek dimenziók|
|c2d.methods.success|Yes|Sikeres közvetlen metódushívások|Darabszám|Összesen|Az összes sikeres közvetlen metódushívás száma.|Nincsenek dimenziók|
|c2d.twin.read.failure|Yes|Sikertelen ikereszköz-olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen, háttér által kezdeményezett ikereszköz-olvasás száma.|Nincsenek dimenziók|
|c2d.twin.read.size|Yes|A háttérből beolvasott ikereszköz válaszmérete|Bájt|Átlag|Az összes sikeresen kezdeményezett ikereszköz-olvasás átlaga, minimuma és maximuma.|Nincsenek dimenziók|
|c2d.twin.read.success|Yes|Sikeres ikereszköz-olvasások a háttérből|Darabszám|Összesen|Az összes sikeres, háttér által kezdeményezett ikereszköz-olvasás száma.|Nincsenek dimenziók|
|c2d.twin.update.failure|Yes|Sikertelen ikerfrissítések a háttérből|Darabszám|Összesen|A háttérbeli, sikertelenül kezdeményezett ikerfrissítések száma.|Nincsenek dimenziók|
|c2d.twin.update.size|Yes|Az ikerfrissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres, a háttér által kezdeményezett ikerfrissítés átlagos, minimális és maximális mérete.|Nincsenek dimenziók|
|c2d.twin.update.success|Yes|Sikeres ikerfrissítések a háttérből|Darabszám|Összesen|Az összes sikeres, háttér által kezdeményezett ikerfrissítés száma.|Nincsenek dimenziók|
|C2DMessagesExpired|Yes|A C2D-üzenetek lejártak|Darabszám|Összesen|Lejárt, felhőből az eszközre küldött üzenetek száma|Nincsenek dimenziók|
|Konfigurációk|Yes|Konfigurációs metrikák|Darabszám|Összesen|Konfigurációs műveletek metrikai|Nincsenek dimenziók|
|connectedDeviceCount|No|Csatlakoztatott eszközök|Darabszám|Átlag|Az IoT Hubhoz csatlakoztatott eszközök száma|Nincsenek dimenziók|
|d2c.endpoints.egress.builtIn.events|Yes|Útválasztás: üzeneteknek/eseményeknek kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket a beépített végpontra (üzenetek/események).|Nincsenek dimenziók|
|d2c.endpoints.egress.eventHubs|Yes|Útválasztás: az eseményközpontba kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket az Event Hub-végpontokra.|Nincsenek dimenziók|
|d2c.endpoints.egress.serviceBusQueues|Yes|Útválasztás: az üzenetsorba Service Bus üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket a Service Bus végpontjaira.|Nincsenek dimenziók|
|d2c.endpoints.egress.serviceBusTopics|Yes|Útválasztás: a témakörbe Service Bus üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket a Service Bus-végpontokra.|Nincsenek dimenziók|
|d2c.endpoints.egress.storage|Yes|Útválasztás: a tárolóba kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub sikeresen kézbesített üzeneteket a tárolási végpontokra.|Nincsenek dimenziók|
|d2c.endpoints.egress.storage.blobs|Yes|Útválasztás: tárolóba kézbesített blobok|Darabszám|Összesen|Az a szám, IoT Hub a blobokat a tárolóvégpontra.|Nincsenek dimenziók|
|d2c.endpoints.egress.storage.bytes|Yes|Útválasztás: tárolóba kézbesített adatok|Bájt|Összesen|A tárolási végpontokra IoT Hub adatok (bájtok) mennyisége.|Nincsenek dimenziók|
|d2c.endpoints.latency.builtIn.events|Yes|Útválasztás: üzenetek/események üzenetkésése|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) a beépített végpontba (üzenetek/események) való bejövő telemetriai üzenetbe.|Nincsenek dimenziók|
|d2c.endpoints.latency.eventHubs|Yes|Útválasztás: üzenetk késése az Event Hubsban|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc).|Nincsenek dimenziók|
|d2c.endpoints.latency.serviceBusQueues|Yes|Útválasztás: üzenetkomikát Service Bus üzenetsorhoz|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő üzenetei közötti átlagos késés (ezredmásodperc) IoT Hub és a telemetriai üzenetek bejövő száma egy Service Bus végpontra.|Nincsenek dimenziók|
|d2c.endpoints.latency.serviceBusTopics|Yes|Útválasztás: üzenetk késése Service Bus témakörben|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) és a telemetriai üzenetek Service Bus végpontra.|Nincsenek dimenziók|
|d2c.endpoints.latency.storage|Yes|Útválasztás: üzenetk késése a tárterületen|Ezredmásodperc|Átlag|Az üzenetek bejövő és bejövő IoT Hub közötti átlagos késés (ezredmásodperc) és a telemetriai üzenetek tárolási végpontba való bejövő száma.|Nincsenek dimenziók|
|d2c.telemetry.egress.dropped|Yes|Útválasztás: telemetriai üzenetek eldobva |Darabszám|Összesen|Az üzenetek megszakadt végpontok miatti IoT Hub eldobott üzenetek száma. Ez az érték nem számolja a tartalék útvonalra kézbesített üzeneteket, mivel az eldobott üzenetek nem ott vannak kézbesítve.|Nincsenek dimenziók|
|d2c.telemetry.egress.fallback|Yes|Útválasztás: a tartaléknak kézbesített üzenetek|Darabszám|Összesen|Az a szám, IoT Hub a tartalék útvonalhoz társított végpontnak küldött üzenetek száma.|Nincsenek dimenziók|
|d2c.telemetry.egress.invalid|Yes|Útválasztás: nem kompatibilis telemetriai üzenetek|Darabszám|Összesen|Az a szám, IoT Hub az útválasztás nem tudott üzeneteket kézbesíteni a végponttal való inkompatibilitás miatt. Ez az érték nem tartalmazza az újrabeküldeteket.|Nincsenek dimenziók|
|d2c.telemetry.egress.orphaned|Yes|Útválasztás: árva telemetriai üzenetek |Darabszám|Összesen|Az üzeneteknek az útválasztás által árva IoT Hub száma, mert nem egyeznek egyik útválasztási szabályokkal sem (beleértve a tartalék szabályt is). |Nincsenek dimenziók|
|d2c.telemetry.egress.success|Yes|Útválasztás: kézbesített telemetriai üzenetek|Darabszám|Összesen|Az üzenetek sikeres kézbesítésének száma az összes végpontra IoT Hub útválasztás használatával. Ha egy üzenet több végpontra van irányítva, ez az érték minden sikeres kézbesítéshez 1-rel nő. Ha egy üzenet többször is ugyanannak a végpontnak lesz kézbesítve, ez az érték minden sikeres kézbesítésnél 1-rel nő.|Nincsenek dimenziók|
|d2c.telemetry.ingress.allProtocol|Yes|Telemetriai üzenetek küldési kísérlete|Darabszám|Összesen|Az eszközről a felhőbe küldött telemetriai üzenetek száma, amelyek az IoT Hubra való küldését kísérelték meg|Nincsenek dimenziók|
|d2c.telemetry.ingress.sendThrottle|Yes|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebességének szabályozása miatti szabályozási hibák száma|Nincsenek dimenziók|
|d2c.telemetry.ingress.success|Yes|Elküldött telemetriai üzenetek|Darabszám|Összesen|Az IoT Hubnak sikeresen elküldött, az eszközről a felhőbe küldött telemetriai üzenetek száma|Nincsenek dimenziók|
|d2c.twin.read.failure|Yes|Sikertelen ikereszköz-olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen ikereszköz-olvasás száma.|Nincsenek dimenziók|
|d2c.twin.read.size|Yes|Az ikereszközökről beolvasott ikereszközök válaszmérete|Bájt|Átlag|Az ikereszköz által kezdeményezett sikeres olvasások átlaga, minimális és maximálisa.|Nincsenek dimenziók|
|d2c.twin.read.success|Yes|Sikeres ikereszköz-olvasások az eszközökről|Darabszám|Összesen|Az ikereszköz által kezdeményezett sikeres olvasások száma.|Nincsenek dimenziók|
|d2c.twin.update.failure|Yes|Sikertelen ikerfrissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett összes sikertelen ikerfrissítés száma.|Nincsenek dimenziók|
|d2c.twin.update.size|Yes|Az ikereszközökről származó frissítések mérete|Bájt|Átlag|Az eszköz által kezdeményezett összes sikeres ikerfrissítés átlagos, minimális és maximális mérete.|Nincsenek dimenziók|
|d2c.twin.update.success|Yes|Sikeres ikerfrissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett összes sikeres ikerfrissítés száma.|Nincsenek dimenziók|
|dailyMessageQuotaUsed|Yes|A használt üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt üzenetek teljes száma|Nincsenek dimenziók|
|deviceDataUsage (eszközadatok használata)|Yes|Eszközök teljes adathasználata|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|Nincsenek dimenziók|
|deviceDataUsageV2|Yes|Eszközök teljes adathasználata (előzetes verzió)|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|Nincsenek dimenziók|
|devices.connectedDevices.allProtocol|Yes|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT Hubhoz csatlakoztatott eszközök száma|Nincsenek dimenziók|
|devices.totalEszközök|Yes|Eszközök teljes száma (elavult)|Darabszám|Összesen|Az IoT Hubon regisztrált eszközök száma|Nincsenek dimenziók|
|EventGridDeliveries|Yes|Event Grid kézbesítések|Darabszám|Összesen|A IoT Hub közzétett események Event Grid. A sikeres és sikertelen kérések számát az Eredmény dimenzióval használhatja. Az EventType dimenzió az esemény típusát () jeleníti https://aka.ms/ioteventgrid) meg.|Eredmény, EventType|
|EventGridLatency|Yes|Event Grid késés|Ezredmásodperc|Átlag|Az átlagos késés (ezredmásodperc) az Iot Hub-esemény generálása és az esemény Event Grid. Ez a szám az összes eseménytípus átlaga. Az EventType dimenzióval egy adott eseménytípus késését láthatja.|EventType (Eseménytípus)|
|jobs.cancelJob.failure|Yes|Sikertelen feladat megszakítása|Darabszám|Összesen|A feladat megszakítását elkenő összes sikertelen hívás száma.|Nincsenek dimenziók|
|jobs.cancelJob.success|Yes|Sikeres feladattörlések|Darabszám|Összesen|A feladat megszakítását sikeresen megszakító összes hívás száma.|Nincsenek dimenziók|
|jobs.completed|Yes|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek dimenziók|
|jobs.createDirectMethodJob.failure|Yes|Metódushívási feladatok sikertelen létrehozása|Darabszám|Összesen|A közvetlen metódushívási feladatok sikertelen létrehozásának száma.|Nincsenek dimenziók|
|jobs.createDirectMethodJob.success|Yes|Metódushívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódushívási feladatok sikeres létrehozásának száma.|Nincsenek dimenziók|
|jobs.createTwinUpdateJob.failure|Yes|Az ikerfrissítési feladatok sikertelen létrehozása|Darabszám|Összesen|Az ikerfrissítési feladatok sikertelen létrehozásának száma.|Nincsenek dimenziók|
|jobs.createTwinUpdateJob.success|Yes|Az ikerfrissítési feladatok sikeres létrehozása|Darabszám|Összesen|Az ikerfrissítési feladatok sikeres létrehozásának száma.|Nincsenek dimenziók|
|jobs.failed|Yes|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek dimenziók|
|jobs.listJobs.failure|Yes|Sikertelen hívások a feladatok listához|Darabszám|Összesen|A feladatok listához sikertelen hívások száma.|Nincsenek dimenziók|
|jobs.listJobs.success|Yes|Sikeres hívások a feladatok listához|Darabszám|Összesen|A feladatok listához sikeres hívások száma.|Nincsenek dimenziók|
|jobs.queryJobs.failure|Yes|Sikertelen feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikertelen hívásának száma.|Nincsenek dimenziók|
|jobs.queryJobs.success|Yes|Sikeres feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek dimenziók|
|RoutingDataSizeInBytesDelivered|Yes|Útválasztási kézbesítési üzenet mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT Hub által a végpontnak küldött üzenetek teljes mérete bájtban. Az EndpointName és az EndpointType dimenzióval megtekintheti a különböző végpontokra küldött üzenetek méretét bájtban. A metrika értéke minden kézbesített üzenetnél nő, beleértve azt is, hogy az üzenet több végpontra van-e kézbesítve, vagy ha az üzenet többször is ugyanannak a végpontnak lesz kézbesítve.|EndpointType, EndpointName, RoutingSource|
|ÚtválasztásDeliveries (Útválasztási útvonalak)|Yes|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Az a szám, IoT Hub megkíséreltek üzeneteket kézbesíteni az összes végpontra útválasztás használatával. A sikeres vagy sikertelen kísérletek számának az Eredmény dimenzió használatával használhatja. A hiba okának (például érvénytelen, eldobott vagy árva) adatokat a FailureReasonCategory dimenzió használatával lehet megnézni. Az EndpointName és az EndpointType dimenziók használatával azt is megértheti, hogy hány üzenet érkezett a különböző végpontokra. A metrika értéke minden egyes kézbesítési kísérletnél 1-sel nő, beleértve azt is, hogy az üzenet több végpontnak van-e kézbesítve, vagy ha az üzenet többször is ugyanannak a végpontnak lesz kézbesítve.|EndpointType, EndpointName, FailureReasonCategory, Eredmény, RoutingSource|
|RoutingDeliveryLatency|Yes|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodperc|Átlag|Az átlagos késés (ezredmásodperc) az üzenet be- és IoT Hub a végpontba bejövő telemetriai üzenetek között. Az EndpointName és az EndpointType dimenzióval megértheti a különböző végpontok késését.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Eszközök összesen|Darabszám|Átlag|Az IoT Hubon regisztrált eszközök száma|Nincsenek dimenziók|
|twinQueries.failure|Yes|Sikertelen ikerlekérdezések|Darabszám|Összesen|Az összes sikertelen ikerlekérdezések száma.|Nincsenek dimenziók|
|twinQueries.resultSize|Yes|Iker lekérdezések eredménymérete|Bájt|Átlag|Az összes sikeres ikerlekérdezések eredményméretének átlaga, minimális és maximális mérete.|Nincsenek dimenziók|
|twinQueries.success|Yes|Sikeres ikerlekérdezések|Darabszám|Összesen|Az összes sikeres ikerlekérdezések száma.|Nincsenek dimenziók|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AttestationAttempts (Igazolási részletek)|Yes|Igazolási kísérletek|Darabszám|Összesen|A megkísérelt eszköztesztek száma|ProvisioningServiceName, Status, Protocol|
|DeviceAssignments|Yes|Hozzárendelt eszközök|Darabszám|Összesen|IoT Hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközregisztrációk száma|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|API-kérelmek|Darabszám|Összesen|Az olvasási, írási, törlési és lekérdezési Digital Twins API-kérések száma.|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|API-kérelmek hibaaránya|Százalék|Átlag|A szolgáltatás által a példányhoz kapott API-kérések százalékos aránya, amelyek belső hibakódot (500) ad vissza Digital Twins olvasási, írási, törlési és lekérdezési műveletekhez.|Művelet, hitelesítés, protokoll|
|ApiRequestsLatency|Yes|API-kérések késése|Ezredmásodperc|Átlag|Az API-kérések válaszideje, vagyis attól az időponttól, amikor a kérést a Azure Digital Twins megkapta, egészen addig, amíg Digital Twins szolgáltatás siker/sikertelen eredményt nem küld olvasási, írási, törlési és lekérdezési műveletekhez.|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations (BillingApiOperations)|Yes|Számlázási API-műveletek|Darabszám|Összesen|Számlázási metrika a Azure Digital Twins api-kérések számához.|MeterId|
|BillingMessagesProcessed|Yes|Feldolgozott számlázási üzenetek|Darabszám|Összesen|Számlázási metrika a külső végpontokra küldött Azure Digital Twins számára.|MeterId|
|BillingQueryUnits|Yes|Számlázási lekérdezési egységek|Darabszám|Összesen|A lekérdezések végrehajtásához felhasznált lekérdezési egységek száma, a szolgáltatáserőforrás-használat belsőleg kiszámított mértéke.|MeterId|
|IngressEvents (IngressEvents)|Yes|Bejövő események|Darabszám|Összesen|A bejövő telemetriai események száma a Azure Digital Twins.|Eredmény|
|IngressEventsFailureRate|Yes|Bejövő események hibaaránya|Százalék|Átlag|Azon bejövő telemetriaesemények százalékos aránya, amelyeknél a szolgáltatás belső hibakódot (500) ad vissza.|Nincsenek dimenziók|
|IngressEventsLatency (IngressEventsLatency)|Yes|Bejövő események késése|Ezredmásodperc|Átlag|Az az idő, amikor egy esemény megérkezik, és amikor az esemény készen áll a Azure Digital Twins, amikor a szolgáltatás sikeres/sikertelen eredményt küld.|Eredmény|
|ModelCount|Yes|Modellek száma|Darabszám|Összesen|A modellpéldányban található modellek Azure Digital Twins száma. Ezzel a metrikát használva megállapíthatja, hogy megközelíti-e a példányonként engedélyezett modellek maximális számának szolgáltatási korlátját.|Nincsenek dimenziók|
|Útválasztás|Yes|Üzenetek irányítva|Darabszám|Összesen|Egy végponti Azure-szolgáltatáshoz, például az Event Hubhoz, az eseményközponthoz vagy Service Bus vagy Event Grid.|EndpointType, Eredmény|
|RoutingFailureRate|Yes|Útválasztási hibák aránya|Százalék|Átlag|Azon események százalékos aránya, amelyek hibát eredményeznek, miközben az Azure Digital Twins egy végponti Azure-szolgáltatáshoz, például eseményközponthoz, eseményközponthoz Service Bus vagy Event Grid.|EndpointType (Végponttípus)|
|ÚtválasztásLatency|Yes|Útválasztási késés|Ezredmásodperc|Átlag|A Azure Digital Twins végpont Azure-szolgáltatásában , például az Event Hubon, a Service Bus-ban vagy az eseményközpontban való Service Bus között eltelt Event Grid.|EndpointType, Eredmény|
|TwinCount|Yes|Ikerszám|Darabszám|Összesen|Az ikerpéldányban található iker Azure Digital Twins száma. Ezzel a metrikével megállapíthatja, hogy megközelíti-e a példányonként engedélyezett ikerek maximális számára vonatkozó szolgáltatási korlátot.|Nincsenek dimenziók|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AddRegion (Hozzáadás régiója)|Yes|Régió hozzáadva|Darabszám|Darabszám|Régió hozzáadva|Region|
|Automatikus skálázásMaxThroughput|No|Automatikus skálázás maximális átviteli sebesség|Darabszám|Maximum|Automatikus skálázás maximális átviteli sebesség|DatabaseName, CollectionName|
|AvailableStorage (Rendelkezésre álló adatok)|No|(elavult) Rendelkezésre álló tár|Bájt|Összesen|A "Rendelkezésre álló tár" 2023 szeptemberének Azure Monitor el a tárolóból. Cosmos DB gyűjtemény tárterületmérete korlátlan. Az egyetlen korlátozás az, hogy az egyes logikai partíciókulcsok tárterületének mérete 20 GB. Engedélyezheti a PartitionKeyStatistics adatokat a diagnosztikai naplóban, hogy tudja a legfelső partíciókulcsok tárhelyfelhasználását. A tárhelykvóta Cosmos DB további információért tekintse meg ezt a https://docs.microsoft.com/azure/cosmos-db/concepts-limits dokumentumot. Az elalasztás után az elavult metrikában még meghatározott riasztási szabályok automatikusan le lesznek tiltva az elalasztás dátuma után.|CollectionName, DatabaseName, Region|
|CassandraConnectionBezárások|No|Cassandra-kapcsolatlezárások|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma, 1 perces részletességgel jelentve|Régió, LezárásReason|
|CassandraConnectorAvgReplicationLatency|No|Cassandra-összekötő átlagos replikációjaLatency|Ezredmásodperc|Átlag|Cassandra-összekötő átlagos replikációjaLatency|Nincsenek dimenziók|
|CassandraConnectorReplicationHealthStatus|No|Cassandra-összekötő replikációjának állapota|Darabszám|Darabszám|Cassandra-összekötő replikációjának állapota|NotStarted, ReplicationInProgress, Hiba|
|CassandraKeyspaceLétrehozás|No|Cassandra-kulcstér létrehozva|Darabszám|Darabszám|Cassandra-kulcstér létrehozva|ResourceName, |
|CassandraKeyspaceDelete|No|Cassandra-kulcstér törölve|Darabszám|Darabszám|Cassandra-kulcstér törölve|ResourceName, |
|CassandraKeyspaceThroughputUpdate|No|A Cassandra Keyspace átviteli sebességének frissítése|Darabszám|Darabszám|A Cassandra Keyspace átviteli sebességének frissítése|ResourceName, |
|CassandraKeyspaceUpdate|No|A Cassandra-kulcstér frissítve|Darabszám|Darabszám|A Cassandra-kulcstér frissítve|ResourceName, |
|CassandraRequestCharges|No|Cassandra-kérelmek díjai|Darabszám|Összesen|A Cassandra-kérelmekhez felhasznált kérelem ru-k|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|No|Cassandra-kérések|Darabszám|Darabszám|A Cassandra-kérelmek száma|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableLétrehozás|No|Cassandra-tábla létrehozva|Darabszám|Darabszám|Cassandra-tábla létrehozva|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra-tábla törölve|Darabszám|Darabszám|Cassandra-tábla törölve|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|A Cassandra-tábla átviteli sebességének frissítése|Darabszám|Darabszám|A Cassandra-tábla átviteli sebességének frissítése|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra-tábla frissítve|Darabszám|Darabszám|Cassandra-tábla frissítve|ResourceName, ChildResourceName, |
|CreateAccount (Fiók létrehozása)|Yes|Fiók létrehozva|Darabszám|Darabszám|Fiók létrehozva|Nincsenek dimenziók|
|DataUsage (Adathasználás)|No|Adathasználat|Bájt|Összesen|5 perces részletességgel jelentett teljes adathasználat|CollectionName, DatabaseName, Region|
|DedicatedGatewayRequests|Yes|DedicatedGatewayRequests|Darabszám|Darabszám|Kérések a dedikált átjárónál|DatabaseName, CollectionName, CacheExerc adattár, műveletnév, régió|
|DeleteAccount (Fiók törlése)|Yes|Fiók törölve|Darabszám|Darabszám|Fiók törölve|Nincsenek dimenziók|
|DocumentCount (Dokumentumszám)|No|Dokumentumok száma|Darabszám|Összesen|5 perces részletességgel jelentett dokumentumok teljes száma|CollectionName, DatabaseName, Region|
|DocumentQuota|No|Dokumentumkvóta|Bájt|Összesen|5 perces részletességgel jelentett teljes tárolási kvóta|CollectionName, DatabaseName, Region|
|GremlinDatabaseLétrehozás|No|Létrehozott Gremlin-adatbázis|Darabszám|Darabszám|Létrehozott Gremlin-adatbázis|ResourceName, |
|GremlinDatabaseDelete|No|Gremlin-adatbázis törölve|Darabszám|Darabszám|Gremlin-adatbázis törölve|ResourceName, |
|GremlinDatabaseThroughputUpdate|No|A Gremlin-adatbázis átviteli sebességének frissítése|Darabszám|Darabszám|A Gremlin-adatbázis átviteli sebességének frissítése|ResourceName, |
|GremlinDatabaseUpdate|No|A Gremlin-adatbázis frissítve|Darabszám|Darabszám|A Gremlin-adatbázis frissítve|ResourceName, |
|GremlinGraphLétrehozás|No|Létrehozott Gremlin-gráf|Darabszám|Darabszám|Létrehozott Gremlin-gráf|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin Graph törölve|Darabszám|Darabszám|Gremlin Graph törölve|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|A Gremlin Graph átviteli sebességének frissítése|Darabszám|Darabszám|A Gremlin Graph átviteli sebességének frissítése|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin Graph frissítve|Darabszám|Darabszám|Gremlin Graph frissítve|ResourceName, ChildResourceName, |
|IndexUsage (Indexusage)|No|Indexhasználat|Bájt|Összesen|Az indexhasználat összesen 5 perces részletességgel jelentve|CollectionName, DatabaseName, Region|
|IntegratedCacheEvictedEntriesSize|No|IntegratedCacheEvictedEntriesSize|Bájt|Átlag|Az integrált gyorsítótárból kiürített bejegyzések mérete|CacheType, Régió|
|IntegratedCacheHitRate|No|IntegratedCacheHitRate|Százalék|Átlag|Gyorsítótár-találati arány integrált gyorsítótárakhoz|CacheType, Régió|
|IntegratedCacheSize|No|IntegratedCacheSize|Bájt|Átlag|A dedikált átjárókérések integrált gyorsítótárának mérete|CacheType, Régió|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Darabszám|Átlag|Az integrált gyorsítótárból a TTL lejárata miatt eltávolított bejegyzések száma|CacheType, Region|
|MetadataRequests|No|Metaadat-kérelmek|Darabszám|Darabszám|Metaadat-kérelmek száma. Cosmos DB minden fiókhoz fenntartja a rendszer metaadat-gyűjtését, amely lehetővé teszi a gyűjtemények, adatbázisok és azok konfigurációjának ingyenes számbavételét.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoCollectionLétrehozás|No|Létrehozott Mongo-gyűjtemény|Darabszám|Darabszám|Létrehozott Mongo-gyűjtemény|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo-gyűjtemény törölve|Darabszám|Darabszám|Mongo-gyűjtemény törölve|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Mongo-gyűjtemény átviteli sebességének frissítése|Darabszám|Darabszám|Mongo-gyűjtemény átviteli sebességének frissítése|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo-gyűjtemény frissítve|Darabszám|Darabszám|Mongo-gyűjtemény frissítve|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo-adatbázis törölve|Darabszám|Darabszám|Mongo-adatbázis törölve|ResourceName, |
|MongoDatabaseThroughputUpdate|No|Mongo-adatbázis átviteli sebességének frissítése|Darabszám|Darabszám|Mongo-adatbázis átviteli sebességének frissítése|ResourceName, |
|MongoDBDatabaseLétrehozás|No|Létrehozott Mongo-adatbázis|Darabszám|Darabszám|Létrehozott Mongo-adatbázis|ResourceName, |
|MongoDBDatabaseUpdate|No|Frissített Mongo-adatbázis|Darabszám|Darabszám|Frissített Mongo-adatbázis|ResourceName, |
|MongoRequestCharge|Yes|Mongo-kérelem díj|Darabszám|Összesen|Felhasznált Mongo-kérelemegységek|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|Yes|Mongo-kérések|Darabszám|Darabszám|A mongo-kérések száma|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequestsCount|No|(elavult) Mongo-kérelmek sebessége|CountPerSecond (Másodpercek száma)|Átlag|Mongo-kérések száma másodpercenként|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|No|(elavult) A Mongo törlési kérelmek aránya|CountPerSecond (Másodpercek száma)|Átlag|Mongo Delete kérés másodpercenként|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|No|(elavult) Mongo Insert Request Rate|CountPerSecond (Másodpercek száma)|Átlag|Mongo-beszúrások száma másodpercenként|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|No|(elavult) Mongo lekérdezési kérelmek sebessége|CountPerSecond (Másodpercek száma)|Átlag|Mongo-lekérdezési kérés másodpercenként|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|No|(elavult) Mongo frissítési kérelmek sebessége|CountPerSecond (Másodpercek száma)|Átlag|Mongo-frissítési kérés másodpercenként|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption (NormalizedRUConsumption)|No|Normalizált ru-használat|Százalék|Maximum|Maximális ru-használat százaléka percenként|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput (Kiépítési sebesség)|No|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|DatabaseName, CollectionName|
|RégióÁtfedő|Yes|Régió átveszi a rel|Darabszám|Darabszám|Régió átveszi a rel|Nincsenek dimenziók|
|RemoveRegion (Regisztráció eltávolítása)|Yes|Régió eltávolítva|Darabszám|Darabszám|Régió eltávolítva|Region|
|ReplikációLatency|Yes|P99-replikáció késése|Ezredmásodperc|Átlag|P99 Replication Latency across source and target regions for geo-enabled account|SourceRegion, TargetRegion|
|ServerSideLatency (Kiszolgálóoldali kapcsolat)|No|Kiszolgálóoldali késés|Ezredmásodperc|Átlag|Kiszolgálóoldali késés|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Szolgáltatás rendelkezésre állása|Százalék|Átlag|Fiókkérések rendelkezésre állása egy órás, napi vagy havi részletességgel|Nincsenek dimenziók|
|SqlContainerLétrehozás|No|Sql-tároló létrehozva|Darabszám|Darabszám|Sql-tároló létrehozva|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|Sql-tároló törölve|Darabszám|Darabszám|Sql-tároló törölve|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|Sql-tároló átviteli sebességének frissítése|Darabszám|Darabszám|Sql-tároló átviteli sebességének frissítése|ResourceName, ChildResourceName, |
|SqlContainerUpdate (SqlContainerUpdate)|No|Sql-tároló frissítve|Darabszám|Darabszám|Sql-tároló frissítve|ResourceName, ChildResourceName, |
|SqlDatabaseLétrehozás|No|Sql Database létrehozva|Darabszám|Darabszám|Sql Database létrehozva|ResourceName, |
|SqlDatabaseDelete|No|Sql Database törölve|Darabszám|Darabszám|Sql Database törölve|ResourceName, |
|SqlDatabaseThroughputUpdate|No|Az SQL Database átviteli sebességének frissítése|Darabszám|Darabszám|Az SQL Database átviteli sebességének frissítése|ResourceName, |
|SqlDatabaseUpdate|No|Sql Database frissítve|Darabszám|Darabszám|Sql Database frissítve|ResourceName, |
|TableTableLétrehozás|No|Létrehozott AzureTable-tábla|Darabszám|Darabszám|Létrehozott AzureTable-tábla|ResourceName, |
|TableTableDelete|No|AzureTable-tábla törölve|Darabszám|Darabszám|AzureTable-tábla törölve|ResourceName, |
|TableTableThroughputUpdate|No|Az AzureTable tábla átviteli sebességének frissítése|Darabszám|Darabszám|Az AzureTable tábla átviteli sebességének frissítése|ResourceName, |
|TableTableUpdate (Táblatáblaupdate)|No|Frissített AzureTable-tábla|Darabszám|Darabszám|Frissített AzureTable-tábla|ResourceName, |
|TotalRequests|Yes|Összes kérelem|Darabszám|Darabszám|A kérések száma|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|Yes|Összes kérelemegység|Darabszám|Összesen|Felhasznált kérelemegységek|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys (Fiókok kulcsának frissítése)|Yes|Fiókkulcsok frissítve|Darabszám|Darabszám|Fiókkulcsok frissítve|KeyType (Kulcstípus)|
|UpdateAccountNetworkSettings|Yes|Fiók hálózati beállításai frissítve|Darabszám|Darabszám|Fiók hálózati beállításai frissítve|Nincsenek dimenziók|
|UpdateAccountReplicationSettings|Yes|Fiókreplikációs beállítások frissítve|Darabszám|Darabszám|Fiókreplikációs beállítások frissítve|Nincsenek dimenziók|
|UpdateDiagnosticsSettings|No|Fiókdiagnosztikai beállítások frissítve|Darabszám|Darabszám|Fiókdiagnosztikai beállítások frissítve|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrőértékelések|Darabszám|Összesen|A témakör esemény-előfizetései között kiértékelt összes speciális szűrő.|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Nem küldött leveles események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, nem küldött összes esemény|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény kézbesítése nem sikerült erre az esemény-előfizetésre|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Yes|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített összes esemény|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Célfeldolgozás időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount (EldobottventCount)|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Egyező események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount (KözzétételifailCount)|Yes|Sikertelen közzétételi események|Darabszám|Összesen|Az összes esemény közzététele nem sikerült ebben a témakörben|Témakör, Hibatípus, Hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishSuccessLatencyInMs|Yes|Sikeres közzététel késése|Ezredmásodperc|Összesen|Sikeres közzététel késése ezredmásodpercben|Nincsenek dimenziók|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nem küldött leveles események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, nem küldött összes esemény|DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény kézbesítése nem sikerült erre az esemény-előfizetésre|Hiba, ErrorType|
|DeliverySuccessCount|Yes|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített összes esemény|Nincsenek dimenziók|
|DestinationProcessingDurationInMs|No|Célfeldolgozás időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|Nincsenek dimenziók|
|DroppedEventCount (EldobottventCount)|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|MatchedEventCount|Yes|Egyező események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek dimenziók|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PublishFailCount (KözzétételifailCount)|Yes|Sikertelen közzétételi események|Darabszám|Összesen|Az összes esemény közzététele nem sikerült ebben a témakörben|ErrorType, Error|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek dimenziók|
|PublishSuccessLatencyInMs|Yes|Sikeres közzététel késése|Ezredmásodperc|Összesen|Sikeres közzététel késése ezredmásodpercben|Nincsenek dimenziók|
|UnmatchedEventCount|Yes|Nem egyező események|Darabszám|Összesen|Az összes esemény, amely nem egyezik a témakör esemény-előfizetésével|Nincsenek dimenziók|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nem küldött események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, nem küldött összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény kézbesítése nem sikerült erre az esemény-előfizetésre|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Célfeldolgozás időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|EventSubscriptionName|
|DroppedEventCount (EldobottventCount)|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyező események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount (KözzétételifailCount)|Yes|Sikertelen közzétételi események|Darabszám|Összesen|Az összes esemény közzététele nem sikerült ebben a témakörben|ErrorType, Error|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek dimenziók|
|PublishSuccessLatencyInMs|Yes|Sikeres közzététel késése|Ezredmásodperc|Összesen|Sikeres közzététel késése ezredmásodpercben|Nincsenek dimenziók|
|UnmatchedEventCount|Yes|Nem egyező események|Darabszám|Összesen|Az összes olyan esemény, amely nem egyezik a témakör egyik esemény-előfizetésével sem|Nincsenek dimenziók|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrőértékelések|Darabszám|Összesen|A témakör esemény-előfizetései között kiértékelt összes speciális szűrő.|EventSubscriptionName|
|DeadLetteredCount|Yes|Nem küldött események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, nem küldött összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény kézbesítése nem sikerült erre az esemény-előfizetésre|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Célfeldolgozás időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|EventSubscriptionName|
|DroppedEventCount (Eldobott száma)|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyező események|Darabszám|Összesen|Az esemény-előfizetéssel egyező összes esemény|EventSubscriptionName|
|PublishFailCount (KözzétételifailCount)|Yes|Sikertelen közzétételi események|Darabszám|Összesen|Az összes esemény közzététele nem sikerült ebben a témakörben|ErrorType, Error|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek dimenziók|
|UnmatchedEventCount|Yes|Nem egyező események|Darabszám|Összesen|Az összes olyan esemény, amely nem egyezik a témakör egyik esemény-előfizetésével sem|Nincsenek dimenziók|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrőértékelések|Darabszám|Összesen|A témakör esemény-előfizetései között kiértékelt összes speciális szűrő.|EventSubscriptionName|
|DeadLetteredCount|Yes|Nem küldött események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, nem küldött összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény nem kézbesítve ebbe az esemény-előfizetésbe|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Célfeldolgozás időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|EventSubscriptionName|
|DroppedEventCount (Eldobott száma)|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyező események|Darabszám|Összesen|Az esemény-előfizetéssel egyező összes esemény|EventSubscriptionName|
|PublishFailCount (KözzétételifailCount)|Yes|Sikertelen események közzététele|Darabszám|Összesen|Az összes esemény közzététele nem sikerült ebben a témakörben|ErrorType, Error|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek dimenziók|
|PublishSuccessLatencyInMs|Yes|Sikeres közzététel késése|Ezredmásodperc|Összesen|Sikeres közzététel késése ezredmásodpercben|Nincsenek dimenziók|
|UnmatchedEventCount|Yes|Nem egyező események|Darabszám|Összesen|Az összes olyan esemény, amely nem egyezik a témakör egyik esemény-előfizetésével sem|Nincsenek dimenziók|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrőértékelések|Darabszám|Összesen|A témakör esemény-előfizetései között kiértékelt összes speciális szűrő.|EventSubscriptionName|
|DeadLetteredCount|Yes|Nem küldött leveles események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, nem küldött összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény nem kézbesítve ebbe az esemény-előfizetésbe|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Célfeldolgozás időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|EventSubscriptionName|
|DroppedEventCount (EldobottventCount)|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyező események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount (KözzétételifailCount)|Yes|Sikertelen események közzététele|Darabszám|Összesen|Az összes esemény közzététele nem sikerült ebben a témakörben|ErrorType, Error|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek dimenziók|
|PublishSuccessLatencyInMs|Yes|Sikeres közzététel késése|Ezredmásodperc|Összesen|Sikeres közzététel késése ezredmásodpercben|Nincsenek dimenziók|
|UnmatchedEventCount|Yes|Nem egyező események|Darabszám|Összesen|Az összes esemény, amely nem egyezik a témakör esemény-előfizetésével|Nincsenek dimenziók|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft.EventHub összes aktív kapcsolata.||
|AvailableMemory (Rendelkezésre álló jegy)|No|Szabad memória|Százalék|Maximum|Az eseményközpont-fürt számára rendelkezésre álló memória a teljes memória százalékában.|Szerepkör|
|CaptureBacklog|No|Rögzítési hátralék.|Darabszám|Összesen|Rögzítési hátralék a Microsoft.EventHubhoz.||
|CapturedBytes|No|Rögzített bájtok.|Bájt|Összesen|A Microsoft.EventHub rögzített bájtja.||
|CapturedMessages|No|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft.EventHub üzenetei rögzítettek.||
|KapcsolatokBezárás|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft.EventHubhoz lezárt kapcsolatok.||
|Kapcsolatok – Bontva|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft.EventHub számára megnyitott kapcsolatok.||
|CPU|No|CPU|Százalék|Maximum|Az eseményközpont-fürt processzorkihasználtsága százalékban|Szerepkör|
|IncomingBytes (Bejövőbájtok)|Yes|Bejövő bájtok.|Bájt|Összesen|Bejövő bájtok a Microsoft.EventHubhoz.||
|IncomingMessages (Bejövő üzenetek)|Yes|Bejövő üzenetek|Darabszám|Összesen|Bejövő üzenetek a Microsoft.EventHubhoz.||
|IncomingRequests (Bejövő kérelem)|Yes|Bejövő kérelmek|Darabszám|Összesen|Bejövő kérések a Microsoft.EventHubhoz.||
|Kimenő memória (BYTE)|Yes|Kimenő bájtok.|Bájt|Összesen|Kimenő bájtok a Microsoft.EventHubhoz.||
|OutgoingMessages (Kimenő üzenetek)|Yes|Kimenő üzenetek|Darabszám|Összesen|Kimenő üzenetek a Microsoft.EventHubhoz.||
|QuotaExceededErrors (QuotaExceededErrors)|No|Kvótatúllépési hibák.|Darabszám|Összesen|Kvóta túllépte a Microsoft.EventHub hibáit.|OperationResult (Művelet eredmény)|
|ServerErrors (Kiszolgálói kiszolgálók)|No|Kiszolgálóhibák.|Darabszám|Összesen|A Microsoft.EventHub kiszolgálóhibái.|OperationResult (Művelet eredmény)|
|Méret|No|Méret|Bájt|Átlag|Az EventHub mérete bájtban.|Szerepkör|
|SuccessfulRequests|No|Sikeres kérelmek|Darabszám|Összesen|Sikeres kérések a Microsoft.EventHubhoz.|OperationResult (Művelet eredmény)|
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft.EventHubra vonatkozó kérelmek szabályozása.|OperationResult (Művelet eredmény)|
|UserErrors (UserErrors)|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft.EventHub felhasználói hibái.|OperationResult (Művelet eredmény)|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/névterek

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft.EventHub összes aktív kapcsolata.||
|CaptureBacklog|No|Rögzítési hátralék.|Darabszám|Összesen|Rögzítési hátralék a Microsoft.EventHubhoz.|EntityName (Entitásnév)|
|CapturedBytes (Rögzített memória)|No|Rögzített bájtok.|Bájt|Összesen|A Microsoft.EventHub rögzített bájtja.|EntityName (Entitásnév)|
|CapturedMessages|No|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft.EventHub üzenetei rögzítettek.|EntityName (Entitásnév)|
|KapcsolatokBezárás|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft.EventHubhoz lezárt kapcsolatok.|EntityName (Entitásnév)|
|Kapcsolatok – Bontva|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft.EventHub számára megnyitott kapcsolatok.|EntityName (Entitásnév)|
|E AKKORL|Yes|Archivált hátraléküzenetek (elavult)|Darabszám|Összesen|Eseményközpont archív üzenetei egy névtér hátralékában (elavult)||
|EHAMBS|Yes|Archivált üzenetátvitel (elavult)|Bájt|Összesen|Az eseményközpont archivált üzenetátvitele egy névtérben (elavult)||
|EHAMSGS|Yes|Archivált üzenetek (elavult)|Darabszám|Összesen|Az eseményközpont archivált üzenetei egy névtérben (elavult)||
|EHINBYTES (EHINBÁJTOK)|Yes|Bejövő bájtok (elavult)|Bájt|Összesen|Eseményközpont bejövő üzenetátvitele egy névtérhez (elavult)||
|EHINMBS|Yes|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Eseményközpont bejövő üzenetátvitele egy névtérhez. Ez a metrika elavult. Használja inkább a Bejövő bájtok metrikát (elavult)||
|EHINMSGS|Yes|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtér összes bejövő üzenete (elavult)||
|EHOUTBYTES|Yes|Kimenő bájtok (elavult)|Bájt|Összesen|Eseményközpont kimenő üzenetátvitele egy névtérhez (elavult)||
|EHOUTMBS|Yes|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Eseményközpont kimenő üzenetátvitele egy névtérhez. Ez a metrika elavult. Használja inkább a Kimenő bájtok metrikát (elavult)||
|EHOUTMSGS|Yes|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér összes kimenő üzenete (elavult)||
|FAILREQ|Yes|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérése (elavult)||
|IncomingBytes (Bejövőbájtok)|Yes|Bejövő bájtok.|Bájt|Összesen|Bejövő bájtok a Microsoft.EventHubhoz.|EntityName (Entitásnév)|
|IncomingMessages (Bejövő üzenetek)|Yes|Bejövő üzenetek|Darabszám|Összesen|Bejövő üzenetek a Microsoft.EventHubhoz.|EntityName (Entitásnév)|
|IncomingRequests (Bejövő kérelem)|Yes|Bejövő kérelmek|Darabszám|Összesen|Bejövő kérések a Microsoft.EventHubhoz.|EntityName (Entitásnév)|
|INMSGS|Yes|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Használja inkább a Bejövő üzenetek metrikát (elavult)||
|INREQS|Yes|Bejövő kérések (elavult)|Darabszám|Összesen|Névtér összes bejövő küldési kérése (elavult)||
|INTERR|Yes|Belső kiszolgálóhibák (elavult)|Darabszám|Összesen|Névtér összes belső kiszolgálóhibája (elavult)||
|MISCERR|Yes|Egyéb hibák (elavult)|Darabszám|Összesen|Egy névtér összes sikertelen kérése (elavult)||
|Kimenő memória (BYTE)|Yes|Kimenő bájtok.|Bájt|Összesen|Kimenő bájtok a Microsoft.EventHubhoz.|EntityName (Entitásnév)|
|OutgoingMessages (Kimenő üzenetek)|Yes|Kimenő üzenetek|Darabszám|Összesen|Kimenő üzenetek a Microsoft.EventHubhoz.|EntityName (Entitásnév)|
|OUTMSGS|Yes|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Használja inkább a Kimenő üzenetek metrikát (elavult)||
|QuotaExceededErrors (QuotaExceededErrors)|No|Kvótatúllépési hibák.|Darabszám|Összesen|Kvóta túllépte a Microsoft.EventHub hibáit.|EntityName, OperationResult|
|ServerErrors (Kiszolgálói kiszolgálók)|No|Kiszolgálóhibák.|Darabszám|Összesen|A Microsoft.EventHub kiszolgálóhibái.|EntityName, OperationResult|
|Méret|No|Méret|Bájt|Átlag|Az EventHub mérete bájtban.|EntityName (Entitásnév)|
|SuccessfulRequests (Sikeresrequests)|No|Sikeres kérelmek|Darabszám|Összesen|Sikeres kérések a Microsoft.EventHubhoz.|EntityName, OperationResult|
|SUCCREQ|Yes|Sikeres kérések (elavult)|Darabszám|Összesen|Névtér összes sikeres kérése (elavult)||
|SVRBSY|Yes|Foglalt kiszolgálóval kapcsolatos hibák (elavult)|Darabszám|Összesen|A névtér foglalt kiszolgálói hibáinak teljes száma (elavult)||
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft.EventHubra vonatkozó kérelmek szabályozása.|EntityName, OperationResult|
|UserErrors (UserErrors)|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft.EventHub felhasználói hibái.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|KategorizáltGatewayRequests|Yes|Kategorizált átjárókérések|Darabszám|Összesen|Átjárókérések száma kategóriák szerint (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Átjárókérések|Darabszám|Összesen|Átjárókérések száma|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|REST-proxy fogyasztói kérésének átviteli sebessége|CountPerSecond (Másodpercek száma)|Összesen|A Kafka REST-proxyra vonatkozó fogyasztói kérések száma|Gép, témakör|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Yes|REST-proxy – Sikertelen fogyasztói kérések|CountPerSecond (Másodpercek száma)|Összesen|Fogyasztói kérelmek kivételei|Gép, témakör|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|REST-proxy fogyasztói kéréseLatency|Ezredmásodperc|Átlag|Üzenetek késése egy fogyasztói kérésben a Kafka REST-proxyn keresztül|Gép, témakör|
|KafkaRestProxy.ConsumerRequestWaitingInQueueTime.p95|Yes|REST-proxy – fogyasztói kérelmek hátraléka|Ezredmásodperc|Átlag|Fogyasztói REST-proxy üzenetsorának hossza|Gép, témakör|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|REST-proxy előállítói üzenetthroughput|CountPerSecond (Másodpercek száma)|Összesen|Előállítói üzenetek száma a Kafka REST-proxyn keresztül|Gép, témakör|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|REST-proxy fogyasztói üzenetátviteli sebesség|CountPerSecond (Másodpercek száma)|Összesen|Fogyasztói üzenetek száma a Kafka REST-proxyn keresztül|Gép, témakör|
|KafkaRestProxy.OpenConnections|Yes|REST-proxy ConcurrentConnections|Darabszám|Összesen|Kafka REST-proxyn keresztüli egyidejű kapcsolatok száma|Gép, témakör|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|REST-proxy Producer RequestThroughput (REST-proxy produceri kérésének átviteli sebessége)|CountPerSecond (Másodpercek száma)|Összesen|A Kafka REST-proxyra vonatkozó előállítói kérelmek száma|Gép, témakör|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Yes|SIKERTELEN REST-proxy-előállítói kérések|CountPerSecond (Másodpercek száma)|Összesen|Előállítói kérelmek kivételei|Gép, témakör|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|REST-proxy Producer RequestLatency (REST-proxy produceri kérése)|Ezredmásodperc|Átlag|Üzenetek késése egy előállítói kérelemben Kafka REST-proxyn keresztül|Gép, témakör|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|Yes|REST-proxy produceri kérelmek hátraléka|Ezredmásodperc|Átlag|Előállító REST-proxy várakozási sorának hossza|Gép, témakör|
|NumActiveWorkers|Yes|Aktív dolgozók száma|Darabszám|Maximum|Aktív dolgozók száma|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A szolgáltatás rendelkezésre állási aránya.|Nincsenek dimenziók|
|CosmosDbCollectionSize|Yes|Cosmos DB gyűjtemény mérete|Bájt|Összesen|A háttér-gyűjtemény Cosmos DB, bájtban meg.|Nincsenek dimenziók|
|CosmosDbIndexSize|Yes|Cosmos DB indexméret|Bájt|Összesen|A biztonsági Cosmos DB gyűjtemény indexének mérete, bájtban meg.|Nincsenek dimenziók|
|CosmosDbRequestCharge|Yes|Cosmos DB ru-használat|Darabszám|Összesen|A szolgáltatás háttérszolgáltatásának kérései ru-használata Cosmos DB.|Művelet, ResourceType|
|CosmosDbRequests|Yes|Szolgáltatáskérések Cosmos DB kérelmek|Darabszám|Sum|A szolgáltatás háttérkiszolgálóinak lekért kérelmek teljes Cosmos DB.|Művelet, ResourceType|
|CosmosDbThrottleRate|Yes|Szolgáltatás Cosmos DB sebességének|Darabszám|Sum|A szolgáltatás háttérkiszolgálóitól származó 429 válasz Cosmos DB.|Művelet, ResourceType|
|IoTConnectorDeviceEvent|Yes|Bejövő üzenetek száma|Darabszám|Sum|Az Azure-beli virtuális IoT Connector az FHIR által a normalizálás előtt fogadott üzenetek teljes száma.|Művelet, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Átlagos normalizálási szakasz késése|Ezredmásodperc|Átlag|Az esemény feldolgozásának átlagos ideje és az esemény normalizálásra való feldolgozása között.|Művelet, ConnectorName|
|IoTConnectorMeasurement|Yes|Mérések száma|Darabszám|Sum|Az FHIR-hez az Azure-beli virtuális IoT Connector FHIR konverziós fázisa által fogadott normalizált értékolvasások száma.|Művelet, ConnectorName|
|IoTConnectorMeasurementGroup|Yes|Üzenetcsoportok száma|Darabszám|Sum|A mérések típus, eszköz, beteg és az FHIR konverziós fázis által generált, konfigurált időszakra vonatkozó egyedi csoportosításának teljes száma.|Művelet, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Csoportszakaszok átlagos késése|Ezredmásodperc|Átlag|Az eszközadatok IoT Connector és az FHIR-átalakítási fázis által feldolgozott adatok közötti időszak.|Művelet, ConnectorName|
|IoTConnectorNormalizedEvent|Yes|Normalizált üzenetek száma|Darabszám|Sum|Az Azure-beli virtuális eszköz FHIR-hez IoT Connector leképezett normalizált értékek teljes száma.|Művelet, ConnectorName|
|IoTConnectorTotalErrors|Yes|Hibák teljes száma|Darabszám|Sum|Az Azure IoT Connector for FHIR által naplózott hibák teljes száma|Name, Operation, ErrorType, ErrorSeverity, ConnectorName|
|ServiceApiErrors (ServiceApiErrors)|Yes|Szolgáltatási hibák|Darabszám|Sum|A szolgáltatás által generált belső kiszolgálóhibák teljes száma.|Protokoll, hitelesítés, művelet, erőforrástípus, állapotkód, statuscodeclass, statuscodetext|
|ServiceApiLatency|Yes|Szolgáltatás késése|Ezredmásodperc|Átlag|A szolgáltatás válaszkomikenciája.|Protokoll, hitelesítés, művelet, erőforrástípus, állapotkód, statuscodeclass, statuscodetext|
|ServiceApiRequests|Yes|Service Requests|Darabszám|Sum|A szolgáltatás által fogadott kérelmek teljes száma.|Protokoll, hitelesítés, művelet, erőforrástípus, állapotkód, statuscodeclass, statuscodetext|
|TotalErrors (Összesítő)|Yes|Összes hiba|Darabszám|Sum|A szolgáltatás által észlelt belső kiszolgálóhibák teljes száma.|Protocol, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency (Teljes leértés)|Yes|Teljes késés|Ezredmásodperc|Átlag|A szolgáltatás válaszkomikenciája.|Protokoll|
|TotalRequests|Yes|Összes kérelem|Darabszám|Sum|A szolgáltatás által fogadott kérelmek teljes száma.|Protokoll|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfunctions

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Átlagos CPU-kihasználtság|Százalék|Átlag|A virtuális CPU-kihasználtság teljes átlagos százalékos aránya egy perces időközönként. A virtuális CPU teljes száma a felhasználó által a termékváltozat definíciójában megadott értéken alapul. További szűrőt a termékváltozatban meghatározott RoleName alapján lehet alkalmazni.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Átlagos CPU-kihasználtság|Százalék|Átlag|A virtuális CPU-kihasználtság teljes átlagos százalékos aránya egy perces időközönként. A virtuális PROCESSZORok teljes száma a felhasználó által a termékváltozat definíciójában megadott értéken alapul. További szűrőt a termékváltozatban meghatározott RoleName alapján lehet alkalmazni.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Metrika küszöbértéke|Darabszám|Átlag|Az automatikus skálázás futtatáskor beállított automatikus méretezési küszöbértéke.|MetricTriggerRule|
|Megfigyelt kapacitás|Yes|Megfigyelt kapacitás|Darabszám|Átlag|Az automatikus skálázásról jelentett kapacitás a végrehajtáskor.||
|ObservedMetricValue (Megfigyelt érték)|Yes|Megfigyelt metrikaérték|Darabszám|Átlag|Az automatikus skálázás által a végrehajtáskor kiszámított érték|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Kezdeményezett méretezési műveletek|Darabszám|Összesen|A skálázítási művelet iránya.|ScaleDirection (Méretezési könyvtár)|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Rendelkezésre állás|Százalék|Átlag|A sikeresen befejezett rendelkezésre állási tesztek százalékos aránya|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|No|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Yes|Rendelkezésre állási teszt időtartama|Ezredmásodperc|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Yes|Lapbetöltési hálózati kapcsolat ideje|Ezredmásodperc|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. DNS-keresési és átviteli kapcsolatot tartalmaz.|Nincsenek dimenziók|
|browserTimings/processingDuration|Yes|Ügyfél feldolgozási ideje|Ezredmásodperc|Átlag|A dokumentum utolsó bájtja fogadása és a DOM betöltése közötti idő. Előfordulhat, hogy az aszinkron kérések feldolgozása még mindig feldolgozásra van szükség.|Nincsenek dimenziók|
|browserTimings/receiveDuration|Yes|Válaszidő fogadása|Ezredmásodperc|Átlag|Az első és az utolsó bájt közötti idő, vagy a leválasztásig.|Nincsenek dimenziók|
|browserTimings/sendDuration|Yes|Kérési idő küldése|Ezredmásodperc|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincsenek dimenziók|
|browserTimings/totalDuration|Yes|Böngésző lapbetöltési ideje|Ezredmásodperc|Átlag|A felhasználói kéréstől a DOM-ig be kell tölteni a stíluslapokat, a szkripteket és a képeket.|Nincsenek dimenziók|
|függőségek/darabszám|No|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által a külső erőforrásokra történő hívások száma.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/szintetikus, cloud/roleInstance, cloud/roleName|
|függőségek/időtartam|Yes|Függőségek időtartama|Ezredmásodperc|Átlag|Az alkalmazás által a külső erőforrásokra történő hívások időtartama.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/szintetikus, cloud/roleInstance, cloud/roleName|
|függőségek/sikertelen|No|Függőségi hívási hibák|Darabszám|Darabszám|Az alkalmazás által a külső erőforrásokra történő sikertelen függőségi hívások száma.|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/szintetikus, cloud/roleInstance, cloud/roleName|
|kivételek/böngésző|No|Böngészőkivételek|Darabszám|Darabszám|A böngészőben történt nem nem okozott kivételek száma.|cloud/roleName|
|kivételek/darabszám|Yes|Kivételek|Darabszám|Darabszám|Az összes nem nem összesített kivétel száma.|cloud/roleName, cloud/roleInstance, ügyfél/típus|
|kivételek/kiszolgáló|No|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálóalkalmazásban történt nem nem okozott kivételek száma.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Yes|Lapnézetek|Darabszám|Darabszám|Lapnézetek száma.|művelet/szintetikus, felhő/roleName|
|pageViews/duration|Yes|Oldal nézet betöltési ideje|Ezredmásodperc|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus, felhő/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Kivételarány|CountPerSecond (Másodpercek száma)|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételek és a .NET-kivételekké konvertált nem kezelt kivételek számát.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Igénybe vehető memória|Bájt|Átlag|Egy folyamathoz vagy rendszerhasználathoz azonnal elérhető fizikai memória.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Yes|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amely alatt az összes folyamatszál a processzort használta az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika csak a w3wp folyamat teljesítményét jelzi.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|Folyamat I/O-sebessége|Bájt/másodperc|Átlag|Fájlokba, hálózatokba és eszközökbe beolvasott és írt bájtok száma másodpercenként.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Processzoridő|Százalék|Átlag|A processzor által nem tétlen szálakban töltött idő százalékos aránya.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Privát bájtok feldolgozása|Bájt|Átlag|Kizárólag a figyelt alkalmazás folyamatához rendelt memória.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Yes|HTTP-kérés végrehajtási ideje|Ezredmásodperc|Átlag|A legutóbbi kérés végrehajtási ideje.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Yes|HTTP-kérések az alkalmazás üzenetsorában|Darabszám|Átlag|Az alkalmazáskérési várólista hossza.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Yes|HTTP-kérések sebessége|CountPerSecond (Másodpercek száma)|Átlag|Az alkalmazáshoz másodpercenkénti kérelmek száma a ASP.NET.|cloud/roleInstance|
|kérések/darabszám|No|Kiszolgálókérések|Darabszám|Darabszám|A befejezett HTTP-kérések száma.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|kérések/időtartam|Yes|Kiszolgáló válaszideja|Ezredmásodperc|Átlag|A HTTP-kérés fogadása és a válasz küldésének befejezése közötti idő.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|kérések/sikertelen|No|Sikertelen kérelmek|Darabszám|Darabszám|A sikertelenként megjelölt HTTP-kérések száma. A legtöbb esetben ezek olyan kérések, amelyek válaszkódja >= 400, és nem egyenlő 401-sel.|request/performanceBucket, request/resultCode, operation/szintetikus, cloud/roleInstance, cloud/roleName|
|kérések/sebesség|No|Kiszolgáló kérési sebessége|CountPerSecond (Másodpercek száma)|Átlag|Kiszolgálókérések másodpercenkénti aránya|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|nyomkövetések/darabszám|Yes|Hívásláncok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|trace/severityLevel, művelet/szintetikus, felhő/szerepkörNév, felhő/szerepkörTelepítés|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|c2d.commands.failure|Yes|Sikertelen parancshívások|Darabszám|Összesen|A parancsból kezdeményezett összes sikertelen IoT Central|Nincsenek dimenziók|
|c2d.commands.requestSize|Yes|Parancshívások kérésmérete|Bájt|Összesen|A parancsból kezdeményezett összes parancskérés IoT Central|Nincsenek dimenziók|
|c2d.commands.responseSize|Yes|Parancshívások válaszmérete|Bájt|Összesen|A parancsból kezdeményezett összes parancsválasz válaszmérete IoT Central|Nincsenek dimenziók|
|c2d.commands.success|Yes|Sikeres parancshívások|Darabszám|Összesen|A IoT Central|Nincsenek dimenziók|
|c2d.property.read.failure|Yes|Sikertelen eszköztulajdonság beolvassa a IoT Central|Darabszám|Összesen|A folyamatból kezdeményezett összes sikertelen tulajdonság beolvasott IoT Central|Nincsenek dimenziók|
|c2d.property.read.success|Yes|Sikeres eszköztulajdonság-olvasások a IoT Central|Darabszám|Összesen|A folyamatból kezdeményezett sikeres tulajdonságbeolvasott IoT Central|Nincsenek dimenziók|
|c2d.property.update.failure|Yes|Sikertelen eszköztulajdonság-frissítések a IoT Central|Darabszám|Összesen|A rendszer által kezdeményezett összes sikertelen tulajdonságfrissítés IoT Central|Nincsenek dimenziók|
|c2d.property.update.success|Yes|Sikeres eszköztulajdonság-frissítések a IoT Central|Darabszám|Összesen|A rendszer által kezdeményezett sikeres tulajdonságfrissítések IoT Central|Nincsenek dimenziók|
|connectedDeviceCount|No|Összes csatlakoztatott eszköz|Darabszám|Átlag|A IoT Central|Nincsenek dimenziók|
|d2c.property.read.failure|Yes|Sikertelen eszköztulajdonság-olvasások az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikertelen tulajdonság beolvasott száma|Nincsenek dimenziók|
|d2c.property.read.success|Yes|Sikeres eszköztulajdonság-olvasások az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikeres tulajdonság beolvasott száma|Nincsenek dimenziók|
|d2c.property.update.failure|Yes|Sikertelen eszköztulajdonság-frissítések az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikertelen tulajdonságfrissítések száma|Nincsenek dimenziók|
|d2c.property.update.success|Yes|Sikeres eszköztulajdonság-frissítések az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikeres tulajdonságfrissítések száma|Nincsenek dimenziók|
|d2c.telemetry.ingress.allProtocol|Yes|Telemetriai üzenetek küldési kísérletének összesen|Darabszám|Összesen|Az eszközről a felhőbe küldött telemetriai üzenetek száma az IoT Central alkalmazásnak|Nincsenek dimenziók|
|d2c.telemetry.ingress.success|Yes|Küldött telemetriai üzenetek összesen|Darabszám|Összesen|Az eszközről a felhőbe küldött telemetriai üzenetek száma, sikeresen elküldve a IoT Central alkalmazásnak|Nincsenek dimenziók|
|dataExport.error|Yes|Adatexport hibák|Darabszám|Összesen|Adatexportnál észlelt hibák száma|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|Yes|Adatexportált üzenetek szűrve|Darabszám|Összesen|A szűrőkön áthaladó üzenetek száma az adatexportban|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.received|Yes|Fogadott adatexport-üzenetek|Darabszám|Összesen|Az adatexportálásba beérkező üzenetek száma a szűrés és a bővített feldolgozás előtt|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.written|Yes|Írt adatexport-üzenetek|Darabszám|Összesen|A célhelyre írt üzenetek száma|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.statusChange|Yes|Adatexport-állapotváltozás|Darabszám|Összesen|Állapotváltozások száma|exportId, exportDisplayName, destinationId, destinationDisplayName, status|
|deviceDataUsage (eszközadatok használata)|Yes|Eszközadatok teljes kihasználtsága|Bájt|Összesen|Az alkalmazáshoz csatlakoztatott eszközökre és eszközökről átvitt IoT Central bájtok|Nincsenek dimenziók|
|provisionedDeviceCount|No|Összes kiépített eszköz|Darabszám|Átlag|Az alkalmazásban üzembe IoT Central eszközök száma|Nincsenek dimenziók|

## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|No|A szolgáltatások általános rendelkezésre állása|Százalék|Átlag|Szolgáltatáskérések rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Szolgáltatási API-találatok összesen|Darabszám|Darabszám|A szolgáltatási API-találatok teljes száma|ActivityType, ActivityName|
|ServiceApiLatency|No|A Szolgáltatási API teljes késése|Ezredmásodperc|Átlag|A szolgáltatási API-kérések teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Szolgáltatási API-k összesített eredményei|Darabszám|Darabszám|A szolgáltatási API-eredmények teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Tároló általános rendelkezésre állása|Százalék|Átlag|Tárolókérések rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Teljes tárolótelítettség|Százalék|Átlag|Felhasznált tárolókapacitás|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Szolgáltatási API-találatok összesen|Darabszám|Darabszám|A szolgáltatási API-találatok teljes száma|ActivityType, ActivityName|
|ServiceApiLatency|Yes|A Szolgáltatási API teljes késése|Ezredmásodperc|Átlag|A szolgáltatási API-kérések teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Szolgáltatási API-eredmények összesen|Darabszám|Darabszám|A szolgáltatási API-eredmények teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|A csatlakoztatott fürtben található processzormagok teljes száma|Darabszám|Összesen|A csatlakoztatott fürtben található processzormagok teljes száma||


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Batch-blobok száma|Darabszám|Átlag|Egy összesített kötegben az adatforrások száma a feldolgozáshoz.|Adatbázis|
|BatchDuration (BatchDuration)|Yes|Köteg időtartama|Másodperc|Átlag|A beolvasó folyamat összesítési fázisának időtartama.|Adatbázis|
|BatchesProcessed|Yes|Feldolgozott kötegek|Darabszám|Összesen|A feldolgozáshoz összesített kötegek száma. Köteg típusa: azt határozza meg, hogy a köteg elérte-e a kötegolási időt, az adatméretet vagy a kötegolási szabályzat által beállított fájlok számára vonatkozó korlátot|Database, SealReason|
|BatchSize|Yes|Köteg mérete|Bájt|Átlag|Tömörítetlen várt adatméret egy aggregált kötegben a feldolgozáshoz.|Adatbázis|
|BlobokDropped|Yes|Eldobott blobok|Darabszám|Összesen|Az összetevő által véglegesen elutasított blobok száma.|Database, ComponentType, ComponentName|
|BlobokFeldolgozott|Yes|Feldolgozott blobok|Darabszám|Összesen|Az összetevő által feldolgozott blobok száma.|Database, ComponentType, ComponentName|
|BlobokReceived|Yes|Fogadott blobok|Darabszám|Összesen|A bemeneti streamből egy összetevő által fogadott blobok száma.|Database, ComponentType, ComponentName|
|CacheUtilization|Yes|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|Nincsenek dimenziók|
|ContinuousExportMaxLatenessMinutes (ContinuousExportMaxLatenessMinutes)|Yes|Folyamatos exportálás maximális késése|Darabszám|Maximum|A fürtben a folyamatos exportálási feladatok által jelentett késés (percben)|Nincsenek dimenziók|
|ContinuousExportNumOfRecordsExported|Yes|Folyamatos exportálás – az exportált rekordok száma|Darabszám|Összesen|Az exportált rekordok száma, az exportálási művelet során írt összes tárolási összetevő után elbocsátva|ContinuousExportName, Database|
|ContinuousExportPendingCount|Yes|Folyamatos exportálás függőben lévő száma|Darabszám|Maximum|A végrehajtásra kész, függőben lévő folyamatos exportálási feladatok száma|Nincsenek dimenziók|
|ContinuousExportResult (Folyamatos jelentési eredmény)|Yes|Folyamatos exportálás eredménye|Darabszám|Darabszám|Jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e|ContinuousExportName, Result, Database|
|CPU|Yes|CPU|Százalék|Átlag|CPU-kihasználtsági szint|Nincsenek dimenziók|
|FelderítésLatency (Felderítés)|Yes|Felderítés késése|Másodperc|Átlag|Adatkapcsolatok jelentése (ha létezik). Az üzenetbesorolt vagy esemény létrejöttének ideje másodpercben, amíg az adatkapcsolat fel nem fedezeti. Ez az idő nem szerepel a teljes Azure Data Explorer időtartamában.|ComponentType, ComponentName|
|Események Leképezve|Yes|Eldobott események|Darabszám|Összesen|Az adatkapcsolat által véglegesen eldobott események száma. A rendszer elküld egy sikertelenség okával kapcsolatos ingestion result metrikát.|ComponentType, ComponentName|
|EseményekFeldolgozott|Yes|Feldolgozott események|Darabszám|Összesen|A fürt által feldolgozott események száma|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Yes|Feldolgozott események (Event/IoT Hubok esetén)|Darabszám|Összesen|A fürt által az eseményből/eseményből/eseményből való beúszás során feldolgozott események IoT Hub|EventStatus|
|EseményekReceived|Yes|Fogadott események|Darabszám|Összesen|Az adatkapcsolat által fogadott események száma.|ComponentType, ComponentName|
|ExportUtilization|Yes|Exportálás kihasználtsága|Százalék|Maximum|Exportálás kihasználtsága|Nincsenek dimenziók|
|IngestionLatencyInSeconds|Yes|Betöltési késés|Másodperc|Átlag|A betöltött adatok késése, az adatok fürtbeli fogadásától a lekérdezésre való előkészítésükig. A betöltési késés időtartama a betöltési forgatókönyvtől függ.|Nincsenek dimenziók|
|IngestionResult|Yes|A begesedés eredménye|Darabszám|Összesen|A bevégrehajtásához szükséges műveletek száma|IngestionResultDetails|
|IngestionUtilization|Yes|Betöltési kihasználtság|Százalék|Átlag|A fürtben használt bebecslési tárolóhelyek aránya|Nincsenek dimenziók|
|IngestionVolumeInMB|Yes|Betöltési mennyiség|Bájt|Összesen|A fürtbe betöltött adatok teljes mennyisége|Adatbázis|
|InstanceCount (Példányszám)|Yes|Példányszám|Darabszám|Átlag|Példányok teljes száma|Nincsenek dimenziók|
|KeepAlive (Eltartás)|Yes|Életben tartás|Darabszám|Átlag|A sanitás-ellenőrzés azt jelzi, hogy a fürt válaszol a lekérdezésekre|Nincsenek dimenziók|
|MaterializedViewAgeMinutes (MaterializedViewAgeMinutes)|Yes|Materializált nézet kora|Darabszám|Átlag|A materializált nézet kora percek alatt|Database, MaterializedViewName|
|MaterializedViewDataLoss (MaterializedViewDataLoss)|Yes|Materializált nézet adatvesztése|Darabszám|Maximum|Lehetséges adatvesztést jelez a materializált nézetben|Database, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|Yes|Materializált nézethez szükséges mértékek újraépítése|Darabszám|Átlag|A kiépítések száma újraépítve|Database, MaterializedViewName|
|MaterializedViewHealth (MaterializedViewHealth)|Yes|Materialized View Health|Darabszám|Átlag|A materializált nézet állapota (1 kifogástalan, 0 a nem kifogástalan állapot esetén)|Database, MaterializedViewName|
|MaterializedViewRecordsInDelta|Yes|Materialized View-rekordok a Delta-ban|Darabszám|Átlag|A nézet nem materializált részének rekordjainak száma|Database, MaterializedViewName|
|MaterializedViewResult|Yes|Materializált nézet eredménye|Darabszám|Átlag|A materializációs folyamat eredménye|Database, MaterializedViewName, Eredmény|
|QueryDuration (Lekérdezési lekérdezés)|Yes|Lekérdezés időtartama|Ezredmásodperc|Átlag|A lekérdezések időtartama másodpercben|QueryStatus (Lekérdezésállapot)|
|QueryResult (Lekérdezési eredmény)|No|Lekérdezés eredménye|Darabszám|Darabszám|A lekérdezések teljes száma.|QueryStatus (Lekérdezésállapot)|
|QueueLength (Várólista hossza)|Yes|Processzor-várólista hossza|Darabszám|Átlag|A függőben lévő üzenetek száma egy összetevő üzenetsorában.|ComponentType (Összetevő típusa)|
|QueueOldestMessage|Yes|Üzenetsor legrégebbi üzenete|Darabszám|Átlag|Az üzenetsorban lévő legrégebbi üzenet beszúrási idejét másodpercben megszabadolt idő.|ComponentType (Összetevő típusa)|
|ReceivedDataSizeBytes|Yes|Fogadott adatméret (bájt)|Bájt|Átlag|Az adatkapcsolat által fogadott adatok mérete. Ez az adatfolyam mérete, vagy a nyers adatméret, ha van ilyen.|ComponentType, ComponentName|
|StageLatency (Szakasz)|Yes|Szakasz késése|Másodperc|Átlag|Az üzenet felderítésének összesített ideje, amíg a jelentéskészítő összetevő meg nem érkezik feldolgozásra (a felderítési idő akkor van beállítva, amikor az üzenet várólistára kerül, vagy amikor az adatkapcsolat felderíti).|Database, ComponentType|
|SteamingIngestRequestRate|Yes|Streamelési betöltési kérelmek gyakorisága|Darabszám|RateRequestsPerSecond|Streamelési bemenő kérelmek sebessége (kérelmek másodpercenként)|Nincsenek dimenziók|
|StreamingIngestDataRate|Yes|Streamelés adatbetöltési sebessége|Darabszám|Átlag|Streamelési adatátviteli sebesség (MB/s)|Nincsenek dimenziók|
|StreamingIngestDuration|Yes|Streamelés betöltési időtartama|Ezredmásodperc|Átlag|Streamelési átvitel időtartama ezredmásodpercben|Nincsenek dimenziók|
|StreamingIngestResults|Yes|Streamelés betöltési eredménye|Darabszám|Átlag|Streamelési bemenő eredmények|Eredmény|
|TotalNumberOfConcurrentQueries|Yes|Egyidejű lekérdezések teljes száma|Darabszám|Maximum|Egyidejű lekérdezések teljes száma|Nincsenek dimenziók|
|TotalNumberOfExtents (TotalNumberOfExtents)|Yes|A kiterjedések teljes száma|Darabszám|Összesen|Az adatredektök teljes száma|Nincsenek dimenziók|
|TotalNumberOfThrottledCommands|Yes|A szabályozásos parancsok teljes száma|Darabszám|Összesen|A szabályozási parancsok teljes száma|CommandType (Parancstípus)|
|TotalNumberOfThrottledQueries|Yes|A leszámolt lekérdezések teljes száma|Darabszám|Maximum|A leszámolt lekérdezések teljes száma|Nincsenek dimenziók|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency (Művelet fordítása)|Yes|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek dimenziók|
|MűveletekBefogva|Yes|Műveletek befejezve |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|ActionsFailed (Műveletek le van oltva)|Yes|A műveletek sikertelenek |Darabszám|Összesen|A munkafolyamat-műveletek száma sikertelen.|Nincsenek dimenziók|
|MűveletekKisziva|Yes|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|MűveletekStarted|Yes|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|ActionsSucceeded (Sikeres műveletek)|Yes|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|ActionSuccessLatency|Yes|Művelet sikeres késleltetése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek dimenziók|
|ActionThrottledEvents (ActionThrottledEvents)|Yes|Művelet által lekért események|Darabszám|Összesen|A munkafolyamat-műveletek által lekért események száma.|Nincsenek dimenziók|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Összekötő memóriahasználata integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználata az integrációs szolgáltatási környezetben.|Nincsenek dimenziók|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|Összekötő processzorhasználata integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő processzorhasználata az integrációs szolgáltatási környezetben.|Nincsenek dimenziók|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|Munkafolyamat memóriahasználata integrációs szolgáltatási környezet|Százalék|Átlag|Munkafolyamat memóriahasználata az integrációs szolgáltatási környezetben.|Nincsenek dimenziók|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|Munkafolyamat processzorhasználata integrációs szolgáltatási környezet|Százalék|Átlag|Munkafolyamat-processzorhasználat integrációs szolgáltatási környezetben.|Nincsenek dimenziók|
|RunFailurePercentage|Yes|Futtatás sikertelenségének százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatás százalékos aránya sikertelen volt.|Nincsenek dimenziók|
|RunLatency (Futtatás lefutása)|Yes|Futtatás késése|Másodperc|Átlag|A befejezett munkafolyamat-futtatás késése.|Nincsenek dimenziók|
|FuttatásKüldve|Yes|Futtatás megszakítva|Darabszám|Összesen|A munkafolyamat-futtatások megszakított száma.|Nincsenek dimenziók|
|Futtatáscompleted|Yes|Futtatás befejezve|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek dimenziók|
|RunsFailed (Futtatás lefutott)|Yes|A futtatás sikertelen|Darabszám|Összesen|A munkafolyamat-futtatások száma sikertelen volt.|Nincsenek dimenziók|
|RunsStarted (Futtatás elindításkor)|Yes|Futtatás elindítva|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek dimenziók|
|RunsSucceeded (Sikeres futtatás)|Yes|Sikeres futtatás|Darabszám|Összesen|A munkafolyamat-futtatások sikeres száma.|Nincsenek dimenziók|
|RunStartThrottledEvents|Yes|A szabályozást kezdjen el eseményeket futtatni|Darabszám|Összesen|A munkafolyamat-futtatás indítási szabályozási eseményeinek száma.|Nincsenek dimenziók|
|RunSuccessLatency|Yes|Futtatás sikeres késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatás késése.|Nincsenek dimenziók|
|RunThrottledEvents (A RunThrottledEvents futtatása)|Yes|Lefutott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy -triggerek száma.|Nincsenek dimenziók|
|TriggerFireLatency|Yes|Trigger Fire Latency |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek dimenziók|
|TriggerLatency (TriggerLatency)|Yes|Eseményindító késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek dimenziók|
|TriggerekCompleted|Yes|Eseményindítók befejezve |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggersFailed (TriggersFailed)|Yes|Sikertelen eseményindítók |Darabszám|Összesen|A munkafolyamat-eseményindítók száma sikertelen volt.|Nincsenek dimenziók|
|Aktivált triggerek|Yes|Aktivált eseményindítók |Darabszám|Összesen|Az aktivált munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|EseményindítókKisziva|Yes|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggerekStarted|Yes|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggersSucceededed (TriggersSucceeded)|Yes|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggerSuccessLatency (TriggerSuccessLatency)|Yes|Eseményindító sikeres késleltetése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek dimenziók|
|TriggerThrottledEvents (TriggerThrottledEvents)|Yes|Lekért események aktiválása|Darabszám|Összesen|A munkafolyamat által aktivált események száma.|Nincsenek dimenziók|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency (Művelet megfelelőség)|Yes|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek dimenziók|
|MűveletekCompleted|Yes|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|ActionsFailed (Műveletek lefért)|Yes|A műveletek sikertelenek |Darabszám|Összesen|A munkafolyamat-műveletek száma sikertelen volt.|Nincsenek dimenziók|
|MűveletekKisziva|Yes|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|MűveletekStarted|Yes|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|MűveletekSucceededed|Yes|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek dimenziók|
|ActionSuccessLatency|Yes|Művelet sikerességéhez szükséges késés |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek dimenziók|
|ActionThrottledEvents (ActionThrottledEvents)|Yes|Művelet által lekért események|Darabszám|Összesen|A munkafolyamat-műveletek által lekorzott események száma.|Nincsenek dimenziók|
|BillableActionExecutions|Yes|Számlázható műveletvégrehajtások|Darabszám|Összesen|A számlázható munkafolyamat-műveletvégrehajtások száma.|Nincsenek dimenziók|
|BillableTriggerExecutions|Yes|Számlázható eseményindító-végrehajtások|Darabszám|Összesen|A munkafolyamat-eseményindító-végrehajtások száma, amelyek számlázása megszabadül.|Nincsenek dimenziók|
|BillingUsageNativeOperation|Yes|Számlázási használat natív műveletvégrehajtások esetén|Darabszám|Összesen|A kiszámlázható natív műveletvégrehajtások száma.|Nincsenek dimenziók|
|BillingUsageStandardConnector|Yes|Számlázás használata standard összekötő-végrehajtások esetén|Darabszám|Összesen|A szabványos összekötő-végrehajtások számlázása.|Nincsenek dimenziók|
|BillingUsageStorageConsumption|Yes|Számlázás használata a tárhelyhasználat-végrehajtások esetén|Darabszám|Összesen|A kiszámlázható tárhelyfelhasználási végrehajtások száma.|Nincsenek dimenziók|
|RunFailurePercentage|Yes|Futtatás sikertelenségének százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya sikertelen volt.|Nincsenek dimenziók|
|RunLatency (Futtatás lefutása)|Yes|Futtatás késése|Másodperc|Átlag|A befejezett munkafolyamat-futtatás késése.|Nincsenek dimenziók|
|RunsCancelled (Futtatásokat nem cellált)|Yes|Futtatás megszakítva|Darabszám|Összesen|A munkafolyamat-futtatások száma megszakítva.|Nincsenek dimenziók|
|FuttatásokCompleted|Yes|Futtatás befejezve|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek dimenziók|
|RunsFailed (Futtatás lefutott)|Yes|A futtatás sikertelen|Darabszám|Összesen|A munkafolyamat-futtatások száma sikertelen volt.|Nincsenek dimenziók|
|RunsStarted (Futtatás elindításkor)|Yes|Futtatás elindítva|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek dimenziók|
|RunsSucceeded (Sikeres futtatás)|Yes|Sikeres futtatás|Darabszám|Összesen|A munkafolyamat-futtatások sikeres száma.|Nincsenek dimenziók|
|RunStartThrottledEvents|Yes|A Szabályozásos indítási események futtatása|Darabszám|Összesen|A munkafolyamat-futtatás indítási szabályozási eseményeinek száma.|Nincsenek dimenziók|
|RunSuccessLatency|Yes|Futtatás sikeres késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatás késése.|Nincsenek dimenziók|
|RunThrottledEvents (RunThrottledEvents)|Yes|Szabályozásos események futtatása|Darabszám|Összesen|Munkafolyamat-műveletek vagy eseményindítók száma.|Nincsenek dimenziók|
|TotalBillableExecutions|Yes|Számlázható végrehajtások összesen|Darabszám|Összesen|A számlázható munkafolyamat-végrehajtások száma.|Nincsenek dimenziók|
|TriggerFireLatency|Yes|Trigger Fire Latency |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek dimenziók|
|TriggerLatency (TriggerLatency)|Yes|Eseményindító késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek dimenziók|
|EseményindítókCompleted|Yes|Eseményindítók befejezve |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggerekFailed|Yes|Sikertelen eseményindítók |Darabszám|Összesen|A sikertelen munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|Aktivált eseményindítók|Yes|Aktivált eseményindítók |Darabszám|Összesen|Az aktivált munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggerekKisziva|Yes|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggerekStarted|Yes|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggersSucceeded|Yes|Eseményindítók sikeresek |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek dimenziók|
|TriggerSuccessLatency (TriggerSuccessLatency)|Yes|Eseményindító sikeres késleltetése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek dimenziók|
|TriggerThrottledEvents (TriggerThrottledEvents)|Yes|Szabályozásos események aktiválása|Darabszám|Összesen|A munkafolyamat által aktivált események száma.|Nincsenek dimenziók|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Yes|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Yes|Aktív csomópontok|Darabszám|Átlag|Acitve-csomópontok száma. Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Forgatókönyv, ClusterName|
|Kért futtatás megszakítása|Yes|Kért futtatás megszakítása|Darabszám|Összesen|Azon futtatások száma, amelyekben a megszakítást kérelmezték ehhez a munkaterülethez. A szám akkor frissül, ha egy futtatás megszakítási kérése érkezett.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Megszakított futtatás|Yes|Megszakított futtatás|Darabszám|Összesen|A munkaterület futtatásának megszakított száma. A futtatás sikeres törlésekor frissül a szám.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Befejezett futtatás|Yes|Befejezett futtatás|Darabszám|Összesen|A munkaterületen sikeresen befejezett futtatások száma. A rendszer akkor frissíti a számlálót, ha a futtatás befejeződött, és a kimenet be lett gyűjtve.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuUtilization|Yes|CpuUtilization|Darabszám|Átlag|Processzorcsomópont kihasználtságának százalékos aránya. A kihasználtságot a jelentés egy perces időközönként jelenti.|Forgatókönyv, futtatásazonosító, NodeId, Fürtnév|
|Hibák|Yes|Hibák|Darabszám|Összesen|A munkaterületen előforduló futtatáshibák száma. A rendszer mindig frissíti a számlálót, ha a futtatás hibát jelez.|Eset|
|Sikertelen futtatás|Yes|Sikertelen futtatás|Darabszám|Összesen|A munkaterület sikertelen futtatásainak száma. A futtatás sikertelen futtatásakor a rendszer frissíti a számlálót.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Futtatás véglegesítő futtatásai|Yes|Futtatás véglegesítő futtatásai|Darabszám|Összesen|A munkaterület véglegesítő állapotba került futtatásszáma. A szám akkor frissül, ha egy futtatás befejeződött, de a kimeneti gyűjtemény még folyamatban van.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|Darabszám|Átlag|A GPU-csomópontok memóriakihasználtságának százalékos aránya. A kihasználtság jelentése egy perces időközönként történik.|Forgatókönyv, futtatásazonosító, NodeId, Eszközazonosító, Fürtnév|
|GpuUtilization|Yes|GpuUtilization|Darabszám|Átlag|GPU-csomópont kihasználtságának százalékos aránya. A kihasználtság jelentése egy perces időközönként történik.|Forgatókönyv, futtatásazonosító, NodeId, Eszközazonosító, Fürtnév|
|Tétlen magok|Yes|Tétlen magok|Darabszám|Átlag|Tétlen magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Yes|Tétlen csomópontok|Darabszám|Átlag|Tétlen csomópontok száma. Az inaktív csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de elfogadnak új feladatokat, ha vannak.|Forgatókönyv, ClusterName|
|Magok elhagyása|Yes|Magok elhagyása|Darabszám|Átlag|A magok elhagyása|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Yes|Csomópontok elhagyása|Darabszám|Átlag|A elhagyó csomópontok száma. A csomópontok elhagyása azok a csomópontok, amelyek éppen befejezték a feladat feldolgozását, és tétlen állapotba fognak kerülni.|Forgatókönyv, ClusterName|
|A modell üzembe helyezése sikertelen|Yes|A modell üzembe helyezése sikertelen|Darabszám|Összesen|A munkaterületen meghiúsult modelltelepítések száma|Forgatókönyv, Állapotkód|
|Modell üzembe helyezése elindítva|Yes|Modell üzembe helyezése elindítva|Darabszám|Összesen|A munkaterületen elindított modelltelepítések száma|Eset|
|A modell üzembe helyezése sikeres|Yes|A modell üzembe helyezése sikeres|Darabszám|Összesen|A munkaterületen sikeres modelltelepítések száma|Eset|
|A modell regisztere nem sikerült|Yes|A modell regisztere nem sikerült|Darabszám|Összesen|A munkaterületen meghiúsult modellregisztrációk száma|Forgatókönyv, Állapotkód|
|A modell regisztrálása sikeres|Yes|A modell regisztrálása sikeres|Darabszám|Összesen|A munkaterületen sikeres modellregisztrációk száma|Eset|
|Nem válaszoló futtatás|Yes|Nem válaszoló futtatás|Darabszám|Összesen|A munkaterületre nem válaszoló futtatások száma. A szám akkor frissül, ha egy futtatás Nem válaszol állapotba kerül.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Nincs elindítva futtatás|Yes|Nincs elindítva futtatás|Darabszám|Összesen|A munkaterület "Nincs elindítva" állapotban futtatott futtatásszáma. A rendszer akkor frissíti a számlálót, ha egy futtatás létrehozására vonatkozó kérés érkezik, de a futtatás adatai még nem voltak feltöltve. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Preempted Cores|Yes|Preempted Cores|Darabszám|Átlag|A preemptált magok száma|Forgatókönyv, ClusterName|
|Előre meghatározott csomópontok|Yes|Előre meghatározott csomópontok|Darabszám|Átlag|A preemptált csomópontok száma. Ezek a csomópontok azok az alacsony prioritású csomópontok, amelyek el vannak távolodva az elérhető csomópontkészlettől.|Forgatókönyv, ClusterName|
|Futtatás előkészítése|Yes|Futtatás előkészítése|Darabszám|Összesen|A munkaterülethez előkészítő futtatások száma. A rendszer frissíti a számlálót, amikor egy futtatás Előkészítés állapotba kerül, amíg a futtatás környezete előkészítés alatt áll.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kiépítési futtatás|Yes|Kiépítési futtatás|Darabszám|Összesen|A munkaterülethez kiépítve futtatott futtatások száma. A szám akkor frissül, ha egy futtatás a számítási cél létrehozására vagy üzembe létrehozására vár.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Várólistán lévő futtatás|Yes|Várólistán lévő futtatás|Darabszám|Összesen|A munkaterületen várólistán lévő futtatások száma. A rendszer frissíti a számlálót, amikor egy futtatás várólistára kerül a számítási célban. Akkor fordulhat elő, ha a szükséges számítási csomópontok elkészülnek.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kvóta kihasználtságának százalékos aránya|Yes|Kvóta kihasználtságának százalékos aránya|Darabszám|Átlag|A felhasznált kvóta százalékos aránya|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Elindított futtatás|Yes|Elindított futtatás|Darabszám|Összesen|A munkaterületen futó futtatások száma. A szám akkor frissül, amikor a futtatás elkezd futni a szükséges erőforrásokon.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Futtatás indítása|Yes|Futtatás indítása|Darabszám|Összesen|A munkaterületen elindított futtatások száma. A futtatás és futtatás létrehozására vonatkozó kérés (például a futtatás azonosítója) feltöltése után frissül a szám|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Magok összesen|Yes|Magok összesen|Darabszám|Átlag|Az összes mag száma|Forgatókönyv, ClusterName|
|Összes csomópont|Yes|Összes csomópont|Darabszám|Átlag|Az összes csomópont száma. Az összeg magában foglal néhány aktív csomópontot, inaktív csomópontot, nem használható csomópontot, előre meghatározott csomópontot, valamint a csomópontokat elhagyó csomópontokat|Forgatókönyv, ClusterName|
|Nem használható magok|Yes|Nem használható magok|Darabszám|Átlag|Nem használható magok száma|Forgatókönyv, ClusterName|
|Nem használható csomópontok|Yes|Nem használható csomópontok|Darabszám|Átlag|A nem használható csomópontok száma. A nem használható csomópontok valamilyen megoldhatatlan probléma miatt nem működnek. Az Azure ezeket a csomópontokat fogja újrafeldolgozni.|Forgatókönyv, ClusterName|
|Figyelmeztetések|Yes|Figyelmeztetések|Darabszám|Összesen|Futtatásra vonatkozó figyelmeztetések száma ezen a munkaterületen. A count (darabszám) frissül, ha egy futtatás figyelmeztetést kap.|Eset|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|
|Használat|No|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AssetCount (Eszközszám)|Yes|Eszközök száma|Darabszám|Átlag|Hány eszköz van már létrehozva az aktuális Media Service-fiókban|Nincsenek dimenziók|
|AssetQuota|Yes|Eszközkvóta|Darabszám|Átlag|Hány eszköz engedélyezett az aktuális media szolgáltatásfiókhoz|Nincsenek dimenziók|
|AssetQuotaUsedPercentage|Yes|Felhasznált eszközkvóta százalékos értéke|Százalék|Átlag|Az aktuális médiaszolgáltatás-fiókban használt eszköz százalékos aránya|Nincsenek dimenziók|
|ChannelsAndLiveEventsCount|Yes|Élő események száma|Darabszám|Átlag|Az aktuális Media Services-fiókban lévő élő események teljes száma|Nincsenek dimenziók|
|ContentKeyPolicyCount (TartalomkulcspolicyCount)|Yes|Tartalomkulcs-szabályzatok száma|Darabszám|Átlag|Hány tartalomkulcs-szabályzat van már létrehozva az aktuális Media Service-fiókban|Nincsenek dimenziók|
|ContentKeyPolicyQuota|Yes|Tartalomkulcs-szabályzat kvótája|Darabszám|Átlag|Hány tartalomkulcs-dokumentum van engedélyezve az aktuális Media Service-fiókhoz|Nincsenek dimenziók|
|ContentKeyPolicyQuotaUsedPercentage|Yes|A tartalomkulcs-szabályzat felhasznált kvótája (százalék)|Százalék|Átlag|Az aktuális médiaszolgáltatás-fiókban használt tartalomkulcs-szabályzat százalékos aránya|Nincsenek dimenziók|
|MaxChannelsAndLiveEventsCount|Yes|Élő események maximális kvótája|Darabszám|Maximum|Az aktuális Media Services-fiókban engedélyezett élő események maximális száma|Nincsenek dimenziók|
|MaxRunningChannelsAndLiveEventsCount|Yes|Élő események maximálisan futó kvótája|Darabszám|Maximum|Az aktuális Media Services-fiókban engedélyezett élő események maximális száma|Nincsenek dimenziók|
|RunningChannelsAndLiveEventsCount|Yes|Élő események száma|Darabszám|Átlag|Az aktuális Media Services-fiókban futó élő események teljes száma|Nincsenek dimenziók|
|StreamingPolicyCount|Yes|Streamelési szabályzatok száma|Darabszám|Átlag|Hány streamelési szabályzat van már létrehozva az aktuális Media Service-fiókban|Nincsenek dimenziók|
|StreamingPolicyQuota|Yes|Streamelési szabályzat kvótája|Darabszám|Átlag|Hány streamelési szabályzat engedélyezett az aktuális Media Service-fiókhoz|Nincsenek dimenziók|
|StreamingPolicyQuotaUsedPercentage|Yes|Streamelési szabályzat felhasznált kvótája (százalék)|Százalék|Átlag|Streamelési szabályzat az aktuális médiaszolgáltatás-fiókban használt százalékos arányban|Nincsenek dimenziók|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|IngestBitrate (IngestBitrate)|Yes|Élő esemény be- és bemenő bitének bitre való áttolása|BitsPerSecond (BitsPerSecond)|Átlag|Az élő eseményhez betöltött bejövő bit/másodperc.|TrackName (Nyomon követése)|
|IngestDriftValue|Yes|Live Event ingest drift value|Másodperc|Maximum|Eltérés a betöltött tartalom időbélyege és a rendszeróra között, perc/másodpercben mérve. A nem nulla érték azt jelzi, hogy a betöltött tartalom lassabban érkezik, mint a rendszeróra ideje.|TrackName (Nyomon követése)|
|IngestLastTimestamp (Betöltés,LastTimestamp)|Yes|Élő esemény utolsó időbélyege|Ezredmásodperc|Maximum|Élő esemény utolsó betöltött időbélyege.|TrackName (Nyomon követése)|
|LiveOutputLastTimestamp|Yes|Utolsó kimeneti időbélyeg|Ezredmásodperc|Maximum|Az élő esemény kimeneteként a tárolóba feltöltött utolsó töredék időbélyege.|TrackName (Nyomon követése)|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CPU|Yes|Processzorhasználat|Százalék|Átlag|Cpu-használat prémium streamvégponthoz. Ezek az adatok standard streamvégponthoz nem érhetők el.|Nincsenek dimenziók|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban.|OutputFormat (Kimeneti formátum)|
|EgressBandwidth|No|Bejövő sávszélesség|BitsPerSecond (BitsPerSecond)|Átlag|A bejövő sávszélesség bit/másodpercben meg van va.|Nincsenek dimenziók|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Streamvégpontra vonatkozó kérések.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|Sikeres végpontok közötti késés|Ezredmásodperc|Átlag|A sikeres kérések átlagos késése ezredmásodpercben.|OutputFormat (Kimeneti formátum)|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Aktív renderelési munkamenetek|Darabszám|Átlag|Az aktív renderelési munkamenetek teljes száma|SessionType, SDKVersion|
|AssetsConverted (Eszközökkonvertált)|Yes|Konvertált eszközök|Darabszám|Összesen|A konvertált eszközök teljes száma|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HorgonyokLétrehozva|Yes|Létrehozott horgonyok|Darabszám|Összesen|A létrehozott horgonyok száma|DeviceFamily, SDKVersion|
|Horgonyok törölve|Yes|Horgonyok törölve|Darabszám|Összesen|Törölt horgonyok száma|DeviceFamily, SDKVersion|
|AnchorsQueried|Yes|Horgonyok lekérdezve|Darabszám|Összesen|Lekérdezett Spatial Anchors lekérdezések száma|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Horgonyok frissítve|Darabszám|Összesen|Frissített horgonyok száma|DeviceFamily, SDKVersion|
|PosesFound|Yes|Testesetek találhatók|Darabszám|Összesen|A visszaadott testesetek száma|DeviceFamily, SDKVersion|
|TotalDailyAnchors (ÖsszesDailyAnchors)|Yes|Napi horgonyok összesen|Darabszám|Átlag|Horgonyok teljes száma – Naponta|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Lefoglalt készlet mérete|Bájt|Átlag|A készlet kiépítve mérete|Nincsenek dimenziók|
|VolumePoolAllocatedToVolumeThroughput|Yes|Készlet által lefoglalt átviteli sebesség|BájtokPerSecond|Átlag|A készlethez tartozó összes kötet átviteli sebességének összege|Nincsenek dimenziók|
|VolumePoolAllocatedUsed|Yes|Kötetmérethez lefoglalt készlet|Bájt|Átlag|A készlet lefoglalt használt mérete|Nincsenek dimenziók|
|VolumePoolProvisionedThroughput|Yes|A készlethez kiépített átviteli sebesség|BájtokPerSecond|Átlag|A készlet kiépített átviteli sebesség|Nincsenek dimenziók|
|VolumePoolTotalLogicalSize|Yes|Készlet felhasznált mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincsenek dimenziók|
|VolumePoolTotalSnapshotSize|Yes|A készlet teljes pillanatképmérete|Bájt|Átlag|A készletben található összes kötet pillanatkép-méretének összege|Nincsenek dimenziók|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageReadLatency (Átlagos leolvasási érték)|Yes|Átlagos olvasási késés|Ezredmásodperc|Átlag|Olvasási késés átlagos száma műveletenként ezredmásodpercben|Nincsenek dimenziók|
|AverageWriteLatency|Yes|Átlagos írási késés|Ezredmásodperc|Átlag|Átlagos írási késés műveletenként ezredmásodpercben|Nincsenek dimenziók|
|CbsVolumeBackupActive|Yes|A kötet biztonsági mentése fel van függesztve|Darabszám|Átlag|A kötet biztonsági mentési szabályzata fel van függesztve? 0, ha igen, 1, ha nem.|Nincsenek dimenziók|
|CbsVolumeLogicalBackupBytes|Yes|Kötet biztonsági mentése (bájt)|Bájt|Átlag|A kötetről biztonságimentett összes bájt.|Nincsenek dimenziók|
|CbsVolumeOperationComplete|Yes|Befejeződött a kötet biztonsági mentési művelete|Darabszám|Átlag|Sikeresen befejeződött a kötet legutóbbi biztonsági mentési vagy visszaállítási művelete? 1, ha igen, 0, ha nem.|Nincsenek dimenziók|
|CbsVolumeOperationTransferredBytes|Yes|Kötet biztonsági mentése – legutóbb átvitt bájtok|Bájt|Átlag|Az utolsó biztonsági mentési vagy visszaállítási művelethez átvitt összes bájt.|Nincsenek dimenziók|
|CbsVolumeProtected|Yes|Engedélyezve van a kötet biztonsági mentése|Darabszám|Átlag|Engedélyezve van a kötet biztonsági mentése? 1, ha igen, 0, ha nem.|Nincsenek dimenziók|
|OtherThroughput (Egyéb átviteli sebesség)|Yes|Egyéb átviteli sebesség|BájtperSecond (bájt/másodperc)|Átlag|Egyéb átviteli sebesség (nem olvasási vagy írási) bájt/másodpercben|Nincsenek dimenziók|
|ReadIops (Olvasásiops)|Yes|Olvasási iops|CountPerSecond (Másodpercek száma)|Átlag|Másodpercenkénti be- és ki- és beolvasott műveletek|Nincsenek dimenziók|
|Olvasási sebesség|Yes|Olvasási átviteli sebesség|BájtperSecond (bájt/másodperc)|Átlag|Olvasási átviteli sebesség bájt/másodpercben|Nincsenek dimenziók|
|Teljes átviteli sebesség|Yes|Teljes átviteli sebesség|BájtokPerSecond|Átlag|Az összes átviteli sebesség összege bájt/másodpercben|Nincsenek dimenziók|
|VolumeAllocatedSize|Yes|Lefoglalt kötet mérete|Bájt|Átlag|Egy kötet kiépített mérete|Nincsenek dimenziók|
|VolumeConsumedSizePercentage|Yes|Százalékos felhasznált kötetméret|Százalék|Átlag|A felhasznált kötet százalékos aránya, beleértve a pillanatképeket is.|Nincsenek dimenziók|
|VolumeLogicalSize (Kötetlogizálás)|Yes|Felhasznált kötet mérete|Bájt|Átlag|A kötet logikai mérete (bájtban)|Nincsenek dimenziók|
|VolumeSnapshotSize|Yes|Kötet pillanatképének mérete|Bájt|Átlag|A köteten található összes pillanatkép mérete|Nincsenek dimenziók|
|WriteIops (Írásiops)|Yes|Írási iops|CountPerSecond (Másodpercek száma)|Átlag|Be- és kiírási műveletek másodpercenként|Nincsenek dimenziók|
|Írási sebesség|Yes|Írási teljesítmény|BájtperSecond (bájt/másodperc)|Átlag|Átviteli sebesség írása bájt/másodpercben|Nincsenek dimenziók|
|XregionReplicationHealthy|Yes|Kifogástalan állapotú-e a kötetreplikáció|Darabszám|Átlag|A kapcsolat feltétele, 1 vagy 0.|Nincsenek dimenziók|
|XregionReplicationLagTime|Yes|Kötetreplikáció késési ideje|Másodperc|Átlag|Az az idő másodpercben, amely alatt a tükrözött adatok a forrás mögött maradnak.|Nincsenek dimenziók|
|XregionReplicationLastTransferDuration|Yes|Kötetreplikáció utolsó átvitelének időtartama|Másodperc|Átlag|Az utolsó átvitel befejezéséhez szükséges idő másodpercben.|Nincsenek dimenziók|
|XregionReplicationLastTransferSize|Yes|Kötetreplikáció utolsó átvitelének mérete|Bájt|Átlag|Az utolsó átvitel részeként átvitt bájtok teljes száma.|Nincsenek dimenziók|
|XregionReplicationRelationshipProgress|Yes|Kötetreplikáció állapota|Bájt|Átlag|Az aktuális átviteli művelethez átvitt adatok teljes mennyisége.|Nincsenek dimenziók|
|XregionReplicationRelationshipTransferring|Yes|Kötetreplikáció átvitele|Darabszám|Átlag|Azt határozza meg, hogy a kötetreplikáció állapota "átvitel"-e.|Nincsenek dimenziók|
|XregionReplicationTotalTransferBytes|Yes|Kötetreplikáció teljes átvitele|Bájt|Átlag|A kapcsolathoz átvitt összegző bájtok.|Nincsenek dimenziók|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway teljes idő|Ezredmásodperc|Átlag|A kérelmek feldolgozásának átlagos ideje és a válasza elküldését. Ez a kiszámított érték a http Application Gateway első bájtja és a válasz-küldési művelet befejezésének ideje közötti időtartam átlaga. Fontos megjegyezni, hogy ez általában magában foglalja Application Gateway feldolgozási időt, azt az időt, amikor Application Gateway kérés- és válaszcsomagok áthaladnak a hálózaton, valamint azt az időt, amely alatt Application Gateway háttérkiszolgáló válaszolt.|Figyelő|
|AvgRequestCountPerHealthyHost|No|Kérések percenként kifogástalan állapotú gazdagépenként|Darabszám|Átlag|A kérések percenkénti átlagos száma kifogástalan állapotú háttér gazdagépenként egy készletben|BackendSettingsPool|
|BackendConnectTime|No|Háttér-csatlakozás ideje|Ezredmásodperc|Átlag|A háttérkiszolgálóval való kapcsolat kialakításával töltött idő|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Háttér– első bájt válaszideja|Ezredmásodperc|Átlag|A háttérkiszolgálóval való kapcsolat létesítésének kezdete és a válaszfejléc első bájtjának fogadása közötti idő, a háttérkiszolgáló feldolgozási idejét közelítő idő|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|No|Háttér–utolsó bájt válaszidő|Ezredmásodperc|Átlag|A háttérkiszolgálóval létesített kapcsolat létrehozásának kezdete és a válasz törzsének utolsó bájtjának fogadása között eltelt idő|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Yes|Háttér válaszának állapota|Darabszám|Összesen|A háttértagok által létrehozott HTTP-válaszkódok száma. Ez nem tartalmazza az alkalmazás által létrehozott Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount (Letiltva száma)|Yes|Web Application Firewall kérelmek szabályelosztása|Darabszám|Összesen|Web Application Firewall kérelmek szabályelosztása|RuleGroup, RuleId|
|BlockedReqCount|Yes|Web Application Firewall kérelmek száma|Darabszám|Összesen|Web Application Firewall kérelmek száma|Nincsenek dimenziók|
|BájtokReceived|Yes|Fogadott bájtok|Bájt|Összesen|Az ügyfelektől fogadott Application Gateway bájtok teljes száma|Figyelő|
|BájtokSent (bájt)|Yes|Küldött bájtok|Bájt|Összesen|Az ügyfél által az ügyfeleknek küldött Application Gateway bájtok teljes száma|Figyelő|
|CapacityUnits (Kapacitásegységek)|No|Aktuális kapacitásegységek|Darabszám|Átlag|Felhasznált kapacitásegységek|Nincsenek dimenziók|
|ClientRtt|No|Client RTT|Ezredmásodperc|Átlag|Az ügyfelek és az ügyfelek közötti átlagos Application Gateway. Ez a metrika jelzi, hogy mennyi ideig tart a kapcsolatok létrehozása és nyugtázások visszaadése|Figyelő|
|ComputeUnits (Számítási egység)|No|Aktuális számítási egységek|Darabszám|Átlag|Felhasznált számítási egységek|Nincsenek dimenziók|
|CpuUtilization|No|Processzor kihasználtsága|Százalék|Átlag|A processzor aktuális CPU-Application Gateway|Nincsenek dimenziók|
|CurrentConnections (Aktuális kapcsolat)|Yes|Aktuális kapcsolatok|Darabszám|Összesen|A meglévő kapcsolatokkal létrehozott Application Gateway|Nincsenek dimenziók|
|EstimatedBilledCapacityUnits|No|Becsült számlázható kapacitásegységek|Darabszám|Átlag|A fizetendő kapacitásegységek becsült száma|Nincsenek dimenziók|
|FailedRequests|Yes|Sikertelen kérelmek|Darabszám|Összesen|A szolgáltatás által kiszolgált Application Gateway kérelmek száma|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Fix számlázandó kapacitásegységek|Darabszám|Átlag|A minimálisan fizetendő kapacitásegységek|Nincsenek dimenziók|
|HealthyHostCount|Yes|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Kifogástalan állapotú háttérbeli gazdagépek száma|BackendSettingsPool|
|MatchedCount (Megfeleltetve száma)|Yes|Web Application Firewall szabályelosztás teljes száma|Darabszám|Összesen|Web Application Firewall bejövő forgalomra vonatkozó összes szabályelosztási szabály eloszlása|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Új kapcsolatok másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Új kapcsolatok létesítése másodpercenként Application Gateway|Nincsenek dimenziók|
|ResponseStatus (Válaszállapot)|Yes|Válasz állapota|Darabszám|Összesen|A http-válasz állapota, amelyet a Application Gateway|HttpStatusGroup|
|Teljesítmény|No|Teljesítmény|BájtperSecond (bájt/másodperc)|Átlag|A másodpercenként kiszolgált Application Gateway bájtok száma|Nincsenek dimenziók|
|TlsProtocol|Yes|Ügyfél TLS protokollja|Darabszám|Összesen|Az ügyfél által kezdeményezett TLS- és nem TLS-kérelmek száma, amely kapcsolatot létesítet a Application Gateway. A TLS-protokoll terjesztésének megtekintéséhez szűrje a TLS-protokoll dimenziót.|Listener, TlsProtocol|
|TotalRequests|Yes|Összes kérelem|Darabszám|Összesen|A sikeresen kiszolgált Application Gateway száma|BackendSettingsPool|
|UnhealthyHostCount|Yes|Nemhealthy gazdagépek száma|Darabszám|Átlag|Nem kifogástalan állapotú háttérbeli gazdagépek száma|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Alkalmazásszabályok találatszáma|Darabszám|Összesen|Az alkalmazásszabályok lekért száma|Állapot, ok, protokoll|
|DataProcessed (Feldolgozva)|Yes|Feldolgozott adatok|Bájt|Összesen|A tűzfal által feldolgozott adatok teljes mennyisége|Nincsenek dimenziók|
|FirewallHealth (Tűzfal-biztonság)|Yes|Tűzfal állapota|Százalék|Átlag|A tűzfal általános állapotát jelzi|Állapot, Ok|
|NetworkRuleHit|Yes|Hálózati szabályok találatszáma|Darabszám|Összesen|A hálózati szabályok lekért száma|Állapot, ok, protokoll|
|SNATPortUtilization|Yes|SNAT-port kihasználtsága|Százalék|Átlag|A jelenleg használatban lévő kimenő SNAT-portok százalékos aránya|Protokoll|
|Teljesítmény|No|Teljesítmény|BitsPerSecond (BitsPerSecond)|Átlag|A tűzfal által feldolgozott átviteli sebesség|Nincsenek dimenziók|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|pingmesh|No|Bastion kommunikációs állapota|Darabszám|Átlag|A kommunikáció állapota 1, ha minden kommunikáció jó, és 0, ha hibás.||
|Ülés|No|Session Count|Darabszám|Összesen|A megerősített munkamenetek száma. View in sum and per instance.|gazda|
|összesen|Yes|Teljes memória|Darabszám|Átlag|Összes memóriastatisztikák.|gazda|
|usage_user|No|Felhasznált CPU|Darabszám|Átlag|CPU-használati statisztikák.|cpu, gazdagép|
|protokollok|Yes|Felhasznált memória|Darabszám|Átlag|Memóriahasználati statisztikák.|gazda|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond (BitsInPerSecond)|Yes|BitsInPerSecond (BitsInPerSecond)|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek dimenziók|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek dimenziók|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume (Lekérdezéskötet)|No|Lekérdezési kötet|Darabszám|Összesen|A DNS-zónához kiszolgált lekérdezések száma|Nincsenek dimenziók|
|RecordSetCapacityUtilization|No|Rekordhalmaz kapacitáskihasználtsága|Százalék|Maximum|A DNS-zóna által használt rekordhalmaz-kapacitás százalékos aránya|Nincsenek dimenziók|
|RecordSetCount (Rekordkészlet száma)|No|Rekordhalmazok száma|Darabszám|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincsenek dimenziók|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ArpAvailability (ArpAvailability)|Yes|Arp rendelkezésre állása|Százalék|Átlag|Az MSEE-től az összes társ felé irányuló ARP-rendelkezésre állás.|Társviszony-létesítés típusa, társ|
|BgpAvailability (BgpAvailability)|Yes|BGP rendelkezésre állása|Százalék|Átlag|BGP-rendelkezésre állás az MSEE-ből az összes társ felé.|Társviszony-létesítés típusa, társ|
|BitsInPerSecond (BitsInPerSecond)|No|BitsInPerSecond (BitsInPerSecond)|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond (DroppedInBitsPerSecond)|BitsPerSecond (BitsPerSecond)|Átlag|Másodpercenként eldobott bejövő adat bitek|Nincsenek dimenziók|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond (EldobottbitekPerSecond)|BitsPerSecond (BitsPerSecond)|Átlag|Másodpercenként eldobott adat-visszalépési bitek|Nincsenek dimenziók|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond (BitsInPerSecond)|Yes|BitsInPerSecond (BitsInPerSecond)|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek dimenziók|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek dimenziók|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond (BitsInPerSecond)|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|ConnectionName (Kapcsolat neve)|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|ConnectionName (Kapcsolat neve)|
|ExpressRouteGatewayCountOfRoutesAdvertatlanToPeer|Yes|Társviszonyba meghirdetett útvonalak száma (előzetes verzió)|Darabszám|Maximum|Az ExpressRouteGateway által társviszonyba hirdetett útvonalak száma|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|A társviszonyból (előzetes verzió) tanult útvonalak száma|Darabszám|Maximum|Az ExpressRouteGateway által a társviszonyból megtanult útvonalak száma|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayCpuUtilization|Yes|Processzorhasználat|Darabszám|Átlag|Az ExpressRoute-átjáró CPU-kihasználtsága|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Útvonalak gyakoriságának módosítása (előzetes verzió)|Darabszám|Összesen|Az útvonalak gyakoriságának módosítása az ExpressRoute-átjáróban|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayNumberOfVmInVnet|No|A virtuális gépek száma a Virtual Network (előzetes verzió)|Darabszám|Maximum|A virtuális gépek száma a Virtual Network|Nincsenek dimenziók|
|ExpressRouteGatewayPacketsPerSecond|No|Csomagok száma másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|ExpressRoute-átjáró csomagszáma|roleInstance (szerepkörtelepítés)|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdminState (Rendszergazdai állam)|Yes|AdminState (Rendszergazdai állam)|Darabszám|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol (Sorösszeg)|Yes|LineProtocol (Sorösszeg)|Darabszám|Átlag|A port vonali protokolljának állapota|Hivatkozás|
|PortBitsInPerSecond|Yes|BitsInPerSecond (BitsInPerSecond)|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Hivatkozás|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond (BitsPerSecond)|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Hivatkozás|
|RxLightLevel|Yes|RxLightLevel|Darabszám|Átlag|Rx Light level in dBm (Rx világos szintje dBm-ben)|Link, Sáv|
|TxLightLevel|Yes|TxLightLevel|Darabszám|Átlag|Tx fényszint dBm-ben|Link, Sáv|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Háttér-állapot százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérkiszolgálókra történő sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészlet|
|BackendRequestCount|Yes|Háttérbeli kérelmek száma|Darabszám|Összesen|A HTTP/S proxyról a háttérkiszolgálóknak küldött kérések száma|HttpStatus, HttpStatusGroup, Háttér|
|BackendRequestLatency|Yes|Háttérkérések késése|Ezredmásodperc|Átlag|A HTTP/S proxy által a háttérnek küldött kérésből kiszámított idő, amíg a HTTP/S-proxy nem kapta meg a háttértől az utolsó válasz bájtot|Háttérrendszer|
|BillableResponseSize|Yes|Számlázható válaszméret|Bájt|Összesen|A HTTP/S proxytól az ügyfeleknek válaszként küldött számlázható bájtok száma (kérésenként legalább 2 KB).|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount (Kérelem száma)|Yes|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfélkérések száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize (Kérések megszagorizálás|Yes|Kérés mérete|Bájt|Összesen|Az ügyfelektől a HTTP/S proxynak küldött kérésként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize (Válaszszava)|Yes|Válasz mérete|Bájt|Összesen|A HTTP/S proxy által az ügyfeleknek válaszként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency (Teljes levallatencia)|Yes|Teljes késés|Ezredmásodperc|Átlag|A HTTP/S proxy ügyfélkérésének bekérése és az utolsó bájt a HTTP/S proxytól való nyugtázásáig számított idő|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall kérelmek száma|Darabszám|Összesen|Az ügyfél által feldolgozott ügyfélkérések Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts (Lefoglalt portok)|No|Lefoglalt SNAT-portok|Darabszám|Átlag|Az adott időszakban lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount (Szám)|Yes|Bájtok száma|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability (DipAvailability)|Yes|Állapotadat-mintavétel|Darabszám|Átlag|Átlagos Load Balancer állapot-állapot időtartamonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount (Csomagszám)|Yes|Csomagok száma|Darabszám|Összesen|Az adott időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Yes|SNAT-kapcsolat száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|SYN-szám|Darabszám|Összesen|Az adott időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts (Felhasznált portok)|No|Használt SNAT-portok|Darabszám|Átlag|Az adott időszakban használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability (VipAvailability)|Yes|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Átlagos Load Balancer elérési út rendelkezésre állása időtartamonként|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bájt|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Protokoll, Irány|
|DatapathAvailability (DatapathAvailability)|Yes|Adatáttárak rendelkezésre állása (előzetes verzió)|Darabszám|Átlag|NAT Gateway Datapath rendelkezésre állása|Nincsenek dimenziók|
|PacketCount (Csomagszám)|Yes|Csomagok|Darabszám|Összesen|Az adott időszakon belül továbbított csomagok teljes száma|Protokoll, Irány|
|PacketDropCount|Yes|Eldobott csomagok|Darabszám|Összesen|Eldobott csomagok száma|Nincsenek dimenziók|
|SNATConnectionCount|Yes|SNAT-kapcsolat száma|Darabszám|Összesen|Egyidejű aktív kapcsolatok összesen|Protokoll, ConnectionState|
|TotalConnectionCount|Yes|SNAT-kapcsolat teljes száma|Darabszám|Összesen|Aktív SNAT-kapcsolatok teljes száma|Protokoll|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceivedRate (bájtokreceivedRate)|Yes|Fogadott bájtok|Bájt|Összesen|A hálózati adapter által fogadott bájtok száma|Nincsenek dimenziók|
|BájtokSentRate|Yes|Küldött bájtok|Bájt|Összesen|A hálózati adapter által küldött bájtok száma|Nincsenek dimenziók|
|PacketsReceivedRate|Yes|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|Nincsenek dimenziók|
|PacketsSentRate|Yes|Elküldött csomagok|Darabszám|Összesen|A hálózati adapter által küldött csomagok száma|Nincsenek dimenziók|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Átlagos körbeúti idő (ms) (klasszikus)|Ezredmásodperc|Átlag|A forrás és a cél között küldött kapcsolatfigyelési mintavételek átlagos hálózati adattitkozási ideje (ms)|Nincsenek dimenziók|
|EllenőrzésekFailedPercent|Yes|A sikertelenségi arány ellenőrzései|Százalék|Átlag|A kapcsolatfigyelési ellenőrzések %-a sikertelen|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|Sikertelen mintavételek %-os (klasszikus)|Százalék|Átlag|A kapcsolatfigyelési mintavételek %-a sikertelen|Nincsenek dimenziók|
|RoundTripTimeMs (RoundTripTimeMs)|Yes|Round-Trip idő (ms)|Ezredmásodperc|Átlag|Adatmásodpercben megtöltve a kapcsolatfigyelési ellenőrzésekhez|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult (Teszt eredménye)|Yes|Teszt eredménye|Darabszám|Átlag|Kapcsolatfigyelő teszteredménye|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Átjáró P2S sávszélessége|BájtokPerSecond|Átlag|Az átjárók átlagos pont–hely sávszélessége bájt/másodpercben|Nincsenek dimenziók|
|P2SConnectionCount|Yes|P2S-kapcsolatok száma|Darabszám|Összesen|Az átjáró P2S-kapcsolatainak száma|Protokoll|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume (Lekérdezéskötet)|Yes|Lekérdezési kötet|Darabszám|Összesen|Egy adott zónához kiszolgált saját DNS száma|Nincsenek dimenziók|
|RecordSetCapacityUtilization|No|Rekordhalmaz kapacitáskihasználtsága|Százalék|Maximum|A rekordhalmaz kapacitásának százalékos kihasználtása saját DNS zónában|Nincsenek dimenziók|
|RecordSetCount|Yes|Rekordhalmazok száma|Darabszám|Maximum|Rekordhalmazok száma egy saját DNS zónában|Nincsenek dimenziók|
|VirtualNetworkLinkCapacityUtilization|No|Virtual Network kapcsolat kapacitáskihasználtságának növelése|Százalék|Maximum|A Virtual Network kapcsolati kapacitás kihasznált saját DNS százaléka|Nincsenek dimenziók|
|VirtualNetworkLinkCount|Yes|Virtual Network hivatkozásszám|Darabszám|Maximum|A virtuális zónához kapcsolt virtuális hálózatok saját DNS száma|Nincsenek dimenziók|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Virtual Network kapcsolat kapacitáskihasználtsága|Százalék|Maximum|A Virtual Network használt automatikus regisztrációs kapacitással való összekapcsolás százalékos aránya saját DNS zónában|Nincsenek dimenziók|
|VirtualNetworkWithRegistrationLinkCount|Yes|Virtual Network hivatkozásszáma|Darabszám|Maximum|A virtuális hálózathoz kapcsolt virtuális hálózatok saját DNS automatikus regisztráció engedélyezése mellett|Nincsenek dimenziók|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PEBytesIn (PeBytesIn)|Yes|Bájtok be|Darabszám|Összesen|A ki nem bájtok teljes száma|PrivateEndpointId (PrivateEndpointId)|
|PEBytesOut|Yes|Bájtok ki|Darabszám|Összesen|A ki nem bájtok teljes száma|PrivateEndpointId (PrivateEndpointId)|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Bájtok be|Darabszám|Összesen|Bájtok teljes száma|PrivateLinkServiceId (Privát kapcsolat szolgáltatásazonosítója)|
|PLSBytesOut|Yes|Bájtok ki|Darabszám|Összesen|Bájtok teljes száma|PrivateLinkServiceId (Privát kapcsolat szolgáltatásazonosítója)|
|PLSNatPortsUsage|Yes|Nat-portok használata|Százalék|Átlag|Nat-portok használata|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount (Szám)|Yes|Bájtok száma|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Port, Irány|
|BájtokDroppedDDoS|Yes|Eldobott DDoS bejövő bájtok|BájtperSecond (bájt/másodperc)|Maximum|Eldobott DDoS bejövő bájtok|Nincsenek dimenziók|
|BytesForwardedDDoS|Yes|Bejövő továbbított DDoS bájtok|BájtperSecond (bájt/másodperc)|Maximum|Bejövő továbbított DDoS bájtok|Nincsenek dimenziók|
|BytesInDDos (BájtindDoS)|Yes|Bejövő bájtok DDoS|BájtokPerSecond|Maximum|Bejövő bájtok DDoS|Nincsenek dimenziók|
|DDoSTriggerSYNPackets|Yes|DDoS elleni védelem aktiválására szolgáló bejövő SYN-csomagok|CountPerSecond (Másodpercek száma)|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő SYN-csomagok|Nincsenek dimenziók|
|DDoSTriggerTCPPackets|Yes|DDoS elleni védelem aktiválására szolgáló bejövő TCP-csomagok|CountPerSecond (Másodpercek száma)|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő TCP-csomagok|Nincsenek dimenziók|
|DDoSTriggerUDPPackets|Yes|DDoS elleni védelem aktiválására szolgáló bejövő UDP-csomagok|CountPerSecond (Másodpercek száma)|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő UDP-csomagok|Nincsenek dimenziók|
|IfUnderDDoSAttack|Yes|DDoS-támadás alatt vagy nem|Darabszám|Maximum|DDoS-támadás alatt vagy nem|Nincsenek dimenziók|
|PacketCount (Csomagszám)|Yes|Csomagok száma|Darabszám|Összesen|Az adott időszakon belül továbbított csomagok teljes száma|Port, Irány|
|PacketsDroppedDDoS|Yes|Bejövő csomagok eldobott DDoS-e|CountPerSecond (Másodpercek száma)|Maximum|Bejövő csomagok eldobott DDoS-e|Nincsenek dimenziók|
|PacketsForwardedDDoS|Yes|Bejövő, továbbított DDoS-csomagok|CountPerSecond (Másodpercek száma)|Maximum|Bejövő, továbbított DDoS-csomagok|Nincsenek dimenziók|
|PacketsInDDoS|Yes|Bejövő csomagok DDoS|CountPerSecond (Másodpercek száma)|Maximum|Bejövő csomagok DDoS|Nincsenek dimenziók|
|SynCount|Yes|SYN-szám|Darabszám|Összesen|Az adott időszakon belül továbbított SYN-csomagok teljes száma|Port, Irány|
|TCPBytesDroppedDDoS|Yes|Eldobott DDoS bejövő TCP-bájtok|BájtokPerSecond|Maximum|Bejövő TCP-bájtok eldobott DDoS|Nincsenek dimenziók|
|TCPBytesForwardedDDoS|Yes|Bejövő TCP bájtos továbbított DDoS|BájtokPerSecond|Maximum|Bejövő TCP bájtos továbbított DDoS|Nincsenek dimenziók|
|TCPBytesInDDoS|Yes|Bejövő TCP-bájtok DDoS-ként|BájtokPerSecond|Maximum|Bejövő TCP-bájtok DDoS-ként|Nincsenek dimenziók|
|TCPPacketsDroppedDDoS|Yes|A bejövő TCP-csomagok eldobott DDoS-t|CountPerSecond (Másodpercek száma)|Maximum|A bejövő TCP-csomagok eldobott DDoS-t|Nincsenek dimenziók|
|TCPPacketsForwardedDDoS|Yes|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond (Másodpercek száma)|Maximum|Bejövő TCP-csomagok továbbított DDoS-ként|Nincsenek dimenziók|
|TCPPacketsInDDoS|Yes|Bejövő TCP-csomagok DDoS|CountPerSecond (Másodpercek száma)|Maximum|Bejövő TCP-csomagok DDoS|Nincsenek dimenziók|
|UDPBytesDroppedDDoS|Yes|Bejövő UDP bájtok eldobott DDoS|BájtperSecond (bájt/másodperc)|Maximum|Bejövő UDP bájtok eldobott DDoS|Nincsenek dimenziók|
|UDPBytesForwardedDDoS|Yes|Bejövő UDP bájtok által továbbított DDoS|BájtperSecond (bájt/másodperc)|Maximum|Bejövő UDP bájtok által továbbított DDoS|Nincsenek dimenziók|
|UDPBytesInDDoS|Yes|Bejövő UDP bájtok DDoS-ként|BájtokPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincsenek dimenziók|
|UDPPacketsDroppedDDoS|Yes|A bejövő UDP-csomagok eldobott DDoS-t|CountPerSecond (Másodpercek száma)|Maximum|A bejövő UDP-csomagok eldobott DDoS-t|Nincsenek dimenziók|
|UDPPacketsForwardedDDoS|Yes|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond (Másodpercek száma)|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincsenek dimenziók|
|UDPPacketsInDDoS|Yes|Bejövő UDP-csomagok DDoS|CountPerSecond (Másodpercek száma)|Maximum|Bejövő UDP-csomagok DDoS|Nincsenek dimenziók|
|VipAvailability (VipAvailability)|Yes|Adatelérési út rendelkezésre állása|Darabszám|Átlag|ÁTLAGOS IP-cím rendelkezésre állása időtartamonként|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Végpont állapota végpont szerint|Darabszám|Maximum|1, ha a végpont mintavételi állapota "Engedélyezve", egyéb esetben 0.|EndpointName (Végpont neve)|
|QpsByEndpoint|Yes|Lekérdezések visszaadott végpont szerint|Darabszám|Összesen|A szám, ahányszor Traffic Manager végpont vissza lett adva az adott időkeretben|EndpointName (Végpont neve)|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Átjáró S2S sávszélessége|BájtperSecond (bájt/másodperc)|Átlag|Az átjárók átlagos, bájt/s sávszélessége a telephelyek között|Nincsenek dimenziók|
|ExpressRouteGatewayCountOfRoutesAdvert ésToPeer|Yes|Társviszonyba meghirdetett útvonalak száma (előzetes verzió)|Darabszám|Maximum|Az ExpressRouteGateway által társviszonyba hirdetett útvonalak száma|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|A társviszonyból (előzetes verzió) tanult útvonalak száma|Darabszám|Maximum|Az ExpressRouteGateway által a társviszonyból megtanult útvonalak száma|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayCpuUtilization|Yes|Processzorhasználat|Darabszám|Átlag|Az ExpressRoute-átjáró CPU-kihasználtsága|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Útvonalak változásának gyakorisága (előzetes verzió)|Darabszám|Összesen|Útvonalak gyakoriságának módosítása az ExpressRoute-átjáróban|roleInstance (szerepkörtelepítés)|
|ExpressRouteGatewayNumberOfVmInVnet|No|A virtuális gépek száma a Virtual Network (előzetes verzió)|Darabszám|Maximum|A virtuális gépek száma a Virtual Network|Nincsenek dimenziók|
|ExpressRouteGatewayPacketsPerSecond|No|Csomagok száma másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|ExpressRoute-átjáró csomagszáma|roleInstance (szerepkörtelepítés)|
|P2SBandwidth|Yes|Átjáró P2S sávszélessége|BájtperSecond (bájt/másodperc)|Átlag|Az átjárók átlagos pont–hely sávszélessége bájt/másodpercben|Nincsenek dimenziók|
|P2SConnectionCount|Yes|P2S-kapcsolatok száma|Darabszám|Maximum|Az átjáró P2S-kapcsolatainak száma|Protokoll|
|TunnelAverageBandwidth|Yes|Alagút sávszélessége|BájtperSecond (bájt/másodperc)|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Alagút bejövő csomagja|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|Alagút NAT-foglalásai|Darabszám|Összesen|Egy alagút NAT-szabályának foglalásai száma|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes|No|Alagút NATed bájtban|Bájt|Összesen|A NAT-szabály által az alagúton NAT-n átszámolt bájtok száma |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|No|NaTed-csomagok bújtatása|Darabszám|Összesen|Az alagúton NAT-szabály által NAT-n keresztül küldött csomagok száma|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|No|Alagút NAT-folyamatait|Darabszám|Összesen|Az alagúton a NAT-folyamatok száma folyamattípus és NAT-szabály szerint|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Alagút NAT-csomagvesztése|Darabszám|Összesen|Az alagúton eldobástípus és NAT-szabály szerint eldobott NATed csomagok száma|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|Alagút fordított NATed bájtja|Bájt|Összesen|Az alagúton NAT-szabály által fordított NAT-val visszafejtett bájtok száma|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Fordított nated csomagok alagútba való bújtatása|Darabszám|Összesen|Azon csomagok száma az alagúton, amelyek NAT-szabály által fordított NAT-val voltak visszafejtve|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Virtuális gép pingelésének oda-visszaúti ideje|Ezredmásodperc|Átlag|A cél virtuális gépre küldött pingelések körbeúti ideje|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Virtuális gép pingelése meghiúsult|Százalék|Átlag|A cél virtuális gép összes elküldött pingelésének sikertelen pingelésének százalékos aránya|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Társviszony-létesítésAvailability (Társviszony-létesítés)|Yes|BGP rendelkezésre állása|Százalék|Átlag|BGP-rendelkezésre állás a VirtualRouter és a távoli társviszonyok között|Társ|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth (Átlagos sávok)|Yes|Átjáró S2S sávszélessége|BájtokPerSecond|Átlag|Az átjárók átlagos, bájt/s sávszélessége a telephelyek között|Nincsenek dimenziók|
|TunnelAverageBandwidth|Yes|Alagút sávszélessége|BájtperSecond (bájt/másodperc)|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Alagút bejövő csomagja|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|Alagút NAT-foglalásai|Darabszám|Összesen|Egy alagút NAT-szabályának foglalásai száma|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes (AlagútnatedBytes)|No|Alagút NATed bájtban|Bájt|Összesen|A NAT-szabály által az alagúton NAT-n átszámolt bájtok száma |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|No|NaTed-csomagok bújtatása|Darabszám|Összesen|Az alagúton NAT-szabály által NAT-n keresztül küldött csomagok száma|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|No|Alagút NAT-folyamatait|Darabszám|Összesen|Az alagúton a NAT-folyamatok száma folyamattípus és NAT-szabály szerint|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Alagút NAT-csomagvesztése|Darabszám|Összesen|Az alagúton eldobott NATed csomagok száma eldobástípus és NAT-szabály szerint|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|Alagút fordított NATed bájtja|Bájt|Összesen|Azon bájtok száma, amelyek nat-szabály által fordított NAT-val voltak megtenve az alagúton|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Fordított nated-csomagok alagútba való bújtatása|Darabszám|Összesen|Azon csomagok száma az alagúton, amelyek NAT-szabály által fordított NAT-val voltak visszafejtve|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Bejövő|Yes|Bejövő üzenetek|Darabszám|Összesen|Az összes sikeres KÜLDÉS API-hívás száma. |Nincsenek dimenziók|
|incoming.all.failedrequests|Yes|Minden sikertelen bejövő kérés|Darabszám|Összesen|Egy értesítési központ összes sikertelen bejövő kérése|Nincsenek dimenziók|
|incoming.all.requests|Yes|Minden bejövő kérelem|Darabszám|Összesen|Értesítési központ összes bejövő kérése|Nincsenek dimenziók|
|incoming.scheduled|Yes|Ütemezett leküldéses értesítések küldve|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek dimenziók|
|incoming.scheduled.cancel|Yes|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek dimenziók|
|installation.all|Yes|Telepítéskezelési műveletek|Darabszám|Összesen|Telepítéskezelési műveletek|Nincsenek dimenziók|
|installation.delete|Yes|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|Nincsenek dimenziók|
|installation.get|Yes|Telepítési műveletek lekérte|Darabszám|Összesen|Telepítési műveletek lekérte|Nincsenek dimenziók|
|installation.patch|Yes|Javítási telepítési műveletek|Darabszám|Összesen|Javítási telepítési műveletek|Nincsenek dimenziók|
|installation.upsert|Yes|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|Nincsenek dimenziók|
|notificationhub.pushes|Yes|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|Nincsenek dimenziók|
|outgoing.allpns.badorexpiredchannel|Yes|Hibás vagy lejárt csatornahibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a channel/token/registrationId lejárt vagy érvénytelen volt.|Nincsenek dimenziók|
|outgoing.allpns.channelerror|Yes|Csatornahibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen volt, nem a megfelelő alkalmazáshoz van társítva vagy lejárt.|Nincsenek dimenziók|
|outgoing.allpns.invalidpayload|Yes|Hasznos terhelési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS rossz hasznos terhelést adott vissza.|Nincsenek dimenziók|
|outgoing.allpns.pnserror|Yes|Külső értesítési rendszer hibái|Darabszám|Összesen|A PNS-sel kommunikáló probléma miatt meghiúsult leküldések száma (kivéve a hitelesítési problémákat).|Nincsenek dimenziók|
|outgoing.allpns.success|Yes|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek dimenziók|
|outgoing.apns.badchannel|Yes|Az APNS hibás csatorna hibája|Darabszám|Összesen|A jogkivonat érvénytelen miatt meghiúsult leküldések száma (APNS-állapotkód: 8).|Nincsenek dimenziók|
|outgoing.apns.expiredchannel|Yes|Lejárt APNS-csatornahiba|Darabszám|Összesen|Az APNS visszajelzési csatorna által érvénytelenné vált jogkivonatok száma.|Nincsenek dimenziók|
|outgoing.apns.invalidcredentials|Yes|APNS-engedélyezési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadja el a megadott hitelesítő adatokat, vagy a hitelesítő adatok blokkolva vannak.|Nincsenek dimenziók|
|outgoing.apns.invalidnotificationsize|Yes|Érvénytelen értesítésméret-hiba az APNS-sel|Darabszám|Összesen|A hasznos adat túl nagy volt miatt meghiúsult leküldések száma (APNS-állapotkód: 7).|Nincsenek dimenziók|
|outgoing.apns.pnserror|Yes|APNS-hibák|Darabszám|Összesen|Az APNS-sel kommunikáló hibák miatt meghiúsult leküldések száma.|Nincsenek dimenziók|
|outgoing.apns.success|Yes|Sikeres APNS-értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek dimenziók|
|outgoing.gcm.authenticationerror|Yes|GCM-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadja el a megadott hitelesítő adatokat, a hitelesítő adatok blokkolva vannak, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM-eredmény: Nem egyezőSenderId).|Nincsenek dimenziók|
|outgoing.gcm.badchannel|Yes|GCM – Hibás csatorna hiba|Darabszám|Összesen|A regisztráció regisztrációsazonosítója nem ismerhető fel (GCM-eredmény: Érvénytelen regisztráció) sikertelen leküldések száma.|Nincsenek dimenziók|
|outgoing.gcm.expiredchannel|Yes|Lejárt GCM-csatorna hibája|Darabszám|Összesen|A regisztrációban a registrationId lejárt miatt meghiúsult leküldések száma (GCM-eredmény: NotRegistered).|Nincsenek dimenziók|
|outgoing.gcm.invalidcredentials|Yes|GCM-engedélyezési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadja el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek dimenziók|
|outgoing.gcm.invalidnotificationformat|Yes|A GCM érvénytelen értesítési formátuma|Darabszám|Összesen|A hasznos adatok helytelen formázása miatt meghiúsult leküldések száma (GCM-eredmény: InvalidDataKey vagy InvalidTtl).|Nincsenek dimenziók|
|outgoing.gcm.invalidnotificationsize|Yes|Érvénytelen értesítésméret-hiba a GCM-en|Darabszám|Összesen|A sikertelen leküldések száma, mert a hasznos adat túl nagy volt (GCM-eredmény: MessageTooBig).|Nincsenek dimenziók|
|outgoing.gcm.pnserror|Yes|GCM-hibák|Darabszám|Összesen|A GCM-el kommunikáló hibák miatt meghiúsult leküldések száma.|Nincsenek dimenziók|
|outgoing.gcm.success|Yes|Sikeres GCM-értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek dimenziók|
|outgoing.gcm.throttled|Yes|GCM – Szabályozásos értesítések|Darabszám|Összesen|A GCM által az alkalmazás szabályozása miatt meghiúsult leküldések száma (GCM-állapotkód: 501-599 vagy eredmény:Nem érhető el).|Nincsenek dimenziók|
|outgoing.gcm.wrongchannel|Yes|A GCM hibás csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban lévő registrationId nincs társítva az aktuális alkalmazáshoz (GCM-eredmény: InvalidPackageName).|Nincsenek dimenziók|
|outgoing.mpns.authenticationerror|Yes|MPNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadja el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek dimenziók|
|outgoing.mpns.badchannel|Yes|Az MPNS hibás csatorna hibája|Darabszám|Összesen|A regisztráció channelURI-ját nem sikerült felismerni (MPNS-állapot: 404 nem található) sikertelen leküldések száma.|Nincsenek dimenziók|
|outgoing.mpns.channeldisconnected|Yes|MpNS-csatorna leválasztva|Darabszám|Összesen|A regisztrációban található ChannelURI kapcsolat megszakadása miatt meghiúsult leküldések száma (MPNS-állapot: 412 nem található).|Nincsenek dimenziók|
|outgoing.mpns.dropped|Yes|Az MPNS eldobott értesítései|Darabszám|Összesen|Az MPNS által eldobott leküldések száma (MPNS-válaszfejléc: X-NotificationStatus: QueueFull vagy Suppressed).|Nincsenek dimenziók|
|outgoing.mpns.invalidcredentials|Yes|Érvénytelen MPNS-hitelesítő adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadja el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek dimenziók|
|outgoing.mpns.invalidnotificationformat|Yes|Az MPNS érvénytelen értesítési formátuma|Darabszám|Összesen|A sikertelen leküldések száma, mert az értesítés hasznos adata túl nagy volt.|Nincsenek dimenziók|
|outgoing.mpns.pnserror|Yes|MPNS-hibák|Darabszám|Összesen|Az MPNS-sel kommunikáló hibák miatt meghiúsult leküldések száma.|Nincsenek dimenziók|
|outgoing.mpns.success|Yes|Sikeres MPNS-értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek dimenziók|
|outgoing.mpns.throttled|Yes|MPNS - szabályozásos értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert az MPNS az alkalmazás szabályozása alatt áll (WNS MPNS: 406 Nem elfogadható).|Nincsenek dimenziók|
|outgoing.wns.authenticationerror|Yes|WNS-hitelesítési hibák|Darabszám|Összesen|Az értesítés nem történt meg a Windows Live érvénytelen hitelesítő adataival vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincsenek dimenziók|
|outgoing.wns.badchannel|Yes|Hibás csatorna WNS-hibája|Darabszám|Összesen|A regisztrációban található ChannelURI nem ismerhető fel (a WNS-állapot: 404 nem található) sikertelen leküldések száma.|Nincsenek dimenziók|
|outgoing.wns.channeldisconnected|Yes|WNS-csatorna leválasztva|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban található ChannelURI le lett kortálva (WNS-válaszfejléc: X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek dimenziók|
|outgoing.wns.channelthrottled|Yes|WNS-csatorna szabályozása|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban található ChannelURI le van szabályozás alatt (WNS-válaszfejléc: X-WNS-NotificationStatus:channelThrottled).|Nincsenek dimenziók|
|outgoing.wns.dropped|Yes|Eldobott WNS-értesítések|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban található ChannelURI le van szabályozás alatt (X-WNS-NotificationStatus: el lett dobott, de nem lett X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek dimenziók|
|outgoing.wns.expiredchannel|Yes|Lejárt WNS-csatornahiba|Darabszám|Összesen|A channelURI lejárt miatt meghiúsult leküldések száma (WNS-állapot: 410 Eltűnt).|Nincsenek dimenziók|
|outgoing.wns.invalidcredentials|Yes|WNS-engedélyezési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadja el a megadott hitelesítő adatokat, vagy a hitelesítő adatok blokkolva vannak. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincsenek dimenziók|
|outgoing.wns.invalidnotificationformat|Yes|A WNS érvénytelen értesítési formátuma|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS-állapot: 400). Vegye figyelembe, hogy a WNS nem utasít el minden érvénytelen hasznos adatot.|Nincsenek dimenziók|
|outgoing.wns.invalidnotificationsize|Yes|Érvénytelen értesítésméret-hiba a WNS-ben|Darabszám|Összesen|Az értesítés hasznos adata túl nagy (WNS-állapot: 413).|Nincsenek dimenziók|
|outgoing.wns.invalidtoken|Yes|WNS-engedélyezési hibák (érvénytelen jogkivonat)|Darabszám|Összesen|A WNS-hez megadott jogkivonat érvénytelen (WNS-állapot: 401 – Jogosulatlan).|Nincsenek dimenziók|
|outgoing.wns.pnserror|Yes|WNS-hibák|Darabszám|Összesen|Az értesítés a WNS-sel kommunikáló hibák miatt nem történt meg.|Nincsenek dimenziók|
|outgoing.wns.success|Yes|Sikeres WNS-értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek dimenziók|
|outgoing.wns.throttled|Yes|WNS –szabályozás alatt|Darabszám|Összesen|A sikertelen leküldések száma, mert a WNS az alkalmazás szabályozását (WNS-állapot: 406 Nem elfogadható).|Nincsenek dimenziók|
|outgoing.wns.tokenproviderunreachable|Yes|WNS-engedélyezési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem elérhető.|Nincsenek dimenziók|
|outgoing.wns.wrongtoken|Yes|WNS-engedélyezési hibák (helytelen jogkivonat)|Darabszám|Összesen|A WNS-nek megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS-állapot: 403 – Tiltott). Ez akkor fordulhat elő, ha a regisztrációban a ChannelURI egy másik alkalmazáshoz van társítva. Ellenőrizze, hogy az ügyfélalkalmazás ugyanhoz az alkalmazáshoz van-e társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincsenek dimenziók|
|registration.all|Yes|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet száma (a létrehozás frissíti a lekérdezéseket és a törléseket). |Nincsenek dimenziók|
|registration.create|Yes|Regisztrációs létrehozási műveletek|Darabszám|Összesen|A sikeres regisztrációk száma.|Nincsenek dimenziók|
|registration.delete|Yes|Regisztrációs törlési műveletek|Darabszám|Összesen|A sikeres regisztrációtörlések száma.|Nincsenek dimenziók|
|registration.get|Yes|Regisztrációs olvasási műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs lekérdezés száma.|Nincsenek dimenziók|
|registration.update|Yes|Regisztrációs frissítési műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs frissítés száma.|Nincsenek dimenziók|
|scheduled.pending|Yes|Ütemezett értesítések függőben|Darabszám|Összesen|Ütemezett értesítések függőben|Nincsenek dimenziók|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Average_%-os rendelkezésre álló memória|Yes|Rendelkezésre álló memória %-os rendelkezésre áll|Darabszám|Átlag|Average_%-os rendelkezésre álló memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló százalékos felcserélési terület|Yes|Rendelkezésre álló felcserélési terület %-ban|Darabszám|Átlag|Average_ rendelkezésre álló százalékos felcserélési terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban lekötött bájtok használata|Yes|Használatban van a lekötött bájtok %-a|Darabszám|Átlag|Average_%-ban lekötött bájtok használata|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ DPC-idő %-ban|Yes|DPC-idő %-ban|Darabszám|Átlag|Average_ DPC-idő %-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os ingyenes inode-k|Yes|% Ingyenes inode-k|Darabszám|Átlag|Average_%-os ingyenes inode-k|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% szabad terület|Yes|Szabad terület %-ban|Darabszám|Átlag|Average_% szabad terület|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os üresjárati idő|Yes|Üresjáratban eltöltött időhányad (%)|Darabszám|Átlag|Average_%-os üresjárati idő|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Yes|Megszakítási idő (% )|Darabszám|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ IO-várakozási idő %-os arányában|Yes|IO-várakozási idő (% )|Darabszám|Átlag|Average_ IO-várakozási idő %-os arányában|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% szép idő|Yes|Szép idő %-ban|Darabszám|Átlag|Average_% szép idő|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% emelt szintű idő|Yes|Emelt szintű jogosultsági idő (% )|Darabszám|Átlag|Average_%-os emelt szintű jogosultsági idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os processzoridő|Yes|A processzor kihasználtsága (%)|Darabszám|Átlag|Average_%-os processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban használt inode-k|Yes|Felhasznált %-os inode-k|Darabszám|Átlag|Average_%-ban használt inode-k|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban felhasznált memória|Yes|Felhasznált memória %-os százaléka|Darabszám|Átlag|Average_%-ban felhasznált memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban felhasznált terület|Yes|Felhasznált terület %-ban|Darabszám|Átlag|Average_%-ban felhasznált terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban felhasznált felcserélési terület|Yes|Felhasznált felcserélési terület %-os arányában|Darabszám|Átlag|Average_%-ban felhasznált felcserélési terület|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Yes|Felhasználói idő (% )|Darabszám|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabájt|Yes|Rendelkezésre álló memória (megabájt)|Darabszám|Átlag|Average_Available megabájt|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB memória|Yes|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB memória|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MByte-os felcserélés|Yes|Rendelkezésre álló mbájtok felcserélhető mérete|Darabszám|Átlag|Average_Available MByte-os felcserélés|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Yes|Átlagos lemez mp/olvasás|Darabszám|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/átvitele|Yes|Átlagos lemez mp/átvitel|Darabszám|Átlag|Average_Avg. Lemez mp/átvitele|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Yes|Átlagos lemez mp/írás|Darabszám|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes fogadott másodpercenként|Yes|Fogadott bájtok másodpercenként|Darabszám|Átlag|Average_Bytes fogadott másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes másodpercenként|Yes|Küldött bájtok másodpercenként|Darabszám|Átlag|Average_Bytes másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes/mp|Yes|Bájtok száma másodpercenként|Darabszám|Átlag|Average_Bytes/mp|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current-várólista hossza|Yes|Aktuális lemez-várólista hossza|Darabszám|Átlag|Average_Current-várólista hossza|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk olvasási bájt/s|Yes|Lemez olvasási bájt/s|Darabszám|Átlag|Average_Disk olvasási bájt/s|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk másodpercenként|Yes|Lemez olvasása másodpercenként|Darabszám|Átlag|Average_Disk másodpercenként|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk átvitelek másodpercenként|Yes|Lemezátvitelek másodpercenként|Darabszám|Átlag|Average_Disk átvitelek másodpercenként|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk írása (bájt/s)|Yes|Lemezírás (bájt/s)|Darabszám|Átlag|Average_Disk írása (bájt/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk írások másodpercenként|Yes|Lemezírások másodpercenként|Darabszám|Átlag|Average_Disk írások másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabájt|Yes|Ingyenes megabájtok|Darabszám|Átlag|Average_Free Megabájt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fizikai memória|Yes|Szabad fizikai memória|Darabszám|Átlag|Average_Free fizikai memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free térköz a lapozófájlokban|Yes|Szabad terület a lapozófájlokban|Darabszám|Átlag|Average_Free térköz a lapozófájlokban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free memória|Yes|Ingyenes virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical lemez (bájt/s) száma|Yes|Logikai lemez (bájt/s)|Darabszám|Átlag|Average_Logical lemez (bájt/s)|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page másodpercenként|Yes|Lap olvasása másodpercenként|Darabszám|Átlag|Average_Page másodpercenként|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page írások másodpercenkénti száma|Yes|Lapírások másodpercenként|Darabszám|Átlag|Average_Page írások másodpercenkénti száma|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/mp|Yes|Oldalak/mp|Darabszám|Átlag|Average_Pages/mp|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|Yes|Pct Privileged Time|Darabszám|Átlag|Average_Pct emelt szintű idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|Yes|Felhasználói idő lekért ideje|Darabszám|Átlag|Average_Pct felhasználói idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical lemez másodpercenkénti száma|Yes|Fizikai lemez (bájt/s)|Darabszám|Átlag|Average_Physical lemez másodpercenkénti száma|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Yes|Folyamatok|Darabszám|Átlag|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Yes|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size fájlokban tárolt adatok|Yes|Lapozófájlokban tárolt méret|Darabszám|Átlag|Average_Size fájlokban tárolt adatok|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bájtok|Yes|Összes bájt|Darabszám|Átlag|Average_Total bájtok|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total fogadott bájtok|Yes|Fogadott bájtok összesen|Darabszám|Átlag|Average_Total fogadott bájtok|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bájtok átvitele|Yes|Továbbított bájtok összesen|Darabszám|Átlag|Average_Total bájtok átvitele|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total ütközések|Yes|Teljes ütközések|Darabszám|Átlag|Average_Total ütközések|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Yes|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Yes|Összes továbbított csomag|Darabszám|Átlag|Average_Total továbbított csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx-hibák|Yes|Rx-hibák összesen|Darabszám|Átlag|Average_Total Rx-hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx-hibák|Yes|Tx-hibák összesen|Darabszám|Átlag|Average_Total Tx-hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Yes|Üzemidő|Darabszám|Átlag|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used mb/s felcserélési terület|Yes|Felhasznált felcserélési terület (MB)|Darabszám|Átlag|Average_Used mb/s felcserélési terület|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória (kByte)|Yes|Felhasznált memória (kByte)|Darabszám|Átlag|Average_Used memória (kByte)|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória (MB)|Yes|Felhasznált memória (MB)|Darabszám|Átlag|Average_Used memória (MB)|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Yes|Felhasználók|Darabszám|Átlag|Average_Users|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual memória|Yes|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual memória|Számítógép, ObjectName, InstanceName, CounterPath, SourceSystem|
|Esemény|Yes|Esemény|Darabszám|Átlag|Esemény|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Szívverés|Yes|Szívverés|Darabszám|Összesen|Szívverés|Számítógép, OSType, Verzió, SourceComputerId|
|Frissítés|Yes|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, besorolás, updateState, választható, jóváhagyott|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Bejövő forgalom sebessége|BitsPerSecond (BitsPerSecond)|Átlag|A bejövő forgalom sebessége bit/másodpercben|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Bejövő forgalom sebessége|BitsPerSecond (BitsPerSecond)|Átlag|Bejövő forgalom sebessége bit/másodpercben|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability (Munkamenet-elágazhatóság)|Yes|Munkamenet rendelkezésre állása|Darabszám|Átlag|A társviszony-létesítés munkamenetének rendelkezésre állása|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ElőtagLatency|Yes|Előtag késése|Ezredmásodperc|Átlag|Előtagok késésének mediánja|PrefixName (Előtagnév)|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Memória (Gen1)|Bájt|Átlag|Memory. 0–3 GB tartomány az A1-hez, 0–5 GB az A2-hez, 0–10 GB az A3- és 0–25 GB-os A4-hez, 0–50 GB az A5-hez, és 0–100 GB az A6-ban. Csak az 1. Power BI Embedded-erőforrásokhoz támogatott.|Nincsenek dimenziók|
|memory_thrashing_metric|Yes|Memóriaakvadozás (adatkészletek) (Gen1)|Százalék|Átlag|Átlagos memóriaakkorazás. Csak az 1. Power BI Embedded-erőforrásokhoz támogatott.|Nincsenek dimenziók|
|qpu_high_utilization_metric|Yes|QPU magas kihasználtsága (Gen1)|Darabszám|Összesen|Magas QPU-kihasználtság az elmúlt percben, 1 magas QPU-kihasználtság, egyéb esetben 0. Csak az 1. Power BI Embedded erőforrásokhoz támogatott.|Nincsenek dimenziók|
|QueryDuration (Lekérdezési lekérdezés)|Yes|Lekérdezések időtartama (adatkészletek) (Gen1)|Ezredmásodperc|Átlag|A DAX-lekérdezések időtartama az utolsó intervallumban. Csak az 1. Power BI Embedded erőforrásokhoz támogatott.|Nincsenek dimenziók|
|QueryPoolJobQueueLength|Yes|Lekérdezéskészlet-feladat várólistájának hossza (adatkészletek) (Gen1)|Darabszám|Átlag|A lekérdezési szálkészlet várólistájának feladatszáma. Csak az 1. Power BI Embedded erőforrásokhoz támogatott.|Nincsenek dimenziók|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ScanCancelled (Vizsgálat)|Yes|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított vizsgálatok számát jelzi.||
|ScanCompleted (Beolvasás beolvasva)|Yes|Vizsgálat befejezve|Darabszám|Összesen|A sikeresen befejezett vizsgálatok számát jelzi.||
|ScanFailed (Beolvasás beolvasva)|Yes|A vizsgálat sikertelen|Darabszám|Összesen|A sikertelen vizsgálatok számát jelzi.||
|ScanTimeTaken (Beolvasás és beolvasás ideje)|Yes|Vizsgálathoz szükséges idő|Másodperc|Összesen|A teljes vizsgálati időt jelzi másodpercben.||


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/névterek

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft.Relay összes aktív kapcsolata.|EntityName (Entitásnév)|
|ActiveListeners (ActiveListeners)|No|ActiveListeners (ActiveListeners)|Darabszám|Összesen|A Microsoft.Relay összes ActiveListeners listája.|EntityName (Entitásnév)|
|BytesTransferred (Bájtok átkövetkeztetve)|Yes|BytesTransferred (Bájtok átkövetkeztetve)|Bájt|Összesen|Total BytesTransferred for Microsoft.Relay.|EntityName (Entitásnév)|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Darabszám|Összesen|ClientError on ListenerConnections for Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Darabszám|Összesen|ServerError on ListenerConnections for Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-Success|No|ListenerConnections-Success|Darabszám|Összesen|Sikeres figyelőCsatlakozások a Microsoft.Relayhez.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Darabszám|Összesen|A Microsoft.Relay összes listenerConnections szolgáltatása.|EntityName (Entitásnév)|
|ListenerDisconnects (Figyelőlemezek)|No|ListenerDisconnects (Figyelőlemezek)|Darabszám|Összesen|A Microsoft.Relayhez kapcsolódó összes ListenerDisconnects.|EntityName (Entitásnév)|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Darabszám|Összesen|ClientError on SenderConnections for Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Darabszám|Összesen|ServerError on SenderConnections for Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|No|SenderConnections-Success|Darabszám|Összesen|Sikeres SenderConnections a Microsoft.Relayhez.|EntityName, OperationResult|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Darabszám|Összesen|Összes SenderConnections-kérelem a Microsoft.Relayhez.|EntityName (Entitásnév)|
|SenderDisconnects|No|SenderDisconnects|Darabszám|Összesen|Összes SenderDisconnects a Microsoft.Relayhez.|EntityName (Entitásnév)|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Késés|Yes|HTTP bejövő kérelmek késési adatai|Darabszám|Átlag|HTTP bejövő kérelmek késési adatai|Method, Namespace, RequestRegion, ResourceType, Microsoft.SubscriptionId|
|Adatforgalom|Yes|Adatforgalom|Darabszám|Átlag|HTTP-forgalom|RequestRegion, StatusCode, StatusCodeClass, ResourceType, Namespace, Microsoft.SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|SearchLatency (Keresés keresése)|Yes|Keresés késése|Másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|Nincsenek dimenziók|
|SearchQueriesPerSecond|Yes|Keresési lekérdezések másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincsenek dimenziók|
|ThrottledSearchQueriesPercentage|Yes|Szabályozásos keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatáshoz lekért keresési lekérdezések százalékos aránya|Nincsenek dimenziók|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/névterek

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft.ServiceBus összes aktív kapcsolata.||
|ActiveMessages|No|Üzenetsorban/témakörben lévő aktív üzenetek száma.|Darabszám|Átlag|Üzenetsorban/témakörben lévő aktív üzenetek száma.|EntityName (Entitásnév)|
|KapcsolatokBezárás|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft.ServiceBus számára lezárt kapcsolatok.|EntityName (Entitásnév)|
|Kapcsolatok – Bontva|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft.ServiceBus számára megnyitott kapcsolatok.|EntityName (Entitásnév)|
|CPUXNS|No|CPU (elavult)|Százalék|Maximum|Prémium szintű Service Bus-névtér CPU-használati metrika. Ez a metrika nincs megszűkülve. Használja inkább a CPU-metrikát (NamespaceCpuUsage).|Replika|
|DeadletteredMessages|No|Az üzenetsorban/témakörben található, nem küldött üzenetek száma.|Darabszám|Átlag|Az üzenetsorban/témakörben található nem szöveges üzenetek száma.|EntityName (Entitásnév)|
|IncomingMessages (Bejövő üzenetek)|Yes|Bejövő üzenetek|Darabszám|Összesen|A Microsoft.ServiceBus bejövő üzenetei.|EntityName (Entitásnév)|
|IncomingRequests (Bejövő kérelem)|Yes|Bejövő kérelmek|Darabszám|Összesen|A Microsoft.ServiceBus bejövő kérései.|EntityName (Entitásnév)|
|Üzenetek|No|Üzenetsor/téma üzeneteinek száma.|Darabszám|Átlag|Üzenetsor/téma üzeneteinek száma.|EntityName (Entitásnév)|
|NamespaceCpuUsage|No|CPU|Százalék|Maximum|Prémium szintű Service Bus-névtér CPU-használati metrika.|Replika|
|NamespaceMemoryUsage|No|Memóriahasználat|Százalék|Maximum|Prémium szintű Service Bus-névtér memóriahasználati metrika.|Replika|
|OutgoingMessages (Kimenő üzenetek)|Yes|Kimenő üzenetek|Darabszám|Összesen|Kimenő üzenetek a Microsoft.ServiceBus szolgáltatáshoz.|EntityName (Entitásnév)|
|ScheduledMessages|No|Ütemezett üzenetek száma egy üzenetsorban/témakörben.|Darabszám|Átlag|Ütemezett üzenetek száma egy üzenetsorban vagy témakörben.|EntityName (Entitásnév)|
|ServerErrors (Kiszolgálói kiszolgálók)|No|Kiszolgálóhibák.|Darabszám|Összesen|A Microsoft.ServiceBus kiszolgálóhibái.|EntityName, OperationResult|
|Méret|No|Méret|Bájt|Átlag|Üzenetsor/témakör mérete bájtban.|EntityName (Entitásnév)|
|SuccessfulRequests (Sikeresrequests)|No|Sikeres kérelmek|Darabszám|Összesen|Névtér sikeres kérései összesen|EntityName, OperationResult|
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft.ServiceBus kérelmek szabályozása.|EntityName, OperationResult|
|UserErrors (UserErrors)|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft.ServiceBus felhasználói hibái.|EntityName, OperationResult|
|WSXNS|No|Memóriahasználat (elavult)|Százalék|Maximum|Prémium szintű Service Bus-névtér memóriahasználati metrika. Ez a metrika elavult. Használja inkább a Memóriahasználat (NamespaceMemoryUsage) metrikát.|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActualCpu (Ténylegespu)|No|ActualCpu (Ténylegespu)|Darabszám|Átlag|Tényleges CPU-használat milli magban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory (Tényleges megjegyezés)|No|ActualMemory (Tényleges megjegyezés)|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu (Lefoglalt memória)|No|AllocatedCpu (Lefoglalt memória)|Darabszám|Átlag|Ehhez a tárolóhoz hozzárendelt processzor milli magban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory (Lefoglalt memória)|No|AllocatedMemory (Lefoglalt memória)|Bájt|Átlag|A tárolóhoz lefoglalt memória MB-ban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus (Alkalmazásállapot)|No|ApplicationStatus (Alkalmazásállapot)|Darabszám|Átlag|A Service Fabric Mesh állapota|ApplicationName, Status|
|ContainerStatus|No|ContainerStatus|Darabszám|Átlag|A tároló állapota az Service Fabric Mesh alkalmazásban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|CpuUtilization|No|CpuUtilization|Százalék|Átlag|A tároló cpu-kihasználtsága a Lefoglalt processzor százalékos arányaként|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Százalék|Átlag|A tároló cpu-kihasználtsága a Lefoglalt processzor százalékos arányaként|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount (Újraindítás száma)|No|RestartCount (Újraindítás száma)|Darabszám|Átlag|Tároló újraindítási száma az Service Fabric Mesh alkalmazásban|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Darabszám|Átlag|Szolgáltatásreplikára vonatkozó állapot az Service Fabric Mesh alkalmazásban|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ServiceStatus (Szolgáltatásállapot)|No|ServiceStatus (Szolgáltatásállapot)|Darabszám|Átlag|A szolgáltatás állapota a Service Fabric Mesh alkalmazásban|ApplicationName, Status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ConnectionCount (Kapcsolat száma)|Yes|Kapcsolatszám|Darabszám|Maximum|A felhasználói kapcsolat mennyisége.|Végpont|
|InboundTraffic (Bejövő forgalom)|Yes|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincsenek dimenziók|
|MessageCount|Yes|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|Nincsenek dimenziók|
|OutboundTraffic (Kimenő forgalom)|Yes|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincsenek dimenziók|
|SystemErrors (SystemErrors)|Yes|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|Nincsenek dimenziók|
|UserErrors (UserErrors)|Yes|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|Nincsenek dimenziók|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|InboundTraffic (Bejövő forgalom)|Yes|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincsenek dimenziók|
|OutboundTraffic (Kimenő forgalom)|Yes|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincsenek dimenziók|
|TotalConnectionCount|Yes|Kapcsolatszám|Darabszám|Maximum|A felhasználói kapcsolat mennyisége.|Nincsenek dimenziók|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Átlagos processzorhasználat|Százalék|Átlag|Átlagos processzorhasználat|Nincsenek dimenziók|
|io_bytes_read|Yes|Beolvasott I/O bájtok|Bájt|Átlag|Beolvasott I/O bájtok|Nincsenek dimenziók|
|io_bytes_written|Yes|Írt I/O-bájtok|Bájt|Átlag|Írt I/O-bájtok|Nincsenek dimenziók|
|io_requests|Yes|IO-kérelmek száma|Darabszám|Átlag|IO-kérelmek száma|Nincsenek dimenziók|
|reserved_storage_mb|Yes|Fenntartott tárterület|Darabszám|Átlag|Fenntartott tárterület|Nincsenek dimenziók|
|storage_space_used_mb|Yes|Felhasznált tárterület|Darabszám|Átlag|Felhasznált tárterület|Nincsenek dimenziók|
|virtual_core_count|Yes|Virtuális magok száma|Darabszám|Átlag|Virtuális magok száma|Nincsenek dimenziók|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_queries|Yes|Aktív lekérdezések|Darabszám|Összesen|Aktív lekérdezések az összes számítási feladatcsoporton. Csak az adattárházakra vonatkozik.|Nincsenek dimenziók|
|allocated_data_storage|Yes|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Adattárházra nem vonatkozik.|Nincsenek dimenziók|
|app_cpu_billed|Yes|Az alkalmazás CPU-ja kiszámlázva|Darabszám|Összesen|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek dimenziók|
|app_cpu_percent|Yes|Alkalmazás CPU-használatának százalékos aránya|Százalék|Átlag|Alkalmazás CPU-használatának százalékos aránya. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek dimenziók|
|app_memory_percent|Yes|Alkalmazásmemória százalékos aránya|Százalék|Átlag|Alkalmazásmemória százalékos aránya. Kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek dimenziók|
|base_blob_size_bytes|Yes|Alap blobtároló mérete|Bájt|Maximum|Alap blobtároló mérete. A hiperméretű adatbázisokra vonatkozik.|Nincsenek dimenziók|
|blocked_by_firewall|Yes|A tűzfal blokkolja|Darabszám|Összesen|A tűzfal blokkolja|Nincsenek dimenziók|
|cache_hit_percent|Yes|Gyorsítótár-találatok százalékos aránya|Százalék|Maximum|Gyorsítótár-találatok százalékos aránya. Csak az adattárházra vonatkozik.|Nincsenek dimenziók|
|cache_used_percent|Yes|Gyorsítótár felhasznált százalékos aránya|Százalék|Maximum|Gyorsítótár felhasznált százalékos aránya. Csak az adattárházra vonatkozik.|Nincsenek dimenziók|
|connection_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek dimenziók|
|connection_successful|Yes|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek dimenziók|
|cpu_limit|Yes|PROCESSZORkorlát|Darabszám|Átlag|Processzorkorlát. Virtuálismag-alapú adatbázisokra vonatkozik.|Nincsenek dimenziók|
|cpu_percent|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek dimenziók|
|cpu_used|Yes|Cpu-használat|Darabszám|Átlag|Cpu-használat. Virtuálismag-alapú adatbázisokra vonatkozik.|Nincsenek dimenziók|
|Holtpont|Yes|Holtpontok|Darabszám|Összesen|Holtpont. Nem vonatkozik az adattárházra.|Nincsenek dimenziók|
|diff_backup_size_bytes|Yes|Különbségi biztonsági mentési tárterület mérete|Bájt|Maximum|Kumulatív különbségi biztonsági mentési tárméret. A virtuálismag-alapú adatbázisokra vonatkozik. Nem alkalmazható a nagy kapacitású adatbázisokra.|Nincsenek dimenziók|
|dtu_consumption_percent|Yes|DTU-kihasználtság (%)|Százalék|Átlag|DTU-százalékos érték. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek dimenziók|
|dtu_limit|Yes|DTU-korlát|Darabszám|Átlag|DTU-korlát. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek dimenziók|
|dtu_used|Yes|Használt DTU|Darabszám|Átlag|Használt DTU. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek dimenziók|
|dwu_consumption_percent|Yes|DWU-százalékos érték|Százalék|Maximum|DWU-százalékos érték. Csak az adattárházakra vonatkozik.|Nincsenek dimenziók|
|dwu_limit|Yes|DWU-korlát|Darabszám|Maximum|DWU-korlát. Csak az adattárházakra vonatkozik.|Nincsenek dimenziók|
|dwu_used|Yes|Használt DWU|Darabszám|Maximum|Használt DWU. Csak az adattárházakra vonatkozik.|Nincsenek dimenziók|
|full_backup_size_bytes|Yes|Teljes biztonsági mentési tárméret|Bájt|Maximum|Összesített teljes biztonsági mentési tárméret. A virtuálismag-alapú adatbázisokra vonatkozik. Nem alkalmazható a nagy kapacitású adatbázisokra.|Nincsenek dimenziók|
|local_tempdb_usage_percent|Yes|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékos értéke. Csak az adattárházra vonatkozik.|Nincsenek dimenziók|
|log_backup_size_bytes|Yes|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|A naplók biztonsági mentésének összesített tárterületmérete. A virtuálismag-alapú és a nagy kapacitású adatbázisokra vonatkozik.|Nincsenek dimenziók|
|log_write_percent|Yes|Napló I/O-aránya|Százalék|Átlag|Napló I/O-aránya. Nem vonatkozik az adattárházra.|Nincsenek dimenziók|
|memory_usage_percent|Yes|Memória százalékos aránya|Százalék|Maximum|Memória százalékos aránya. Csak az adattárházakra vonatkozik.|Nincsenek dimenziók|
|physical_data_read_percent|Yes|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek dimenziók|
|queued_queries|Yes|Várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések az összes számítási feladatcsoportban. Csak az adattárházakra vonatkozik.|Nincsenek dimenziók|
|sessions_percent|Yes|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya. Adattárházra nem vonatkozik.|Nincsenek dimenziók|
|snapshot_backup_size_bytes|Yes|Pillanatkép biztonsági mentésének tármérete|Bájt|Maximum|A pillanatkép biztonsági mentésének összesített tármérete. A hiperméretű adatbázisokra vonatkozik.|Nincsenek dimenziók|
|sqlserver_process_core_percent|Yes|SQL Server folyamatmag százalékos aránya|Százalék|Maximum|Processzorhasználat az SQL DB-folyamat százalékában. Nem vonatkozik az adattárházra.|Nincsenek dimenziók|
|sqlserver_process_memory_percent|Yes|SQL Server folyamatmemória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékában. Nem vonatkozik az adattárházra.|Nincsenek dimenziók|
|storage|Yes|Felhasznált adatterület|Bájt|Maximum|Felhasznált adatterület. Nem vonatkozik az adattárházra.|Nincsenek dimenziók|
|storage_percent|Yes|Felhasznált adatterület (százalék)|Százalék|Maximum|Felhasznált adatterület százalékos aránya. Nem vonatkozik az adattárházra vagy a nagy kapacitású adatbázisokra.|Nincsenek dimenziók|
|tempdb_data_size|Yes|Tempdb-adatfájl mérete Kilobájtban|Darabszám|Maximum|A tempdb-adatfájlokban használt terület kilobájtban. Nem vonatkozik az adattárházra.|Nincsenek dimenziók|
|tempdb_log_size|Yes|Tempdb-naplófájl mérete Kilobájtban|Darabszám|Maximum|A tempdb tranzakciós naplófájlban használt terület kilobájtban. Adattárházra nem vonatkozik.|Nincsenek dimenziók|
|tempdb_log_used_percent|Yes|Tempdb százalékos napló használata|Százalék|Maximum|A tempdb tranzakciós naplófájljában felhasznált terület százalékos értéke. Adattárházra nem vonatkozik.|Nincsenek dimenziók|
|wlg_active_queries|Yes|Számítási feladatcsoport aktív lekérdezései|Darabszám|Összesen|Aktív lekérdezések a számítási feladatcsoporton belül. Csak az adattárházra vonatkozik.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Számítási feladatcsoport lekérdezési időtúllépései|Darabszám|Összesen|A számítási feladatcsoport időkorreklott lekérdezései. Csak az adattárházra vonatkozik.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Számítási feladatok csoportfelosztása rendszer százalék szerint|Százalék|Maximum|Az erőforrások lefoglalt százalékos aránya a teljes rendszerhez viszonyítva számítási feladatcsoportonként. Csak az adattárházra vonatkozik.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Számítási feladatok csoportfelosztása az erőforrás-kapacitás százaléka szerint|Százalék|Maximum|Az erőforrások lefoglalt százalékos aránya a számítási feladatcsoportonként megadott maximális erőforrásokhoz viszonyítva. Csak az adattárházra vonatkozik.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Tényleges cap erőforrás-százalék|Százalék|Maximum|A számítási feladatcsoport számára engedélyezett erőforrások százalékos arányának korlátozása, figyelembe véve a többi számítási feladatcsoport számára lefoglalt tényleges minimális erőforrás-százalékos arányt. Csak az adattárházakra vonatkozik.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Tényleges minimális erőforrás-százalék|Százalék|Maximum|A számítási feladatcsoport számára lefoglalt és elkülönített erőforrások minimális százalékos aránya, figyelembe véve a szolgáltatásszint minimumát. Csak az adattárházra vonatkozik.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Számítási feladatcsoport várólistán lévő lekérdezései|Darabszám|Összesen|Várólistán lévő lekérdezések a számítási feladatcsoporton belül. Csak az adattárházakra vonatkozik.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Dolgozók százalékos aránya|Százalék|Átlag|Dolgozók százalékos aránya. Adattárházra nem vonatkozik.|Nincsenek dimenziók|
|xtp_storage_percent|Yes|In-Memory OLTP-tárhely százalékos aránya|Százalék|Átlag|In-Memory OLTP-tárterület százalékos aránya. Nem vonatkozik az adattárházra.|Nincsenek dimenziók|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|Nincsenek dimenziók|
|allocated_data_storage_percent|Yes|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|Nincsenek dimenziók|
|cpu_limit|Yes|Processzorkorlát|Darabszám|Átlag|PROCESSZORkorlát. Virtuálismag-alapú rugalmas készletekre vonatkozik.|Nincsenek dimenziók|
|cpu_percent|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek dimenziók|
|cpu_used|Yes|Cpu-használat|Darabszám|Átlag|Cpu-használat. Virtuálismag-alapú rugalmas készletekre vonatkozik.|Nincsenek dimenziók|
|database_allocated_data_storage|No|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId (Adatforrás-azonosító)|
|database_cpu_limit|No|PROCESSZORkorlát|Darabszám|Átlag|PROCESSZORkorlát|DatabaseResourceId (Adatforrás-azonosító)|
|database_cpu_percent|No|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId (Adatforrás-azonosító)|
|database_cpu_used|No|Cpu-használat|Darabszám|Átlag|Cpu-használat|DatabaseResourceId (Adatforrás-azonosító)|
|database_dtu_consumption_percent|No|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId (Adatforrás-azonosító)|
|database_eDTU_used|No|eDTU-t használ|Darabszám|Átlag|eDTU-t használ|DatabaseResourceId (Adatbázisforrás-azonosító)|
|database_log_write_percent|No|Napló IO-aránya (százalék)|Százalék|Átlag|Napló I/O-aránya|DatabaseResourceId (Adatbázisforrás-azonosító)|
|database_physical_data_read_percent|No|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId (Adatbázisforrás-azonosító)|
|database_sessions_percent|No|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId (Adatbázisforrás-azonosító)|
|database_storage_used|No|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId (Adatbázisforrás-azonosító)|
|database_workers_percent|No|Dolgozók százalékos aránya|Százalék|Átlag|Dolgozók százalékos aránya|DatabaseResourceId (Adatforrás-azonosító)|
|dtu_consumption_percent|Yes|DTU-kihasználtság (%)|Százalék|Átlag|DTU-százalékos érték. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek dimenziók|
|eDTU_limit|Yes|eDTU-korlát|Darabszám|Átlag|eDTU-korlát. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek dimenziók|
|eDTU_used|Yes|eDTU használata|Darabszám|Átlag|eDTU-t használ. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek dimenziók|
|log_write_percent|Yes|Napló IO-értéke (százalék)|Százalék|Átlag|Napló IO-értéke (százalék)|Nincsenek dimenziók|
|physical_data_read_percent|Yes|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek dimenziók|
|sessions_percent|Yes|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincsenek dimenziók|
|sqlserver_process_core_percent|Yes|SQL Server folyamatmag százalékos aránya|Százalék|Maximum|Processzorhasználat az SQL DB-folyamat százalékában. A rugalmas készletekre vonatkozik.|Nincsenek dimenziók|
|sqlserver_process_memory_percent|Yes|SQL Server folyamatmemória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékában. A rugalmas készletekre vonatkozik.|Nincsenek dimenziók|
|storage_limit|Yes|Maximális adatméret|Bájt|Átlag|Maximális adatméret|Nincsenek dimenziók|
|storage_percent|Yes|Felhasznált adatterület (százalék)|Százalék|Átlag|Felhasznált adatterület (százalék)|Nincsenek dimenziók|
|storage_used|Yes|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|Nincsenek dimenziók|
|tempdb_data_size|Yes|Tempdb-adatfájl mérete Kilobájtban|Darabszám|Maximum|A tempdb-adatfájlokban használt terület kilobájtban.|Nincsenek dimenziók|
|tempdb_log_size|Yes|Tempdb-naplófájl mérete Kilobájtban|Darabszám|Maximum|A tempdb tranzakciós naplófájlban használt terület kilobájtban.|Nincsenek dimenziók|
|tempdb_log_used_percent|Yes|Tempdb százalékos napló használata|Százalék|Maximum|A tempdb tranzakciós naplófájljában felhasznált terület százalékos aránya|Nincsenek dimenziók|
|workers_percent|Yes|Dolgozók százalékos aránya|Százalék|Átlag|Dolgozók százalékos aránya|Nincsenek dimenziók|
|xtp_storage_percent|Yes|In-Memory OLTP-tárhely százalékos aránya|Százalék|Átlag|In-Memory OLTP-tárterület százalékos aránya|Nincsenek dimenziók|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage-ból külső ügyfélre, valamint az Azure-ban bejövő bejövő forgalomra is. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatásnak vagy a megadott API-műveletnek adott sikeres kérések végpontok közötti átlagos késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency (Sikeres kiszolgálólatency)|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity (Felhasznált kapacitás)|Yes|Felhasznált kapacitás|Bájt|Átlag|A tárfiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. Prémium szintű tárfiókok és Blob Storage-fiókok esetén ugyanaz, mint a BlobCapacity vagy a FileCapacity.|Nincsenek dimenziók|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|BlobCapacity (Blobkapacitás)|No|Blobkapacitás|Bájt|Átlag|A tárfiók tárfiókja által felhasznált Blob service bájtban meg.|BlobType, Szint|
|BlobCount|No|Blobok száma|Darabszám|Átlag|A tárfiókban tárolt blobobjektumok száma.|BlobType, Szint|
|BlobProvisionedSize|No|Blob által kiépített méret|Bájt|Átlag|A tárfiók tárterületében kiépített tárterület mennyisége Blob service bájtban meg.|BlobType, Szint|
|ContainerCount|Yes|Blobtárolók száma|Darabszám|Átlag|A tárfiókban található tárolók száma.|Nincsenek dimenziók|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage-ból külső ügyfélre, valamint az Azure-ban bejövő bejövő forgalomra is. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|IndexCapacity (Indexkapacitás)|No|Indexkapacitás|Bájt|Átlag|A hierarchikus index által Azure Data Lake Storage Gen2 tárterület mennyisége.|Nincsenek dimenziók|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kérésének végpontok közötti átlagos késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage-ból a külső ügyfélre, valamint az Azure-ban bejövő bejövő forgalomra is. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|FileCapacity (Fájlkapacitás)|No|Fájlkapacitás|Bájt|Átlag|A tárfiók által használt File Storage mennyisége.|Fájlmegosztás|
|FileCount (Fájlszám)|No|Fájlok száma|Darabszám|Átlag|A tárfiókban lévő fájlok száma.|Fájlmegosztás|
|FileShareCapacityQuota|No|Fájlmegosztási kapacitáskvóta|Bájt|Átlag|A szolgáltatás által bájtban Azure Files tárterület felső korlátja.|Fájlmegosztás|
|FileShareCount (Fájlmegosztás száma)|No|Fájlmegosztások száma|Darabszám|Átlag|A tárfiókban található fájlmegosztások száma.|Nincsenek dimenziók|
|FileShareProvisionedIOPS|No|Fájlmegosztás által kiépített IOPS|Bájt|Átlag|A prémium szintű fájlmegosztás kiépített IOPS-ének alapszáma a prémium szintű fájlok tárfiókjában. Ezt a számot a rendszer a megosztási kapacitás kiépített mérete (kvótája) alapján számítja ki.|Fájlmegosztás|
|FileShareSnapshotCount|No|Fájlmegosztási pillanatképek száma|Darabszám|Átlag|A tárfiók Files szolgáltatásában található megosztáson található pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|No|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A tárfiók File szolgáltatásában a pillanatképek által használt tárterület bájtban.|Fájlmegosztás|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kérésének végpontok közötti átlagos késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|SuccessServerLatency|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés, Fájlmegosztás|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage-ból a külső ügyfélre, valamint az Azure-ban bejövő bejövő forgalomra is. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|QueueCapacity (Várólistakapacitás)|Yes|Üzenetsor kapacitása|Bájt|Átlag|A tárfiók által használt Queue Storage mennyisége.|Nincsenek dimenziók|
|QueueCount (Várólista száma)|Yes|Üzenetsorok száma|Darabszám|Átlag|A tárfiókban lévő üzenetsorok száma.|Nincsenek dimenziók|
|QueueMessageCount|Yes|Üzenetsor üzenetszáma|Darabszám|Átlag|A tárfiókban lévő, még le nem hagyott üzenetsor-üzenetek száma.|Nincsenek dimenziók|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatásnak vagy a megadott API-műveletnek adott sikeres kérések végpontok közötti átlagos késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency (Sikeres kiszolgálólatency)|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage-ból a külső ügyfélre, valamint az Azure-ban bejövő bejövő forgalomra is. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kérésének végpontok közötti átlagos késése, ezredmásodpercben megadva. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Hitelesítés|
|SuccessServerLatency|Yes|Sikeres kiszolgálók késése|Ezredmásodperc|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, Hitelesítés|
|TableCapacity (Táblakapacitás)|Yes|Táblakapacitás|Bájt|Átlag|A tárfiók által használt Table Storage mennyisége.|Nincsenek dimenziók|
|TableCount (Táblaszám)|Yes|Táblaszám|Darabszám|Átlag|A tárfiók tábláinak száma.|Nincsenek dimenziók|
|TableEntityCount (Tábla száma)|Yes|Táblaentitások száma|Darabszám|Átlag|A tárfiókban található táblaentitások száma.|Nincsenek dimenziók|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A különböző típusú válaszok számában használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|Ügyfél teljes IOPS-száma|Darabszám|Átlag|A gyorsítótár által feldolgozott ügyfélfájl-műveletek sebessége.|Nincsenek dimenziók|
|ClientLatency (Ügyféloldal)|Yes|Ügyfél átlagos késése|Ezredmásodperc|Átlag|Az ügyfélfájlműveletek átlagos késése a gyorsítótárban.|Nincsenek dimenziók|
|ClientLockIOPS|Yes|ÜgyfélzárolásI IOPS|CountPerSecond (Másodpercek száma)|Átlag|Ügyfélfájl-zárolási műveletek másodpercenként.|Nincsenek dimenziók|
|ClientMetadataReadIOPS|Yes|Ügyfél metaadatainak olvasási IOPS-értékével|CountPerSecond (Másodpercek száma)|Átlag|A gyorsítótárba küldött ügyfélfájlműveletek sebessége az adatbeolvasott adatok kivételével, amelyek nem módosítják az állandó állapotot.|Nincsenek dimenziók|
|ClientMetadataWriteIOPS|Yes|Ügyfél metaadatainak írási IOPS-értékével|CountPerSecond (Másodpercek száma)|Átlag|A gyorsítótárba küldött ügyfélfájlműveletek sebessége az állandó állapotot módosító adatírások kivételével.|Nincsenek dimenziók|
|ClientReadIOPS|Yes|Ügyfél olvasási IOPS-érték|CountPerSecond (Másodpercek száma)|Átlag|Ügyfél másodpercenkénti olvasási műveletei.|Nincsenek dimenziók|
|ClientReadThroughput (Ügyfélolvasási sebesség)|Yes|Gyorsítótár átlagos olvasási átviteli sebessége|BájtperSecond (bájt/másodperc)|Átlag|Az ügyfél olvasási adatátviteli sebessége.|Nincsenek dimenziók|
|ClientWriteIOPS|Yes|Ügyfél írási IOPS-érték|CountPerSecond (Másodpercek száma)|Átlag|Ügyfélírási műveletek másodpercenként.|Nincsenek dimenziók|
|ClientWriteThroughput (ClientWriteThroughput)|Yes|Gyorsítótár átlagos írási átviteli sebessége|BájtperSecond (bájt/másodperc)|Átlag|Ügyfél írási adatátviteli sebessége.|Nincsenek dimenziók|
|StorageTargetAsyncWriteThroughput|Yes|StorageTarget Aszinkron írási átviteli sebesség|BájtokPerSecond|Átlag|A gyorsítótár aszinkron módon adatokat ír egy adott StorageTarget-fájlba. Ezek olyan opportunisztikus írási műveletek, amelyek nem okítják az ügyfelek blokkolását.|StorageTarget|
|StorageTargetFillThroughput|Yes|StorageTarget Fill Throughput|BájtokPerSecond|Átlag|Az a sebesség, amely alapján a gyorsítótár beolvassa az adatokat a StorageTargetből a gyorsítótár-tévesztés kezeléséhez.|StorageTarget (Céltároló)|
|StorageTargetHealth|Yes|Tárolási cél állapota|Darabszám|Átlag|A gyorsítótár- és tárolócélok közötti kapcsolati teszt logikai eredményei.|Nincsenek dimenziók|
|StorageTargetIOPS|Yes|Teljes tárterületCél IOPS-érték|Darabszám|Átlag|A gyorsítótár által egy adott StorageTargetnek küldött összes fájlművelet sebessége.|StorageTarget (Céltároló)|
|StorageTargetLatency|Yes|StorageTarget-késés|Ezredmásodperc|Átlag|A gyorsítótár által a partritkulizált StorageTargetnek küldött összes fájlművelet átlagos körbeúti késése.|StorageTarget|
|StorageTargetMetadataReadIOPS|Yes|StorageTarget Metadata Read IOPS|CountPerSecond (Másodpercek száma)|Átlag|Az állandó állapotot nem módosító fájlműveletek sebessége, valamint az olvasási művelet kizárása, amit a gyorsítótár küld egy adott StorageTarget-nek.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|StorageTarget Metadata Write IOPS|CountPerSecond (Másodpercek száma)|Átlag|Az állandó állapotot módosító és az írási műveletet ki nem módosító fájlműveletek sebessége, amit a gyorsítótár küld egy adott StorageTarget-nek.|StorageTarget|
|StorageTargetReadAheadThroughput (StorageTargetReadAheadThroughput)|Yes|StorageTarget előre olvasási átviteli sebesség|BájtperSecond (bájt/másodperc)|Átlag|Az a sebesség, amely alapján a gyorsítótár opportunisztikusan beolvassa az adatokat a StorageTarget-ból.|StorageTarget|
|StorageTargetReadIOPS|Yes|StorageTarget olvasási IOPS|CountPerSecond (Másodpercek száma)|Átlag|A gyorsítótár által az adott StorageTargetnek küldött fájl olvasási műveletek sebessége.|StorageTarget (Céltároló)|
|StorageTargetSyncWriteThroughput|Yes|StorageTarget szinkron írási átviteli sebesség|BájtokPerSecond|Átlag|Az a sebesség, amely alapján a gyorsítótár szinkron módon ír adatokat egy adott StorageTarget-fájlba. Ezek olyan írások, amelyek blokkolják az ügyfeleket.|StorageTarget (Céltároló)|
|StorageTargetTotalReadThroughput|Yes|StorageTarget teljes olvasási átviteli sebesség|BájtokPerSecond|Átlag|A teljes sebesség, amely alapján a gyorsítótár adatokat olvas egy adott StorageTargetből.|StorageTarget (Céltároló)|
|StorageTargetTotalWriteThroughput|Yes|StorageTarget teljes írási átviteli sebesség|BájtokPerSecond|Átlag|Az a teljes sebesség, amely alapján a gyorsítótár adatokat ír egy adott StorageTarget-be.|StorageTarget (Céltároló)|
|StorageTargetWriteIOPS|Yes|StorageTarget írási IOPS|Darabszám|Átlag|A gyorsítótár által az adott StorageTargetnek küldött fájlírási műveletek sebessége.|StorageTarget|
|Üzemidő|Yes|Üzemidő|Darabszám|Átlag|A gyorsítótár és a monitorozási rendszer közötti kapcsolatteszt logikai eredményei.|Nincsenek dimenziók|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Szinkronizálási munkamenet eredménye|Darabszám|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálóvégpont sikeresen befejez egy szinkronizálási munkamenetet a felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Szinkronizált bájtok|Bájt|Összesen|Szinkronizálási munkamenetek átvitt teljes fájlmérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecallComputedSuccessRate|Yes|Felhőbeli rétegezés visszahívási sikeresség aránya|Százalék|Átlag|Az összes sikeres visszahívás százalékos aránya|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Felhőbeli rétegezés visszahívási mérete alkalmazás szerint|Bájt|Összesen|Az alkalmazás által visszahívott adatok mérete|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Felhőbeli rétegezés visszahívási mérete|Bájt|Összesen|A visszahívott adatok mérete|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Yes|Felhőbeli rétegezés visszahívása|Bájt|Összesen|A kiszolgáló által visszahívott adatok teljes mérete|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Felhőbeli rétegezés visszahívási átviteli sebesség|BájtokPerSecond|Átlag|Az adatvisszahívás átviteli sebességének mérete|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Yes|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor az áttért kiszolgáló sikeresen rögzíti a szívverést a felhővégponttal|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|A fájlok nem szinkronizálódnak|Darabszám|Összesen|A fájlok száma, amelyek szinkronizálása sikertelen volt|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerHeartbeat (Kiszolgálói egészség)|Yes|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor az áttért kiszolgáló sikeresen rögzíti a szívverést a felhőbeli végponttal|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Yes|Felhőbeli rétegezés visszahívása|Bájt|Összesen|A kiszolgáló által visszahívott adatok teljes mérete|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Szinkronizált bájtok|Bájt|Összesen|A szinkronizálási munkamenetek átvitt teljes fájlmérete|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|A fájlok nem szinkronizálódnak|Darabszám|Összesen|A nem szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Szinkronizált bájtok|Bájt|Összesen|A szinkronizálási munkamenetek átvitt teljes fájlmérete|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|A fájlok nem szinkronizálódnak|Darabszám|Összesen|A fájlok száma, amelyek szinkronizálása sikertelen volt|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Sikertelen függvénykérések|Darabszám|Összesen|Sikertelen függvénykérések|Logikai név, PartitionId|
|AMLCalloutInputEvents|Yes|Függvényesemények|Darabszám|Összesen|Függvényesemények|Logikai név, PartitionId|
|AMLCalloutRequests|Yes|Függvénykérések|Darabszám|Összesen|Függvénykérések|Logikai név, PartitionId|
|ConversionErrors (Konverzió-átalakítók)|Yes|Adatátalakítási hibák|Darabszám|Összesen|Adatátalakítási hibák|Logikai név, PartitionId|
|DeserializationError|Yes|Bemeneti deserializálási hibák|Darabszám|Összesen|Bemeneti deserializálási hibák|Logikai név, PartitionId|
|DroppedOrAdjustedEvents|Yes|Nem sorrendben események|Darabszám|Összesen|Nem sorrendben események|Logikai név, PartitionId|
|EarlyInputEvents|Yes|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|Logikai név, PartitionId|
|Hibák|Yes|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|Logikai név, PartitionId|
|InputEventBytes (InputEventBytes)|Yes|Bemeneti esemény bájtja|Bájt|Összesen|Bemeneti esemény bájtja|Logikai név, PartitionId|
|InputEvents (Bemenetekventek)|Yes|Bemeneti események|Darabszám|Összesen|Bemeneti események|Logikai név, PartitionId|
|InputEventsSourcesBacklogged|Yes|Háttérbemeneti események|Darabszám|Maximum|Háttérbemeneti események|Logikai név, PartitionId|
|InputEventsSourcesPerSecond|Yes|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|Logikai név, PartitionId|
|LateInputEvents (LateInputEvents)|Yes|Kései bemeneti események|Darabszám|Összesen|Kései bemeneti események|Logikai név, PartitionId|
|OutputEvents (Kimenetek)|Yes|Kimeneti események|Darabszám|Összesen|Kimeneti események|Logikai név, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Vízjel késleltetése|Másodperc|Maximum|Vízjel késleltetése|Logikai név, PartitionId|
|ProcessCPUUsagePercentage|Yes|CPU százalékos kihasználtsága (előzetes verzió)|Százalék|Maximum|CPU százalékos kihasználtsága (előzetes verzió)|Logikai név, PartitionId|
|ResourceUtilization|Yes|SU – Kihasználtság (% )|Százalék|Maximum|SU – Kihasználtság (% )|Logikai név, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces
|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Feldolgozott adatok (bájt)|Bájt|Összesen|A lekérdezések által feldolgozott adatok mennyisége|Nincsenek dimenziók|
|BuiltinSqlPoolLoginAttempts|No|Bejelentkezési kísérletek|Darabszám|Összesen|Succed vagy sikertelen bejelentkezési kísérletek száma|Eredmény|
|BuiltinSqlPoolRequestsEnded|No|A kérések véget értek|Darabszám|Összesen|Sikeres, sikertelen vagy megszakított kérelmek száma|Eredmény|
|IntegrationActivityRunsEnded (IntegrationActivityRunsEnded)|No|A tevékenységfut futtatás véget ért|Darabszám|Összesen|Sikeres, sikertelen vagy megszakított integrációs tevékenységek száma|Result, FailureType, Activity, ActivityType, Pipeline|
|IntegrationPipelineRunsEnded (IntegrationPipelineRunsEnded)|No|A folyamatfut futtatás véget ért|Darabszám|Összesen|Az integrációs folyamat sikeres, sikertelen vagy megszakított futtatásainak száma|Eredmény, FailureType, Folyamat|
|IntegrationTriggerRunsEnded|No|Az eseményindító-futtatás véget ért|Darabszám|Összesen|Sikeres, sikertelen vagy megszakított integrációs eseményindítók száma|Eredmény, FailureType, Trigger|
|SQLStreamingBackloggedInputEventSources|No|Háttérbemeneti események (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. A bemeneti események forrásának száma háttérbe van jelölve.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingConversionErrors (SqlStreamingConversionErrors)|No|Adatátalakítási hibák (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. Azon kimeneti események száma, amelyek nem konvertálhatók a várt kimeneti sémára. A hiba szabályzata módosítható "Drop" (Eldobás) szabályzatra az ilyen forgatókönyvvel kapcsolatos események eldobás érdekében.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingDeserializationError|No|Bemeneti deserializálási hibák (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. A nem deserializálható bemeneti események száma.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingEarlyInputEvents|No|Korai bemeneti események (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti és nyugat-európai régióiban érhető el. Azon bemeneti események száma, amelyek az alkalmazásidőt korainek számítanak az érkezési időhez képest, a korai érkezési szabályzatnak megfelelően.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventBytes|No|Bemeneti esemény bájtja (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. A streamelési feladat által fogadott adatok mennyisége bájtban. Ezzel ellenőrizheti, hogy a rendszer elküldi-e az eseményeket a bemeneti forrásnak.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEvents (SQLStreamingInputEvents)|No|Bemeneti események (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti és nyugat-európai régióiban érhető el. A bemeneti események száma.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventsSourcesPerSecond|No|Fogadott bemeneti források (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti és nyugat-európai régióiban érhető el. A bemeneti események forrásainak másodpercenkénti száma.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingLateInputEvents|No|Kései bemeneti események (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. A késő érkezési szabályzatnak megfelelően azon bemeneti események száma, amelyek az alkalmazás idejét késnek a beérkezés időpontjaihoz képest.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutOfOrderEvents|No|Nem sorrendben események (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. Az eseményközpont bemeneti adaptere által fogadott eseményközpont-események (szerializált üzenetek) száma, az eseményrendezés házirendje alapján eldobott vagy módosított időbélyegzővel megkapott események száma.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputEvents|No|Kimeneti események (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. A kimeneti események száma.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputWatermarkDelaySeconds|No|Vízjel késleltetése (előzetes verzió)|Darabszám|Maximum|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el. Kimeneti vízjel késleltetése másodpercben.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingResourceUtilization|No|Erőforrás%-os kihasználtsága (előzetes verzió)|Százalék|Maximum|Ez egy előzetes verziójú metrika, amely az USA keleti régiója és Nyugat-Európa területén érhető el.
 Százalékos értékben kifejezett erőforrás-kihasználtság. A magas kihasználtság azt jelzi, hogy a feladat a lefoglalt erőforrások maximumának közelében van használatban.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingRuntimeErrors (SQLStreamingRuntimeErrors)|No|Futásidejű hibák (előzetes verzió)|Darabszám|Összesen|Ez egy előzetes verziójú metrika, amely az USA keleti és nyugat-európai régióiban érhető el. A lekérdezések feldolgozásával kapcsolatos hibák teljes száma (kivéve az események feldolgozásakor vagy az eredmények kimenetekor előforduló hibákat).|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores (BigDataPoolAllocatedCores)|No|Lefoglalt virtuális magok|Darabszám|Maximum|Lefoglalt virtuális magok egy Apache Spark készlethez|SubmitterId (Beküldőazonosító)|
|BigDataPoolAllocatedMemory|No|Lefoglalt memória (GB)|Darabszám|Maximum|Memória lefoglalva a Mach Spark-készlethez (GB)|SubmitterId (Beküldőazonosító)|
|BigDataPoolApplicationsActive|No|Aktív Apache Spark alkalmazások|Darabszám|Maximum|Összes aktív Apache Spark készlet-alkalmazás|JobState (Feladat állama)|
|BigDataPoolApplicationsEnded|No|Véget Apache Spark alkalmazások|Darabszám|Összesen|A Apache Spark alkalmazások száma|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveQueries (Aktív lekérdezések)|No|Aktív lekérdezések|Darabszám|Összesen|Az aktív lekérdezések. Ha ezt a metrikát használja, a nem szűrt és a nem látható, megjeleníti a rendszeren futó összes aktív lekérdezést|IsUserDefined (IsUserdefined)|
|AdaptiveCacheHitPercent|No|Adaptív gyorsítótár-találatok százalékos aránya|Százalék|Maximum|Azt méri, hogy a számítási feladatok mennyire kihasználják az adaptív gyorsítótárat. Használja ezt a metrikát a gyorsítótár-találatok százalékos aránya metrikával annak meghatározásához, hogy a rendszer skálázza-e a kapacitást, vagy újra futtassa a számítási feladatokat a gyorsítótárhidratáláshoz|Nincsenek dimenziók|
|AdaptiveCacheUsedPercent|No|Adaptív gyorsítótár felhasznált százalékos aránya|Százalék|Maximum|Azt méri, hogy a számítási feladatok mennyire kihasználják az adaptív gyorsítótárat. Használja ezt a metrikát a gyorsítótár százalékos kihasználtság mérőszámával annak meghatározásához, hogy a rendszer skálázza-e a kapacitást, vagy újra futtassa a számítási feladatokat a gyorsítótár hidratálása érdekében|Nincsenek dimenziók|
|Kapcsolatok|Yes|Kapcsolatok|Darabszám|Összesen|Az SQL-készletbe való összes bejelentkezés száma|Eredmény|
|ConnectionsBlockedByFirewall|No|Tűzfal által blokkolt kapcsolatok|Darabszám|Összesen|A tűzfalszabályok által blokkolt kapcsolatok száma. Az SQL-készlet hozzáférés-vezérlési szabályzatának újra megszámlálása és a kapcsolatok figyelése, ha a szám magas|Nincsenek dimenziók|
|CPUPercent (PROCESSZOR/teljesítmény)|No|Processzorhasználat százalékos aránya|Százalék|Maximum|CPU-kihasználtság az SQL-készlet összes csomópontja között|Nincsenek dimenziók|
|DWULimit (DWULimit)|No|DWU-korlát|Darabszám|Maximum|Az SQL-készlet szolgáltatásiszint-célkitűzése|Nincsenek dimenziók|
|DWUUsed|No|Használt DWU|Darabszám|Maximum|A használat magas szintű reprezentációja az SQL-készletben. DWU-korlát * DWU-százalékos érték mérése|Nincsenek dimenziók|
|DWUUsedPercent|No|Felhasznált DWU-százalék|Százalék|Maximum|A használat magas szintű reprezentációja az SQL-készletben. Mérés a processzorhasználat és az adat-IO százalékos aránya közötti maximális érték alapján|Nincsenek dimenziók|
|LocalTempDBUsedPercent|No|A helyi tempdb felhasznált százaléka|Százalék|Maximum|Helyi tempdb-kihasználtság az összes számítási csomóponton – az értékek 5 percenként vannak kiadva|Nincsenek dimenziók|
|MemoryUsedPercent|No|Felhasznált memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL-készlet összes csomópontja között|Nincsenek dimenziók|
|QueuedQueries (Üzenetsor-lekérdezések)|No|Várólistán lévő lekérdezések|Darabszám|Összesen|A maximális egyidejűségi korlát elérése után várakozó kérelmek összesített száma|IsUserDefined (IsUserDefined)|
|WLGActiveQueries|No|Számítási feladatcsoport aktív lekérdezései|Darabszám|Összesen|A számítási feladatcsoporton belüli aktív lekérdezések. Ennek a metrikanak a használata szűretlen és nem látható, és megjeleníti a rendszeren futó összes aktív lekérdezést|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Számítási feladatcsoport lekérdezési időtúllépései|Darabszám|Összesen|A számítási feladat időkorrekta csoportjának lekérdezései. A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának elkezdődtek (nem tartalmazza a zárolás vagy az erőforrás-várakozások miatti várakozási időt)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|No|Számítási feladatok csoportfelosztása maximális erőforrás-százalék szerint|Százalék|Maximum|Megjeleníti az erőforrások százalékos lefoglalását a számítási feladatcsoportonkénti tényleges felső erőforrás-százalékhoz viszonyítva. Ez a metrika a számítási feladatcsoport hatékony kihasználtságát biztosítja|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Számítási feladatok csoportfelosztása rendszer százalék szerint|Százalék|Maximum|Az erőforrások százalékos kiosztása a teljes rendszerhez viszonyítva|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|No|Tényleges cap erőforrás-százalék|Százalék|Maximum|A számítási feladatcsoport tényleges erőforrás-kapacitásának százalékos aránya. Ha más, 0 min_percentage_resource > munkaterhelési csoportok is vannak, a effective_cap_percentage_resource arányosan csökken|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|No|Tényleges minimális erőforrás-százalék|Százalék|Maximum|A tényleges minimális erőforrás-százalékos beállítás lehetővé tette a szolgáltatási szint és a számítási feladatcsoport beállításainak figyelembe való beállítását. A hatékony min_percentage_resource magasabb szolgáltatási szinteken is módosíthatók|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Számítási feladatcsoport várólistán lévő lekérdezései|Darabszám|Összesen|A maximális egyidejűségi korlát elérése után várakozó kérelmek összesített száma|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Bejövő forgalomReceivedBytes|Yes|Fogadott bejövő forgalom bájtja|Bájt|Összesen|Az összes eseményforrásból beolvasott bájtok száma|Nincsenek dimenziók|
|IngressReceivedInvalidMessages|Yes|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az összes eseményközpontból vagy IoT Hub-eseményforrásból beolvasott érvénytelen üzenetek száma|Nincsenek dimenziók|
|IngressReceivedMessages|Yes|Fogadott bejövő üzenetek|Darabszám|Összesen|Az összes eseményközpontból vagy IoT Hub-eseményforrásból beolvasott üzenetek száma|Nincsenek dimenziók|
|IngressReceivedMessagesCountLag|Yes|Fogadott bejövő üzenetek száma késés|Darabszám|Átlag|Az eseményforrás partícióban található utolsó üzenet sorszáma és a bejövő forgalomban feldolgozott üzenetek sorrendje közötti különbség|Nincsenek dimenziók|
|IngressReceivedMessagesTimeLag|Yes|Bejövő forgalom fogadott üzenetei – idő késése|Másodperc|Maximum|Az üzenet eseményforrásba való besoroltságának és a bejövő forgalomban való feldolgozásának ideje közötti különbség|Nincsenek dimenziók|
|Bejövő forgalomStoredBytes|Yes|Bejövő forgalom tárolt bájtja|Bájt|Összesen|A sikeresen feldolgozott és lekérdezésre elérhető események teljes mérete|Nincsenek dimenziók|
|IngressStoredEvents (IngressStoredEvents)|Yes|Bejövő forgalom tárolt eseményei|Darabszám|Összesen|Sikeresen feldolgozott és lekérdezésre elérhető, ellapított események száma|Nincsenek dimenziók|
|WarmStorageMaxProperties|Yes|A Warm Storage maximális tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 termékváltozathoz engedélyezett tulajdonságok maximális száma és a Warm Store által a PAYG termékváltozathoz engedélyezett tulajdonságok maximális száma|Nincsenek dimenziók|
|WarmStorageUsedProperties|Yes|A Warm Storage használt tulajdonságai |Darabszám|Maximum|A környezet által az S1/S2 termékváltozathoz használt tulajdonságok száma és a Warm Store által a PAYG termékváltozathoz használt tulajdonságok száma|Nincsenek dimenziók|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Bejövő forgalomReceivedBytes|Yes|Fogadott bejövő forgalom bájtja|Bájt|Összesen|Az eseményforrásból beolvasott bájtok száma|Nincsenek dimenziók|
|IngressReceivedInvalidMessages|Yes|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrásból beolvasott érvénytelen üzenetek száma|Nincsenek dimenziók|
|IngressReceivedMessages|Yes|Fogadott bejövő üzenetek|Darabszám|Összesen|Az eseményforrásból beolvasott üzenetek száma|Nincsenek dimenziók|
|IngressReceivedMessagesCountLag|Yes|Fogadott bejövő üzenetek száma késés|Darabszám|Átlag|Az eseményforrás partícióban található utolsó üzenet sorszáma és a bejövő forgalomban feldolgozott üzenetek sorrendje közötti különbség|Nincsenek dimenziók|
|IngressReceivedMessagesTimeLag|Yes|Bejövő forgalom fogadott üzenetei – idő késése|Másodperc|Maximum|Az üzenet eseményforrásba való besoroltságának és a bejövő forgalomban való feldolgozásának ideje közötti különbség|Nincsenek dimenziók|
|IngressStoredBytes|Yes|Bejövő forgalom tárolt bájtja|Bájt|Összesen|A sikeresen feldolgozott és lekérdezésre elérhető események teljes mérete|Nincsenek dimenziók|
|IngressStoredEvents (IngressStoredEvents)|Yes|Bejövő forgalom tárolt eseményei|Darabszám|Összesen|Sikeresen feldolgozott és lekérdezésre elérhető, ellapított események száma|Nincsenek dimenziók|
|WarmStorageMaxProperties|Yes|A Warm Storage maximális tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 termékváltozathoz engedélyezett tulajdonságok maximális száma és a Warm Store által a PAYG termékváltozathoz engedélyezett tulajdonságok maximális száma|Nincsenek dimenziók|
|WarmStorageUsedProperties|Yes|A Warm Storage használt tulajdonságai |Darabszám|Maximum|A környezet által az S1/S2 termékváltozathoz használt tulajdonságok száma és a Warm Store által a PAYG termékváltozathoz használt tulajdonságok száma|Nincsenek dimenziók|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtja|Yes|Lemez olvasási bájtja|Bájt|Összesen|A lemez teljes átviteli sebességét a mintaidőszak olvasási műveleteinek köszönhető.|Nincsenek dimenziók|
|Lemez olvasási műveletei másodpercenként|Yes|Lemez olvasási műveletei másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Az előző mintaidőszakban az I/O-olvasási műveletek átlagos száma. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek dimenziók|
|Lemezírási bájtok|Yes|Lemezírási bájtok|Bájt|Összesen|A lemez teljes átviteli sebességének oka az írási műveletek száma a mintaidőszakban.|Nincsenek dimenziók|
|Lemezírási műveletek másodpercenként|Yes|Lemezírási műveletek másodpercenként|CountPerSecond (Másodpercek száma)|Átlag|Az io-írási műveletek átlagos száma az előző mintaidőszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek dimenziók|
|DiskReadBytesPerSecond|Yes|Lemez olvasási bájt/s|BájtokPerSecond|Átlag|Átlagos lemez-átviteli sebesség az olvasási műveletek miatt a mintaidőszakban.|Nincsenek dimenziók|
|DiskReadLatency (DiskReadLatency)|Yes|Lemez olvasási késése|Ezredmásodperc|Átlag|Teljes olvasási késés. Az eszköz és a kernel olvasási késésének összege.|Nincsenek dimenziók|
|DiskReadOperations|Yes|Lemez olvasási műveletei|Darabszám|Összesen|Az io-olvasási műveletek száma az előző mintaidőszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek dimenziók|
|DiskWriteBytesPerSecond|Yes|Lemezírási memória (bájt/s)|BájtokPerSecond|Átlag|A lemez átlagos átviteli sebességének oka az írási műveletek száma a mintaidőszakban.|Nincsenek dimenziók|
|DiskWriteLatency|Yes|Lemezírás késése|Ezredmásodperc|Átlag|Teljes írási késés. Az eszköz és a kernel írási késésének összege.|Nincsenek dimenziók|
|DiskWriteOperations|Yes|Lemezírási műveletek|Darabszám|Összesen|Az io-írási műveletek száma az előző mintaidőszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek dimenziók|
|MemoryActive (Memóriaaktív)|Yes|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez annak a "true" (igaz) száma, hogy a virtuális gépnek jelenleg mennyi memóriára van szüksége. Emellett a nem használt memória felcserélheti vagy ballonba olthatja a vendég teljesítményét.|Nincsenek dimenziók|
|MemoryGranted (Memória- regisztrálva)|Yes|Memória meg van adni|Bájt|Átlag|A virtuális gép számára a gazdagép által biztosított memória mennyisége. Ha a VMkernelnek szüksége van a memóriára, a gazdagép nem kap memóriát, amíg egyszer meg nem érintette, és a memóriát felcserélheti vagy elballontálhatja.|Nincsenek dimenziók|
|MemoryUsed (Memória)|Yes|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt számítógépmemória mennyisége.|Nincsenek dimenziók|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom teljes hálózati átviteli sebességét.|Nincsenek dimenziók|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|Az átvitt forgalom teljes hálózati átviteli sebességét.|Nincsenek dimenziók|
|NetworkInBytesPerSecond|Yes|Hálózat (bájt/s)|BájtokPerSecond|Átlag|Átlagos hálózati átviteli sebesség a fogadott forgalomhoz.|Nincsenek dimenziók|
|NetworkOutBytesPerSecond|Yes|Ki nem bájt/s hálózati teljesítmény|BájtokPerSecond|Átlag|Az átvitt forgalom átlagos hálózati átviteli sebességét.|Nincsenek dimenziók|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A cpu-kihasználtság. Ennek az értéknek a jelentése 100%, amely a rendszer összes processzormagját képviseli. Egy négymagos rendszer 50%-át használó két ű virtuális gép például teljes mértékben két magot használ.|Nincsenek dimenziók|
|PercentageCpuReady|Yes|Százalékos processzorhasználatra kész|Ezredmásodperc|Összesen|A kész idő az a várakozási idő, amíg a cpu(k) elérhetővé válnak a korábbi frissítési időszakban.|Nincsenek dimenziók|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Aktív kérések (elavult)|Darabszám|Összesen|Aktív kérések|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő (elavult)|Másodperc|Átlag|A kérések kiszolgálására igénybe vett átlagos idő az előlapon másodpercben.|Példány|
|BájtokReceived|Yes|Adatok a következőben:|Bájt|Összesen|Az összes előtere átlagos bejövő sávszélessége a MiB-ban.|Példány|
|BájtokSent (bájt)|Yes|Adatok ki|Bájt|Összesen|Az összes előtere által felhasznált átlagos bejövő sávszélesség a MiB-ban.|Példány|
|CpuPercentage (Processzorteljesítmény)|Yes|Processzorhasználat (%)|Százalék|Átlag|Az előtere összes példánya által felhasznált átlagos processzorhasználat.|Példány|
|DiskQueueLength (LemezqueueLength)|Yes|Lemez várakozási sorának hossza|Darabszám|Átlag|A tárolóban várakozó olvasási és írási kérelmek átlagos száma. A lemez várakozási sorának magas hossza egy olyan alkalmazásra utal, amely túlzott lemez I/O-lassulása miatt lassulhat.|Példány|
|Http101|Yes|HTTP 101|Darabszám|Összesen|A 101-es HTTP-állapotkódot eredményező kérések száma.|Példány|
|Http2xx|Yes|Http 2xx|Darabszám|Összesen|A HTTP-állapotkódot = 200, de 300-< eredményező kérelmek száma.|Példány|
|Http3xx|Yes|Http 3xx|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 300, de < 400.|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|A HTTP 401-es állapotkódot eredményező kérelmek száma.|Példány|
|Http403|Yes|HTTP 403|Darabszám|Összesen|A HTTP 403 állapotkódot eredményező kérelmek száma.|Példány|
|Http404|Yes|HTTP 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérelmek száma.|Példány|
|Http406|Yes|HTTP 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérelmek száma.|Példány|
|Http4xx|Yes|Http 4xx|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 400, de < 500.|Példány|
|Http5xx|Yes|HTTP-kiszolgálóval kapcsolatos hibák|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 500, de < 600.|Példány|
|HttpQueueLength (HttpQueueLength)|Yes|HTTP-várólista hossza|Darabszám|Átlag|Azon HTTP-kérések átlagos száma, amelyeknek a várakozási sorban kellett várakozni a teljesítése előtt. A HTTP-várólista magas vagy növekvő hossza a nagy terhelés alatt lévő csomag tünete.|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|Az az idő, amely alatt az előlap másodpercben kiszolgálja a kéréseket.|Példány|
|LargeAppServicePlanInstances|Yes|Nagy méretű App Service tervezze meg a dolgozókat|Darabszám|Átlag|Nagy méretű App Service tervezze meg a dolgozókat|Nincsenek dimenziók|
|MediumAppServicePlanInstances|Yes|Közepes App Service tervezőmunkások|Darabszám|Átlag|Közepes App Service tervezőmunkások|Nincsenek dimenziók|
|MemoryPercentage (Memóriateljesítmény)|Yes|Memória százalékos aránya|Százalék|Átlag|Az előtere összes példánya által felhasznált átlagos memória.|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|A kérések teljes száma az eredményül kapott HTTP-állapotkódtól függetlenül.|Példány|
|SmallAppServicePlanInstances|Yes|Kis méretű App Service tervezze meg a dolgozókat|Darabszám|Átlag|Kis méretű App Service tervezze meg a dolgozókat|Nincsenek dimenziók|
|TotalFrontEnds|Yes|Összes előtét|Darabszám|Átlag|Összes előtét|Nincsenek dimenziók|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CpuPercentage (Processzorteljesítmény)|Yes|Processzorhasználat (%)|Százalék|Átlag|A feldolgozókészlet összes példánya által felhasznált átlagos PROCESSZORhasználat.|Példány|
|MemóriaTeljesítmény|Yes|Memória százalékos aránya|Százalék|Átlag|A feldolgozókészlet összes példánya által felhasznált átlagos memória.|Példány|
|A dolgozók nem érhetők el|Yes|Elérhető dolgozók|Darabszám|Átlag|Elérhető dolgozók|Nincsenek dimenziók|
|WorkersTotal (Dolgozókösszeg)|Yes|Összes dolgozó|Darabszám|Átlag|Összes dolgozó|Nincsenek dimenziók|
|WorkersUsed (Dolgozók általhasznált)|Yes|Használt dolgozók|Darabszám|Átlag|Használt dolgozók|Nincsenek dimenziók|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BájtokReceived|Yes|Adatok be|Bájt|Összesen|A csomag összes példánya által felhasznált átlagos bejövő sávszélesség.|Példány|
|BájtokSent (bájtok)|Yes|Adatok ki|Bájt|Összesen|A csomag összes példánya által felhasznált átlagos kimenő sávszélesség.|Példány|
|CpuPercentage (Processzorteljesítmény)|Yes|Processzorhasználat (%)|Százalék|Átlag|A csomag összes példánya által felhasznált átlagos processzorhasználat.|Példány|
|DiskQueueLength (LemezqueueLength)|Yes|Lemez várakozási sorának hossza|Darabszám|Átlag|A tárolóban várakozó olvasási és írási kérelmek átlagos száma. A magas lemez-várólistahossz olyan alkalmazásra utal, amely túlzott lemez I/O-lassulása miatt lelassulhat.|Példány|
|HttpQueueLength|Yes|HTTP-várólista hossza|Darabszám|Átlag|Azon HTTP-kérések átlagos száma, amelyek a várakozási sorban kellett várakozni a teljesítése előtt. A HTTP-várólista magas vagy növekvő hossza a nagy terhelés alatt lévő csomag tünete.|Példány|
|MemóriaTeljesítmény|Yes|Memória százalékos aránya|Százalék|Átlag|A csomag összes példánya által felhasznált átlagos memória.|Példány|
|SocketInboundAll|Yes|SocketInboundAll|Darabszám|Átlag|SocketInboundAll|Példány|
|SocketLoopback|Yes|SocketLoopback|Darabszám|Átlag|SocketLoopback|Példány|
|SocketOutboundAll|Yes|SocketOutboundAll|Darabszám|Átlag|SocketOutboundAll|Példány|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Darabszám|Átlag|SocketOutboundEstablished|Példány|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Darabszám|Átlag|SocketOutboundTimeWait|Példány|
|TcpCloseWait (TcpCloseWait)|Yes|TCP Close Wait|Darabszám|Átlag|TCP Close Wait|Példány|
|TcpClosing (Tcp-bezárás)|Yes|TCP-lezárás|Darabszám|Átlag|TCP-lezárás|Példány|
|TcpEstablished (TcpEstablished)|Yes|TCP-kapcsolat létrejött|Darabszám|Átlag|TCP-kapcsolat létrejött|Példány|
|TcpFinWait1|Yes|TCP Fin Wait 1|Darabszám|Átlag|TCP Fin Wait 1|Példány|
|TcpFinWait2|Yes|TCP Fin Wait 2|Darabszám|Átlag|TCP Fin Wait 2|Példány|
|TcpLastAck|Yes|TCP Last Ack|Darabszám|Átlag|TCP Last Ack|Példány|
|TcpSynReceived|Yes|Fogadott TCP Syn|Darabszám|Átlag|Fogadott TCP Syn|Példány|
|TcpSynSent|Yes|TCP Syn Elküldve|Darabszám|Átlag|TCP Syn Elküldve|Példány|
|TcpTimeWait (TcpTimeWait)|Yes|TCP-idő várakozása|Darabszám|Átlag|TCP-idő várakozása|Példány|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections (Alkalmazáscsatlakozások)|Yes|Kapcsolatok|Darabszám|Átlag|A sandboxban meglévő kötött szoftvercsatornák száma (w3wp.exe és annak gyermekfolyamatai). A kötött szoftvercsatornák létrehozása a bind()/connect() API-k meghívásával jön létre, és addig marad, amíg a szoftvercsatornát be nem zárja a CloseHandle()/closesocket() segítségével.|Példány|
|AverageMemoryWorkingSet|Yes|Átlagos memória-munkakészlet|Bájt|Átlag|Az alkalmazás által felhasznált memória átlagos mennyisége megabájtban (MiB).|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő (elavult)|Másodperc|Átlag|Az alkalmazás kérések kiszolgálása során igénybe vett átlagos idő másodpercben.|Példány|
|BájtokReceived|Yes|Adatok a következőben:|Bájt|Összesen|Az alkalmazás által felhasznált bejövő sávszélesség mib-beli mennyisége.|Példány|
|BájtokSent (bájtok)|Yes|Adatok ki|Bájt|Összesen|Az alkalmazás által felhasznált kimenő sávszélesség mib-beli mennyisége.|Példány|
|CpuTime (Processzoridő)|Yes|CPU-idő|Másodperc|Összesen|Az alkalmazás által felhasznált PROCESSZOR mennyisége másodpercben. További információ erről a metrikákról. Nem vonatkozik a Azure Functions. Lásd: https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (PROCESSZORidő és százalékos processzorhasználat).|Példány|
|CurrentAssemblies|Yes|Aktuális szerelvények|Darabszám|Átlag|Az alkalmazás összes AppDomainsében betöltött szerelvények aktuális száma.|Példány|
|FileSystemUsage (Fájlrendszer használata)|Yes|Fájlrendszer-használat|Bájt|Átlag|Az alkalmazás által felhasznált fájlrendszerkvóták százalékos aránya.|Nincsenek dimenziók|
|FunctionExecutionCount|Yes|Függvényvégrehajtások száma|Darabszám|Összesen|Függvényvégrehajtások száma. Csak a Azure Functions.|Példány|
|FunctionExecutionUnits|Yes|Függvény-végrehajtási egységek|Darabszám|Összesen|Függvény-végrehajtási egységek. Csak a Azure Functions.|Példány|
|Gen0Collections (0. generációscollections)|Yes|Gen 0 szemétgyűjtés|Darabszám|Összesen|A 0. generációs objektumok szemétgyűjtésének száma az alkalmazásfolyamat kezdete óta. A magasabb generációs GCs-k közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen1Collections (1. generációscollections)|Yes|Gen 1 szemétgyűjtés|Darabszám|Összesen|Az alkalmazásfolyamat kezdete óta az 1. generációs objektumok szemétgyűjtésének száma. A magasabb generációs GCs-k közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen2Collections (2. generációscollections)|Yes|Gen 2 szemétgyűjtés|Darabszám|Összesen|A 2. generációs objektumok szemétgyűjtésének száma az alkalmazásfolyamat kezdete óta.|Példány|
|Kezeli|Yes|Leírók száma|Darabszám|Átlag|Az alkalmazásfolyamat által jelenleg megnyitott leírók teljes száma.|Példány|
|HealthCheckStatus|Yes|Állapot-ellenőrzés|Darabszám|Átlag|Állapot-ellenőrzés|Példány|
|Http101|Yes|HTTP 101|Darabszám|Összesen|A 101-es HTTP-állapotkódot eredményező kérések száma.|Példány|
|Http2xx|Yes|Http 2xx|Darabszám|Összesen|A HTTP-állapotkódot = 200, de 300-< eredményező kérelmek száma.|Példány|
|Http3xx|Yes|Http 3xx|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 300, de < 400.|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|A HTTP 401-es állapotkódot eredményező kérelmek száma.|Példány|
|Http403|Yes|HTTP 403|Darabszám|Összesen|A HTTP 403-as állapotkódot eredményező kérelmek száma.|Példány|
|Http404|Yes|HTTP 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérelmek száma.|Példány|
|Http406|Yes|HTTP 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérelmek száma.|Példány|
|Http4xx|Yes|HTTP 4xx|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 400, de < 500.|Példány|
|Http5xx|Yes|HTTP-kiszolgálóval kapcsolatos hibák|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 500, de < 600.|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|Az az idő, amely alatt az alkalmazás kiszolgálja a kérelmeket, másodpercek alatt.|Példány|
|IoOtherBytesPerSecond|Yes|Egyéb bájtok másodpercenkénti I/O-száma|BájtperSecond (bájt/másodperc)|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat bájtokat ad ki olyan I/O-műveleteknek, amelyek nem tartalmaznak adatokat, például vezérlési műveleteket.|Példány|
|IoOtherOperationsPerSecond|Yes|I/O-műveletek másodpercenként|Bájt/másodperc|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat olyan I/O-műveleteket ad ki, amelyek nem olvasási vagy írási műveletek.|Példány|
|IoReadBytesPerSecond|Yes|I/O olvasási bájt/másodperc|BájtokPerSecond|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat bájtokat olvas be az I/O-műveletekből.|Példány|
|IoReadOperationsPerSecond|Yes|I/O-olvasási műveletek másodpercenként|BájtokPerSecond|Összesen|Az alkalmazásfolyamat olvasási I/O-műveletek kiadásának sebessége.|Példány|
|IoWriteBytesPerSecond|Yes|I/O írási bájt/másodperc|BájtokPerSecond|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat bájtokat ír az I/O-műveletekbe.|Példány|
|IoWriteOperationsPerSecond|Yes|I/O-írási műveletek másodpercenként|BájtokPerSecond|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat írási I/O-műveleteket ad ki.|Példány|
|MemoryWorkingSet (Memóriamunkakészlet)|Yes|Memória-munkakészlet|Bájt|Átlag|Az alkalmazás által aktuálisan használt memória mennyisége a MiB-ban.|Példány|
|PrivateBytes (Magánbájtok)|Yes|Saját bájtok|Bájt|Átlag|A Privát bájtok az alkalmazásfolyamat által lefoglalt memória aktuális mérete bájtban, amely nem megosztható más folyamatokkal.|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|A kérések teljes száma az eredményül kapott HTTP-állapotkódtól függetlenül.|Példány|
|RequestsInApplicationQueue|Yes|Kérelmek az alkalmazás üzenetsorában|Darabszám|Átlag|Az alkalmazáskérések várólistán lévő kérések száma.|Példány|
|Szálak|Yes|Szálak száma|Darabszám|Átlag|Az alkalmazásfolyamatban jelenleg aktív szálak száma.|Példány|
|TotalAppDomains (Összes alkalmazástartományok)|Yes|Alkalmazástartományok összesen|Darabszám|Átlag|Az alkalmazásban betöltött AppDomainek aktuális száma.|Példány|
|TotalAppDomainsUnloaded (TotalAppDomainsUnloaded)|Yes|Összes eltávolított alkalmazástartomány|Darabszám|Átlag|Az alkalmazás kezdete óta eltávolított AppDomain-ök teljes száma.|Példány|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections (Alkalmazáscsatlakozások)|Yes|Kapcsolatok|Darabszám|Átlag|A sandboxban meglévő kötött szoftvercsatornák száma (w3wp.exe és annak gyermekfolyamatai). A kötött szoftvercsatornák létrehozása a bind()/connect() API-k meghívásával jön létre, és addig marad, amíg a szoftvercsatornát be nem zárja a CloseHandle()/closesocket() segítségével.|Példány|
|AverageMemoryWorkingSet|Yes|Átlagos memória-munkakészlet|Bájt|Átlag|Az alkalmazás által felhasznált memória átlagos mennyisége megabájtban (MiB).|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő (elavult)|Másodperc|Átlag|Az alkalmazás kérések kiszolgálásának átlagos ideje másodpercben.|Példány|
|BájtokReceived|Yes|Adatok be|Bájt|Összesen|Az alkalmazás által felhasznált bejövő sávszélesség mib-beli mennyisége.|Példány|
|BájtokSent (bájtok)|Yes|Adatok ki|Bájt|Összesen|Az alkalmazás által felhasznált kimenő sávszélesség mib-beli mennyisége.|Példány|
|CpuTime (Processzoridő)|Yes|CPU-idő|Másodperc|Összesen|Az alkalmazás által felhasznált PROCESSZOR mennyisége másodpercben. További információ erről a metrikákról. Nem vonatkozik a Azure Functions. Lásd: https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (PROCESSZORidő és százalékos processzorhasználat).|Példány|
|CurrentAssemblies|Yes|Aktuális szerelvények|Darabszám|Átlag|Az alkalmazás összes AppDomainsében betöltött szerelvények aktuális száma.|Példány|
|FileSystemUsage (Fájlrendszer használata)|Yes|Fájlrendszer-használat|Bájt|Átlag|Az alkalmazás által felhasznált fájlrendszerkvóták százalékos aránya.|Nincsenek dimenziók|
|FunctionExecutionCount|Yes|Függvényvégrehajtások száma|Darabszám|Összesen|Függvényvégrehajtások száma|Példány|
|FunctionExecutionUnits|Yes|Függvény-végrehajtási egységek|Darabszám|Összesen|Függvény-végrehajtási egységek|Példány|
|Gen0Collections (0. generációscollections)|Yes|Gen 0 szemétgyűjtés|Darabszám|Összesen|A 0. generációs objektumok szemétgyűjtésének száma az alkalmazásfolyamat kezdete óta. A magasabb generációs GCs-k közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen1Collections (1. generációscollections)|Yes|Gen 1 szemétgyűjtés|Darabszám|Összesen|Az alkalmazásfolyamat kezdete óta az 1. generációs objektumok szemétgyűjtésének száma. A magasabb generációs GCs-k közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen2Collections (2. generációscollections)|Yes|Gen 2 szemétgyűjtés|Darabszám|Összesen|A 2. generációs objektumok szemétgyűjtésének száma az alkalmazásfolyamat kezdete óta.|Példány|
|Kezeli|Yes|Leírók száma|Darabszám|Átlag|Az alkalmazásfolyamat által jelenleg megnyitott leírók teljes száma.|Példány|
|HealthCheckStatus|Yes|Állapot-ellenőrzés|Darabszám|Átlag|Állapot-ellenőrzés|Példány|
|Http101|Yes|HTTP 101|Darabszám|Összesen|A 101-es HTTP-állapotkódot eredményező kérések száma.|Példány|
|Http2xx|Yes|Http 2xx|Darabszám|Összesen|A HTTP-állapotkódot = 200, de 300-< eredményező kérések száma.|Példány|
|Http3xx|Yes|Http 3xx|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 300, de < 400.|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|A HTTP 401-es állapotkódot eredményező kérelmek száma.|Példány|
|Http403|Yes|HTTP 403|Darabszám|Összesen|A HTTP 403-as állapotkódot eredményező kérelmek száma.|Példány|
|Http404|Yes|HTTP 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérelmek száma.|Példány|
|Http406|Yes|HTTP 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérelmek száma.|Példány|
|Http4xx|Yes|Http 4xx|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 400, de < 500.|Példány|
|Http5xx|Yes|HTTP-kiszolgálóval kapcsolatos hibák|Darabszám|Összesen|A HTTP-állapotkódot eredményező kérések száma = 500, de < 600.|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|Az az idő, amely alatt az alkalmazás kiszolgálja a kérelmeket, másodpercek alatt.|Példány|
|IoOtherBytesPerSecond|Yes|Egyéb bájtok másodpercenkénti I/O-száma|BájtperSecond (bájt/másodperc)|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat bájtokat ad ki olyan I/O-műveleteknek, amelyek nem tartalmaznak adatokat, például vezérlési műveleteket.|Példány|
|IoOtherOperationsPerSecond|Yes|I/O – egyéb műveletek másodpercenként|BájtokPerSecond|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat olyan I/O-műveleteket ad ki, amelyek nem olvasási vagy írási műveletek.|Példány|
|IoReadBytesPerSecond|Yes|I/O olvasási bájt/másodperc|BájtokPerSecond|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat bájtokat olvas be az I/O-műveletekből.|Példány|
|IoReadOperationsPerSecond|Yes|I/O-olvasási műveletek másodpercenként|BájtokPerSecond|Összesen|Az alkalmazásfolyamat olvasási I/O-műveletek kiadásának sebessége.|Példány|
|IoWriteBytesPerSecond|Yes|I/O írási bájt/másodperc|BájtokPerSecond|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat bájtokat ír az I/O-műveletekbe.|Példány|
|IoWriteOperationsPerSecond|Yes|I/O-írási műveletek másodpercenként|BájtokPerSecond|Összesen|Az a sebesség, amellyel az alkalmazásfolyamat írási I/O-műveleteket ad ki.|Példány|
|MemoryWorkingSet (Memóriamunkakészlet)|Yes|Memória-munkakészlet|Bájt|Átlag|Az alkalmazás által aktuálisan használt memória mennyisége a MiB-ban.|Példány|
|PrivateBytes (Privát memória)|Yes|Saját bájtok|Bájt|Átlag|A Privát bájtok az alkalmazásfolyamat által lefoglalt memória aktuális mérete bájtban, amely más folyamatokkal nem megosztható.|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|A kérések teljes száma az eredményül kapott HTTP-állapotkódtól függetlenül.|Példány|
|RequestsInApplicationQueue|Yes|Kérelmek az alkalmazás üzenetsorában|Darabszám|Átlag|Az alkalmazáskérések várólistán lévő kérések száma.|Példány|
|Szálak|Yes|Szálak száma|Darabszám|Átlag|Az alkalmazásfolyamatban jelenleg aktív szálak száma.|Példány|
|TotalAppDomains (Összes alkalmazástartományok)|Yes|Alkalmazástartományok összesen|Darabszám|Átlag|Az alkalmazásban betöltött AppDomainek aktuális száma.|Példány|
|TotalAppDomainsUnloaded (TotalAppDomainsUnloaded)|Yes|Összes eltávolított alkalmazástartomány|Darabszám|Átlag|Az alkalmazás kezdete óta eltávolított AppDomain-ök teljes száma.|Példány|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|Metric|Exportálható a diagnosztikai beállításokon keresztül?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BájtokSent (bájtok)|Yes|Adatok ki|Bájt|Összesen|BájtokSent (bájtok)|Példány|
|FunctionErrors (Függvényerrorok)|Yes|FunctionErrors (Függvényerrorok)|Darabszám|Összesen|FunctionErrors (Függvényerrorok)|Példány|
|FunctionHits|Yes|FunctionHits|Darabszám|Összesen|FunctionHits|Példány|
|SiteErrors (Helyerrorok)|Yes|SiteErrors (Helyerrorok)|Darabszám|Összesen|SiteErrors (Helyerrorok)|Példány|
|SiteHits|Yes|SiteHits|Darabszám|Összesen|SiteHits|Példány|

## <a name="next-steps"></a>Következő lépések

- [További információ a metrikákról a Azure Monitor](../data-platform.md)
- [Riasztások létrehozása metrikákhoz](../alerts/alerts-overview.md)
- [Metrikák exportálása tárolóba, Event Hubba vagy Log Analyticsbe](../essentials/platform-logs-overview.md)
