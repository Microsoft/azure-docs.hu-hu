---
title: Azure Monitor támogatott metrikák erőforrástípus szerint
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 02/06/2021
ms.author: robb
ms.openlocfilehash: 2437ab80a23ffc39c180bcdf72921fdf13768541
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033503"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

> [!NOTE]
> Ez a lista nagyrészt automatikusan jön létre. A listán a GitHubon keresztül végrehajtott módosítások figyelmeztetés nélkül is megírhatók. A végleges frissítések végrehajtásával kapcsolatban a cikk szerzője nyújt tájékoztatást.

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. 

Ez a cikk a Azure Monitor konszolidált metrikus folyamatával jelenleg elérhető összes platform (azaz automatikusan összegyűjtött) mérőszámok teljes listáját tartalmazza. A cikk elején található dátum után módosított vagy hozzáadott mérőszámok nem jelennek meg alább. A metrikák listájának programozott módon történő lekérdezéséhez és eléréséhez használja a [2018-01-01 API-Version](/rest/api/monitor/metricdefinitions). A listán nem szereplő egyéb mérőszámok a portálon vagy a régi API-k használatával is elérhetők.

A metrikák az erőforrás-szolgáltatók és az erőforrástípus szerint vannak rendezve. A szolgáltatások, valamint az azokhoz tartozó erőforrás-szolgáltatók és típusok listáját az Azure- [szolgáltatások erőforrás-szolgáltatói](../../azure-resource-manager/management/azure-services-resource-providers.md)című részében tekintheti meg.  

## <a name="exporting-platform-metrics-to-other-locations"></a>Platform metrikáinak exportálása más helyszínekre

Az Azure monitor folyamatának platform metrikáit kétféleképpen exportálhatja más helyekre.
1. A [metrikák REST API](/rest/api/monitor/metrics/list) használata
2. [Diagnosztikai beállítások](../essentials/diagnostic-settings.md) használata a platform metrikáinak átirányításához 
    - Azure Storage
    - Naplók Azure Monitor (és így Log Analytics)
    - Event hubok, amelyekkel a nem a Microsofttól származó rendszerekhez juthat 

A diagnosztikai beállítások használata a metrikák átirányításának legegyszerűbb módja, de bizonyos korlátozások vannak: 

- **Néhány nem exportálható** – az összes metrika exportálható a REST API használatával, néhányat azonban nem lehet a diagnosztikai beállítások használatával exportálni a Azure monitor háttérbeli munkafolyamatok miatt. Az alábbi táblázatokban szereplő *diagnosztikai beállításokkal exportálható* oszlop, amely így exportálható.  

- **Többdimenziós mérőszámok** – a többdimenziós metrikák más helyekre való küldése jelenleg nem támogatott a diagnosztikai beállítások használatával. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel. *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.

## <a name="guest-os-and-host-os-metrics"></a>Vendég operációs rendszer és a gazdagép operációs rendszerének mérőszámai

> [!WARNING]
> Az Azure Virtual Machines, Service Fabric és Cloud Services operációs rendszert futtató vendég operációs rendszer mérőszámai itt **nem** szerepelnek. A vendég operációs rendszer metrikáit egy vagy több olyan ügynökön keresztül kell gyűjteni, amely a vendég operációs rendszer részeként fut vagy sem.  A vendég operációs rendszer metrikái olyan teljesítményszámlálók, amelyek a vendég CPU-százalékos vagy memóriahasználat nyomon követésére szolgálnak, amelyek közül mindkettő gyakran használatos automatikus méretezéshez vagy riasztáshoz. 
>
> **A gazdagép operációs rendszer metrikái elérhetők és alább láthatók.** Ezek nem azonosak. A gazdagép operációs rendszer metrikái a vendég operációs rendszer munkamenetét működtető Hyper-V-munkamenethez kapcsolódnak. 

> [!TIP]
> Az ajánlott eljárás az [Azure Diagnostics-bővítmény](../agents/diagnostics-extension-overview.md) használata és konfigurálása, hogy a vendég operációs rendszer teljesítményének mérőszámait ugyanarra a Azure monitor metrikus adatbázisba küldje el, ahol a platform metrikái vannak tárolva. A bővítmény a vendég operációs rendszer metrikáit az [Egyéni metrikák](../essentials/metrics-custom-overview.md) API-n keresztül irányítja. Ezután diagramot, riasztást és egyéb módon használhatja a vendég operációs rendszer metrikáit, például a platform metrikáit. Emellett a Log Analytics ügynökkel is elküldheti a vendég operációs rendszer metrikáit Azure Monitor naplókba/Log Analytics. A mérőszámokat nem metrikus adatokkal kombinálva is lekérdezheti. 

További információ: [monitoring Agents – áttekintés](../agents/agents-overview.md).

## <a name="table-formatting"></a>Táblázat formázása

> [!IMPORTANT] 
> Ez a legújabb frissítés egy új oszlopot hoz létre, és átrendezi a mérőszámokat ABC-ként. A hozzáadási információ azt jelenti, hogy az alábbi táblázatok a böngészőablak szélessége alapján vízszintes görgetősávtal rendelkezhetnek az alsó sarokban. Ha úgy véli, hogy hiányoznak az adatok, a görgetősávon láthatja a teljes táblázatot.

## <a name="microsoftaadiamazureadmetrics"></a>Microsoft. aadiam/azureADMetrics

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|Darabszám|Átlag|azureADMetrics típusú metrika|Nincsenek méretek|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Memória: tisztító – aktuális ár|Darabszám|Átlag|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Memória: nem csökkenthető a tisztább memória|Bájt|Átlag|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|ServerResourceType|
|CleanerMemoryShrinkable|Yes|Memória: tisztító memória csökkenthető|Bájt|Átlag|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Szálak: a parancssori készlet foglalt szálak|Darabszám|Átlag|A parancsfájl-készletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Szálak: parancssori készlet üresjárati szálai|Darabszám|Átlag|Az üresjárati szálak száma a parancs szálának készletében.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Parancssori feladatok várólistájának hossza|Darabszám|Átlag|A parancsfájl-készlet várólistájában lévő feladatok száma.|ServerResourceType|
|Összege|Yes|Kapcsolat: aktuális kapcsolatok|Darabszám|Átlag|A létesített ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CurrentUserSessions|Yes|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrejött felhasználói munkamenetek aktuális száma.|ServerResourceType|
|LongParsingBusyThreads|Yes|Szálak: hosszú elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingIdleThreads|Yes|Szálak: tartós folyamatok elemzése – üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Szálak: hosszú elemzési feladatok várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|mashup_engine_memory_metric|Yes|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|M motor saját bájtjai|Bájt|Átlag|Saját bájtok használata az adategyesítési motor folyamatai alapján.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|M motor QPU|Darabszám|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|M motor virtuális bájtjai|Bájt|Átlag|Az adategyesítési motor folyamatai által használt virtuális bájtok használata.|ServerResourceType|
|memory_metric|Yes|Memória|Bájt|Átlag|Memory. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|memory_thrashing_metric|Yes|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|MemoryLimitHard|Yes|Memória: rögzített memória|Bájt|Átlag|Rögzített memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Yes|Memória: magas a memória korlátozása|Bájt|Átlag|Magas memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Yes|Memória: kevés a memória korlátja|Bájt|Átlag|Kevés a memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Memória: VertiPaq korlátja|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|MemoryUsage|Yes|Memória: memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memória által leképezett adatmennyiséggel, figyelmen kívül hagyva azokat a memóriát, amelyeket a xVelocity memórián belüli elemzési motorja (VertiPaq) leképezett vagy lefoglalt, és amely meghaladja a xVelocity-memória korlátját.|ServerResourceType|
|private_bytes_metric|Yes|Saját bájtok|Bájt|Átlag|Saját bájtok.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai|Darabszám|Átlag|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva|Darabszám|Átlag|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Szálak: feldolgozási készlet üresjáratban nem I/O-szálai|Darabszám|Átlag|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Feldolgozási készlet nyomtatási várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|ServerResourceType|
|qpu_metric|Yes|QPU|Darabszám|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|QueryPoolBusyThreads|Yes|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szál készletében lévő foglalt szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Szálak: lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Szálak: lekérdezési készlet – feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|Kvóta|Yes|Memória: kvóta|Bájt|Átlag|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|ServerResourceType|
|QuotaBlocked|Yes|Memória: blokkolt kvóta|Darabszám|Átlag|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|ServerResourceType|
|RowsConvertedPerSec|Yes|Feldolgozás: másodpercenként konvertált sorok száma|CountPerSecond|Átlag|A sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsReadPerSec|Yes|Feldolgozás: másodpercenként beolvasott sorok száma|CountPerSecond|Átlag|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|ServerResourceType|
|RowsWrittenPerSec|Yes|Feldolgozás: másodpercenként írt sorok száma|CountPerSecond|Átlag|A sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Szálak: rövid elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Szálak: rövid elemzési üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Szálak: rövid elemzési feladatok várólistájának hossza|Darabszám|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Sikeres kapcsolatok másodpercenként|CountPerSecond|Átlag|A sikeres kapcsolatok gyakorisága.|ServerResourceType|
|TotalConnectionFailures|Yes|Összes sikertelen Kapcsolatfelvétel|Darabszám|Átlag|A sikertelen csatlakozási kísérletek teljes száma.|ServerResourceType|
|TotalConnectionRequests|Yes|Kapcsolatkérelmek teljes száma|Darabszám|Átlag|A kapcsolatkérelmek teljes száma. Ezek az érkezések.|ServerResourceType|
|VertiPaqNonpaged|Yes|Memória: VertiPaq, nem lapozható|Bájt|Átlag|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|ServerResourceType|
|VertiPaqPaged|Yes|Memória: VertiPaq lapozható|Bájt|Átlag|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|ServerResourceType|
|virtual_bytes_metric|Yes|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|A háttérbeli kérelmek időtartama|Ezredmásodpercben|Átlag|A háttérbeli kérelmek időtartama (ezredmásodpercben)|Hely, állomásnév|
|Kapacitás|Yes|Kapacitás|Százalék|Átlag|A ApiManagement szolgáltatás kihasználtsági metrikája|Hely|
|Időtartam|Yes|Az átjárók kéréseinek teljes időtartama|Ezredmásodpercben|Átlag|Az átjáró kérelmek teljes időtartama ezredmásodpercben|Hely, állomásnév|
|EventHubDroppedEvents|Yes|Eldobott EventHub események|Darabszám|Összesen|A várólista-méretkorlát elérte miatt kihagyott események száma|Hely|
|EventHubRejectedEvents|Yes|EventHub-események visszautasítva|Darabszám|Összesen|Visszautasított EventHub-események száma (helytelen konfiguráció vagy jogosulatlan)|Hely|
|EventHubSuccessfulEvents|Yes|Sikeres EventHub események|Darabszám|Összesen|Sikeres EventHub események száma|Hely|
|EventHubThrottledEvents|Yes|Szabályozott EventHub események|Darabszám|Összesen|A szabályozott EventHub események száma|Hely|
|EventHubTimedoutEvents|Yes|Időtúllépés a EventHub eseményeinél|Darabszám|Összesen|Az időtúllépés miatti EventHub események száma|Hely|
|EventHubTotalBytesSent|Yes|EventHub-események mérete|Bájt|Összesen|EventHub-események teljes mérete bájtban|Hely|
|EventHubTotalEvents|Yes|Összes EventHub esemény|Darabszám|Összesen|A EventHub eljuttatott események száma|Hely|
|EventHubTotalFailedEvents|Yes|Sikertelen EventHub események|Darabszám|Összesen|Sikertelen EventHub események száma|Hely|
|FailedRequests|Yes|Sikertelen átjáró-kérelmek (elavult)|Darabszám|Összesen|Hibák száma az átjáró kéréseiben – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|NetworkConnectivity|Yes|Erőforrások hálózati kapcsolati állapota (előzetes verzió)|Darabszám|Átlag|A API Management szolgáltatástól függő erőforrástípusok hálózati kapcsolati állapota|Hely, ResourceType|
|OtherRequests|Yes|Egyéb átjáró-kérelmek (elavult)|Darabszám|Összesen|Egyéb átjáró-kérelmek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Több dimenzióval rendelkező átjáró-kérelmek metrikái|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Sikeres átjáró-kérelmek (elavult)|Darabszám|Összesen|Sikeres átjáró-kérések száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzió helyett|Hely, állomásnév|
|TotalRequests|Yes|Összes átjáró kérelme (elavult)|Darabszám|Összesen|Átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|UnauthorizedRequests|Yes|Nem engedélyezett átjáró-kérelmek (elavult)|Darabszám|Összesen|Jogosulatlan átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Darabszám|Darabszám|A bejövő HTTP-kérelmek teljes száma.|StatusCode, hitelesítés|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Darabszám|Átlag|Egy HTTP-kérelem késése.|StatusCode, hitelesítés|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Darabszám|Darabszám|Szabályozott HTTP-kérelmek.|Nincsenek méretek|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|aktív – időzítő – darabszám|Yes|aktív – időzítő – darabszám|Darabszám|Átlag|Jelenleg aktív Időzítők száma|Üzembe helyezés, AppName, Pod|
|foglalási – arány|Yes|foglalási – arány|Bájt|Átlag|A felügyelt halomban lefoglalt bájtok száma|Üzembe helyezés, AppName, Pod|
|AppCpuUsage|Yes|Alkalmazás CPU-használata |Százalék|Átlag|Az alkalmazás legutóbbi CPU-használata|Üzembe helyezés, AppName, Pod|
|szerelvény – darabszám|Yes|szerelvény – darabszám|Darabszám|Átlag|Betöltött szerelvények száma|Üzembe helyezés, AppName, Pod|
|CPU – használat|Yes|CPU – használat|Százalék|Átlag|a folyamat%-os kihasználtsága (%)|Üzembe helyezés, AppName, Pod|
|aktuális – kérelmek|Yes|aktuális – kérelmek|Darabszám|Átlag|A folyamat teljes élettartamában folyamatban lévő kérelmek száma összesen|Üzembe helyezés, AppName, Pod|
|kivételek száma|Yes|kivételek száma|Darabszám|Összesen|Kivételek száma|Üzembe helyezés, AppName, Pod|
|Sikertelen – kérelmek|Yes|Sikertelen – kérelmek|Darabszám|Átlag|A sikertelen kérelmek teljes száma a folyamat élettartamában|Üzembe helyezés, AppName, Pod|
|GC-heap-size|Yes|GC-heap-size|Darabszám|Átlag|A GC által jelentett teljes halom mérete (MB)|Üzembe helyezés, AppName, Pod|
|Gen-0-GC-darabszám|Yes|Gen-0-GC-darabszám|Darabszám|Átlag|0. generációs GCs száma|Üzembe helyezés, AppName, Pod|
|Gen-0 – méret|Yes|Gen-0 – méret|Bájt|Átlag|0. generációs halom mérete|Üzembe helyezés, AppName, Pod|
|Gen-1 – GC-darabszám|Yes|Gen-1 – GC-darabszám|Darabszám|Átlag|1. generációs GCs száma|Üzembe helyezés, AppName, Pod|
|1. generációs méret|Yes|1. generációs méret|Bájt|Átlag|1. generációs halom mérete|Üzembe helyezés, AppName, Pod|
|Gen-2 – GC – darabszám|Yes|Gen-2 – GC – darabszám|Darabszám|Átlag|A 2. generációs GCs száma|Üzembe helyezés, AppName, Pod|
|2. generációs méret|Yes|2. generációs méret|Bájt|Átlag|2. generációs halom mérete|Üzembe helyezés, AppName, Pod|
|JVM. GC. Live. Resize|Yes|JVM. GC. Live. Resize|Bájt|Átlag|Régi generációs memória-készlet mérete teljes GC után|Üzembe helyezés, AppName, Pod|
|JVM. GC. max. az adatméret|Yes|JVM. GC. max. az adatméret|Bájt|Átlag|A régi generációs memória maximális mérete|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. lefoglalt|Yes|JVM. GC. Memory. lefoglalt|Bájt|Maximum|Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután egy GC a következőre kerül|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. előléptetve|Yes|JVM. GC. Memory. előléptetve|Bájt|Maximum|A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC a GC-re lenne|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Count|Yes|JVM. GC. pause. Total. Count|Darabszám|Összesen|GC-szüneteltetések száma|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Time|Yes|JVM. GC. pause. Total. Time|Ezredmásodpercben|Összesen|GC felfüggesztésének teljes ideje|Üzembe helyezés, AppName, Pod|
|JVM. Memory. véglegesített|Yes|JVM. Memory. véglegesített|Bájt|Átlag|JVM rendelt memória bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. max|Yes|JVM. Memory. max|Bájt|Maximum|A memória-kezeléshez használható memória maximális mennyisége bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. felhasználva|Yes|JVM. Memory. felhasználva|Bájt|Átlag|Az alkalmazás memóriája bájtban használatban|Üzembe helyezés, AppName, Pod|
|Kissné méret|Yes|Kissné méret|Bájt|Átlag|Halmozott halom mérete|Üzembe helyezés, AppName, Pod|
|figyelő – zárolási tartalom – darabszám|Yes|figyelő – zárolási tartalom – darabszám|Darabszám|Átlag|A figyelő zárolásának megkísérlése során megjelenő időpontok száma|Üzembe helyezés, AppName, Pod|
|Process. CPU. használat|Yes|Process. CPU. használat|Százalék|Átlag|A JVM folyamat legutóbbi CPU-használata|Üzembe helyezés, AppName, Pod|
|kérelmek/másodperc|Yes|kérelmek – arány|Darabszám|Átlag|Kérelmek gyakorisága|Üzembe helyezés, AppName, Pod|
|System. CPU. használat|Yes|System. CPU. használat|Százalék|Átlag|A legújabb CPU-használat a teljes rendszerhez|Üzembe helyezés, AppName, Pod|
|szálkészlet munkaszála belépett – befejezett elemek – darabszám|Yes|szálkészlet munkaszála belépett – befejezett elemek – darabszám|Darabszám|Átlag|A szálkészlet munkaszála belépett befejezte a munkaelemek darabszámát|Üzembe helyezés, AppName, Pod|
|szálkészlet munkaszála belépett – várólista hossza|Yes|szálkészlet munkaszála belépett – várólista hossza|Darabszám|Átlag|Szálkészlet munkaszála belépett munkaelemek várólistájának hossza|Üzembe helyezés, AppName, Pod|
|szálkészlet munkaszála belépett – szálak száma|Yes|szálkészlet munkaszála belépett – szálak száma|Darabszám|Átlag|Szálkészlet munkaszála belépett-szálak száma|Üzembe helyezés, AppName, Pod|
|idő – GC|Yes|idő – GC|Százalék|Átlag|%-os idő a GC-ben az utolsó GC óta|Üzembe helyezés, AppName, Pod|
|tomcat. Global. error|Yes|tomcat. Global. error|Darabszám|Összesen|Tomcat globális hiba|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Received|Yes|tomcat. Global. Received|Bájt|Összesen|Tomcat összesen fogadott bájtok|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. AVG. Time|Yes|tomcat. Global. Request. AVG. Time|Ezredmásodpercben|Átlag|Tomcat-kérelem átlagos ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. max|Yes|tomcat. Global. Request. max|Ezredmásodpercben|Maximum|Tomcat-kérelem maximális ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Count|Yes|tomcat. Global. Request. Total. Count|Darabszám|Összesen|Tomcat-kérelem összesített száma|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Time|Yes|tomcat. Global. Request. Total. Time|Ezredmásodpercben|Összesen|Tomcat-kérelem teljes ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. elküldve|Yes|tomcat. Global. elküldve|Bájt|Összesen|Tomcat összesen eljuttatott bájtok száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. Current|Yes|tomcat. Sessions. Active. Current|Darabszám|Összesen|Tomcat-munkamenet aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. max|Yes|tomcat. Sessions. Active. max|Darabszám|Összesen|Tomcat-munkamenet maximális aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Alive. max|Yes|tomcat. Sessions. Alive. max|Ezredmásodpercben|Maximum|Tomcat-munkamenet maximális élettartama|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. created|Yes|tomcat. Sessions. created|Darabszám|Összesen|Tomcat-munkamenet létrehozva szám|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. lejárt|Yes|tomcat. Sessions. lejárt|Darabszám|Összesen|A Tomcat-munkamenet lejárt|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. elutasítva|Yes|tomcat. Sessions. elutasítva|Darabszám|Összesen|A Tomcat-munkamenet elutasította a darabszámot|Üzembe helyezés, AppName, Pod|
|tomcat.threads.config. max|Yes|tomcat.threads.config. max|Darabszám|Összesen|Tomcat-konfiguráció maximális szálának száma|Üzembe helyezés, AppName, Pod|
|tomcat. Threads. Current|Yes|tomcat. Threads. Current|Darabszám|Összesen|Tomcat jelenlegi szálak száma|Üzembe helyezés, AppName, Pod|
|kérelmek összesen|Yes|kérelmek összesen|Darabszám|Átlag|A folyamat élettartamán belüli kérelmek teljes száma|Üzembe helyezés, AppName, Pod|
|munkakészlet|Yes|munkakészlet|Darabszám|Átlag|A folyamat által használt munkakészlet mennyisége (MB)|Üzembe helyezés, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Feladatok összesen|Darabszám|Összesen|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentMachineRuns|Yes|Összes frissítés központi telepítési gép futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Összes frissítés központi telepítési futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, állapot|


## <a name="microsoftavsprivateclouds"></a>Microsoft. AVS/privateClouds

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CapacityLatest|Yes|Adattár lemezének teljes kapacitása|Bájt|Átlag|Az adattárban lévő lemez teljes kapacitása|DSNAME|
|DiskUsedPercentage|Yes| Felhasznált adattároló-lemez százalékos aránya|Százalék|Átlag|Az adattárban használt szabad lemez százaléka|DSNAME|
|EffectiveCpuAverage|Yes|Százalékos processzorhasználat|Százalék|Átlag|A fürtben használt CPU-erőforrások százalékos aránya|clustername|
|EffectiveMemAverage|Yes|Átlagos tényleges memória|Bájt|Átlag|A fürtben rendelkezésre álló teljes memória mennyisége|clustername|
|OverheadAverage|Yes|Átlagos memória terhelése|Bájt|Átlag|A virtualizációs infrastruktúra által felhasznált fizikai memória üzemeltetése|clustername|
|TotalMbAverage|Yes|Teljes memória átlagos száma|Bájt|Átlag|Teljes memória a fürtben|clustername|
|UsageAverage|Yes|Memóriahasználat átlagos kihasználtsága|Százalék|Átlag|Memóriahasználat a teljes konfigurált vagy rendelkezésre álló memória százalékaként|clustername|
|UsedLatest|Yes|Felhasznált adattár-lemez|Bájt|Átlag|Az adattárban használt teljes lemez mennyisége|DSNAME|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CoreCount|No|Dedikált mag száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált magok teljes száma|Nincsenek méretek|
|CreatingNodeCount|No|Csomópontok számának létrehozása|Darabszám|Összesen|Létrehozandó csomópontok száma|Nincsenek méretek|
|IdleNodeCount|No|Tétlen csomópontok száma|Darabszám|Összesen|Üresjárati csomópontok száma|Nincsenek méretek|
|JobDeleteCompleteEvent|Yes|Feladatok törlése – befejezett események|Darabszám|Összesen|A sikeresen törölt feladatok teljes száma.|jobId|
|JobDeleteStartEvent|Yes|Feladatok törlésének indítási eseményei|Darabszám|Összesen|A törölni kívánt feladatok teljes száma.|jobId|
|JobDisableCompleteEvent|Yes|A feladatok letiltják a teljes eseményeket|Darabszám|Összesen|A sikeresen letiltott feladatok teljes száma.|jobId|
|JobDisableStartEvent|Yes|Feladatok letiltásának indítási eseményei|Darabszám|Összesen|A letiltani kívánt feladatok teljes száma.|jobId|
|JobStartEvent|Yes|Feladatok indítási eseményei|Darabszám|Összesen|A sikeresen elindított feladatok teljes száma.|jobId|
|JobTerminateCompleteEvent|Yes|A feladatokhoz tartozó befejezett események befejezése|Darabszám|Összesen|A sikeresen leállított feladatok teljes száma.|jobId|
|JobTerminateStartEvent|Yes|Feladatokból indított események leállítása|Darabszám|Összesen|A leállítani kívánt feladatok teljes száma.|jobId|
|LeavingPoolNodeCount|No|A készlet-csomópontok számának elhagyása|Darabszám|Összesen|A készletet elhagyó csomópontok száma|Nincsenek méretek|
|LowPriorityCoreCount|No|LowPriority mag száma|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincsenek méretek|
|OfflineNodeCount|No|Offline csomópontok száma|Darabszám|Összesen|Offline csomópontok száma|Nincsenek méretek|
|PoolCreateEvent|Yes|Készlet-létrehozási események|Darabszám|Összesen|A létrehozott készletek teljes száma|poolId|
|PoolDeleteCompleteEvent|Yes|Készlet törlése – befejezett események|Darabszám|Összesen|A befejezett készlet-törlések teljes száma|poolId|
|PoolDeleteStartEvent|Yes|Készlet törlése – indítási események|Darabszám|Összesen|Az elindított készlet-törlések száma összesen|poolId|
|PoolResizeCompleteEvent|Yes|Készlet átméretezése – befejezett események|Darabszám|Összesen|A készlet összes újraméretezésének száma|poolId|
|PoolResizeStartEvent|Yes|Készlet átméretezésének indítási eseményei|Darabszám|Összesen|A készlet elindított újraméretezésének teljes száma|poolId|
|PreemptedNodeCount|No|Előzik-csomópontok száma|Darabszám|Összesen|Előzik-csomópontok száma|Nincsenek méretek|
|RebootingNodeCount|No|Csomópontok számának újraindítása|Darabszám|Összesen|Újraindítási csomópontok száma|Nincsenek méretek|
|ReimagingNodeCount|No|Csomópontok rendszerképének alaphelyzetbe állítása|Darabszám|Összesen|Rendszerkép-csomópontok száma|Nincsenek méretek|
|RunningNodeCount|No|Csomópontok száma|Darabszám|Összesen|Futó csomópontok száma|Nincsenek méretek|
|StartingNodeCount|No|Csomópontok számának indítása|Darabszám|Összesen|Kiinduló csomópontok száma|Nincsenek méretek|
|StartTaskFailedNodeCount|No|Indítási feladat sikertelen csomópontok száma|Darabszám|Összesen|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|Nincsenek méretek|
|TaskCompleteEvent|Yes|Feladat teljes eseményei|Darabszám|Összesen|A Befejezett feladatok teljes száma|poolId, jobId|
|TaskFailEvent|Yes|Feladat sikertelen eseményei|Darabszám|Összesen|A meghiúsult állapotban Befejezett feladatok teljes száma|poolId, jobId|
|TaskStartEvent|Yes|Tevékenységek indítási eseményei|Darabszám|Összesen|Az elindított feladatok teljes száma|poolId, jobId|
|TotalLowPriorityNodeCount|No|Low-Priority csomópontok száma|Darabszám|Összesen|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincsenek méretek|
|TotalNodeCount|No|Dedikált csomópontok száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált csomópontok teljes száma|Nincsenek méretek|
|UnusableNodeCount|No|Nem használható csomópontok száma|Darabszám|Összesen|Használhatatlan csomópontok száma|Nincsenek méretek|
|WaitingForStartTaskNodeCount|No|Várakozás a feladat-csomópontok számának megkezdésére|Darabszám|Összesen|Az indítási tevékenység befejezésére váró csomópontok száma|Nincsenek méretek|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Yes|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Yes|Aktív csomópontok|Darabszám|Átlag|Futó csomópontok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Yes|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Yes|Tétlen csomópontok|Darabszám|Átlag|Üresjárati csomópontok száma|Forgatókönyv, ClusterName|
|A feladatok befejeződtek|Yes|A feladatok befejeződtek|Darabszám|Összesen|Befejezett feladatok száma|Forgatókönyv, ClusterName, ResultType|
|Feladatok elküldve|Yes|Feladatok elküldve|Darabszám|Összesen|Elküldött feladatok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Yes|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Yes|Csomópontok elhagyása|Darabszám|Átlag|Kilépő csomópontok száma|Forgatókönyv, ClusterName|
|Előzik magok|Yes|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Yes|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Yes|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Magok összesen|Yes|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Yes|Csomópontok összesen|Darabszám|Átlag|Összes csomópont száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Yes|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Yes|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma|Forgatókönyv, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|Darabszám|Átlag|A feldolgozott tranzakciók száma.|Csomópont, csatorna, típus, állapot|
|ChaincodeExecuteTimeouts|Yes|ChaincodeExecuteTimeoutsDisplayName|Darabszám|Átlag|Az időkorláttal rendelkező chaincode-végrehajtások (init vagy meghívás) száma.|Csomópont, chaincode|
|ChaincodeLaunchFailures|Yes|ChaincodeLaunchFailuresDisplayName|Darabszám|Átlag|A sikertelenül indított chaincode száma.|Csomópont, chaincode|
|ChaincodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|Darabszám|Átlag|Az időtúllépés miatt elindított chaincode száma.|Csomópont, chaincode|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Darabszám|Átlag|A chaincode-kiigazítási kérelmek száma befejeződött.|Csomópont, típus, csatorna, chaincode, sikeres|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott chaincode-kiigazítási kérelmek száma.|Csomópont, típus, csatorna, chaincode|
|ClusterCommEgressQueueCapacity|Yes|ClusterCommEgressQueueCapacityDisplayName|Darabszám|Átlag|A kimenő forgalom várólistájának kapacitása.|Csomópont, gazdagép, msg_type, csatorna|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Darabszám|Átlag|A kimenő forgalom várólistájának hossza.|Csomópont, gazdagép, msg_type, csatorna|
|ClusterCommEgressQueueWorkers|Yes|ClusterCommEgressQueueWorkersDisplayName|Darabszám|Átlag|A kimenő várólista-feldolgozók száma.|Csomópont, csatorna|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|Darabszám|Átlag|A többi csomóponthoz tartozó adatfolyamok száma.|Csomópont, csatorna|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Darabszám|Átlag|Más csomópontokhoz tartozó TLS-kapcsolatok száma.|Csomópont|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Darabszám|Átlag|A többi csomóponttól származó adatfolyamok száma.|Csomópont|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Darabszám|Átlag|Eldobott üzenetek száma.|Csomópont, állomás, csatorna|
|ConnectionAccepted|Yes|Elfogadott kapcsolatok|Darabszám|Összesen|Elfogadott kapcsolatok|Csomópont|
|ConnectionActive|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Csomópont|
|ConnectionHandled|Yes|Kezelt kapcsolatok|Darabszám|Összesen|Kezelt kapcsolatok|Csomópont|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Darabszám|Átlag|A csatorna aktív csomópontjainak száma.|Csomópont, csatorna|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Darabszám|Átlag|A csatorna csomópontjainak száma.|Csomópont, csatorna|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Darabszám|Átlag|A legutóbb véglegesített blokk blokkjának száma.|Csomópont, csatorna|
|ConsensusEtcdraftConfigProposalsReceived|Yes|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Darabszám|Átlag|A konfigurációs típusú tranzakciókhoz fogadott javaslatok teljes száma.|Csomópont, csatorna|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|Darabszám|Átlag|Az aktuális csomópont vezetői állapota: 1, ha a vezető Else 0.|Csomópont, csatorna|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Darabszám|Átlag|A folyamat kezdete óta megjelenő vezető változások száma.|Csomópont, csatorna|
|ConsensusEtcdraftNormalProposalsReceived|Yes|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Darabszám|Átlag|A normál típusú tranzakciókhoz fogadott javaslatok teljes száma.|Csomópont, csatorna|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Darabszám|Átlag|A javaslati hibák száma.|Csomópont, csatorna|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Darabszám|Átlag|A legújabb pillanatkép letiltási száma.|Csomópont, csatorna|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Darabszám|Átlag|Az átlagos batch-méret a témakörökbe eljuttatott bájtokban.|Csomópont, témakör|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Darabszám|Átlag|A témakörök átlagos tömörítési aránya (százalék).|Csomópont, témakör|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Darabszám|Átlag|Bájt/másodperc elolvasása brókerek.|Csomópont, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Darabszám|Átlag|A legutóbb véglegesített blokk blokk metaadataiban megadott eltolás.|Csomópont, csatorna|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Darabszám|Átlag|A brókereknek írt bájtok másodpercenkénti száma.|Csomópont, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Darabszám|Átlag|A témakörök számára másodpercenként elküldett rekordok száma.|Csomópont, témakör|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Darabszám|Átlag|A témakörökre vonatkozó kérelmek által elküldett rekordok átlagos száma.|Csomópont, témakör|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Darabszám|Átlag|Az átlagos kérelmek késése az MS-ban a brókerek számára.|Csomópont, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Darabszám|Átlag|A brókereknek küldött kérelmek/másodpercek.|Csomópont, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Darabszám|Átlag|Az átlagos kérelem mérete bájtban a brókerek számára.|Csomópont, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Darabszám|Átlag|A brókereknek küldött kérelmek/másodpercek.|Csomópont, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Darabszám|Átlag|Az átlagos válasz mérete bájtban a brókerek között.|Csomópont, broker_id|
|CpuUsagePercentageInDouble|Yes|CPU-használat százaléka|Százalék|Maximum|CPU-használat százaléka|Csomópont|
|DeliverBlocksSent|Yes|DeliverBlocksSentDisplayName|Darabszám|Átlag|A kézbesítési szolgáltatás által eljuttatott blokkok száma.|Csomópont, csatorna, szűrt, data_type|
|DeliverRequestsCompleted|Yes|DeliverRequestsCompletedDisplayName|Darabszám|Átlag|A befejezett kézbesítési kérelmek száma.|Csomópont, csatorna, szűrt, data_type, sikeres|
|DeliverRequestsReceived|Yes|DeliverRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott kézbesítési kérelmek száma.|Csomópont, csatorna, szűrt, data_type|
|DeliverStreamsClosed|Yes|DeliverStreamsClosedDisplayName|Darabszám|Átlag|A kézbesítési szolgáltatás számára lezárt GRPC-adatfolyamok száma.|Csomópont|
|DeliverStreamsOpened|Yes|DeliverStreamsOpenedDisplayName|Darabszám|Átlag|A kézbesítési szolgáltatás számára megnyitott GRPC-adatfolyamok száma.|Csomópont|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|Darabszám|Átlag|A sikertelen chaincode-példányok vagy-verziófrissítések száma.|Csomópont, csatorna, chaincode|
|EndorserDuplicateTransactionFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|Darabszám|Átlag|A sikertelen javaslatok száma ismétlődő tranzakciós azonosító miatt.|Csomópont, csatorna, chaincode|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|Darabszám|Átlag|A sikertelen jóváhagyások száma.|Csomópont, csatorna, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|Darabszám|Átlag|Azon javaslatok száma, amelyek nem felelnek meg az ACL-ellenőrzéseknek.|Csomópont, csatorna, chaincode|
|EndorserProposalSimulationFailures|Yes|EndorserProposalSimulationFailuresDisplayName|Darabszám|Átlag|A sikertelen javaslat-szimulációk száma.|Csomópont, csatorna, chaincode|
|EndorserProposalsReceived|Yes|EndorserProposalsReceivedDisplayName|Darabszám|Átlag|A fogadott javaslatok száma.|Csomópont|
|EndorserProposalValidationFailures|Yes|EndorserProposalValidationFailuresDisplayName|Darabszám|Átlag|Azon javaslatok száma, amelyeken sikertelen volt a kezdeti ellenőrzés.|Csomópont|
|EndorserSuccessfulProposals|Yes|EndorserSuccessfulProposalsDisplayName|Darabszám|Átlag|A sikeres javaslatok száma.|Csomópont|
|FabricVersion|Yes|FabricVersionDisplayName|Darabszám|Átlag|A háló aktív verziója.|Csomópont, verzió|
|GossipCommMessagesReceived|Yes|GossipCommMessagesReceivedDisplayName|Darabszám|Átlag|Fogadott üzenetek száma.|Csomópont|
|GossipCommMessagesSent|Yes|GossipCommMessagesSentDisplayName|Darabszám|Átlag|Az elküldött üzenetek száma.|Csomópont|
|GossipCommOverflowCount|Yes|GossipCommOverflowCountDisplayName|Darabszám|Átlag|A kimenő várólista pufferének túlcsordult száma.|Csomópont|
|GossipLeaderElectionLeader|Yes|GossipLeaderElectionLeaderDisplayName|Darabszám|Átlag|A társ a vezető (1) vagy követő (0).|Csomópont, csatorna|
|GossipMembershipTotalPeersKnown|Yes|GossipMembershipTotalPeersKnownDisplayName|Darabszám|Átlag|Összes ismert társ.|Csomópont, csatorna|
|GossipPayloadBufferSize|Yes|GossipPayloadBufferSizeDisplayName|Darabszám|Átlag|A hasznos adatok pufferének mérete.|Csomópont, csatorna|
|GossipStateHeight|Yes|GossipStateHeightDisplayName|Darabszám|Átlag|Aktuális Főkönyv magassága|Csomópont, csatorna|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Darabszám|Átlag|gRPC-kapcsolatok lezárva. A nyitott mínusz lezárt érték a kapcsolatok aktív száma.|Csomópont|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Darabszám|Átlag|a gRPC-kapcsolatok megnyitva. A nyitott mínusz lezárt érték a kapcsolatok aktív száma.|Csomópont|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|Darabszám|Átlag|A fogadott adatfolyam-üzenetek száma.|Csomópont, szolgáltatás, metódus|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|Darabszám|Átlag|Az elküldött adatfolyam-üzenetek száma.|Csomópont, szolgáltatás, metódus|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Darabszám|Átlag|Az adatfolyam-kérelmek száma befejeződött.|Csomópont, szolgáltatás, metódus, kód|
|GrpcServerUnaryRequestsReceived|Yes|GrpcServerUnaryRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott egyoperandusú kérelmek száma.|Csomópont, szolgáltatás, metódus|
|IOReadBytes|Yes|IO olvasási bájtok|Bájt|Összesen|IO olvasási bájtok|Csomópont|
|IOWriteBytes|Yes|IO írási bájtjai|Bájt|Összesen|IO írási bájtjai|Csomópont|
|LedgerBlockchainHeight|Yes|LedgerBlockchainHeightDisplayName|Darabszám|Átlag|A lánc magassága blokkokban.|Csomópont, csatorna|
|LedgerTransactionCount|Yes|LedgerTransactionCountDisplayName|Darabszám|Átlag|A feldolgozott tranzakciók száma.|Csomópont, csatorna, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Yes|LoggingEntriesCheckedDisplayName|Darabszám|Átlag|Az aktív naplózási szinttel ellenőrzött naplóbejegyzések száma.|Csomópont, szint|
|LoggingEntriesWritten|Yes|LoggingEntriesWrittenDisplayName|Darabszám|Átlag|Az írt naplóbejegyzések száma.|Csomópont, szint|
|MemoryLimit|Yes|Memória korlátja|Bájt|Átlag|Memória korlátja|Csomópont|
|MemoryUsage|Yes|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryUsagePercentageInDouble|Yes|Memóriahasználat százaléka|Százalék|Átlag|Memóriahasználat százaléka|Csomópont|
|PendingTransactions|Yes|Függőben lévő tranzakciók|Darabszám|Átlag|Függőben lévő tranzakciók|Csomópont|
|ProcessedBlocks|Yes|Feldolgozott blokkok|Darabszám|Összesen|Feldolgozott blokkok|Csomópont|
|ProcessedTransactions|Yes|Feldolgozott tranzakciók|Darabszám|Összesen|Feldolgozott tranzakciók|Csomópont|
|QueuedTransactions|Yes|Várólistán lévő tranzakciók|Darabszám|Átlag|Várólistán lévő tranzakciók|Csomópont|
|RequestHandled|Yes|Kezelt kérelmek|Darabszám|Összesen|Kezelt kérelmek|Csomópont|
|StorageUsage|Yes|Tárterület-használat|Bájt|Átlag|Tárterület-használat|Csomópont|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|RequestLatency|Yes|Kérelem késése|Ezredmásodpercben|Összesen|A kiszolgáló által a kérelem feldolgozásához szükséges idő|Művelet, hitelesítés, protokoll|
|RequestsTraffic|Yes|Forgalom kérése|Százalék|Darabszám|Benyújtott kérelmek száma|Művelet, hitelesítés, protokoll, StatusCode, StatusCodeClass|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Gyorsítótár-találatok (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allcachemisses|Yes|Gyorsítótár-lemaradás (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allcacheRead|Yes|Gyorsítótár olvasása (példány-alapú)|BytesPerSecond|Maximum||ShardId, port, elsődleges|
|allcacheWrite|Yes|Gyorsítótár-írás (példány-alapú)|BytesPerSecond|Maximum||ShardId, port, elsődleges|
|allconnectedclients|Yes|Csatlakoztatott ügyfelek (példány-alapú)|Darabszám|Maximum||ShardId, port, elsődleges|
|allevictedkeys|Yes|Kizárt kulcsok (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allexpiredkeys|Yes|Lejárt kulcsok (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allgetcommands|Yes|Lekérdezi (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|alloperationsPerSecond|Yes|Másodpercenkénti műveletek (példány-alapú)|Darabszám|Maximum||ShardId, port, elsődleges|
|allserverLoad|Yes|Kiszolgáló terhelése (példány-alapú)|Százalék|Maximum||ShardId, port, elsődleges|
|allsetcommands|Yes|Készletek (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|alltotalcommandsprocessed|Yes|Összes művelet (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|alltotalkeys|Yes|Összes kulcs (példány-alapú)|Darabszám|Maximum||ShardId, port, elsődleges|
|allusedmemory|Yes|Felhasznált memória (példány-alapú)|Bájt|Maximum||ShardId, port, elsődleges|
|allusedmemorypercentage|Yes|Felhasznált memória százalékos aránya (példány-alapú)|Százalék|Maximum||ShardId, port, elsődleges|
|allusedmemoryRss|Yes|Használt memória RSS-je (példány-alapú)|Bájt|Maximum||ShardId, port, elsődleges|
|cachehits|Yes|Gyorsítótár-találatok|Darabszám|Összesen||ShardId|
|cachehits0|Yes|Gyorsítótárbeli találatok (szegmens 0)|Darabszám|Összesen||Nincsenek méretek|
|cachehits1|Yes|Gyorsítótárbeli találatok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits2|Yes|Gyorsítótárbeli találatok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachehits3|Yes|Gyorsítótárbeli találatok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits4|Yes|Gyorsítótár-találatok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits5|Yes|Gyorsítótárbeli találatok (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|cachehits6|Yes|Gyorsítótárbeli találatok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|cachehits7|Yes|Gyorsítótárbeli találatok (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|cachehits8|Yes|Gyorsítótárbeli találatok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|cachehits9|Yes|Gyorsítótárbeli találatok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|cacheLatency|Yes|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||ShardId|
|cachemisses|Yes|Gyorsítótár-tévesztések|Darabszám|Összesen||ShardId|
|cachemisses0|Yes|Gyorsítótár-kihagyás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses1|Yes|Gyorsítótár-lemaradás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses2|Yes|Gyorsítótár-lemaradás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses3|Yes|Gyorsítótár-lemaradás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses4|Yes|Gyorsítótár-lemaradás (4. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses5|Yes|Gyorsítótár-lemaradás (5. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses6|Yes|Gyorsítótár-lemaradás (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses7|Yes|Gyorsítótár-lemaradás (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses8|Yes|Gyorsítótár-lemaradás (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses9|Yes|Gyorsítótár-lemaradás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|cachemissrate|Yes|Gyorsítótár-kihagyás aránya|Százalék|cachemissrate||ShardId|
|cacheRead|Yes|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|cacheRead0|Yes|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead1|Yes|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead2|Yes|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead3|Yes|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead4|Yes|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead5|Yes|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead6|Yes|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead7|Yes|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead8|Yes|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead9|Yes|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite|Yes|Gyorsítótár-írás|BytesPerSecond|Maximum||ShardId|
|cacheWrite0|Yes|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite1|Yes|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite2|Yes|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite3|Yes|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite4|Yes|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite5|Yes|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite6|Yes|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite7|Yes|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite8|Yes|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite9|Yes|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|connectedclients|Yes|Csatlakoztatott ügyfelek|Darabszám|Maximum||ShardId|
|connectedclients0|Yes|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients1|Yes|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients2|Yes|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients3|Yes|Csatlakoztatott ügyfelek (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients4|Yes|Csatlakoztatott ügyfelek (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients5|Yes|Csatlakoztatott ügyfelek (5. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients6|Yes|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients7|Yes|Csatlakoztatott ügyfelek (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients8|Yes|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients9|Yes|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|Hibák|Yes|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|evictedkeys|Yes|Kizárt kulcsok|Darabszám|Összesen||ShardId|
|evictedkeys0|Yes|Kizárt kulcsok (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys1|Yes|Kizárt kulcsok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys2|Yes|Kizárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys3|Yes|Kizárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys4|Yes|Kizárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys5|Yes|Kizárt kulcsok (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys6|Yes|Kizárt kulcsok (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys7|Yes|Kizárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys8|Yes|Kizárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys9|Yes|Kizárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys|Yes|Lejárt kulcsok|Darabszám|Összesen||ShardId|
|expiredkeys0|Yes|Lejárt kulcsok (szilánk 0)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys1|Yes|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys2|Yes|Lejárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys3|Yes|Lejárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys4|Yes|Lejárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys5|Yes|Lejárt kulcsok (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys6|Yes|Lejárt kulcsok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys7|Yes|Lejárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys8|Yes|Lejárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys9|Yes|Lejárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|getcommands|Yes|Lekérések|Darabszám|Összesen||ShardId|
|getcommands0|Yes|Beolvasás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands1|Yes|Beolvasás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands2|Yes|Beolvasás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands3|Yes|Beolvasás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands4|Yes|Beolvasás (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|getcommands5|Yes|Beolvasás (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|getcommands6|Yes|Beolvasás (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|getcommands7|Yes|Beolvasás (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|getcommands8|Yes|Beolvasás (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|getcommands9|Yes|Beolvasás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|operationsPerSecond|Yes|Műveletek száma másodpercenként|Darabszám|Maximum||ShardId|
|operationsPerSecond0|Yes|Művelet/másodperc (0. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond1|Yes|Művelet/másodperc (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond2|Yes|Művelet/másodperc (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond3|Yes|Művelet/másodperc (3. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond4|Yes|Művelet/másodperc (4. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond5|Yes|Művelet/másodperc (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond6|Yes|Művelet/másodperc (szegmens 6)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond7|Yes|Művelet/másodperc (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond8|Yes|Művelet/másodperc (szegmens 8)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond9|Yes|Művelet/másodperc (szegmens 9)|Darabszám|Maximum||Nincsenek méretek|
|percentProcessorTime|Yes|CPU|Százalék|Maximum||ShardId|
|percentProcessorTime0|Yes|CPU (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime1|Yes|CPU (1. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime2|Yes|CPU (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime3|Yes|CPU (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime4|Yes|CPU (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime5|Yes|CPU (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime6|Yes|CPU (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime7|Yes|PROCESSZOR (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime8|Yes|CPU (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime9|Yes|CPU (szilánk 9)|Százalék|Maximum||Nincsenek méretek|
|serverLoad|Yes|Kiszolgáló terhelése|Százalék|Maximum||ShardId|
|serverLoad0|Yes|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad1|Yes|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad2|Yes|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad3|Yes|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad4|Yes|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad5|Yes|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad6|Yes|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|serverLoad7|Yes|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad8|Yes|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|serverLoad9|Yes|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||Nincsenek méretek|
|setcommands|Yes|Halmazok|Darabszám|Összesen||ShardId|
|setcommands0|Yes|Készletek (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands1|Yes|Készletek (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands2|Yes|Készletek (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands3|Yes|Készletek (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands4|Yes|Készletek (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|setcommands5|Yes|Készletek (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|setcommands6|Yes|Készletek (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|setcommands7|Yes|Készletek (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|setcommands8|Yes|Készletek (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|setcommands9|Yes|Készletek (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed|Yes|Műveletek összesen|Darabszám|Összesen||ShardId|
|totalcommandsprocessed0|Yes|Összes művelet (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed1|Yes|Összes művelet (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed2|Yes|Összes művelet (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed3|Yes|Összes művelet (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed4|Yes|Összes művelet (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed5|Yes|Összes művelet (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed6|Yes|Összes művelet (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed7|Yes|Összes művelet (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed8|Yes|Összes művelet (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed9|Yes|Összes művelet (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|totalkeys|Yes|Kulcsok összesen|Darabszám|Maximum||ShardId|
|totalkeys0|Yes|Összes kulcs (szilánk 0)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys1|Yes|Összes kulcs (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys2|Yes|Összes kulcs (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys3|Yes|Összes kulcs (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys4|Yes|Összes kulcs (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys5|Yes|Összes kulcs (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys6|Yes|Összes kulcs (szilánk 6)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys7|Yes|Összes kulcs (szegmens 7)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys8|Yes|Összes kulcs (szilánk 8)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys9|Yes|Összes kulcs (szilánk 9)|Darabszám|Maximum||Nincsenek méretek|
|usedmemory|Yes|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemory0|Yes|Felhasznált memória (0. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory1|Yes|Felhasznált memória (1. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory2|Yes|Felhasznált memória (2. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory3|Yes|Felhasznált memória (3. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory4|Yes|Felhasznált memória (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory5|Yes|Felhasznált memória (5. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory6|Yes|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory7|Yes|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory8|Yes|Felhasznált memória (8. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory9|Yes|Felhasznált memória (10. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemorypercentage|Yes|Felhasznált memória százalékos aránya|Százalék|Maximum||ShardId|
|usedmemoryRss|Yes|Felhasznált memória RSS-címe|Bájt|Maximum||ShardId|
|usedmemoryRss0|Yes|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss1|Yes|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss2|Yes|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss3|Yes|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss4|Yes|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss5|Yes|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss6|Yes|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss7|Yes|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss8|Yes|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss9|Yes|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||Nincsenek méretek|


## <a name="microsoftcacheredisenterprise"></a>Microsoft. cache/redisEnterprise

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|cachehits|Yes|Gyorsítótár-találatok|Darabszám|Összesen||Nincsenek méretek|
|cacheLatency|Yes|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||InstanceId|
|cachemisses|Yes|Gyorsítótár-tévesztések|Darabszám|Összesen||InstanceId|
|cacheRead|Yes|Gyorsítótár-olvasás|BytesPerSecond|Maximum||InstanceId|
|cacheWrite|Yes|Gyorsítótár-írás|BytesPerSecond|Maximum||InstanceId|
|connectedclients|Yes|Csatlakoztatott ügyfelek|Darabszám|Maximum||InstanceId|
|Hibák|Yes|Hibák|Darabszám|Maximum||InstanceId, ErrorType|
|evictedkeys|Yes|Kizárt kulcsok|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys|Yes|Lejárt kulcsok|Darabszám|Összesen||Nincsenek méretek|
|getcommands|Yes|Lekérések|Darabszám|Összesen||Nincsenek méretek|
|operationsPerSecond|Yes|Műveletek száma másodpercenként|Darabszám|Maximum||Nincsenek méretek|
|percentProcessorTime|Yes|CPU|Százalék|Maximum||InstanceId|
|serverLoad|Yes|Kiszolgáló terhelése|Százalék|Maximum||Nincsenek méretek|
|setcommands|Yes|Halmazok|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed|Yes|Műveletek összesen|Darabszám|Összesen||Nincsenek méretek|
|totalkeys|Yes|Kulcsok összesen|Darabszám|Maximum||Nincsenek méretek|
|usedmemory|Yes|Felhasznált memória|Bájt|Maximum||Nincsenek méretek|
|usedmemorypercentage|Yes|Felhasznált memória százalékos aránya|Százalék|Maximum||InstanceId|
|usedmemoryRss|Yes|Felhasznált memória RSS-címe|Bájt|Maximum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profilok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Bájtos találatok aránya|Százalék|Átlag|A gyorsítótárból kiszolgált teljes bájtok aránya a teljes válasz bájtjaihoz képest|Végpont|
|OriginHealthPercentage|Yes|Forrás állapotának százaléka|Százalék|Átlag|A AFDX és a háttér közötti sikeres állapot-mintavételek százalékos aránya.|Forrás, OriginPool|
|OriginLatency|Yes|Forrás késése|Ezredmásodpercben|Átlag|A AFDX Edge által a háttérbe irányuló kérés elküldésekor kiszámított idő, amíg a AFDX nem kapta meg a háttér utolsó válaszának bájtját.|Forrás, végpont|
|OriginRequestCount|Yes|Származási kérelmek száma|Darabszám|Összesen|A AFDX és a forrás között küldött kérések száma.|HttpStatus, HttpStatusGroup, forrás, végpont|
|Percentage4XX|Yes|4XX százalékos aránya|Százalék|Átlag|Az összes olyan ügyfél-kérelem százaléka, amelynél a válasz állapotkód 4XX|Végpont, ClientRegion, ClientCountry|
|Percentage5XX|Yes|5XX százalékos aránya|Százalék|Átlag|Az összes olyan ügyfél-kérelem százaléka, amelynél a válasz állapotkód 5XX|Végpont, ClientRegion, ClientCountry|
|RequestCount|Yes|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|RequestSize|Yes|Kérelem mérete|Bájt|Összesen|Az ügyfelektől a AFDX küldött kérelmekként küldött bájtok száma.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|ResponseSize|Yes|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|TotalLatency|Yes|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|WebApplicationFirewallRequestCount|Yes|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|No|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|No|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|No|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|Nincsenek méretek|
|Lemez írási sebessége (bájt/s)|No|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|Nincsenek méretek|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincsenek méretek|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|Nincsenek méretek|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|No|Felhasznált kapacitás|Bájt|Átlag|Felhasznált fiókkapacitás|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|No|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|No|Blobok száma|Darabszám|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|Yes|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiók Blob service lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|No|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|Nincsenek méretek|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|No|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|No|Fájlok száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|No|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareQuota|No|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareSnapshotCount|No|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|No|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Yes|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|QueueCount|Yes|Várólista száma|Darabszám|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|Nincsenek méretek|
|QueueMessageCount|Yes|Üzenetsor-üzenetek száma|Darabszám|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|Nincsenek méretek|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Yes|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|TableCount|Yes|Táblák száma|Darabszám|Átlag|A Storage-fiók Table service található tábla száma.|Nincsenek méretek|
|TableEntityCount|Yes|Tábla entitások száma|Darabszám|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|Nincsenek méretek|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Blokkolt hívások|Darabszám|Összesen|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|CharactersTrained|Yes|Betanított karakterek|Darabszám|Összesen|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTranslated|Yes|Lefordított karakterek|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|ClientErrors|Yes|Ügyfél-hibák|Darabszám|Összesen|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Yes|Kimenő adatvesztés|Bájt|Összesen|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Yes|Késés|Ezredmásodpercben|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|LearnedEvents|Yes|Megtanult események|Darabszám|Összesen|A megtanult események száma.|IsMatchBaseline, mód, RunId|
|MatchedRewards|Yes|Egyeztetett jutalmak|Darabszám|Összesen| Egyeztetett jutalmak száma.|IsMatchBaseline, mód, RunId|
|ObservedRewards|Yes|Megfigyelt jutalmak|Darabszám|Összesen|Megfigyelt jutalmak száma.|IsMatchBaseline, mód, RunId|
|ProcessedCharacters|Yes|Feldolgozott karakterek|Darabszám|Összesen|Karakterek száma.|ApiName, FeatureName, UsageChannel, régió|
|ProcessedTextRecords|Yes|Feldolgozott szöveges rekordok|Darabszám|Összesen|Szöveges rekordok száma|ApiName, FeatureName, UsageChannel, régió|
|Kiszolgálóhibái|Yes|Kiszolgálói hibák|Darabszám|Összesen|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|SpeechSessionDuration|Yes|Beszédfelismerési munkamenet időtartama|Másodperc|Összesen|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|SuccessfulCalls|Yes|Sikeres hívások|Darabszám|Összesen|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalCalls|Yes|Hívások összesen|Darabszám|Összesen|A hívások száma összesen|ApiName, OperationName, régió|
|TotalErrors|Yes|Összes hiba|Darabszám|Összesen|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|TotalTokenCalls|Yes|Jogkivonat-hívások összesen|Darabszám|Összesen|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|
|TotalTransactions|Yes|Tranzakciók összesen|Darabszám|Összesen|A tranzakciók száma összesen|Nincsenek méretek|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|Hitelesítési API-kérelmek|Darabszám|Darabszám|A kommunikációs szolgáltatások hitelesítési végpontján megjelenő kérelmek száma.|Művelet, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Csevegési API-kérelmek|Darabszám|Darabszám|A kommunikációs szolgáltatások csevegési végpontján megjelenő kérelmek száma.|Művelet, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|SMS API-kérelmek|Darabszám|Darabszám|A kommunikációs szolgáltatások SMS-végpontján megjelenő kérelmek száma.|Művelet, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft. számítás/cloudServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|RoleInstanceId, Szerepkörazonosítónak|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|RoleInstanceId, Szerepkörazonosítónak|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft. számítási/cloudServices/szerepkörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|RoleInstanceId, Szerepkörazonosítónak|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|RoleInstanceId, Szerepkörazonosítónak|


## <a name="microsoftcomputedisks"></a>Microsoft. számítás/lemezek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Összetett lemez olvasási sebessége (bájt/s)|No|Lemez olvasási sebessége (bájt/mp) (előzetes verzió)|Bájt|Átlag|A figyelési időszak során lemezről beolvasott bájtok másodpercenkénti száma, Megjegyzés: Ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez olvasási művelete/mp|No|Lemez olvasási művelete/mp (előzetes verzió)|Bájt|Átlag|A figyelési időszak során a lemezen végrehajtott olvasási IOs-műveletek száma, vegye figyelembe, hogy ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez írási sebessége (bájt/s)|No|Lemez írási sebessége (bájt/s) (előzetes verzió)|Bájt|Átlag|A megfigyelési időszak során lemezre írt bájtok másodpercenkénti száma: Ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez írási műveletei másodpercenként|No|Lemez írási műveletei/mp (előzetes verzió)|Bájt|Átlag|A figyelési időszak során egy lemezen végrehajtott írási IOs-szám (a mérőszám előzetes verzióban érhető el), és az általánosan elérhetővé válás előtt változhat.||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez-sávszélesség felhasznált százaléka|Yes|Adatlemez-sávszélesség felhasznált százaléka|Százalék|Átlag|A percenként felhasznált adatlemez-sávszélesség százalékos aránya|LUN|
|Adatlemez IOPS felhasznált százaléka|Yes|Adatlemez IOPS felhasznált százaléka|Százalék|Átlag|Az adatlemez I/o-kihasználtságának százalékos aránya percenként|LUN|
|Adatlemez maximális burst sávszélessége|Yes|Adatlemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége adatlemez esetén|LUN|
|Adatlemez maximális burst IOPS|Yes|Adatlemez maximális burst IOPS|Darabszám|Átlag|A maximális IOPS adatlemez|LUN|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Yes|Adatlemez olvasási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez céljának sávszélessége|Yes|Adatlemez céljának sávszélessége|Darabszám|Átlag|Az alapszintű bájtok másodpercenkénti átviteli sebessége adatlemez nélkül elérhető|LUN|
|Adatlemez céljának IOPS|Yes|Adatlemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS adatlemeze feltörtség nélkül is elérhető|LUN|
|Adatlemez – a burst BPS-kreditek százalékos aránya|Yes|Adatlemez – a burst BPS-kreditek százalékos aránya|Százalék|Átlag|A felhasznált adatlemezek százalékos aránya (%)|LUN|
|Adatlemez – feltört IO-kreditek százalékos aránya|Yes|Adatlemez – feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az eddig felhasznált adatlemez burst I/O-kreditének százalékos aránya|LUN|
|Adatlemez-írási sebesség (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Yes|Adatlemez írási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Yes|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Yes|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Yes|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Yes|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Yes|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Yes|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Százalék|Átlag|Az operációsrendszer-lemez által felhasznált I/o-műveletek százalékos aránya percenként|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Yes|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége az operációsrendszer-lemezen elvégezhető|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Yes|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Darabszám|Átlag|A IOPS operációsrendszer-lemez maximális méretének elérése a burst használatával|LUN|
|OS-lemez várakozási sorának mélysége|Yes|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Yes|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Darabszám|Átlag|A másodpercenkénti átlagos átviteli sebesség (bájt/mp)|LUN|
|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Yes|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS operációsrendszer-lemeze feltörtség nélkül is elérhető|LUN|
|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Yes|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Százalék|Átlag|Eddig használt operációsrendszer-lemez burst sávszélességének aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Yes|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének eddig felhasznált I/O-kreditek százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Yes|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Yes|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Yes|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Yes|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|
|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Yes|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Yes|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS százaléka|Nincsenek méretek|
|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Yes|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Yes|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemezek százalékos aránya IOPS|Nincsenek méretek|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez-sávszélesség felhasznált százaléka|Yes|Adatlemez-sávszélesség felhasznált százaléka|Százalék|Átlag|A percenként felhasznált adatlemez-sávszélesség százalékos aránya|LUN, VMName|
|Adatlemez IOPS felhasznált százaléka|Yes|Adatlemez IOPS felhasznált százaléka|Százalék|Átlag|Az adatlemez I/o-kihasználtságának százalékos aránya percenként|LUN, VMName|
|Adatlemez maximális burst sávszélessége|Yes|Adatlemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége adatlemez esetén|LUN, VMName|
|Adatlemez maximális burst IOPS|Yes|Adatlemez maximális burst IOPS|Darabszám|Átlag|A maximális IOPS adatlemez|LUN, VMName|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|Adatlemez-olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Yes|Adatlemez olvasási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez céljának sávszélessége|Yes|Adatlemez céljának sávszélessége|Darabszám|Átlag|Az alapszintű bájtok másodpercenkénti átviteli sebessége adatlemez nélkül elérhető|LUN, VMName|
|Adatlemez céljának IOPS|Yes|Adatlemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS adatlemeze feltörtség nélkül is elérhető|LUN, VMName|
|Adatlemez – a burst BPS-kreditek százalékos aránya|Yes|Adatlemez – a burst BPS-kreditek százalékos aránya|Százalék|Átlag|A felhasznált adatlemezek százalékos aránya (%)|LUN, VMName|
|Adatlemez – feltört IO-kreditek százalékos aránya|Yes|Adatlemez – feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az eddig felhasznált adatlemez burst I/O-kreditének százalékos aránya|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Yes|Adatlemez írási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Yes|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő hálózat|Yes|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|VMName|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat|Yes|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|VMName|
|Kimenő hálózat összesen|Yes|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|
|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Yes|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Yes|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Százalék|Átlag|Az operációsrendszer-lemez által felhasznált I/o-műveletek százalékos aránya percenként|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Yes|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége az operációsrendszer-lemezen elvégezhető|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Yes|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Darabszám|Átlag|A IOPS operációsrendszer-lemez maximális méretének elérése a burst használatával|LUN, VMName|
|OS-lemez várakozási sorának mélysége|Yes|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Yes|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Darabszám|Átlag|A másodpercenkénti átlagos átviteli sebesség (bájt/mp)|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Yes|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS operációsrendszer-lemeze feltörtség nélkül is elérhető|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Yes|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Százalék|Átlag|Eddig használt operációsrendszer-lemez burst sávszélességének aránya|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Yes|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének eddig felhasznált I/O-kreditek százalékos aránya|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Yes|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Yes|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Yes|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Yes|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|
|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Yes|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez sávszélességének százalékos aránya|VMName|
|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Yes|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS százaléka|VMName|
|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Yes|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemez sávszélességének százalékos aránya|VMName|
|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Yes|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemezek százalékos aránya IOPS|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. számítás/virtualMachineScaleSets/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez-sávszélesség felhasznált százaléka|Yes|Adatlemez-sávszélesség felhasznált százaléka|Százalék|Átlag|A percenként felhasznált adatlemez-sávszélesség százalékos aránya|LUN|
|Adatlemez IOPS felhasznált százaléka|Yes|Adatlemez IOPS felhasznált százaléka|Százalék|Átlag|Az adatlemez I/o-kihasználtságának százalékos aránya percenként|LUN|
|Adatlemez maximális burst sávszélessége|Yes|Adatlemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége adatlemez esetén|LUN|
|Adatlemez maximális burst IOPS|Yes|Adatlemez maximális burst IOPS|Darabszám|Átlag|A maximális IOPS adatlemez|LUN|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Yes|Adatlemez olvasási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez céljának sávszélessége|Yes|Adatlemez céljának sávszélessége|Darabszám|Átlag|Az alapszintű bájtok másodpercenkénti átviteli sebessége adatlemez nélkül elérhető|LUN|
|Adatlemez céljának IOPS|Yes|Adatlemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS adatlemeze feltörtség nélkül is elérhető|LUN|
|Adatlemez – a burst BPS-kreditek százalékos aránya|Yes|Adatlemez – a burst BPS-kreditek százalékos aránya|Százalék|Átlag|A felhasznált adatlemezek százalékos aránya (%)|LUN|
|Adatlemez – feltört IO-kreditek százalékos aránya|Yes|Adatlemez – feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az eddig felhasznált adatlemez burst I/O-kreditének százalékos aránya|LUN|
|Adatlemez-írási sebesség (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Yes|Adatlemez írási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Yes|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Yes|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Yes|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Yes|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Yes|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Yes|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Százalék|Átlag|Az operációsrendszer-lemez által felhasznált I/o-műveletek százalékos aránya percenként|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Yes|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége az operációsrendszer-lemezen elvégezhető|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Yes|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Darabszám|Átlag|A IOPS operációsrendszer-lemez maximális méretének elérése a burst használatával|LUN|
|OS-lemez várakozási sorának mélysége|Yes|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Yes|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Darabszám|Átlag|A másodpercenkénti átlagos átviteli sebesség (bájt/mp)|LUN|
|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Yes|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS operációsrendszer-lemeze feltörtség nélkül is elérhető|LUN|
|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Yes|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Százalék|Átlag|Eddig használt operációsrendszer-lemez burst sávszélességének aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Yes|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének eddig felhasznált I/O-kreditek százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Yes|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Yes|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Yes|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Yes|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|
|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Yes|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Yes|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS százaléka|Nincsenek méretek|
|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Yes|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Yes|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemezek százalékos aránya IOPS|Nincsenek méretek|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|CPU-használat|Darabszám|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Yes|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Yes|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|Nincsenek méretek|
|NetworkBytesTransmittedPerSecond|Yes|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|Nincsenek méretek|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|AgentPool CPU-idő|Másodperc|Összesen|AgentPool CPU-idő másodpercben|Nincsenek méretek|
|RunDuration|Yes|Futtatás időtartama|Ezredmásodpercben|Összesen|Futtatás időtartama (ezredmásodpercben)|Nincsenek méretek|
|SuccessfulPullCount|Yes|Sikeres lekérések száma|Darabszám|Átlag|Sikeres Képkeresések száma|Nincsenek méretek|
|SuccessfulPushCount|Yes|Sikeres leküldések száma|Darabszám|Átlag|Sikeres leküldések száma|Nincsenek méretek|
|TotalPullCount|Yes|Lekérések száma összesen|Darabszám|Átlag|A lekérések száma összesen|Nincsenek méretek|
|TotalPushCount|Yes|Leküldések száma összesen|Darabszám|Átlag|Leküldések száma összesen|Nincsenek méretek|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Fedélzeti kérelmek|Darabszám|Átlag|A jelenleg felhasznált fedélzeti kérelmek maximális száma a apiserver az elmúlt másodpercben|requestKind|
|kube_node_status_allocatable_cpu_cores|No|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Darabszám|Átlag|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma||
|kube_node_status_allocatable_memory_bytes|No|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége||
|kube_node_status_condition|No|Különböző csomóponti feltételek állapota|Darabszám|Átlag|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|No|Hüvelyek száma fázis szerint|Darabszám|Átlag|Hüvelyek száma fázis szerint|fázis, névtér, Pod|
|kube_pod_status_ready|No|A hüvelyek száma üzemkész állapotban|Darabszám|Átlag|A hüvelyek száma üzemkész állapotban|névtér, Pod, Condition|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Sikertelen kérelmek|Darabszám|Összesen|A rendelkezésre álló naplók beolvasása egyéni erőforrás-szolgáltatók számára|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Sikeres kérelmek|Darabszám|Összesen|Az egyéni szolgáltató által kezdeményezett sikeres kérések|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Yes|Rendelkezésre álló kapacitás|Bájt|Átlag|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|Nincsenek méretek|
|BytesUploadedToCloud|Yes|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|Nincsenek méretek|
|BytesUploadedToCloudPerShare|Yes|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Átlag|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|CloudReadThroughput|Yes|Felhőbeli letöltési sebesség|BytesPerSecond|Átlag|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudReadThroughputPerShare|Yes|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughput|Yes|Felhőbeli feltöltési sebesség|BytesPerSecond|Átlag|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudUploadThroughputPerShare|Yes|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|HyperVMemoryUtilization|Yes|Edge-számítás – memóriahasználat|Százalék|Átlag|Használatban lévő RAM mennyisége|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Edge-számítás – százalékos CPU|Százalék|Átlag|CPU-használat százalékos aránya|InstanceName|
|NICReadThroughput|Yes|Olvasási sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Yes|Írási átviteli sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|TotalCapacity|Yes|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|Nincsenek méretek|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. DataCollaboration/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Adategységek létrehozva|Darabszám|Maximum|Létrehozott adategységek száma|DataAssetName|
|PipelineCount|Yes|Létrehozott folyamatok|Darabszám|Maximum|Létrehozott folyamatok száma|PipelineName|
|ProposalCount|Yes|Létrehozott javaslatok|Darabszám|Maximum|Létrehozott javaslatok száma|ProposalName|
|ScriptCount|Yes|Létrehozott parancsfájlok|Darabszám|Maximum|Létrehozott parancsfájlok száma|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Sikertelen futtatások|Darabszám|Összesen||pipelineName, activityName|
|SuccessfulRuns|Yes|Sikeres futtatások|Darabszám|Összesen||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|A megszakított tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivityFailedRuns|Yes|Sikertelen tevékenység-futtatási metrikák|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|Yes|A sikeres tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|FactorySizeInGbUnits|Yes|Gyári méret összesen (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|IntegrationRuntimeAvailableMemory|Yes|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableNodeNumber|Yes|Integrációs modul elérhető csomópontok száma|Darabszám|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Integration Runtime-várólista időtartama|Másodperc|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeQueueLength|Yes|Integration Runtime-várólista hossza|Darabszám|Átlag||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Maximálisan engedélyezett gyári méret (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|MaxAllowedResourceCount|Yes|Engedélyezett entitások maximális száma|Darabszám|Maximum||Nincsenek méretek|
|PipelineCancelledRuns|Yes|Megszakított folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineElapsedTimeRuns|Yes|Az eltelt idő folyamata metrikákat futtat|Darabszám|Összesen||RunId, név|
|PipelineFailedRuns|Yes|Sikertelen folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineSucceededRuns|Yes|A folyamat sikeresen futtatja a metrikákat|Darabszám|Összesen||FailureType, név|
|ResourceCount|Yes|Entitások száma összesen|Darabszám|Maximum||Nincsenek méretek|
|SSISIntegrationRuntimeStartCancel|Yes|Megszakított SSIS Integration Runtime Start mérőszámai|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Yes|Nem sikerült SSIS az Integration Runtime indítási mérőszámait|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Yes|Sikeres SSIS Integration Runtime indítási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|Yes|Beragadt SSIS Integration Runtime leállítási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Yes|Sikeres SSIS integrációs modul leállítási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Yes|SSIS-csomag végrehajtási metrikáinak megszakítva|Darabszám|Összesen||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Yes|Sikertelen SSIS-csomag végrehajtási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Yes|Sikeres SSIS-csomag végrehajtási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|TriggerCancelledRuns|Yes|Megszakított trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerFailedRuns|Yes|Sikertelen trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerSucceededRuns|Yes|A sikeres trigger metrikákat futtat|Darabszám|Összesen||Név, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Yes|Megszakított AU-idő|Másodperc|Összesen|A megszakított feladatok teljes AU-ideje.|Nincsenek méretek|
|JobAUEndedFailure|Yes|Sikertelen AU-idő|Másodperc|Összesen|A sikertelen feladatok összes AU-ideje.|Nincsenek méretek|
|JobAUEndedSuccess|Yes|Sikeres AU-idő|Másodperc|Összesen|A sikeres feladatok teljes AU-ideje.|Nincsenek méretek|
|JobEndedCancelled|Yes|Megszakított feladatok|Darabszám|Összesen|Megszakított feladatok száma.|Nincsenek méretek|
|JobEndedFailure|Yes|Sikertelen feladatok|Darabszám|Összesen|Sikertelen feladatok száma.|Nincsenek méretek|
|JobEndedSuccess|Yes|Sikeres feladatok|Darabszám|Összesen|A sikeres feladatok száma.|Nincsenek méretek|
|JobStage|Yes|Feladatok a fázisban|Darabszám|Összesen|A feladatok száma az egyes fázisokban.|Nincsenek méretek|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DataRead|Yes|Olvasott információk|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|Nincsenek méretek|
|DataWritten|Yes|Írt adatértékek|Bájt|Összesen|A fiókba írt összes adatmennyiség.|Nincsenek méretek|
|ReadRequests|Yes|Olvasási kérelmek|Darabszám|Összesen|A fióknak küldött adatolvasási kérelmek száma.|Nincsenek méretek|
|TotalStorage|Yes|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|Nincsenek méretek|
|WriteRequests|Yes|Írási kérelmek|Darabszám|Összesen|A fiókra vonatkozó adatírási kérelmek száma.|Nincsenek méretek|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Yes|Fogadott megosztás sikertelen Pillanatképek|Darabszám|Darabszám|A fogadott megosztások száma sikertelen Pillanatképek a fiókban|Nincsenek méretek|
|FailedShareSynchronizations|Yes|Nem sikerült elküldeni a megosztást pillanatképeket|Darabszám|Darabszám|A fiókban sikertelenül továbbított megosztási Pillanatképek száma|Nincsenek méretek|
|ShareCount|Yes|Eljuttatott megosztások|Darabszám|Maximum|A fiókban eljuttatott megosztások száma|ShareName|
|ShareSubscriptionCount|Yes|Fogadott megosztások|Darabszám|Maximum|A fiókban fogadott megosztások száma|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Yes|A kapott megosztás sikeres pillanatképei|Darabszám|Darabszám|A fogadott megosztás sikeres pillanatképei a fiókban|Nincsenek méretek|
|SucceededShareSynchronizations|Yes|Az eljuttatott megosztás pillanatképei sikeresen megtörténtek|Darabszám|Darabszám|A fiókban sikeresen elvégezte a sikeres megosztási Pillanatképek számát|Nincsenek méretek|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Yes|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Yes|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Yes|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Yes|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Yes|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Megszakított kapcsolatok|Darabszám|Összesen|Megszakított kapcsolatok|Nincsenek méretek|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Maximum|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Maximum|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|cpu_percent|Yes|Gazda CPU-százaléka|Százalék|Maximum|Gazda CPU-százaléka|Nincsenek méretek|
|io_consumption_percent|Yes|IO-százalék|Százalék|Maximum|IO-százalék|Nincsenek méretek|
|memory_percent|Yes|Gazdagép memória százaléka|Százalék|Maximum|Gazdagép memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Gazda hálózat kimenő|Bájt|Összesen|A gazda hálózati kimenő forgalom bájtban|Nincsenek méretek|
|network_bytes_ingress|Yes|Gazdagép hálózata|Bájt|Összesen|Gazdagép hálózati bejövő forgalom (bájt)|Nincsenek méretek|
|Lekérdezések|Yes|Lekérdezések|Darabszám|Összesen|Lekérdezések|Nincsenek méretek|
|replication_lag|Yes|Replikálás késése másodpercben|Másodperc|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|storage_limit|Yes|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Maximum|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Maximum|Felhasznált tárterület|Nincsenek méretek|
|total_connections|Yes|Összes kapcsolat|Darabszám|Összesen|Összes kapcsolat|Nincsenek méretek|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Yes|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Yes|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Yes|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Yes|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Yes|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connections_succeeded|Yes|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_credits_consumed|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|Az adatbázis-kiszolgáló által felhasznált kreditek teljes száma|Nincsenek méretek|
|cpu_credits_remaining|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|disk_queue_depth|Yes|Lemez várólistájának mélysége|Darabszám|Átlag|Az adatlemezre vonatkozó függőben lévő I/O-műveletek száma|Nincsenek méretek|
|IOPS|Yes|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|maximum_used_transactionIDs|Yes|Maximálisan használt tranzakciós azonosítók|Darabszám|Átlag|Maximálisan használt tranzakciós azonosítók|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|read_iops|Yes|IOPS olvasása|Darabszám|Átlag|Adatlemez I/O-olvasási műveleteinek másodpercenkénti száma|Nincsenek méretek|
|read_throughput|Yes|Olvasási sebesség sebessége (bájt/s)|Darabszám|Átlag|A megfigyelési időszak során az adatlemezről másodpercenként olvasott bájtok száma|Nincsenek méretek|
|storage_free|Yes|Szabad tárterület|Bájt|Átlag|Szabad tárterület|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|
|txlogs_storage_used|Yes|A tranzakciós napló tárterülete használatban van|Bájt|Átlag|A tranzakciós napló tárterülete használatban van|Nincsenek méretek|
|write_iops|Yes|IOPS írása|Darabszám|Átlag|Adatlemez I/O-írási műveleteinek másodpercenkénti száma|Nincsenek méretek|
|write_throughput|Yes|Írási átviteli sebesség (bájt/s)|Darabszám|Átlag|A megfigyelési időszak során az adatlemezre másodpercenként írt bájtok száma|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|pg_replica_log_delay_in_bytes|Yes|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|Nincsenek méretek|
|pg_replica_log_delay_in_seconds|Yes|Replika késése|Másodperc|Maximum|Replika késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Yes|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Yes|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Yes|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Yes|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|IOPS|Yes|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdeviceselasticpools"></a>Microsoft. Devices/ElasticPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|kért használati arány|Százalék|Átlag|kért használati arány|Nincsenek méretek|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. commands. kimenő. elhagyása. sikeres|Yes|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. Complete. success|Yes|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. elutasítás. sikeres|Yes|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. Methods. failure|Yes|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincsenek méretek|
|C2D. Methods. requestSize|Yes|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. responseSize|Yes|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. success|Yes|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincsenek méretek|
|C2D. Twin. Read. failure|Yes|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. size|Yes|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Read. success|Yes|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Update. failure|Yes|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. size|Yes|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|C2D. Twin. Update. success|Yes|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2DMessagesExpired|Yes|C2D üzenetek lejártak|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|Nincsenek méretek|
|konfigurációk|Yes|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|Nincsenek méretek|
|connectedDeviceCount|Yes|Csatlakoztatott eszközök|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. endpoints. kimenő. beépített. események|Yes|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. kimenő. eventHubs|Yes|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusQueues|Yes|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusTopics|Yes|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage|Yes|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Blobok|Yes|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Bytes|Yes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincsenek méretek|
|D2C. endpoints. késleltetés. beépített. események|Yes|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. késleltetés. eventHubs|Yes|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusQueues|Yes|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusTopics|Yes|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. látencia. Storage|Yes|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincsenek méretek|
|D2C. telemetria. kimenő. eldobott|Yes|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincsenek méretek|
|D2C. telemetria. kimenő. tartalék|Yes|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincsenek méretek|
|D2C. telemetria. kimenő. érvénytelen|Yes|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincsenek méretek|
|D2C. telemetria. kimenő. árva|Yes|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincsenek méretek|
|D2C. telemetria. kimenő. sikeres|Yes|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincsenek méretek|
|D2C. telemetria. beáramló. allProtocol|Yes|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sendThrottle|Yes|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sikeres|Yes|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincsenek méretek|
|D2C. Twin. Read. failure|Yes|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. size|Yes|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|D2C. Twin. Read. success|Yes|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|D2C. Twin. Update. failure|Yes|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|D2C. Twin. Update. size|Yes|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|D2C. Twin. Update. success|Yes|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincsenek méretek|
|dailyMessageQuotaUsed|Yes|A felhasznált üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt összes üzenet száma|Nincsenek méretek|
|deviceDataUsage|Yes|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|deviceDataUsageV2|Yes|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|Devices. connectedDevices. allProtocol|Yes|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|Devices. totalDevices|Yes|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|EventGridDeliveries|Yes|Event Grid kézbesítések|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|Eredmény, EventType|
|EventGridLatency|Yes|Event Grid késés|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|feladatok. cancelJob. hiba|Yes|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. sikeres|Yes|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. kész|Yes|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. hiba|Yes|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. sikeres|Yes|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. hiba|Yes|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. sikeres|Yes|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. sikertelen|Yes|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek méretek|
|feladatok. listJobs. hiba|Yes|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincsenek méretek|
|feladatok. listJobs. sikeres|Yes|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. queryJobs. hiba|Yes|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincsenek méretek|
|feladatok. queryJobs. sikeres|Yes|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek méretek|
|RoutingDataSizeInBytesDelivered|Yes|Továbbítási üzenetek mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT hub által egy végpontra küldött üzenetek teljes mérete bájtban megadva. A Végpontneve és a EndpointType dimenzió használatával megtekintheti az üzenetek méretét a különböző végpontokra küldött bájtokban. A metrika értéke minden leküldött üzenetnél növekszik, beleértve azt is, hogy az üzenetet több végpontra is továbbítják-e, vagy ha az üzenet többször is ugyanarra a végpontra érkezik.|EndpointType, Végpontneve, RoutingSource|
|RoutingDeliveries|Yes|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Azon időpontok száma, IoT Hub az összes végpont felé irányuló üzenetek továbbítására történt kísérlet az Útválasztás használatával. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az eredmény dimenziót. Ha szeretné megtekinteni a hiba okát (például érvénytelen, eldobott vagy árva), használja az FailureReasonCategory dimenziót. A Végpontneve és a EndpointType dimenzióval is megtudhatja, hogy hány üzenet lett továbbítva a különböző végpontoknak. A metrika értéke eggyel nő az egyes kézbesítési kísérleteknél, beleértve azt is, hogy az üzenetet több végpontra kézbesítik-e, vagy ha az üzenet többször is ugyanarra a végpontra van kézbesítve.|EndpointType, Végpontneve, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Yes|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodpercben|Átlag|Az üzenet bejövő értékének átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria az üzeneteket a végpontba. A Végpontneve és a EndpointType méretek segítségével megismerheti a különböző végpontok késését.|EndpointType, Végpontneve, RoutingSource|
|tenantHub.requestedUsageRate|Yes|kért használati arány|Százalék|Átlag|kért használati arány|Nincsenek méretek|
|totalDeviceCount|Yes|Összes eszköz|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|twinQueries. hiba|Yes|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries.resultSize|Yes|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincsenek méretek|
|twinQueries. success|Yes|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincsenek méretek|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. commands. kimenő. elhagyása. sikeres|Yes|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. Complete. success|Yes|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. elutasítás. sikeres|Yes|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. Methods. failure|Yes|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincsenek méretek|
|C2D. Methods. requestSize|Yes|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. responseSize|Yes|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. success|Yes|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincsenek méretek|
|C2D. Twin. Read. failure|Yes|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. size|Yes|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Read. success|Yes|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Update. failure|Yes|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. size|Yes|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|C2D. Twin. Update. success|Yes|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2DMessagesExpired|Yes|C2D üzenetek lejártak|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|Nincsenek méretek|
|konfigurációk|Yes|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|Nincsenek méretek|
|connectedDeviceCount|No|Csatlakoztatott eszközök|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. endpoints. kimenő. beépített. események|Yes|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. kimenő. eventHubs|Yes|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusQueues|Yes|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusTopics|Yes|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage|Yes|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Blobok|Yes|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Bytes|Yes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincsenek méretek|
|D2C. endpoints. késleltetés. beépített. események|Yes|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. késleltetés. eventHubs|Yes|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusQueues|Yes|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusTopics|Yes|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. látencia. Storage|Yes|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincsenek méretek|
|D2C. telemetria. kimenő. eldobott|Yes|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincsenek méretek|
|D2C. telemetria. kimenő. tartalék|Yes|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincsenek méretek|
|D2C. telemetria. kimenő. érvénytelen|Yes|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincsenek méretek|
|D2C. telemetria. kimenő. árva|Yes|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincsenek méretek|
|D2C. telemetria. kimenő. sikeres|Yes|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincsenek méretek|
|D2C. telemetria. beáramló. allProtocol|Yes|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sendThrottle|Yes|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sikeres|Yes|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincsenek méretek|
|D2C. Twin. Read. failure|Yes|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. size|Yes|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|D2C. Twin. Read. success|Yes|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|D2C. Twin. Update. failure|Yes|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|D2C. Twin. Update. size|Yes|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|D2C. Twin. Update. success|Yes|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincsenek méretek|
|dailyMessageQuotaUsed|Yes|A felhasznált üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt összes üzenet száma|Nincsenek méretek|
|deviceDataUsage|Yes|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|deviceDataUsageV2|Yes|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|Devices. connectedDevices. allProtocol|Yes|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|Devices. totalDevices|Yes|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|EventGridDeliveries|Yes|Event Grid kézbesítések|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|Eredmény, EventType|
|EventGridLatency|Yes|Event Grid késés|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|feladatok. cancelJob. hiba|Yes|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. sikeres|Yes|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. kész|Yes|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. hiba|Yes|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. sikeres|Yes|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. hiba|Yes|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. sikeres|Yes|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. sikertelen|Yes|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek méretek|
|feladatok. listJobs. hiba|Yes|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincsenek méretek|
|feladatok. listJobs. sikeres|Yes|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. queryJobs. hiba|Yes|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincsenek méretek|
|feladatok. queryJobs. sikeres|Yes|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek méretek|
|RoutingDataSizeInBytesDelivered|Yes|Továbbítási üzenetek mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT hub által egy végpontra küldött üzenetek teljes mérete bájtban megadva. A Végpontneve és a EndpointType dimenzió használatával megtekintheti az üzenetek méretét a különböző végpontokra küldött bájtokban. A metrika értéke minden leküldött üzenetnél növekszik, beleértve azt is, hogy az üzenetet több végpontra is továbbítják-e, vagy ha az üzenet többször is ugyanarra a végpontra érkezik.|EndpointType, Végpontneve, RoutingSource|
|RoutingDeliveries|Yes|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Azon időpontok száma, IoT Hub az összes végpont felé irányuló üzenetek továbbítására történt kísérlet az Útválasztás használatával. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az eredmény dimenziót. Ha szeretné megtekinteni a hiba okát (például érvénytelen, eldobott vagy árva), használja az FailureReasonCategory dimenziót. A Végpontneve és a EndpointType dimenzióval is megtudhatja, hogy hány üzenet lett továbbítva a különböző végpontoknak. A metrika értéke eggyel nő az egyes kézbesítési kísérleteknél, beleértve azt is, hogy az üzenetet több végpontra kézbesítik-e, vagy ha az üzenet többször is ugyanarra a végpontra van kézbesítve.|EndpointType, Végpontneve, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Yes|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodpercben|Átlag|Az üzenet bejövő értékének átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria az üzeneteket a végpontba. A Végpontneve és a EndpointType méretek segítségével megismerheti a különböző végpontok késését.|EndpointType, Végpontneve, RoutingSource|
|totalDeviceCount|No|Összes eszköz|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|twinQueries. hiba|Yes|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries.resultSize|Yes|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincsenek méretek|
|twinQueries. success|Yes|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincsenek méretek|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Igazolási kísérletek|Darabszám|Összesen|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, állapot, protokoll|
|DeviceAssignments|Yes|Hozzárendelt eszközök|Darabszám|Összesen|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, állapot|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|API-kérelmek|Darabszám|Összesen|A digitális Twins olvasási, írási, törlési és lekérdezési műveleteire vonatkozó API-kérések száma.|Művelet, hitelesítés, protokoll, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|API-kérelmek meghibásodási aránya|Százalék|Átlag|A szolgáltatás által a példányhoz kapott API-kérelmek százalékos aránya, amely belső hibát (500) ad vissza a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteihez.|Művelet, hitelesítés, protokoll|
|ApiRequestsLatency|Yes|API-kérelmek késése|Ezredmásodpercben|Átlag|Az API-kérések válaszideje, ha a kérést az Azure digitális ikrek fogadják el, amíg a szolgáltatás nem küld sikeres/sikertelen eredményt a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteinek.|Művelet, hitelesítés, protokoll, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Yes|Számlázási API-műveletek|Darabszám|Összesen|Az Azure Digital Twins szolgáltatásban végrehajtott API-kérelmek számának számlázási mérőszáma.|MeterId|
|BillingMessagesProcessed|Yes|Feldolgozott számlázási üzenetek|Darabszám|Összesen|Számlázási metrika az Azure digitális Twins-ból külső végpontokra küldött üzenetek számának megadásához.|MeterId|
|BillingQueryUnits|Yes|Számlázási lekérdezési egységek|Darabszám|Összesen|A lekérdezési egységek száma, a szolgáltatások erőforrás-felhasználásának belsőleg számított mértéke, a lekérdezések végrehajtásához felhasználva.|MeterId|
|IngressEvents|Yes|Bejövő események|Darabszám|Összesen|A bejövő telemetria események száma az Azure digitális Twins-ban.|Eredmény|
|IngressEventsFailureRate|Yes|Bejövő események meghibásodási aránya|Százalék|Átlag|Azon bejövő telemetria-események százalékos aránya, amelyek esetében a szolgáltatás belső hibát (500) ad vissza.|Nincsenek méretek|
|IngressEventsLatency|Yes|Bejövő események késése|Ezredmásodpercben|Átlag|Az az idő, amikor egy esemény megérkezik, amikor készen áll az Azure digitális ikrek általi egressed, és ekkor a szolgáltatás sikeres/sikertelen eredményt küld.|Eredmény|
|ModelCount|Yes|Modellek száma|Darabszám|Összesen|Az Azure Digital Twins-példány modelljeinek száma összesen. Ezzel a metrikával meghatározhatja, hogy az adott példányon engedélyezett modellek maximális száma eléri-e a szolgáltatási korlátot.|Nincsenek méretek|
|Útválasztás|Yes|Átirányított üzenetek|Darabszám|Összesen|Az Azure-szolgáltatásokhoz (például Event hub, Service Bus vagy Event Grid) továbbított üzenetek száma.|EndpointType, eredmény|
|RoutingFailureRate|Yes|Útválasztási hibák aránya|Százalék|Átlag|Az olyan események százalékos aránya, amelyek az Azure digitális ikrekről egy végponti Azure-szolgáltatásba (például az Event hub, Service Bus vagy Event Grid) való továbbítás során hibát eredményeznek.|EndpointType|
|RoutingLatency|Yes|Útválasztási késés|Ezredmásodpercben|Átlag|Az Azure Digital Twins-ból átirányított esemény között eltelt idő a végponti Azure-szolgáltatásba (például Event hub, Service Bus vagy Event Grid) való közzétételkor.|EndpointType, eredmény|
|TwinCount|Yes|Dupla darabszám|Darabszám|Összesen|Az ikrek teljes száma az Azure Digital Twins-példányban. Ezzel a metrikával meghatározhatja, hogy a szolgáltatás legfeljebb hány ikrek számára engedélyezett.|Nincsenek méretek|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Régió hozzáadva|Darabszám|Darabszám|Régió hozzáadva|Region|
|AutoscaleMaxThroughput|No|Maximális átviteli sebesség|Darabszám|Maximum|Maximális átviteli sebesség|DatabaseName, CollectionName|
|AvailableStorage|No|elavult Rendelkezésre álló tár|Bájt|Összesen|A "rendelkezésre álló tár" el lesz távolítva a Azure Monitorról a 2023 szeptember végén. Cosmos DB a gyűjtemény tárterületének mérete mostantól korlátlan. Az egyetlen korlátozás az, hogy az egyes logikai partíciós kulcsok tárolási mérete 20 GB. A PartitionKeyStatistics a diagnosztikai naplóban engedélyezheti a felső partíciós kulcsok tárolási felhasználásának megismeréséhez. A Cosmos DB tárolási kvótával kapcsolatos további információkért tekintse meg ezt a dokumentációt https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Az elavulás után a fennmaradó riasztási szabályok az elavult metrika esetében még mindig le lesznek tiltva az elavult dátum után.|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|No|Cassandra-kapcsolatok bezárása|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|Régió, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|Cassandra-összekötő átlagos ReplicationLatency|Ezredmásodpercben|Átlag|Cassandra-összekötő átlagos ReplicationLatency|Nincsenek méretek|
|CassandraConnectorReplicationHealthStatus|No|Cassandra Connector replikációs állapotának állapota|Darabszám|Darabszám|Cassandra Connector replikációs állapotának állapota|NotStarted, ReplicationInProgress, hiba|
|CassandraKeyspaceCreate|No|Cassandra-terület létrehozva|Darabszám|Darabszám|Cassandra-terület létrehozva|ResourceName |
|CassandraKeyspaceDelete|No|Cassandra szóköz törölve|Darabszám|Darabszám|Cassandra szóköz törölve|ResourceName |
|CassandraKeyspaceThroughputUpdate|No|Cassandra Space-átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Space-átviteli sebesség frissítve|ResourceName |
|CassandraKeyspaceUpdate|No|Cassandra Space frissítve|Darabszám|Darabszám|Cassandra Space frissítve|ResourceName |
|CassandraRequestCharges|No|Cassandra-kérelmek díjai|Darabszám|Összesen|A Cassandra-kérelmekhez felhasznált RUs|DatabaseName, CollectionName, régió, OperationType, ResourceType|
|CassandraRequests|No|Cassandra-kérelmek|Darabszám|Darabszám|A végrehajtott Cassandra-kérelmek száma|DatabaseName, CollectionName, régió, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Cassandra-tábla létrehozva|Darabszám|Darabszám|Cassandra-tábla létrehozva|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra-tábla törölve|Darabszám|Darabszám|Cassandra-tábla törölve|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Cassandra Table átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Table átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra-tábla frissítve|Darabszám|Darabszám|Cassandra-tábla frissítve|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Fiók létrehozva|Darabszám|Darabszám|Fiók létrehozva|Nincsenek méretek|
|DataUsage|No|Adathasználat|Bájt|Összesen|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DeleteAccount|Yes|Fiók törölve|Darabszám|Darabszám|Fiók törölve|Nincsenek méretek|
|DocumentCount|No|Dokumentumok száma|Darabszám|Összesen|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|No|Dokumentum kvótája|Bájt|Összesen|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|GremlinDatabaseCreate|No|Gremlin-adatbázis létrehozva|Darabszám|Darabszám|Gremlin-adatbázis létrehozva|ResourceName |
|GremlinDatabaseDelete|No|Gremlin-adatbázis törölve|Darabszám|Darabszám|Gremlin-adatbázis törölve|ResourceName |
|GremlinDatabaseThroughputUpdate|No|Gremlin adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin adatbázis átviteli sebessége frissítve|ResourceName |
|GremlinDatabaseUpdate|No|Gremlin-adatbázis frissítve|Darabszám|Darabszám|Gremlin-adatbázis frissítve|ResourceName |
|GremlinGraphCreate|No|Gremlin gráf létrehozva|Darabszám|Darabszám|Gremlin gráf létrehozva|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin gráf törölve|Darabszám|Darabszám|Gremlin gráf törölve|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Gremlin gráf átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin gráf átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin gráf frissítve|Darabszám|Darabszám|Gremlin gráf frissítve|ResourceName, ChildResourceName, |
|IndexUsage|No|Indexhasználat|Bájt|Összesen|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|IntegratedCacheEvictedEntriesSize|No|IntegratedCacheEvictedEntriesSize|Bájt|Átlag|Az integrált gyorsítótárból kizárt bejegyzések mérete|CacheType, régió|
|IntegratedCacheHitRate|No|IntegratedCacheHitRate|Százalék|Átlag|Gyorsítótár-találatok aránya az integrált gyorsítótárak esetében|CacheType, régió|
|IntegratedCacheSize|No|IntegratedCacheSize|Bájt|Átlag|A dedikált átjáró-kérelmekhez tartozó integrált gyorsítótárak mérete|CacheType, régió|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Darabszám|Átlag|Az integrált gyorsítótárból a TTL lejárta miatt eltávolított bejegyzések száma|CacheType, régió|
|MetadataRequests|No|Metaadat-kérelmek|Darabszám|Darabszám|A metaadat-kérelmek száma. Cosmos DB az egyes fiókok rendszermetaadat-gyűjteményét kezeli, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, |
|MongoCollectionCreate|No|Mongo-gyűjtemény létrehozva|Darabszám|Darabszám|Mongo-gyűjtemény létrehozva|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo-gyűjtemény törölve|Darabszám|Darabszám|Mongo-gyűjtemény törölve|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|A Mongo-gyűjtési átviteli sebesség frissítve|Darabszám|Darabszám|A Mongo-gyűjtési átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo-gyűjtemény frissítve|Darabszám|Darabszám|Mongo-gyűjtemény frissítve|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo-adatbázis törölve|Darabszám|Darabszám|Mongo-adatbázis törölve|ResourceName |
|MongoDatabaseThroughputUpdate|No|Mongo adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Mongo adatbázis átviteli sebessége frissítve|ResourceName |
|MongoDBDatabaseCreate|No|Mongo-adatbázis létrehozva|Darabszám|Darabszám|Mongo-adatbázis létrehozva|ResourceName |
|MongoDBDatabaseUpdate|No|Mongo-adatbázis frissítve|Darabszám|Darabszám|Mongo-adatbázis frissítve|ResourceName |
|MongoRequestCharge|Yes|Mongo-kérelem díja|Darabszám|Összesen|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequests|Yes|Mongo kérelmek|Darabszám|Darabszám|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequestsCount|No|elavult Mongo kérelmek gyakorisága|CountPerSecond|Átlag|Mongo kérelmek száma másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsDelete|No|elavult Mongo-törlési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-törlési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsInsert|No|elavult Mongo-beszúrási kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-beszúrási szám másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsQuery|No|elavult Mongo-lekérdezési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-lekérdezési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsUpdate|No|elavult Mongo-frissítési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo frissítési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|NormalizedRUConsumption|No|Normalizált RU-felhasználás|Százalék|Maximum|Maximális RU-százalék/perc|CollectionName, DatabaseName, régió, PartitionKeyRangeId|
|ProvisionedThroughput|No|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|DatabaseName, CollectionName|
|RegionFailover|Yes|A régió feladatátvétele megtörtént|Darabszám|Darabszám|A régió feladatátvétele megtörtént|Nincsenek méretek|
|RemoveRegion|Yes|Régió eltávolítva|Darabszám|Darabszám|Régió eltávolítva|Region|
|ReplicationLatency|Yes|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion, TargetRegion|
|ServerSideLatency|No|Kiszolgálóoldali késés|Ezredmásodpercben|Átlag|Kiszolgálóoldali késés|DatabaseName, CollectionName, régió, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|Nincsenek méretek|
|SqlContainerCreate|No|SQL-tároló létrehozva|Darabszám|Darabszám|SQL-tároló létrehozva|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|SQL-tároló törölve|Darabszám|Darabszám|SQL-tároló törölve|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|SQL-tároló átviteli sebessége frissítve|Darabszám|Darabszám|SQL-tároló átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|SQL-tároló frissítve|Darabszám|Darabszám|SQL-tároló frissítve|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|SQL-adatbázis létrehozva|Darabszám|Darabszám|SQL-adatbázis létrehozva|ResourceName |
|SqlDatabaseDelete|No|SQL-adatbázis törölve|Darabszám|Darabszám|SQL-adatbázis törölve|ResourceName |
|SqlDatabaseThroughputUpdate|No|Az SQL Database átviteli sebessége frissítve|Darabszám|Darabszám|Az SQL Database átviteli sebessége frissítve|ResourceName |
|SqlDatabaseUpdate|No|SQL-adatbázis frissítve|Darabszám|Darabszám|SQL-adatbázis frissítve|ResourceName |
|TableTableCreate|No|AzureTable tábla létrehozva|Darabszám|Darabszám|AzureTable tábla létrehozva|ResourceName |
|TableTableDelete|No|AzureTable-tábla törölve|Darabszám|Darabszám|AzureTable-tábla törölve|ResourceName |
|TableTableThroughputUpdate|No|AzureTable-tábla átviteli sebessége frissítve|Darabszám|Darabszám|AzureTable-tábla átviteli sebessége frissítve|ResourceName |
|TableTableUpdate|No|AzureTable tábla frissítve|Darabszám|Darabszám|AzureTable tábla frissítve|ResourceName |
|TotalRequests|Yes|Összes kérelem|Darabszám|Darabszám|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|TotalRequestUnits|Yes|Kérelmek összes egysége|Darabszám|Összesen|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|UpdateAccountKeys|Yes|A fiók kulcsai frissítve|Darabszám|Darabszám|A fiók kulcsai frissítve|KeyType|
|UpdateAccountNetworkSettings|Yes|A fiók hálózati beállításai frissítve|Darabszám|Darabszám|A fiók hálózati beállításai frissítve|Nincsenek méretek|
|UpdateAccountReplicationSettings|Yes|A fiók replikációs beállításainak frissítése megtörtént|Darabszám|Darabszám|A fiók replikációs beállításainak frissítése megtörtént|Nincsenek méretek|
|UpdateDiagnosticsSettings|No|Fiók diagnosztikai beállításainak frissítése|Darabszám|Darabszám|Fiók diagnosztikai beállításainak frissítése|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincsenek méretek|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincsenek méretek|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek méretek|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/fürtök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.||
|AvailableMemory|No|Szabad memória|Százalék|Maximum|Az Event hub-fürt rendelkezésre álló memóriája a teljes memória százalékában.|Szerepkör|
|CaptureBacklog|No|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.||
|CapturedBytes|No|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.||
|CapturedMessages|No|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.||
|ConnectionsClosed|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.||
|ConnectionsOpened|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.||
|CPU|No|CPU|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|IncomingBytes|Yes|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.||
|IncomingMessages|Yes|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.||
|IncomingRequests|Yes|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.||
|OutgoingBytes|Yes|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.||
|OutgoingMessages|Yes|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.||
|QuotaExceededErrors|No|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|Operationresult tevékenységen|
|Kiszolgálóhibái|No|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|Operationresult tevékenységen|
|Méret|No|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|Szerepkör|
|SuccessfulRequests|No|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|Operationresult tevékenységen|
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|Operationresult tevékenységen|
|UserErrors|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|Operationresult tevékenységen|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.||
|CaptureBacklog|No|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedBytes|No|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|CapturedMessages|No|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|ConnectionsClosed|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|EHABL|Yes|Archivált várakozó üzenetek (elavult)|Darabszám|Összesen|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)||
|EHAMBS|Yes|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összesen|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)||
|EHAMSGS|Yes|Archivált üzenetek (elavult)|Darabszám|Összesen|Event hub archivált üzenetek egy névtérben (elavult)||
|EHINBYTES|Yes|Bejövő bájtok (elavult)|Bájt|Összesen|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)||
|EHINMBS|Yes|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)||
|EHINMSGS|Yes|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtér összes bejövő üzenete (elavult)||
|EHOUTBYTES|Yes|Kimenő bájtok (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)||
|EHOUTMBS|Yes|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)||
|EHOUTMSGS|Yes|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér összes kimenő üzenete (elavult)||
|FAILREQ|Yes|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)||
|IncomingBytes|Yes|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|IncomingMessages|Yes|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|IncomingRequests|Yes|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|EntityName|
|INMSGS|Yes|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)||
|INREQS|Yes|Bejövő kérelmek (elavult)|Darabszám|Összesen|Egy névtér összes bejövő küldési kérelme (elavult)||
|INTERer|Yes|Belső kiszolgálói hibák (elavult)|Darabszám|Összesen|Névtér összes belső kiszolgálóhiba (elavult)||
|MISCERR|Yes|Egyéb hibák (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)||
|OutgoingBytes|Yes|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|OutgoingMessages|Yes|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|OUTMSGS|Yes|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)||
|QuotaExceededErrors|No|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName, Operationresult tevékenységen|
|Kiszolgálóhibái|No|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName, Operationresult tevékenységen|
|Méret|No|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|EntityName|
|SuccessfulRequests|No|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName, Operationresult tevékenységen|
|SUCCREQ|Yes|Sikeres kérések (elavult)|Darabszám|Összesen|A névtér összes sikeres kérelme (elavult)||
|SVRBSY|Yes|Kiszolgáló által foglalt hibák (elavult)|Darabszám|Összesen|Egy névtér összes kiszolgáló által foglalt hibája (elavult)||
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|UserErrors|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|EntityName, Operationresult tevékenységen|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/fürtök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Kategorizált átjáró-kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Átjáróra vonatkozó kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|REST proxy fogyasztói RequestThroughput|CountPerSecond|Összesen|A Kafka REST proxy iránti fogyasztói kérelmek száma|Gép, témakör|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|REST proxy fogyasztói RequestLatency|Ezredmásodpercben|Átlag|Üzenet késése egy fogyasztói kérelemben a Kafka REST proxyn keresztül|Gép, témakör|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|REST proxy gyártó MessageThroughput|CountPerSecond|Összesen|Termelői üzenetek száma a Kafka REST proxyn keresztül|Gép, témakör|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|REST proxy fogyasztói MessageThroughput|CountPerSecond|Összesen|Fogyasztói üzenetek száma a Kafka REST proxyn keresztül|Gép, témakör|
|KafkaRestProxy.OpenConnections|Yes|REST-proxy ConcurrentConnections|Darabszám|Összesen|A Kafka REST proxyn keresztüli egyidejű kapcsolatok száma|Gép, témakör|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|REST proxy gyártó RequestThroughput|CountPerSecond|Összesen|A Kafka REST proxyhoz tartozó termelői kérelmek száma|Gép, témakör|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|REST proxy gyártó RequestLatency|Ezredmásodpercben|Átlag|Üzenet késése egy gyártói kérelemben a Kafka REST proxyn keresztül|Gép, témakör|
|NumActiveWorkers|Yes|Aktív feldolgozók száma|Darabszám|Maximum|Aktív feldolgozók száma|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/szolgáltatások

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A szolgáltatás rendelkezésre állási sebessége.|Nincsenek méretek|
|CosmosDbCollectionSize|Yes|Cosmos DB gyűjtemény mérete|Bájt|Összesen|A háttérbeli Cosmos DB gyűjtemény mérete bájtban megadva.|Nincsenek méretek|
|CosmosDbIndexSize|Yes|Cosmos DB index mérete|Bájt|Összesen|A Cosmos DB gyűjtemény indexének mérete bájtban kifejezve.|Nincsenek méretek|
|CosmosDbRequestCharge|Yes|RU-használat Cosmos DB|Darabszám|Összesen|A szolgáltatásra irányuló kérések RU-használata Cosmos DB.|Művelet, ResourceType|
|CosmosDbRequests|Yes|Szolgáltatás-Cosmos DB kérelmek|Darabszám|Sum|A szolgáltatás által Cosmos DBre küldött kérelmek teljes száma.|Művelet, ResourceType|
|CosmosDbThrottleRate|Yes|Szolgáltatási Cosmos DB szabályozásának sebessége|Darabszám|Sum|A szolgáltatás Cosmos DBi biztonsági mentésére adott válaszok teljes 429 száma.|Művelet, ResourceType|
|IoTConnectorDeviceEvent|Yes|Bejövő üzenetek száma|Darabszám|Sum|Az Azure IoT-összekötő által az FHIR számára fogadott üzenetek teljes száma a normalizálás előtt.|Művelet, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Átlagos normalizálás szakasz késése|Ezredmásodpercben|Átlag|Egy esemény betöltési idejének és a normalizálás során az esemény feldolgozásának időpontjának átlagos ideje.|Művelet, ConnectorName|
|IoTConnectorMeasurement|Yes|Mérések száma|Darabszám|Sum|A FHIR Azure IoT-összekötő FHIR átalakítási szakasza által fogadott Normalizált érték-olvasási értékek száma.|Művelet, ConnectorName|
|IoTConnectorMeasurementGroup|Yes|Üzenetszöveg-csoportok száma|Darabszám|Sum|A FHIR-átalakítási fázis által generált típusok, eszközök, páciensek és beállított időszakok egyedi csoportosítási csoportjainak teljes száma.|Művelet, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Csoportosítási szakasz átlagos késése|Ezredmásodpercben|Átlag|Az az időtartam, amikor az IoT-összekötő megkapta az eszközre vonatkozó adatgyűjtést, és amikor az FHIR-átalakítási fázis dolgozza fel az adatfeldolgozást.|Művelet, ConnectorName|
|IoTConnectorNormalizedEvent|Yes|Normalizált üzenetek száma|Darabszám|Sum|A FHIR-hez készült Azure IoT-összekötő normalizálás szakasza által leképezett megfeleltetett normalizált értékek teljes száma.|Művelet, ConnectorName|
|IoTConnectorTotalErrors|Yes|Hibák száma összesen|Darabszám|Sum|Az Azure IoT-összekötő által a FHIR számára naplózott hibák teljes száma|Név, művelet, ErrorType, ErrorSeverity, ConnectorName|
|ServiceApiErrors|Yes|Szolgáltatási hibák|Darabszám|Sum|A szolgáltatás által létrehozott belső kiszolgálói hibák teljes száma.|Protokoll, hitelesítés, művelet, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiLatency|Yes|Szolgáltatás késése|Ezredmásodpercben|Átlag|A szolgáltatás válaszának késése.|Protokoll, hitelesítés, művelet, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Yes|Service Requests|Darabszám|Sum|A szolgáltatás által fogadott kérelmek teljes száma.|Protokoll, hitelesítés, művelet, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|TotalErrors|Yes|Összes hiba|Darabszám|Sum|A szolgáltatás által észlelt belső kiszolgálói hibák teljes száma.|Protokoll, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Teljes késés|Ezredmásodpercben|Átlag|A szolgáltatás válaszának késése.|Protokoll|
|TotalRequests|Yes|Összes kérelem|Darabszám|Sum|A szolgáltatás által fogadott kérelmek teljes száma.|Protokoll|


## <a name="microsofthybridnetworknetworkfunctions"></a>Microsoft. hybridnetwork/networkfunctions

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|CPU átlagos kihasználtsága|Százalék|Átlag|A virtuális processzor kihasználtságának átlagos százalékos aránya egy percen belül. A virtuális CPU teljes száma az SKU-definícióban a felhasználó által konfigurált értéken alapul. A további szűrést az SKU-ban definiált RoleName alapján lehet alkalmazni.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>Microsoft. hybridnetwork/virtualnetworkfunctions

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|CPU átlagos kihasználtsága|Százalék|Átlag|A virtuális processzor kihasználtságának átlagos százalékos aránya egy percen belül. A virtuális CPU teljes száma az SKU-definícióban a felhasználó által konfigurált értéken alapul. A további szűrést az SKU-ban definiált RoleName alapján lehet alkalmazni.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/autoscalesettings

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Metrika küszöbértéke|Darabszám|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Yes|Megfigyelt kapacitás|Darabszám|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.||
|ObservedMetricValue|Yes|Megfigyelt metrika értéke|Darabszám|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Elindított skálázási műveletek|Darabszám|Összesen|A skálázási művelet iránya.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Rendelkezésre állás|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|No|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Yes|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Yes|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|Nincsenek méretek|
|browserTimings/processingDuration|Yes|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|Nincsenek méretek|
|browserTimings/receiveDuration|Yes|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|Nincsenek méretek|
|browserTimings/sendDuration|Yes|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincsenek méretek|
|browserTimings/totalDuration|Yes|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|Nincsenek méretek|
|függőségek/darabszám|No|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/időtartam|Yes|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/sikertelen|No|Függőségi hívások hibái|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|függőség/típus, függőség/performanceBucket, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kivételek/böngésző|No|Böngészőkivételek|Darabszám|Darabszám|A böngészőben fellépő nem kezelt kivételek száma.|felhő/roleName|
|kivételek/darabszám|Yes|Kivételek|Darabszám|Darabszám|Az összes nem kezelt kivétel összesített száma.|felhő/roleName, felhő/roleInstance, ügyfél/típus|
|kivételek/kiszolgáló|No|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|felhő/roleName, felhő/roleInstance|
|Oldalmegtekintések/darabszám|Yes|Lapok nézetei|Darabszám|Darabszám|Lapok nézeteinek száma.|művelet/szintetikus, felhő/roleName|
|Oldalmegtekintések/időtartam|Yes|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus, felhő/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Igénybe vehető memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Yes|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|performanceCounters/requestExecutionTime|Yes|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|Yes|HTTP-kérelmek az alkalmazás-várólistában|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájának hossza|felhő/roleInstance|
|performanceCounters/requestsPerSecond|Yes|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|felhő/roleInstance|
|kérelmek/darabszám|No|Kiszolgálói kérelmek|Darabszám|Darabszám|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/időtartam|Yes|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|No|Sikertelen kérelmek|Darabszám|Darabszám|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések >= 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|No|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|nyomkövetés/darabszám|Yes|Hívásláncok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|nyomkövetés/severityLevel, művelet/szintetikus, felhő/roleName, felhő/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. Property. Read. failure|Yes|Nem sikerült beolvasni az eszköz tulajdonságát IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok összes olvasásának száma|Nincsenek méretek|
|C2D. Property. Read. success|Yes|Az eszköz tulajdonságainak olvasása IoT Central|Darabszám|Összesen|Az IoT Centralról kezdeményezett sikeres tulajdonságok száma|Nincsenek méretek|
|C2D. Property. Update. hiba|Yes|Nem sikerült frissíteni az eszköz tulajdonságait IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok frissítéseinek száma|Nincsenek méretek|
|C2D. Property. Update. success|Yes|Az eszköz tulajdonságainak frissítése IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett összes sikeres tulajdonság-frissítés száma|Nincsenek méretek|
|connectedDeviceCount|No|Csatlakoztatott eszközök összesen|Darabszám|Átlag|IoT Centralhoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. Property. Read. failure|Yes|Nem sikerült beolvasni az eszköz tulajdonságait az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikertelen tulajdonságok összes olvasásának száma|Nincsenek méretek|
|D2C. Property. Read. success|Yes|Az eszköz tulajdonságainak olvasása az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikeres tulajdonságok összes olvasásának száma|Nincsenek méretek|
|D2C. Property. Update. hiba|Yes|Nem sikerült az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről indított összes sikertelen tulajdonság-frissítés száma|Nincsenek méretek|
|D2C. Property. Update. success|Yes|Az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikeres tulajdonság-frissítés száma|Nincsenek méretek|
|dataExport. error|Yes|Adatexportálási hibák|Darabszám|Összesen|Az adatexportálás során észlelt hibák száma|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport. messages. FILTERED|Yes|Adatexportálási üzenetek szűrve|Darabszám|Összesen|Az adatexportálási szűrőkkel továbbított üzenetek száma|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport. messages. Received|Yes|Fogadott adatexportálási üzenetek|Darabszám|Összesen|Az adatexportálásba bejövő üzenetek száma a szűrés és a dúsítás feldolgozása előtt|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport. messages. írásos|Yes|Az adatexportálási üzenetek írása|Darabszám|Összesen|Célhelyre írt üzenetek száma|exportId, exportDisplayName, destinationId, destinationDisplayName|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApiLatency|No|ApiLatency|6|0|A Microsoft. IoTSpaces API-kérelmek késésének mértéke ezredmásodpercben|Nincsenek méretek|
|FunctionExecutionLatency|No|FunctionExecutionLatency|6|0|A felhasználó által definiált függvények késését méri a Microsoft. IoTSpaces ezredmásodpercben.|Nincsenek méretek|
|MessageEgressFailure|No|MessageEgressFailure|2|3|Megkeres egy honosított karakterláncot, amely a mértékek esetében a Microsoft. IoTSpaces esetében nem sikerült darabszámú eseményt eredményezett.|Nincsenek méretek|
|MessageEgressLatency|No|MessageEgressLatency|6|0|A Microsoft. IoTSpaces ezredmásodpercben méri a kiosztótól a többi végpontig eltelt időt.|Nincsenek méretek|
|MessageEgressSuccess|No|MessageEgressSuccess|2|3|Megkeres egy honosított karakterláncot, amely a Microsoft. IoTSpaces számának befejezett mértékek száma eseményéhez hasonló.|Nincsenek méretek|
|ProcessingLatency|No|ProcessingLatency|6|0|A Microsoft. IoTSpaces által a kiküldett eseményhez betöltött üzenetek késését adja meg ezredmásodpercben.|Nincsenek méretek|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. kulcstartó/managedhsms

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|No|Teljes szolgáltatás rendelkezésre állása|Százalék|Átlag|Szolgáltatási kérelmek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|No|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Tár teljes rendelkezésre állása|Százalék|Átlag|Tár kéréseinek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Tár teljes telítettsége|Százalék|Átlag|Tár kapacitásának használata|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Yes|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>Microsoft. kubernetes/connectedClusters

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|A csatlakoztatott fürtben található CPU-magok teljes száma|Darabszám|Összesen|A csatlakoztatott fürtben található CPU-magok teljes száma||


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Köteg Blobok száma|Darabszám|Átlag|Adatforrások száma egy összesített kötegben a betöltéshez.|Adatbázis|
|BatchDuration|Yes|Köteg időtartama|Másodperc|Átlag|A betöltési folyamat összesítési fázisának időtartama.|Adatbázis|
|BatchesProcessed|Yes|Feldolgozott kötegek|Darabszám|Összesen|A betöltéshez összesített kötegek száma. Kötegelt feldolgozás típusa: azt határozza meg, hogy a köteg elérte-e a kötegelt feldolgozás időpontját, az adatméretet vagy a fájlokra vonatkozó korlátot.|Adatbázis, SealReason|
|BatchSize|Yes|Köteg mérete|Bájt|Átlag|Tömörítetlen várt adatméret egy összesített kötegben a betöltéshez.|Adatbázis|
|BlobsDropped|Yes|Blobok elvetve|Darabszám|Összesen|Egy összetevő által véglegesen visszautasított Blobok száma.|Adatbázis, ComponentType, ComponentName|
|BlobsProcessed|Yes|Feldolgozott Blobok|Darabszám|Összesen|Egy összetevő által feldolgozott Blobok száma.|Adatbázis, ComponentType, ComponentName|
|BlobsReceived|Yes|Blobok fogadva|Darabszám|Összesen|Egy összetevő által a bemeneti streamből fogadott Blobok száma.|Adatbázis, ComponentType, ComponentName|
|CacheUtilization|Yes|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|Nincsenek méretek|
|ContinuousExportMaxLatenessMinutes|Yes|Folyamatos exportálás maximális késői|Darabszám|Maximum|A fürt folyamatos exportálási feladatai által jelentett késői idő (percben)|Nincsenek méretek|
|ContinuousExportNumOfRecordsExported|Yes|Folyamatos exportálás – számú exportált rekord|Darabszám|Összesen|Az exportált rekordok száma az exportálási művelet során írt összes tárolási összetevő esetében|ContinuousExportName, adatbázis|
|ContinuousExportPendingCount|Yes|Folyamatos exportálás függőben lévő darabszáma|Darabszám|Maximum|A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma|Nincsenek méretek|
|ContinuousExportResult|Yes|Folyamatos exportálás eredménye|Darabszám|Darabszám|Azt jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e|ContinuousExportName, eredmény, adatbázis|
|CPU|Yes|CPU|Százalék|Átlag|CPU-kihasználtsági szint|Nincsenek méretek|
|DiscoveryLatency|Yes|Felderítés késése|Másodperc|Átlag|Adatkapcsolatok jelentik (ha vannak ilyenek). Az az idő másodpercben, amikor egy üzenet várólistán lévő vagy esemény jön létre, amíg az adatkapcsolat fel nem deríti. Ezt az időt nem tartalmazza az Azure Adatkezelő teljes betöltési időtartama.|ComponentType, ComponentName|
|EventsDropped|Yes|Eldobott események|Darabszám|Összesen|Az adatkapcsolatok által véglegesen eldobott események száma. A rendszer elküld egy betöltési eredmény metrikáját a hiba okának megfelelően.|ComponentType, ComponentName|
|EventsProcessed|Yes|Feldolgozott események|Darabszám|Összesen|A fürt által feldolgozott események száma|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Yes|Feldolgozott események (Event/IoT hubok esetében)|Darabszám|Összesen|A fürt által az esemény/IoT Hub betöltéskor feldolgozott események száma|EventStatus|
|EventsReceived|Yes|Fogadott események|Darabszám|Összesen|Az adatkapcsolatok által fogadott események száma.|ComponentType, ComponentName|
|ExportUtilization|Yes|Exportálás kihasználtsága|Százalék|Maximum|Exportálás kihasználtsága|Nincsenek méretek|
|IngestionLatencyInSeconds|Yes|Betöltési késés|Másodperc|Átlag|A betöltött adatok késése, az adatok fürtbeli fogadásától a lekérdezésre való előkészítésükig. A betöltési késés időtartama a betöltési forgatókönyvtől függ.|Nincsenek méretek|
|IngestionResult|Yes|Betöltés eredménye|Darabszám|Összesen|Betöltési műveletek száma|IngestionResultDetails|
|IngestionUtilization|Yes|Betöltési kihasználtság|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|Nincsenek méretek|
|IngestionVolumeInMB|Yes|Betöltési mennyiség|Bájt|Összesen|Teljes mennyiségű betöltött adatot a fürtbe|Adatbázis|
|InstanceCount|Yes|Példányszám|Darabszám|Átlag|Példányok száma összesen|Nincsenek méretek|
|KeepAlive|Yes|Életben tartása|Darabszám|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|Nincsenek méretek|
|MaterializedViewAgeMinutes|Yes|Jelentős nézet kora|Darabszám|Átlag|Az anyag nézet életkora percben kifejezve|Adatbázis, MaterializedViewName|
|MaterializedViewDataLoss|Yes|Jelentős adatvesztés|Darabszám|Maximum|A lehetséges adatvesztést jelzi a jelentős nézetben|Adatbázis, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|Yes|Az anyagbeli megtekintési egységek újraépítése|Darabszám|Átlag|Egységek számának újraépítése|Adatbázis, MaterializedViewName|
|MaterializedViewHealth|Yes|Anyagbeli nézet állapota|Darabszám|Átlag|Az anyagbeli nézet állapota (1 – kifogástalan, 0 – nem kifogástalan)|Adatbázis, MaterializedViewName|
|MaterializedViewRecordsInDelta|Yes|A különbözeti rekordok megtekintése|Darabszám|Átlag|A nézet nem anyagként szolgáló részében lévő rekordok száma|Adatbázis, MaterializedViewName|
|MaterializedViewResult|Yes|Anyagbeli nézet eredménye|Darabszám|Átlag|A megvalósításának gyakorlatban folyamat eredménye|Adatbázis, MaterializedViewName, eredmény|
|QueryDuration|Yes|Lekérdezés időtartama|Ezredmásodpercben|Átlag|Lekérdezések időtartama másodpercben|QueryStatus|
|QueryResult|No|Lekérdezés eredménye|Darabszám|Darabszám|A lekérdezések teljes száma.|QueryStatus|
|QueueLength|Yes|Processzor-várólista hossza|Darabszám|Átlag|Egy összetevő várólistájában várakozó üzenetek száma.|ComponentType|
|QueueOldestMessage|Yes|Várólista legrégebbi üzenete|Darabszám|Átlag|Az az idő másodpercben, amikor a rendszer beszúrta a legrégebbi üzenetet a várólistába.|ComponentType|
|ReceivedDataSizeBytes|Yes|Fogadott adatméret bájtjai|Bájt|Átlag|Az adatkapcsolatok által fogadott adatmennyiség. Ez az adatfolyam mérete, vagy ha meg van adni a nyers adatméret.|ComponentType, ComponentName|
|StageLatency|Yes|Szakasz késése|Másodperc|Átlag|Kumulatív idő, amikor egy üzenet fel lett derítve, amíg meg nem érkezik a jelentéskészítési összetevő a feldolgozáshoz (a felderítési idő be van állítva, ha az üzenet várólistán lévő a betöltési sorhoz, vagy amikor az adatcsatlakozás során felderíti az üzenetet).|Adatbázis, ComponentType|
|SteamingIngestRequestRate|Yes|Streamelési betöltési kérelmek gyakorisága|Darabszám|RateRequestsPerSecond|Folyamatos átviteli kérelmek aránya (kérelmek/másodperc)|Nincsenek méretek|
|StreamingIngestDataRate|Yes|Streamelés adatbetöltési sebessége|Darabszám|Átlag|Adatforgalom adatátviteli sebessége (MB/s)|Nincsenek méretek|
|StreamingIngestDuration|Yes|Streamelés betöltési időtartama|Ezredmásodpercben|Átlag|Folyamatos átvitel időtartama (ezredmásodperc)|Nincsenek méretek|
|StreamingIngestResults|Yes|Streamelés betöltési eredménye|Darabszám|Átlag|Folyamatos átvitel eredménye|Eredmény|
|TotalNumberOfConcurrentQueries|Yes|Az egyidejű lekérdezések teljes száma|Darabszám|Maximum|Az egyidejű lekérdezések teljes száma|Nincsenek méretek|
|TotalNumberOfExtents|Yes|Egységek teljes száma|Darabszám|Összesen|Adategységek teljes száma|Nincsenek méretek|
|TotalNumberOfThrottledCommands|Yes|A szabályozott parancsok teljes száma|Darabszám|Összesen|A szabályozott parancsok teljes száma|CommandType|
|TotalNumberOfThrottledQueries|Yes|A szabályozott lekérdezések teljes száma|Darabszám|Maximum|A szabályozott lekérdezések teljes száma|Nincsenek méretek|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Yes|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Yes|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Yes|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Yes|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Yes|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Yes|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Yes|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|Nincsenek méretek|
|RunFailurePercentage|Yes|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Yes|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Yes|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Yes|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Yes|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Yes|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Yes|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Yes|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Yes|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Yes|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TriggerFireLatency|Yes|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Yes|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Yes|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Yes|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Yes|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Yes|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Yes|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Yes|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Yes|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Yes|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Yes|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Yes|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Yes|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Yes|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Yes|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Yes|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Yes|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|BillableActionExecutions|Yes|Számlázható műveletek végrehajtása|Darabszám|Összesen|A számlázott munkafolyamat-műveletek végrehajtásának száma.|Nincsenek méretek|
|BillableTriggerExecutions|Yes|Számlázható trigger-végrehajtások|Darabszám|Összesen|A számlázott munkafolyamat-trigger-végrehajtások száma.|Nincsenek méretek|
|BillingUsageNativeOperation|Yes|A natív művelet végrehajtásához használt számlázási használat|Darabszám|Összesen|A számlázott natív művelet-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStandardConnector|Yes|A standard szintű összekötők végrehajtásának számlázási használata|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStorageConsumption|Yes|Számlázási használat a tárolási felhasználás végrehajtásához|Darabszám|Összesen|A számlázható tárterület-használat végrehajtásának száma.|Nincsenek méretek|
|RunFailurePercentage|Yes|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Yes|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Yes|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Yes|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Yes|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Yes|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Yes|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Yes|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Yes|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Yes|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TotalBillableExecutions|Yes|Számlázandó végrehajtások összesen|Darabszám|Összesen|A számlázandó munkafolyamat-végrehajtások száma.|Nincsenek méretek|
|TriggerFireLatency|Yes|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Yes|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Yes|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Yes|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Yes|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Yes|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Yes|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Yes|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Yes|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Yes|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Yes|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Yes|Aktív csomópontok|Darabszám|Átlag|Acitve-csomópontok száma Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Forgatókönyv, ClusterName|
|A kért futtatások megszakítása|Yes|A kért futtatások megszakítása|Darabszám|Összesen|Azon futtatások száma, amelyek esetében a rendszer a megszakítást kérelmezte ehhez a munkaterülethez. A Count akkor frissül, ha a lemondási kérelem érkezett a futtatáshoz.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Megszakított futtatások|Yes|Megszakított futtatások|Darabszám|Összesen|A munkaterülethez megszakított futtatások száma. A rendszer a Futtatás sikeres megszakítása után frissíti a darabszámot.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Befejezett futtatások|Yes|Befejezett futtatások|Darabszám|Összesen|A munkaterületen sikeresen befejeződött a futtatások száma. A számláló a Futtatás befejeződése után frissül, és a rendszer a kimenetet gyűjtötte.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuUtilization|Yes|CpuUtilization|Darabszám|Átlag|A CPU-csomópont kihasználtságának százalékos értéke. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, ClusterName|
|Hibák|Yes|Hibák|Darabszám|Összesen|A munkaterületen futtatott hibák száma. A Count frissítése akkor történik meg, amikor a Futtatás hibát észlel.|Eset|
|Sikertelen futtatások|Yes|Sikertelen futtatások|Darabszám|Összesen|A munkaterületen nem sikerült a futtatások száma. A Count a Futtatás meghiúsulása esetén frissül.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Futtatások véglegesítése|Yes|Futtatások véglegesítése|Darabszám|Összesen|A munkaterületre vonatkozó véglegesítési állapotba lépett futtatások száma. A Count akkor frissül, ha egy Futtatás befejeződött, de a kimeneti gyűjtemény még folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|Darabszám|Átlag|A memória kihasználtságának százalékos aránya egy GPU-csomóponton. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, DeviceId, ClusterName|
|GpuUtilization|Yes|GpuUtilization|Darabszám|Átlag|A GPU-csomópont kihasználtságának százalékos értéke. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, DeviceId, ClusterName|
|Üresjárati magok|Yes|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Yes|Tétlen csomópontok|Darabszám|Átlag|Az üresjárati csomópontok száma. Az üresjárati csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de az elérhetővé tehetik az új feladatot.|Forgatókönyv, ClusterName|
|Magok kihagyása|Yes|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Yes|Csomópontok elhagyása|Darabszám|Átlag|A csomópontok elhagyásának száma. A csomópontok elhagyják azokat a csomópontokat, amelyek éppen befejezték a feladatok feldolgozását, és az inaktív állapotba kerülnek.|Forgatókönyv, ClusterName|
|Modell-üzembehelyezés sikertelen|Yes|Modell-üzembehelyezés sikertelen|Darabszám|Összesen|A munkaterületen sikertelen telepítési modellek száma|Forgatókönyv, StatusCode|
|Modell-üzembehelyezés elindítva|Yes|Modell-üzembehelyezés elindítva|Darabszám|Összesen|A munkaterületen elindított modellek központi telepítésének száma|Eset|
|Modell-üzembehelyezés sikerült|Yes|Modell-üzembehelyezés sikerült|Darabszám|Összesen|A munkaterületen sikeres központi telepítési modellek száma|Eset|
|A modell regisztrálása nem sikerült|Yes|A modell regisztrálása nem sikerült|Darabszám|Összesen|A munkaterületen meghiúsult modell-regisztrációk száma|Forgatókönyv, StatusCode|
|A modell regisztrálása sikerült|Yes|A modell regisztrálása sikerült|Darabszám|Összesen|A munkaterületen sikeres modell-regisztrációk száma|Eset|
|Nem válaszoló futtatások|Yes|Nem válaszoló futtatások|Darabszám|Összesen|A munkaterületre nem válaszoló futtatások száma. A Count akkor frissül, ha a Futtatás nem válaszol.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Nem indult el a futtatások|Yes|Nem indult el a futtatások|Darabszám|Összesen|A nem elindított futtatások száma ehhez a munkaterülethez. A Count frissítése akkor történik meg, ha egy futtatási kérelem érkezik, de a futtatási adatok még nem lettek feltöltve. |Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Előzik magok|Yes|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Yes|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma Ezek a csomópontok az alacsony prioritású csomópontok, amelyek a rendelkezésre álló csomópont-készletből származnak.|Forgatókönyv, ClusterName|
|Futtatások előkészítése|Yes|Futtatások előkészítése|Darabszám|Összesen|A munkaterületre felkészülő futtatások száma. A Count akkor frissül, ha egy Futtatás előkészítési állapotba kerül, amíg a futtatási környezet előkészítése folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kiépítési futtatások|Yes|Kiépítési futtatások|Darabszám|Összesen|A munkaterülethez kiépített futtatások száma. A Count frissítése akkor történik meg, ha egy Futtatás a számítási cél létrehozására vagy kiépítési célra vár.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Várólistán lévő futtatások|Yes|Várólistán lévő futtatások|Darabszám|Összesen|Azon futtatások száma, amelyek várólistára kerülnek ehhez a munkaterülethez. A Count akkor frissül, ha egy Futtatás várólistára kerül a számítási célra. Akkor fordulhat elő, ha a szükséges számítási csomópontok üzemkész állapotra várnak.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kvóta kihasználtsága (%)|Yes|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Elindított futtatások|Yes|Elindított futtatások|Darabszám|Összesen|A munkaterületen futó futtatások száma. A Count akkor frissül, ha a Futtatás a szükséges erőforrásokon fut.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Futtatások indítása|Yes|Futtatások indítása|Darabszám|Összesen|A munkaterülethez elindított futtatások száma. A Count frissítése a futtatási és futtatási adatok (például a futtatási azonosító) létrehozási kérelme alapján történt.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Magok összesen|Yes|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Yes|Csomópontok összesen|Darabszám|Átlag|A csomópontok száma összesen. Ez az összeg magában foglalja az aktív csomópontok, a tétlen csomópontok, a nem használható csomópontok, a Premepted csomópontok és a csomópontok elhagyását|Forgatókönyv, ClusterName|
|Használhatatlan magok|Yes|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Yes|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma Néhány feloldhatatlan probléma miatt nem használható csomópontok nem működőképesek. Az Azure újrahasznosítja ezeket a csomópontokat.|Forgatókönyv, ClusterName|
|Figyelmeztetések|Yes|Figyelmeztetések|Darabszám|Összesen|A futtatási figyelmeztetések száma ebben a munkaterületen. A darabszám akkor frissül, amikor egy Futtatás figyelmeztetést észlel.|Eset|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|
|Használat|No|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Eszközök száma|Darabszám|Átlag|Az aktuális Media Service-fiókban már létrehozott eszközök száma|Nincsenek méretek|
|AssetQuota|Yes|Eszköz kvótája|Darabszám|Átlag|Hány eszköz engedélyezett a jelenlegi Media Service-fiókhoz|Nincsenek méretek|
|AssetQuotaUsedPercentage|Yes|Eszköz kvótájának kihasználtsága (%)|Százalék|Átlag|Az eszköz által használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|
|ChannelsAndLiveEventsCount|Yes|Élő események száma|Darabszám|Átlag|Az aktuális Media Services-fiókban élő események teljes száma|Nincsenek méretek|
|ContentKeyPolicyCount|Yes|Tartalmi kulcsokra vonatkozó szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejött a tartalmi kulcs házirendjeinek száma|Nincsenek méretek|
|ContentKeyPolicyQuota|Yes|Tartalmi kulcs házirend-kvótája|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó tartalmi kulcsokra vonatkozó házirendek száma|Nincsenek méretek|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|Százalék|Átlag|A tartalmi kulcsra vonatkozó házirend százaléka a jelenlegi Media Service-fiókban|Nincsenek méretek|
|RunningChannelsAndLiveEventsCount|Yes|Élő események számának futtatása|Darabszám|Átlag|Az aktuális Media Services-fiókban futó élő események teljes száma|Nincsenek méretek|
|StreamingPolicyCount|Yes|Folyamatos átviteli szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejöttek a folyamatos átviteli házirendek|Nincsenek méretek|
|StreamingPolicyQuota|Yes|Streaming Policy-kvóta|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó folyamatos átviteli házirendek száma|Nincsenek méretek|
|StreamingPolicyQuotaUsedPercentage|Yes|Adatfolyam-házirend kvótájának kihasználtsága (%)|Százalék|Átlag|A streaming Policy használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft. Media/Mediaservices/liveEvents

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|IngestBitrate|Yes|Élő esemény betöltési bitrátája|BitsPerSecond|Átlag|Egy élő eseményhez (bit/mp) betöltött bejövő bitráta.|TrackName|
|IngestDriftValue|Yes|Élő esemény betöltésének drift értéke|Másodperc|Maximum|A betöltött tartalom és a rendszeróra időbélyege közötti eltolódás a percenkénti másodpercben kifejezve. A nullától eltérő érték azt jelzi, hogy a betöltött tartalom lassabban érkezik a rendszeridőt jelző időpontig.|TrackName|
|IngestLastTimestamp|Yes|Élő esemény betöltésének utolsó időbélyeg|Ezredmásodpercben|Maximum|Élő esemény utolsó időbélyege.|TrackName|
|LiveOutputLastTimestamp|Yes|Utolsó kimeneti időbélyeg|Ezredmásodpercben|Maximum|Az élő esemény kimenetére a tárterületre feltöltött utolsó töredék időbélyegzője.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Mediaservices/streamingEndpoints

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CPU|Yes|Processzorhasználat|Százalék|Átlag|A prémium szintű streaming-végpontok CPU-használata. Ezek az adatátviteli végpontok nem érhetők el.|VmId|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve.|OutputFormat|
|EgressBandwidth|No|Kimenő sávszélesség|BitsPerSecond|Átlag|Kimenő forgalom (bit/mp).|VmId|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Továbbítási végpontra irányuló kérelmek.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|A Befejezés végének késése|Ezredmásodpercben|Átlag|A sikeres kérelmek átlagos késése ezredmásodpercben.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Aktív renderelési munkamenetek|Darabszám|Átlag|Aktív renderelési munkamenetek száma összesen|SessionType, SDKVersion|
|AssetsConverted|Yes|Átalakított eszközök|Darabszám|Összesen|Átalakított eszközök teljes száma|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft. MixedReality/spatialAnchorsAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AnchorsCreated|Yes|Létrehozott horgonyok|Darabszám|Összesen|Létrehozott horgonyok száma|DeviceFamily, SDKVersion|
|AnchorsDeleted|Yes|Törölt horgonyok|Darabszám|Összesen|Törölt horgonyok száma|DeviceFamily, SDKVersion|
|AnchorsQueried|Yes|Lekérdezett horgonyok|Darabszám|Összesen|Lekérdezett térbeli horgonyok száma|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Frissített horgonyok|Darabszám|Összesen|Frissített horgonyok száma|DeviceFamily, SDKVersion|
|PosesFound|Yes|Talált eredmény|Darabszám|Összesen|Visszaadott eredmény száma|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Yes|Napi horgonyok összesen|Darabszám|Átlag|A horgonyok teljes száma – naponta|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Készlet lefoglalt mérete|Bájt|Átlag|A készlet kiépített mérete|Nincsenek méretek|
|VolumePoolAllocatedToVolumeThroughput|Yes|Lefoglalt készlet átviteli sebessége|BytesPerSecond|Átlag|A készlethez tartozó összes kötet átviteli sebességének összege|Nincsenek méretek|
|VolumePoolAllocatedUsed|Yes|A kötet méretéhez lefoglalt készlet|Bájt|Átlag|A készlet lefoglalt felhasznált mérete|Nincsenek méretek|
|VolumePoolProvisionedThroughput|Yes|Kiosztott átviteli sebesség a készlethez|BytesPerSecond|Átlag|A készlet kiépített átviteli sebessége|Nincsenek méretek|
|VolumePoolTotalLogicalSize|Yes|Készlet felhasznált mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincsenek méretek|
|VolumePoolTotalSnapshotSize|Yes|A készlethez tartozó pillanatképek teljes mérete|Bájt|Átlag|A készletben lévő összes kötet pillanatkép-méretének összege|Nincsenek méretek|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/kötetek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Olvasási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|Nincsenek méretek|
|AverageWriteLatency|Yes|Írási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|Nincsenek méretek|
|CbsVolumeBackupActive|Yes|A kötet biztonsági mentése fel van függesztve|Darabszám|Átlag|Felfüggesztette a biztonsági mentési szabályzatot a kötetre? 1 Ha igen, 0, ha nem.|Nincsenek méretek|
|CbsVolumeLogicalBackupBytes|Yes|Kötet biztonsági másolatának bájtjai|Bájt|Átlag|A köteten biztonsági mentéssel elkészített bájtok száma.|Nincsenek méretek|
|CbsVolumeOperationComplete|Yes|A kötet biztonsági mentési művelete befejeződött|Darabszám|Átlag|Sikerült befejezni az utolsó kötet biztonsági mentési vagy visszaállítási műveletét? 1 Ha igen, 0, ha nem.|Nincsenek méretek|
|CbsVolumeOperationTransferredBytes|Yes|Kötet biztonsági mentése utoljára továbbított bájtok|Bájt|Átlag|A legutóbbi biztonsági mentési vagy visszaállítási művelet során átvitt bájtok teljes száma.|Nincsenek méretek|
|CbsVolumeProtected|Yes|Engedélyezve van-e a kötet biztonsági mentése|Darabszám|Átlag|Engedélyezve van a biztonsági másolat a köteten? 1 Ha igen, 0, ha nem.|Nincsenek méretek|
|OtherThroughput|Yes|Egyéb átviteli sebesség|BytesPerSecond|Átlag|Egyéb átviteli sebesség (amely nem írható vagy írható) bájt/másodpercben|Nincsenek méretek|
|ReadIops|Yes|IOPS olvasása|CountPerSecond|Átlag|Olvasási/kimenő műveletek másodpercenként|Nincsenek méretek|
|ReadThroughput|Yes|Olvasási sebesség|BytesPerSecond|Átlag|Olvasási sebesség (bájt/s)|Nincsenek méretek|
|TotalThroughput|Yes|Teljes átviteli sebesség|BytesPerSecond|Átlag|Az összes átviteli sebesség (bájt/s) összege másodpercenként|Nincsenek méretek|
|VolumeAllocatedSize|Yes|Kötet lefoglalt mérete|Bájt|Átlag|Kötet kiépített mérete|Nincsenek méretek|
|VolumeConsumedSizePercentage|Yes|Százalékos mennyiség felhasznált mérete|Százalék|Átlag|A felhasznált kötet százalékos aránya, beleértve a pillanatképeket is.|Nincsenek méretek|
|VolumeLogicalSize|Yes|Kötet felhasznált mérete|Bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|Nincsenek méretek|
|VolumeSnapshotSize|Yes|Kötet pillanatképének mérete|Bájt|Átlag|A köteten található összes pillanatkép mérete|Nincsenek méretek|
|WriteIops|Yes|IOPS írása|CountPerSecond|Átlag|Írási/kimenő műveletek másodpercenként|Nincsenek méretek|
|WriteThroughput|Yes|Írási sebesség|BytesPerSecond|Átlag|Írási sebesség (bájt/s) másodpercenként|Nincsenek méretek|
|XregionReplicationHealthy|Yes|A kötet replikálási állapota Kifogástalan|Darabszám|Átlag|A kapcsolat feltétele, 1 vagy 0.|Nincsenek méretek|
|XregionReplicationLagTime|Yes|Kötet replikációs késési ideje|Másodperc|Átlag|Az az időtartam másodpercben, ameddig a tükrözött adatmennyiség a forrás mögött marad.|Nincsenek méretek|
|XregionReplicationLastTransferDuration|Yes|Kötet replikálásának utolsó átvitelének időtartama|Másodperc|Átlag|Az utolsó átvitel befejezéséhez szükséges idő másodpercben.|Nincsenek méretek|
|XregionReplicationLastTransferSize|Yes|Kötet replikálásának utolsó átvitelének mérete|Bájt|Átlag|Az utolsó átvitel részeként átvitt bájtok teljes száma.|Nincsenek méretek|
|XregionReplicationRelationshipProgress|Yes|Kötet replikálásának folyamata|Bájt|Átlag|Az aktuális adatátviteli művelethez továbbított adatok teljes mennyisége.|Nincsenek méretek|
|XregionReplicationRelationshipTransferring|Yes|A kötet-replikálás átadása|Darabszám|Átlag|Azt jelzi, hogy a kötet replikálásának állapota "átvitel".|Nincsenek méretek|
|XregionReplicationTotalTransferBytes|Yes|Kötet replikálásának teljes átvitele|Bájt|Átlag|A kapcsolathoz továbbított összesített bájtok száma.|Nincsenek méretek|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway teljes idő|Ezredmásodpercben|Átlag|A kérelem feldolgozásának és a válasz elküldésekor elvégezhető átlagos idő. Ez az időszak átlaga, amikor a Application Gateway megkapja egy HTTP-kérelem első bájtját, amikor a válasz küldése művelet befejeződik. Fontos megjegyezni, hogy ez általában a Application Gateway feldolgozási időt, az időpontot, amikor a kérés és a válasz csomagjai a hálózaton keresztül utaznak, valamint a háttér-kiszolgáló válaszának időpontját.|Figyelő|
|AvgRequestCountPerHealthyHost|No|Percenkénti kérelmek száma kifogástalan állapotú gazdagépen|Darabszám|Átlag|Kérelmek átlagos száma percenként, a készletben lévő kifogástalan állapotú gazdagépek esetében|BackendSettingsPool|
|BackendConnectTime|No|Háttérbeli kapcsolat ideje|Ezredmásodpercben|Átlag|A háttér-kiszolgálóval létesített kapcsolatok létrehozásához töltött idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendFirstByteResponseTime|No|Háttérbeli első bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz fejléc első bájtjának fogadása között eltelt idő, a háttér-kiszolgáló feldolgozási idejének megközelítő időpontja|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendLastByteResponseTime|No|Háttérbeli utolsó bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz törzse utolsó bájtjának fogadása között eltelt idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendResponseStatus|Yes|Háttérbeli válasz állapota|Darabszám|Összesen|A háttérbeli tagok által létrehozott HTTP-válasz kódok száma. Ez nem tartalmazza a Application Gateway által létrehozott válasz-kódokat.|BackendServer, Háttérkészletek, Backendhttpsetting értékre, HttpStatusGroup|
|BlockedCount|Yes|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Szerepkörcsoportot, RuleId|
|BlockedReqCount|Yes|Webalkalmazási tűzfal letiltott kérelmek száma|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek száma|Nincsenek méretek|
|BytesReceived|Yes|Fogadott bájtok száma|Bájt|Összesen|A Application Gateway által az ügyfelektől fogadott bájtok teljes száma|Figyelő|
|BytesSent|Yes|Eljuttatott bájtok|Bájt|Összesen|A Application Gateway által az ügyfeleknek eljuttatott bájtok teljes száma|Figyelő|
|CapacityUnits|No|Aktuális kapacitási egységek|Darabszám|Átlag|Felhasznált kapacitási egységek|Nincsenek méretek|
|ClientRtt|No|Ügyfél-RTT|Ezredmásodpercben|Átlag|Az ügyfelek és a Application Gateway közötti átlagos menetidő. Ez a metrika azt jelzi, hogy mennyi időt vesz igénybe a kapcsolatok és a visszaküldési visszaigazolás|Figyelő|
|ComputeUnits|No|Aktuális számítási egységek|Darabszám|Átlag|Felhasznált számítási egységek|Nincsenek méretek|
|CpuUtilization|No|Processzor kihasználtsága|Százalék|Átlag|A Application Gateway aktuális CPU-kihasználtsága|Nincsenek méretek|
|Összege|Yes|Aktuális kapcsolatok|Darabszám|Összesen|Application Gatewaysal létesített aktuális kapcsolatok száma|Nincsenek méretek|
|EstimatedBilledCapacityUnits|No|Becsült számlázott kapacitási egységek|Darabszám|Átlag|A felszámított becsült kapacitási egységek|Nincsenek méretek|
|FailedRequests|Yes|Sikertelen kérelmek|Darabszám|Összesen|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Fix számlázandó kapacitásegységek|Darabszám|Átlag|Minimálisan felszámított kapacitási egységek|Nincsenek méretek|
|HealthyHostCount|Yes|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|MatchedCount|Yes|Webalkalmazási tűzfal teljes szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal – teljes szabály eloszlása a bejövő forgalomhoz|Szerepkörcsoportot, RuleId|
|NewConnectionsPerSecond|No|Új kapcsolatok másodpercenként|CountPerSecond|Átlag|Új kapcsolatok másodpercenként létesített Application Gateway|Nincsenek méretek|
|ResponseStatus|Yes|Válasz állapota|Darabszám|Összesen|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Teljesítmény|No|Teljesítmény|BytesPerSecond|Átlag|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|Nincsenek méretek|
|TlsProtocol|Yes|Ügyfél TLS protokoll|Darabszám|Összesen|Azoknak a TLS-és nem TLS-kérelmeknek a száma, amelyek az ügyfél által kezdeményezett Application Gatewaykal létesített kapcsolatban állnak. A TLS protokoll terjesztésének megtekintéséhez szűrje a Dimension TLS protokollt.|Figyelő, TlsProtocol|
|TotalRequests|Yes|Összes kérelem|Darabszám|Összesen|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|UnhealthyHostCount|Yes|Nem kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Alkalmazás-szabályok találatok száma|Darabszám|Összesen|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|DataProcessed|Yes|Feldolgozott adatok|Bájt|Összesen|A tűzfal által feldolgozott adatmennyiség teljes mennyisége|Nincsenek méretek|
|FirewallHealth|Yes|Tűzfal állapota|Százalék|Átlag|A tűzfal általános állapotát jelzi|Állapot, ok|
|NetworkRuleHit|Yes|Hálózati szabályok találatok száma|Darabszám|Összesen|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|
|SNATPortUtilization|Yes|SNAT-portok kihasználtsága|Százalék|Átlag|Jelenleg használatban lévő kimenő SNAT-portok százalékos aránya|Protokoll|
|Teljesítmény|No|Teljesítmény|BitsPerSecond|Átlag|A tűzfal által feldolgozott átviteli sebesség|Nincsenek méretek|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume|No|Lekérdezési kötet|Darabszám|Összesen|A DNS-zónák számára kiszolgált lekérdezések száma|Nincsenek méretek|
|RecordSetCapacityUtilization|No|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|Nincsenek méretek|
|RecordSetCount|No|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|ARP rendelkezésre állása|Százalék|Átlag|Az ARP és a MSEE közötti rendelkezésre állás az összes társ felé.|PeeringType, társ|
|BgpAvailability|Yes|BGP rendelkezésre állása|Százalék|Átlag|A BGP rendelkezésre állása a MSEE az összes társ felé.|PeeringType, társ|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond|BitsPerSecond|Átlag|Bejövő adatforgalom (bit/mp)|Nincsenek méretek|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond|BitsPerSecond|Átlag|Kimenő adatforgalom másodpercenkénti száma|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/társak

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Kapcsolatnév|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Kapcsolatnév|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|A társnak hirdetett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által hirdetett útvonalak száma|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|A társtól megszerzett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által megszerzett útvonalak száma|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|CPU-kihasználtság (előzetes verzió)|Darabszám|Átlag|A ExpressRoute-átjáró CPU-kihasználtsága|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Útvonalak változásának gyakorisága (előzetes verzió)|Darabszám|Összesen|Útvonalak változásának gyakorisága a ExpressRoute-átjáróban|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Virtuális gépek száma a Virtual Networkban (előzetes verzió)|Darabszám|Maximum|A Virtual Networkban lévő virtuális gépek száma|Nincsenek méretek|
|ExpressRouteGatewayPacketsPerSecond|No|Csomag/másodperc (előzetes verzió)|CountPerSecond|Átlag|ExpressRoute-átjáró csomagjainak száma|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Darabszám|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol|Yes|LineProtocol|Darabszám|Átlag|A port protokolljának állapota|Hivatkozás|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Hivatkozás|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Hivatkozás|
|RxLightLevel|Yes|RxLightLevel|Darabszám|Átlag|Rx-fény szintje dBm-ben|Hivatkozás, sáv|
|TxLightLevel|Yes|TxLightLevel|Darabszám|Átlag|TX-fény szintje dBm-ben|Hivatkozás, sáv|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|BackendRequestCount|Yes|Háttérbeli kérelmek száma|Darabszám|Összesen|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Yes|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Háttérrendszer|
|BillableResponseSize|Yes|Számlázható válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött számlázandó bájtjainak (minimális 2KB) száma az ügyfeleknek.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Kérelem mérete|Bájt|Összesen|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Lefoglalt SNAT-portok|Darabszám|Átlag|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Bájtok száma|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, irány|
|DipAvailability|Yes|Állapotadat-mintavétel|Darabszám|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SnatConnectionCount|Yes|SNAT-kapcsolatok száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|UsedSnatPorts|No|Használt SNAT-portok|Darabszám|Átlag|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Yes|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft. Network/natGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bájt|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Protokoll, irány|
|DatapathAvailability|Yes|DataPath elérhetősége (előzetes verzió)|Darabszám|Átlag|NAT-átjáró DataPath rendelkezésre állása|Nincsenek méretek|
|PacketCount|Yes|Csomagok|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Protokoll, irány|
|PacketDropCount|Yes|Eldobott csomagok|Darabszám|Összesen|Eldobott csomagok száma|Nincsenek méretek|
|SNATConnectionCount|Yes|SNAT-kapcsolatok száma|Darabszám|Összesen|Egyidejű aktív kapcsolatok összesen|Protokoll, ConnectionState|
|TotalConnectionCount|Yes|SNAT-kapcsolatok száma összesen|Darabszám|Összesen|Aktív SNAT-kapcsolatok teljes száma|Protokoll|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Fogadott bájtok száma|Bájt|Összesen|A hálózati adapter által fogadott bájtok száma|Nincsenek méretek|
|BytesSentRate|Yes|Eljuttatott bájtok|Bájt|Összesen|A hálózati adapter által eljuttatott bájtok száma|Nincsenek méretek|
|PacketsReceivedRate|Yes|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|Nincsenek méretek|
|PacketsSentRate|Yes|Küldött csomagok|Darabszám|Összesen|A hálózati adapter által küldött csomagok száma|Nincsenek méretek|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Átlagos menetidő (MS) (klasszikus)|Ezredmásodpercben|Átlag|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|Nincsenek méretek|
|ChecksFailedPercent|Yes|Sikertelen ellenőrzések százalékos aránya|Százalék|Átlag|a kapcsolat figyelési ellenőrzése nem sikerült|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|%-Os mintavétel sikertelen (klasszikus)|Százalék|Átlag|a kapcsolat figyelési mintavételének%-a meghiúsult|Nincsenek méretek|
|RoundTripTimeMs|Yes|Round-Trip idő (MS)|Ezredmásodpercben|Átlag|A kapcsolat figyelési ellenőrzésének időkorlátja ezredmásodpercben|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|Yes|Teszt eredménye|Darabszám|Átlag|A Csatlakozáskezelő teszt eredménye|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SConnectionCount|Yes|P2S-kapcsolatok száma|Darabszám|Összesen|Az átjáró P2S-kapcsolatainak száma|Protokoll|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Lekérdezési kötet|Darabszám|Összesen|Egy saját DNS zónában kiszolgált lekérdezések száma|Nincsenek méretek|
|RecordSetCapacityUtilization|No|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy saját DNS zóna által használt rekordazonosító-kapacitás százalékos aránya|Nincsenek méretek|
|RecordSetCount|Yes|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy saját DNS zónában|Nincsenek méretek|
|VirtualNetworkLinkCapacityUtilization|No|Virtual Network kapcsolat kapacitásának kihasználtsága|Százalék|Maximum|saját DNS zóna által használt Virtual Network kapcsolat kapacitásának százaléka|Nincsenek méretek|
|VirtualNetworkLinkCount|Yes|Virtual Network kapcsolatok száma|Darabszám|Maximum|saját DNS zónához csatolt virtuális hálózatok száma|Nincsenek méretek|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Virtual Network regisztrációs kapcsolat kapacitásának kihasználtsága|Százalék|Maximum|Virtual Network-hivatkozás százaléka, amely egy saját DNS zóna által használt automatikus regisztrációs kapacitással rendelkezik|Nincsenek méretek|
|VirtualNetworkWithRegistrationLinkCount|Yes|Virtual Network regisztrációs kapcsolatok száma|Darabszám|Maximum|Egy saját DNS zónához kapcsolt virtuális hálózatok száma, amelyeken engedélyezve van az automatikus regisztráció|Nincsenek méretek|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft. Network/privateEndpoints

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Bájtok itt|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateEndpointId|
|PEBytesOut|Yes|Kimenő bájtok|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft. Network/privateLinkServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Bájtok itt|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateLinkServiceId|
|PLSBytesOut|Yes|Kimenő bájtok|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateLinkServiceId|
|PLSNatPortsUsage|Yes|NAT-portok használata|Százalék|Átlag|NAT-portok használata|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bájtok száma|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|BytesDroppedDDoS|Yes|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|Nincsenek méretek|
|BytesForwardedDDoS|Yes|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|Nincsenek méretek|
|BytesInDDoS|Yes|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincsenek méretek|
|DDoSTriggerSYNPackets|Yes|DDoS elleni védelem aktiválására szolgáló bejövő SYN-csomagok|CountPerSecond|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő SYN-csomagok|Nincsenek méretek|
|DDoSTriggerTCPPackets|Yes|DDoS elleni védelem aktiválására szolgáló bejövő TCP-csomagok|CountPerSecond|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő TCP-csomagok|Nincsenek méretek|
|DDoSTriggerUDPPackets|Yes|DDoS elleni védelem aktiválására szolgáló bejövő UDP-csomagok|CountPerSecond|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő UDP-csomagok|Nincsenek méretek|
|IfUnderDDoSAttack|Yes|DDoS-támadás alatt vagy nem|Darabszám|Maximum|DDoS-támadás alatt vagy nem|Nincsenek méretek|
|PacketCount|Yes|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|PacketsDroppedDDoS|Yes|Bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő csomagok eldobott DDoS|Nincsenek méretek|
|PacketsForwardedDDoS|Yes|Bejövő csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő csomagok továbbított DDoS|Nincsenek méretek|
|PacketsInDDoS|Yes|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|Nincsenek méretek|
|SynCount|Yes|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|
|TCPBytesDroppedDDoS|Yes|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|Nincsenek méretek|
|TCPBytesForwardedDDoS|Yes|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|Nincsenek méretek|
|TCPBytesInDDoS|Yes|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincsenek méretek|
|TCPPacketsDroppedDDoS|Yes|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincsenek méretek|
|TCPPacketsForwardedDDoS|Yes|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|Nincsenek méretek|
|TCPPacketsInDDoS|Yes|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|Nincsenek méretek|
|UDPBytesDroppedDDoS|Yes|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|Nincsenek méretek|
|UDPBytesForwardedDDoS|Yes|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|Nincsenek méretek|
|UDPBytesInDDoS|Yes|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincsenek méretek|
|UDPPacketsDroppedDDoS|Yes|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincsenek méretek|
|UDPPacketsForwardedDDoS|Yes|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincsenek méretek|
|UDPPacketsInDDoS|Yes|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|Nincsenek méretek|
|VipAvailability|Yes|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Végponti állapot végpont szerint|Darabszám|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|
|QpsByEndpoint|Yes|Visszaadott végponti lekérdezések|Darabszám|Összesen|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincsenek méretek|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|A társnak hirdetett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által hirdetett útvonalak száma|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|A társtól megszerzett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által megszerzett útvonalak száma|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|CPU-kihasználtság (előzetes verzió)|Darabszám|Átlag|A ExpressRoute-átjáró CPU-kihasználtsága|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Útvonalak változásának gyakorisága (előzetes verzió)|Darabszám|Összesen|Útvonalak változásának gyakorisága a ExpressRoute-átjáróban|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Virtuális gépek száma a Virtual Networkban (előzetes verzió)|Darabszám|Maximum|A Virtual Networkban lévő virtuális gépek száma|Nincsenek méretek|
|ExpressRouteGatewayPacketsPerSecond|No|Csomag/másodperc (előzetes verzió)|CountPerSecond|Átlag|ExpressRoute-átjáró csomagjainak száma|roleInstance|
|P2SBandwidth|Yes|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SConnectionCount|Yes|P2S-kapcsolatok száma|Darabszám|Maximum|Az átjáró P2S-kapcsolatainak száma|Protokoll|
|TunnelAverageBandwidth|Yes|Alagút sávszélessége|BytesPerSecond|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Yes|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Yes|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Yes|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Yes|Bújtatási bejövő csomagok|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|Kapcsolatnév, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|A pingelések időpontjának kerekítése egy virtuális géphez|Ezredmásodpercben|Átlag|A célként megadott virtuális gépre eljuttatott pingelések menetének időpontja|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Sikertelen pingelések egy virtuális géphez|Százalék|Átlag|A hibás pingelések száma a cél virtuális gép összes elküldéses pingelésének százalékában|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft. Network/virtualRouters

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PeeringAvailability|Yes|BGP rendelkezésre állása|Százalék|Átlag|A BGP rendelkezésre állása a VirtualRouter és a távoli társak között|Társ|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincsenek méretek|
|TunnelAverageBandwidth|Yes|Alagút sávszélessége|BytesPerSecond|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Yes|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Yes|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Yes|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Yes|Bújtatási bejövő csomagok|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|Kapcsolatnév, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|bejövő|Yes|Bejövő üzenetek|Darabszám|Összesen|A sikeres küldési API-hívások száma. |Nincsenek méretek|
|bejövő. ALL. failedrequests|Yes|Minden bejövő sikertelen kérelem|Darabszám|Összesen|Értesítési központ bejövő sikertelen kéréseinek száma|Nincsenek méretek|
|bejövő. ALL. requests|Yes|Minden bejövő kérelem|Darabszám|Összesen|Értesítési központ összes bejövő kérelme|Nincsenek méretek|
|bejövő. ütemezett|Yes|Ütemezett leküldéses értesítések elküldve|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|bejövő. ütemezett. Mégse|Yes|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|a telepítés. All|Yes|Telepítési felügyeleti műveletek|Darabszám|Összesen|Telepítési felügyeleti műveletek|Nincsenek méretek|
|telepítés. Törlés|Yes|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|Nincsenek méretek|
|telepítés. Get|Yes|Telepítési műveletek beolvasása|Darabszám|Összesen|Telepítési műveletek beolvasása|Nincsenek méretek|
|Installation. patch|Yes|Javítások telepítési műveletei|Darabszám|Összesen|Javítások telepítési műveletei|Nincsenek méretek|
|telepítési. upsert|Yes|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|Nincsenek méretek|
|notificationhub. leküldések|Yes|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|Nincsenek méretek|
|kimenő. allpns. badorexpiredchannel|Yes|Rossz vagy lejárt csatorna-hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|Nincsenek méretek|
|kimenő. allpns. channelerror|Yes|Csatorna hibái|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|Nincsenek méretek|
|kimenő. allpns. invalidpayload|Yes|Hasznos adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|Nincsenek méretek|
|kimenő. allpns. pnserror|Yes|Külső értesítési rendszerhibák|Darabszám|Összesen|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|Nincsenek méretek|
|kimenő. allpns. sikeres|Yes|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. apns. badchannel|Yes|APNS hibás csatorna hiba|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS: 8).|Nincsenek méretek|
|kimenő. apns. expiredchannel|Yes|APNS lejárt csatorna hibája|Darabszám|Összesen|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|Nincsenek méretek|
|kimenő. apns. invalidcredentials|Yes|APNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. apns. invalidnotificationsize|Yes|A APNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|Nincsenek méretek|
|kimenő. apns. pnserror|Yes|APNS hibák|Darabszám|Összesen|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. apns. sikeres|Yes|APNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. authenticationerror|Yes|GCM-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|Nincsenek méretek|
|kimenő. GCM. badchannel|Yes|GCM hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|Nincsenek méretek|
|kimenő. GCM. expiredchannel|Yes|GCM lejárt csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|Nincsenek méretek|
|kimenő. GCM. invalidcredentials|Yes|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. GCM. invalidnotificationformat|Yes|GCM – érvénytelen értesítési formátum|Darabszám|Összesen|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincsenek méretek|
|kimenő. GCM. invalidnotificationsize|Yes|A GCM érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|Nincsenek méretek|
|kimenő. GCM. pnserror|Yes|GCM hibák|Darabszám|Összesen|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. GCM. sikeres|Yes|GCM sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. szabályozva|Yes|GCM-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|Nincsenek méretek|
|kimenő. GCM. wrongchannel|Yes|GCM rossz csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|Nincsenek méretek|
|kimenő. mpns. authenticationerror|Yes|MPNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. badchannel|Yes|MPNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. mpns. channeldisconnected|Yes|MPNS csatorna leválasztva|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|Nincsenek méretek|
|kimenő. mpns. Dropped|Yes|MPNS eldobott értesítések|Darabszám|Összesen|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|Nincsenek méretek|
|kimenő. mpns. invalidcredentials|Yes|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. invalidnotificationformat|Yes|MPNS – érvénytelen értesítési formátum|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|Nincsenek méretek|
|kimenő. mpns. pnserror|Yes|MPNS hibák|Darabszám|Összesen|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. mpns. sikeres|Yes|MPNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. mpns. szabályozva|Yes|MPNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|Nincsenek méretek|
|kimenő. wns. authenticationerror|Yes|WNS-hitelesítési hibák|Darabszám|Összesen|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincsenek méretek|
|kimenő. wns. badchannel|Yes|WNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. wns. channeldisconnected|Yes|WNS csatorna leválasztva|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. channelthrottled|Yes|WNS csatorna szabályozása|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|Nincsenek méretek|
|kimenő. wns. Dropped|Yes|WNS eldobott értesítések|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. expiredchannel|Yes|WNS lejárt csatorna hibája|Darabszám|Összesen|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|Nincsenek méretek|
|kimenő. wns. invalidcredentials|Yes|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincsenek méretek|
|kimenő. wns. invalidnotificationformat|Yes|WNS – érvénytelen értesítési formátum|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|Nincsenek méretek|
|kimenő. wns. invalidnotificationsize|Yes|A WNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az értesítési tartalom túl nagy (WNS állapot: 413).|Nincsenek méretek|
|kimenő. wns. invalidtoken|Yes|WNS-hitelesítési hibák (érvénytelen token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|Nincsenek méretek|
|kimenő. wns. pnserror|Yes|WNS hibák|Darabszám|Összesen|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|Nincsenek méretek|
|kimenő. wns. sikeres|Yes|WNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. wns. szabályozva|Yes|WNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|Nincsenek méretek|
|kimenő. wns. tokenproviderunreachable|Yes|WNS-hitelesítési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem érhető el.|Nincsenek méretek|
|kimenő. wns. wrongtoken|Yes|WNS-hitelesítési hibák (hibás token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincsenek méretek|
|regisztráció. All|Yes|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |Nincsenek méretek|
|regisztráció. Create|Yes|Regisztráció-létrehozási műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs létrehozás száma.|Nincsenek méretek|
|regisztráció. Delete|Yes|Regisztrációs törlési műveletek|Darabszám|Összesen|A regisztráció sikeres törléseinak száma.|Nincsenek méretek|
|regisztráció. Get|Yes|Regisztrálási olvasási műveletek|Darabszám|Összesen|A sikeres regisztrációs lekérdezések száma.|Nincsenek méretek|
|regisztráció. frissítés|Yes|Regisztrációs frissítési műveletek|Darabszám|Összesen|A sikeres regisztrációs frissítések száma.|Nincsenek méretek|
|ütemezett. függőben|Yes|Függőben lévő ütemezett értesítések|Darabszám|Összesen|Függőben lévő ütemezett értesítések|Nincsenek méretek|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre álló memória Average_%-ban|Yes|Rendelkezésre álló memória%-ban|Darabszám|Átlag|Rendelkezésre álló memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Yes|Rendelkezésre álló swap-terület (%)|Darabszám|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Yes|Előjegyzett memória%-ban használatban|Darabszám|Átlag|Average_%-os előjegyzett bájtok használatban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% DPC idő|Yes|% DPC idő|Darabszám|Átlag|Average_% DPC idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad inode (%)|Yes|Szabad inode%-ban|Darabszám|Átlag|Average_ szabad inode (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Yes|Szabad terület (%)|Darabszám|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Yes|Üresjáratban eltöltött időhányad (%)|Darabszám|Átlag|Average_ üresjárati idő%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Yes|Megszakítási idő%-ban|Darabszám|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% IO várakozási idő|Yes|I/o várakozási idő%-ban|Darabszám|Átlag|Average_% IO várakozási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os szép idő|Yes|% Nice idő|Darabszám|Átlag|Average_%-os szép idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|Yes|%-Os privilegizált idő|Darabszám|Átlag|Average_%-os privilegizált idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|Yes|A processzor kihasználtsága (%)|Darabszám|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ használt inode|Yes|Felhasznált inode%-ban|Darabszám|Átlag|Average_ használt inode|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Yes|Felhasznált memória (%)|Darabszám|Átlag|Felhasznált memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Yes|Felhasznált terület (%)|Darabszám|Átlag|Average_ felhasznált terület%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Yes|Felhasznált swap-terület%-ban|Darabszám|Átlag|Average_ felhasznált lapozófájl-terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Yes|Felhasználói idő%-ban|Darabszám|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban|Yes|Rendelkezésre álló memória (megabájt)|Darabszám|Átlag|Average_Available MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB memória|Yes|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Yes|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban való swap|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Yes|Átlagos írási idő (mp/olvasás)|Darabszám|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Lemez (mp/átvitel)|Yes|Átlagos műveleti idő (mp/átvitel)|Darabszám|Átlag|Average_Avg. Lemez (mp/átvitel)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Yes|Átlagos írási idő (mp/írás)|Darabszám|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Yes|Fogadott bájtok/s|Darabszám|Átlag|Fogadott Average_Bytes/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Yes|Küldési sebesség (bájt/s)|Darabszám|Átlag|Küldési Average_Bytes másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Yes|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összesen/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Yes|Lemez aktuális várólistájának hossza|Darabszám|Átlag|Average_Current a lemez várólistájának hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Yes|Lemez olvasási sebessége (bájt/s)|Darabszám|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Yes|Olvasási sebesség (lemez/mp)|Darabszám|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Yes|Lemez átvitele másodpercenként|Darabszám|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Yes|Lemez írási sebessége (bájt/s)|Darabszám|Átlag|Írási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Yes|Írási sebesség (írás/mp)|Darabszám|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Yes|Szabad terület (MB)|Darabszám|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Yes|Szabad fizikai memória|Darabszám|Átlag|Fizikai memória Average_Free|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Yes|Szabad terület a Lapozófájlokban|Darabszám|Átlag|Average_Free lemezterület a Lapozófájlokban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free virtuális memória|Yes|Szabad virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Yes|Logikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Logical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Yes|Olvasott lap/mp|Darabszám|Átlag|Olvasási Average_Page másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Page|Yes|Írási idő/mp|Darabszám|Átlag|Írás/mp Average_Page|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pages előállított/másodperc|Yes|Lap/mp|Darabszám|Átlag|Average_Pages előállított/másodperc|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|Yes|PCT rendszerjogosultságú idő|Darabszám|Átlag|Average_Pct emelt szintű idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|Yes|PCT felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Yes|Fizikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Physical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processes|Yes|Folyamatok|Darabszám|Átlag|Average_Processes|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Yes|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Yes|Lapozófájlokban tárolt méret|Darabszám|Átlag|Lapozófájlokban tárolt Average_Size|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total bájtok|Yes|Bájtok összesen|Darabszám|Átlag|Average_Total bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Total bájtok száma|Yes|Fogadott bájtok összesen|Darabszám|Átlag|Fogadott Average_Total bájtok száma|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított bájtok|Yes|Továbbított bájtok összesen|Darabszám|Átlag|Average_Total továbbított bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Ütközések Average_Total|Yes|Ütközések összesen|Darabszám|Átlag|Ütközések Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Yes|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Yes|Továbbított csomagok összesen|Darabszám|Átlag|Average_Total továbbított csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rx-hibák Average_Total|Yes|Rx-hibák összesen|Darabszám|Átlag|Rx-hibák Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total TX-hibák|Yes|TX-hibák összesen|Darabszám|Átlag|Average_Total TX-hibák|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Uptime|Yes|Üzemidő|Darabszám|Átlag|Average_Uptime|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used MB-ban felcserélt terület|Yes|Felhasznált memória (MB) – lapozófájl|Darabszám|Átlag|Average_Used MB-ban felcserélt terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Yes|Felhasznált memória (kilobájt)|Darabszám|Átlag|Average_Used memória kilobájtban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Yes|Felhasznált memória (MB)|Darabszám|Átlag|Average_Used memória MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Users|Yes|Felhasználók|Darabszám|Átlag|Average_Users|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Yes|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Esemény|Yes|Esemény|Darabszám|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|
|Szívverés|Yes|Szívverés|Darabszám|Összesen|Szívverés|Számítógép, OSType, verzió, SourceComputerId|
|Frissítés|Yes|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/társaik

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Kimenő forgalom aránya|BitsPerSecond|Átlag|Kimenő forgalom sebessége (bit/mp)|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Bejövő forgalom aránya|BitsPerSecond|Átlag|Bejövő forgalom sebessége bit/másodpercben|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability|Yes|Munkamenet rendelkezésre állása|Darabszám|Átlag|A társ-munkamenet rendelkezésre állása|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Előtag-késés|Ezredmásodpercben|Átlag|Medián előtag késése|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Memória (Gen1)|Bájt|Átlag|Memory. Tartomány 0-3 GB a1, 0-5 GB az a2, 0-10 GB, a3, 0-25 GB, a4, 0-50 GB, a5 és 0-100 GB, A6. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|memory_thrashing_metric|Yes|Memória-Kiverés (adatkészletek) (Gen1)|Százalék|Átlag|Memória átlagos kiverése. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|qpu_high_utilization_metric|Yes|QPU magas kihasználtsága (Gen1)|Darabszám|Összesen|A QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|QueryDuration|Yes|Lekérdezés időtartama (adatkészletek) (Gen1)|Ezredmásodpercben|Átlag|A DAX-lekérdezés időtartama az utolsó intervallumban. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|QueryPoolJobQueueLength|Yes|Lekérdezési készlet feladatok várólistájának hossza (adatkészletek) (Gen1)|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított ellenőrzések számát jelzi.|ResourceId|
|ScanCompleted|Yes|A vizsgálat befejeződött|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma sikeresen befejeződött.|ResourceId|
|ScanFailed|Yes|Sikertelen vizsgálat|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma nem sikerült.|ResourceId|
|ScanTimeTaken|Yes|Vizsgálat ideje elvégezve|Másodperc|Összesen|A teljes vizsgálati időt jelzi másodpercben.|ResourceId|


## <a name="microsoftpurviewaccounts"></a>Microsoft. hatáskörébe/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított ellenőrzések számát jelzi.||
|ScanCompleted|Yes|A vizsgálat befejeződött|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma sikeresen befejeződött.||
|ScanFailed|Yes|Sikertelen vizsgálat|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma nem sikerült.||
|ScanTimeTaken|Yes|Vizsgálat ideje elvégezve|Másodperc|Összesen|A teljes vizsgálati időt jelzi másodpercben.||


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|ActiveListeners|No|ActiveListeners|Darabszám|Összesen|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|BytesTransferred|Yes|BytesTransferred|Bájt|Összesen|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|ListenerConnections-Success|No|ListenerConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName, Operationresult tevékenységen|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|Bontásai|No|Bontásai|Darabszám|Összesen|A Microsoft. Relay bontásai összesen.|EntityName|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|SenderConnections-Success|No|SenderConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName, Operationresult tevékenységen|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|SenderDisconnects|No|SenderDisconnects|Darabszám|Összesen|A Microsoft. Relay SenderDisconnects összesen.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>Microsoft. Resources/előfizetések

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Késés|Yes|Bejövő HTTP-kérelmek késésének adatvédelme|Darabszám|Átlag|Bejövő HTTP-kérelmek késésének adatvédelme|Metódus, névtér, RequestRegion, ResourceType, Microsoft. SubscriptionId|
|Adatforgalom|Yes|Adatforgalom|Darabszám|Átlag|Http-forgalom|RequestRegion, StatusCode, StatusCodeClass, ResourceType, névtér, Microsoft. SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Keresési késés|Másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|Nincsenek méretek|
|SearchQueriesPerSecond|Yes|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincsenek méretek|
|ThrottledSearchQueriesPercentage|Yes|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|Nincsenek méretek|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. ServiceBus összes aktív kapcsolata.||
|ActiveMessages|No|Üzenetsor vagy témakör aktív üzeneteinek száma.|Darabszám|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma.|EntityName|
|ConnectionsClosed|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus megnyitott kapcsolatainak száma.|EntityName|
|CPUXNS|No|CPU (elavult)|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája. Ez a metrika depricated. Használja helyette a CPU-metrikát (NamespaceCpuUsage).|Replika|
|DeadletteredMessages|No|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|Darabszám|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|EntityName|
|IncomingMessages|Yes|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő üzenetei.|EntityName|
|IncomingRequests|Yes|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő kérései.|EntityName|
|Üzenetek|No|Üzenetsor/téma üzeneteinek száma.|Darabszám|Átlag|Üzenetsor/téma üzeneteinek száma.|EntityName|
|NamespaceCpuUsage|No|CPU|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája.|Replika|
|NamespaceMemoryUsage|No|Memóriahasználat|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma.|Replika|
|OutgoingMessages|Yes|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus kimenő üzenetei.|EntityName|
|ScheduledMessages|No|Az üzenetsor/témakör ütemezett üzeneteinek száma.|Darabszám|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma.|EntityName|
|Kiszolgálóhibái|No|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. ServiceBus esetében.|EntityName, Operationresult tevékenységen|
|Méret|No|Méret|Bájt|Átlag|Várólista/témakör mérete bájtban.|EntityName|
|SuccessfulRequests|No|Sikeres kérelmek|Darabszám|Összesen|Névtér összes sikeres kérelme|EntityName, Operationresult tevékenységen|
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. ServiceBus által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|UserErrors|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. ServiceBus felhasználói hibái.|EntityName, Operationresult tevékenységen|
|WSXNS|No|Memóriahasználat (elavult)|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma. Ez a metrika elavult. Használja helyette a memóriahasználat (NamespaceMemoryUsage) metrikáját.|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/alkalmazások

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Darabszám|Átlag|Tényleges CPU-használat a Millon magokban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedCpu|No|AllocatedCpu|Darabszám|Átlag|A tárolóhoz a Millet magokban lefoglalt CPU|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedMemory|No|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|Tároló állapota:|No|Tároló állapota:|Darabszám|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName, állapot|
|CpuUtilization|No|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, status, szolgáltatásnév, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, status, szolgáltatásnév, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, állapot, szolgáltatásnév|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|InboundTraffic|Yes|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincsenek méretek|
|MessageCount|Yes|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|Nincsenek méretek|
|OutboundTraffic|Yes|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincsenek méretek|
|SystemErrors|Yes|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|Nincsenek méretek|
|UserErrors|Yes|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|Nincsenek méretek|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|Nincsenek méretek|
|io_bytes_read|Yes|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|Nincsenek méretek|
|io_bytes_written|Yes|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|Nincsenek méretek|
|io_requests|Yes|IO-kérelmek száma|Darabszám|Átlag|IO-kérelmek száma|Nincsenek méretek|
|reserved_storage_mb|Yes|Tárterület fenntartva|Darabszám|Átlag|Tárterület fenntartva|Nincsenek méretek|
|storage_space_used_mb|Yes|Felhasznált tárterület|Darabszám|Átlag|Felhasznált tárterület|Nincsenek méretek|
|virtual_core_count|Yes|Virtuális mag száma|Darabszám|Átlag|Virtuális mag száma|Nincsenek méretek|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_queries|Yes|Aktív lekérdezések|Darabszám|Összesen|Aktív lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|allocated_data_storage|Yes|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|app_cpu_billed|Yes|Az alkalmazás CPU-számlázása|Darabszám|Összesen|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_cpu_percent|Yes|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_memory_percent|Yes|Az alkalmazás memóriájának százaléka|Százalék|Átlag|Az alkalmazás memóriájának százalékos aránya. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|base_blob_size_bytes|Yes|Az alap blob tárterületének mérete|Bájt|Maximum|Az alap blob tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|blocked_by_firewall|Yes|A tűzfal blokkolja|Darabszám|Összesen|A tűzfal blokkolja|Nincsenek méretek|
|cache_hit_percent|Yes|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|cache_used_percent|Yes|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|connection_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connection_successful|Yes|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_limit|Yes|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|cpu_percent|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Yes|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|holtpont|Yes|Holtpontok|Darabszám|Összesen|Holtpontok. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|diff_backup_size_bytes|Yes|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|dtu_consumption_percent|Yes|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_limit|Yes|DTU korlátja|Darabszám|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_used|Yes|Használt DTU|Darabszám|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dwu_consumption_percent|Yes|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_limit|Yes|DWU korlátja|Darabszám|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_used|Yes|Használt DWU|Darabszám|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|full_backup_size_bytes|Yes|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|local_tempdb_usage_percent|Yes|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|log_backup_size_bytes|Yes|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. A virtuális mag-alapú és a nagy kapacitású adatbázisokra vonatkozik.|Nincsenek méretek|
|log_write_percent|Yes|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|memory_usage_percent|Yes|Memória százaléka|Százalék|Maximum|Memória százalékos aránya Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|physical_data_read_percent|Yes|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|queued_queries|Yes|Várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|sessions_percent|Yes|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|snapshot_backup_size_bytes|Yes|Pillanatkép biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő pillanatkép biztonsági mentési tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|sqlserver_process_core_percent|Yes|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|sqlserver_process_memory_percent|Yes|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage|Yes|Felhasznált adatterület|Bájt|Maximum|Felhasznált adatterület. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage_percent|Yes|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatterület felhasznált százaléka százalékban. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|tempdb_data_size|Yes|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|A tempdb adatfájljaiban használt lemezterület kilobájtban. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_size|Yes|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|A tempdb tranzakciós naplófájljában használt lemezterület kilobájtban megadva. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_used_percent|Yes|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb tranzakciós naplófájlban használt százalékos érték. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|wlg_active_queries|Yes|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|Aktív lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Azok a lekérdezések, amelyek túllépték a munkaterhelés-csoport időtúllépését. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások a teljes rendszerterhelési csoporthoz viszonyított százalékos aránya. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Munkaterhelés-csoport lefoglalása a Cap-erőforrás százaléka alapján|Százalék|Maximum|Az erőforrások megadott százalékos aránya a munkaterhelési csoportok meghatározott erőforrásaihoz viszonyítva. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport számára engedélyezett erőforrások százalékos arányának korlátozása, figyelembe véve a más munkaterhelés-csoportok számára kiosztott tényleges minimális erőforrás-százalékos arányt. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Effektív minimális erőforrás százaléka|Százalék|Maximum|A munkaterhelés-csoport számára fenntartott és elkülönített erőforrások minimális százalékos aránya, a szolgáltatási szint minimumának figyelembevételével. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|xtp_storage_percent|Yes|In-Memory OLTP-tárolási százalék|Százalék|Átlag|In-Memory OLTP a tárolási százalékot. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/kiszolgálók/elasticPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|Nincsenek méretek|
|allocated_data_storage_percent|Yes|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|Nincsenek méretek|
|cpu_limit|Yes|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|cpu_percent|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Yes|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|database_allocated_data_storage|No|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId|
|database_cpu_limit|No|CPU-korlát|Darabszám|Átlag|CPU-korlát|DatabaseResourceId|
|database_cpu_percent|No|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId|
|database_cpu_used|No|Felhasznált CPU|Darabszám|Átlag|Felhasznált CPU|DatabaseResourceId|
|database_dtu_consumption_percent|No|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId|
|database_eDTU_used|No|használt eDTU|Darabszám|Átlag|használt eDTU|DatabaseResourceId|
|database_log_write_percent|No|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|DatabaseResourceId|
|database_physical_data_read_percent|No|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId|
|database_sessions_percent|No|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId|
|database_storage_used|No|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId|
|database_workers_percent|No|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|DatabaseResourceId|
|dtu_consumption_percent|Yes|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_limit|Yes|eDTU korlátja|Darabszám|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_used|Yes|használt eDTU|Darabszám|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|log_write_percent|Yes|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|Nincsenek méretek|
|physical_data_read_percent|Yes|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|sessions_percent|Yes|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincsenek méretek|
|sqlserver_process_core_percent|Yes|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|sqlserver_process_memory_percent|Yes|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|storage_limit|Yes|Maximális adatméret|Bájt|Átlag|Maximális adatméret|Nincsenek méretek|
|storage_percent|Yes|Felhasznált adatterület százalékos aránya|Százalék|Átlag|Felhasznált adatterület százalékos aránya|Nincsenek méretek|
|storage_used|Yes|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|Nincsenek méretek|
|tempdb_data_size|Yes|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|A tempdb adatfájljaiban használt lemezterület kilobájtban.|Nincsenek méretek|
|tempdb_log_size|Yes|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|A tempdb tranzakciós naplófájljában használt lemezterület kilobájtban megadva.|Nincsenek méretek|
|tempdb_log_used_percent|Yes|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb tranzakciós naplófájlban használt százalékos arány|Nincsenek méretek|
|workers_percent|Yes|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|Nincsenek méretek|
|xtp_storage_percent|Yes|In-Memory OLTP-tárolási százalék|Százalék|Átlag|In-Memory OLTP-tárolási százalék|Nincsenek méretek|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|Yes|Felhasznált kapacitás|Bájt|Átlag|A Storage-fiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. A Premium Storage-fiókok és a blob Storage-fiókok esetében ugyanaz, mint a BlobCapacity vagy a FileCapacity.|Nincsenek méretek|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|No|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|No|Blobok száma|Darabszám|Átlag|A Storage-fiókban tárolt blob-objektumok száma.|BlobType, szintű|
|BlobProvisionedSize|No|BLOB kiépített mérete|Bájt|Átlag|A Storage-fiók Blob serviceban kiépített tárterület mérete bájtban megadva.|BlobType, szintű|
|ContainerCount|Yes|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiókban lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|No|Index kapacitása|Bájt|Átlag|A Azure Data Lake Storage Gen2 hierarchikus index által használt tárterület mennyisége.|Nincsenek méretek|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|No|Fájl kapacitása|Bájt|Átlag|A Storage-fiók által használt file Storage mennyisége.|Fájlmegosztás|
|FileCount|No|Fájlok száma|Darabszám|Átlag|A Storage-fiókban található fájlok száma.|Fájlmegosztás|
|FileShareCapacityQuota|No|Fájlmegosztás kapacitásának kvótája|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareCount|No|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiókban lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareProvisionedIOPS|No|Fájlmegosztási kiépített IOPS|Bájt|Átlag|A Premium file Storage-fiókban a prémium fájlmegosztás kiépített IOPS alapszáma. Ezt a számot a megosztási kapacitás kiosztott mérete (kvóta) alapján számítjuk ki.|Fájlmegosztás|
|FileShareSnapshotCount|No|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|No|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Yes|Várólista kapacitása|Bájt|Átlag|A Storage-fiók által használt üzenetsor-tároló mennyisége.|Nincsenek méretek|
|QueueCount|Yes|Várólista száma|Darabszám|Átlag|A Storage-fiókban lévő várólisták száma.|Nincsenek méretek|
|QueueMessageCount|Yes|Üzenetsor-üzenetek száma|Darabszám|Átlag|A nem lejárt üzenetsor-üzenetek száma a Storage-fiókban.|Nincsenek méretek|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Yes|Tábla kapacitása|Bájt|Átlag|A Storage-fiók által használt Table Storage mennyisége.|Nincsenek méretek|
|TableCount|Yes|Táblák száma|Darabszám|Átlag|A Storage-fiókban lévő táblák száma.|Nincsenek méretek|
|TableEntityCount|Yes|Tábla entitások száma|Darabszám|Átlag|A Storage-fiókban lévő tábla entitások száma.|Nincsenek méretek|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/gyorsítótárak

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|Ügyfél teljes IOPS|Darabszám|Átlag|A gyorsítótár által feldolgozott ügyféloldali fájlok műveleteinek aránya.|Nincsenek méretek|
|ClientLatency|Yes|Ügyfél átlagos késése|Ezredmásodpercben|Átlag|Az ügyféloldali fájl műveleteinek átlagos késése a gyorsítótárban.|Nincsenek méretek|
|ClientLockIOPS|Yes|Ügyfél-zárolási IOPS|CountPerSecond|Átlag|Az ügyfél fájljának zárolási műveletei másodpercenként.|Nincsenek méretek|
|ClientMetadataReadIOPS|Yes|Ügyfél metaadatainak olvasása IOPS|CountPerSecond|Átlag|Az adatolvasások kivételével a gyorsítótárba elküldett ügyféloldali műveletek sebessége, amely nem módosítja az állandó állapotot.|Nincsenek méretek|
|ClientMetadataWriteIOPS|Yes|Ügyfél metaadatainak írási IOPS|CountPerSecond|Átlag|A gyorsítótárba küldendő, az adatírásokat nem tartalmazó, az állandó állapotot módosító ügyfél-fájl műveleteinek aránya.|Nincsenek méretek|
|ClientReadIOPS|Yes|Ügyfél olvasási IOPS|CountPerSecond|Átlag|Az ügyfél olvasási műveletei másodpercenként.|Nincsenek méretek|
|ClientReadThroughput|Yes|Gyorsítótár átlagos olvasási átviteli sebessége|BytesPerSecond|Átlag|Az ügyfél olvasási adatátviteli sebessége.|Nincsenek méretek|
|ClientWriteIOPS|Yes|Ügyfél írási IOPS|CountPerSecond|Átlag|Az ügyfél írási műveletei másodpercenként.|Nincsenek méretek|
|ClientWriteThroughput|Yes|Gyorsítótár átlagos írási sebessége|BytesPerSecond|Átlag|Az ügyfél írási adatátviteli sebessége.|Nincsenek méretek|
|StorageTargetAsyncWriteThroughput|Yes|StorageTarget aszinkron írási átviteli sebesség|BytesPerSecond|Átlag|A gyorsítótárnak az adatokat egy adott StorageTarget való aszinkron módon írási sebessége. Ezek olyan alkalmi írások, amelyek nem okozzák az ügyfelek blokkolását.|StorageTarget|
|StorageTargetFillThroughput|Yes|StorageTarget-kitöltési sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár beolvassa az adatokat a StorageTarget a gyorsítótár-kihagyás kezelésére.|StorageTarget|
|StorageTargetHealth|Yes|Tárolási cél állapota|Darabszám|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a tárolási célok között.|Nincsenek méretek|
|StorageTargetIOPS|Yes|Összes StorageTarget-IOPS|Darabszám|Átlag|Az összes fájl műveletének sebessége, amelyet a gyorsítótár küld egy adott StorageTarget.|StorageTarget|
|StorageTargetLatency|Yes|StorageTarget késés|Ezredmásodpercben|Átlag|A gyorsítótár által a partricular-StorageTarget küldött összes fájl műveletének átlagos ciklikus késése.|StorageTarget|
|StorageTargetMetadataReadIOPS|Yes|StorageTarget-metaadatok IOPS olvasása|CountPerSecond|Átlag|A nem állandó állapotot módosító, valamint az olvasási művelet kihagyása után a gyorsítótár egy adott StorageTarget küldi az adatokat.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|StorageTarget-metaadatok írási IOPS|CountPerSecond|Átlag|Az állandó állapotot módosító és az írási műveletet nem tartalmazó, a gyorsítótár által egy adott StorageTarget küldött fájl-műveletek aránya.|StorageTarget|
|StorageTargetReadAheadThroughput|Yes|StorageTarget – olvasási sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár a StorageTarget beolvassa az adatokat.|StorageTarget|
|StorageTargetReadIOPS|Yes|StorageTarget olvasási IOPS|CountPerSecond|Átlag|A fájl olvasási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|StorageTargetSyncWriteThroughput|Yes|StorageTarget szinkron írási sebessége|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár szinkron módon ír adatokat egy adott StorageTarget. Ezek az írások, amelyek az ügyfelek blokkolását okozzák.|StorageTarget|
|StorageTargetTotalReadThroughput|Yes|StorageTarget összesen olvasási átviteli sebesség|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat olvas egy adott StorageTarget.|StorageTarget|
|StorageTargetTotalWriteThroughput|Yes|StorageTarget összes írási sebessége|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat ír egy adott StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Yes|StorageTarget írási IOPS|Darabszám|Átlag|A fájl írási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|Üzemidő|Yes|Üzemidő|Darabszám|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a figyelési rendszer között.|Nincsenek méretek|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Szinkronizálási munkamenet eredménye|Darabszám|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Felhőbeli rétegek felidézésének mérete alkalmazás szerint|Bájt|Összesen|Az alkalmazás által visszahívott adatmennyiség|SyncGroupName, kiszolgálónév, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Felhőbeli rétegek felidézésének mérete|Bájt|Összesen|Visszahívott adatmennyiség|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Yes|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Felhőbeli rétegek felidézésének átviteli sebessége|BytesPerSecond|Átlag|Adatvisszahívási sebesség mérete|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Yes|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Yes|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Yes|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Sikertelen függvények kérései|Darabszám|Összesen|Sikertelen függvények kérései|LogicalName, PartitionId|
|AMLCalloutInputEvents|Yes|Függvények eseményei|Darabszám|Összesen|Függvények eseményei|LogicalName, PartitionId|
|AMLCalloutRequests|Yes|Függvények kérései|Darabszám|Összesen|Függvények kérései|LogicalName, PartitionId|
|ConversionErrors|Yes|Adatátalakítási hibák|Darabszám|Összesen|Adatátalakítási hibák|LogicalName, PartitionId|
|DeserializationError|Yes|Bemeneti deszerializálási hibák|Darabszám|Összesen|Bemeneti deszerializálási hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Yes|Megrendelési események|Darabszám|Összesen|Megrendelési események|LogicalName, PartitionId|
|EarlyInputEvents|Yes|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|LogicalName, PartitionId|
|Hibák|Yes|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|LogicalName, PartitionId|
|InputEventBytes|Yes|Bemeneti esemény bájtjai|Bájt|Összesen|Bemeneti esemény bájtjai|LogicalName, PartitionId|
|InputEvents|Yes|Bemeneti események|Darabszám|Összesen|Bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Yes|Várakozó bemeneti eseményei|Darabszám|Maximum|Várakozó bemeneti eseményei|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Yes|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|LogicalName, PartitionId|
|LateInputEvents|Yes|Késői bemeneti események|Darabszám|Összesen|Késői bemeneti események|LogicalName, PartitionId|
|OutputEvents|Yes|Kimeneti események|Darabszám|Összesen|Kimeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Vízjel késleltetése|Másodperc|Maximum|Vízjel késleltetése|LogicalName, PartitionId|
|ResourceUtilization|Yes|SU% kihasználtsága|Százalék|Maximum|SU% kihasználtsága|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Feldolgozott adatmennyiség (bájt)|Bájt|Összesen|Lekérdezések által feldolgozott adatmennyiség|Nincsenek méretek|
|BuiltinSqlPoolLoginAttempts|No|Bejelentkezési kísérletek|Darabszám|Összesen|A sikerül vagy sikertelen bejelentkezési kísérletek száma|Eredmény|
|BuiltinSqlPoolRequestsEnded|No|Befejezett kérelmek|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított kérelmek száma|Eredmény|
|IntegrationActivityRunsEnded|No|A tevékenység futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított integrációs tevékenységek száma|Eredmény, FailureType, tevékenység, ActivityType, folyamat|
|IntegrationPipelineRunsEnded|No|A folyamat futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított integrációs folyamat-futtatások száma|Eredmény, FailureType, folyamat|
|IntegrationTriggerRunsEnded|No|A trigger futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított integrációs eseményindítók száma|Eredmény, FailureType, trigger|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|lefoglalt virtuális mag|Darabszám|Maximum|Apache Spark készlethez lefoglalt virtuális mag|SubmitterId|
|BigDataPoolAllocatedMemory|No|Lefoglalt memória (GB)|Darabszám|Maximum|Lefoglalt memória a Apach Spark-készlethez (GB)|SubmitterId|
|BigDataPoolApplicationsActive|No|Aktív Apache Spark alkalmazások|Darabszám|Maximum|Aktív Apache Spark készlet összes alkalmazása|JobState|
|BigDataPoolApplicationsEnded|No|Lejárt Apache Spark alkalmazások|Darabszám|Összesen|A Apache Spark készlet-alkalmazások száma befejeződött|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Aktív lekérdezések|Darabszám|Összesen|Az aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|IsUserDefined|
|AdaptiveCacheHitPercent|No|Adaptív gyorsítótár-találatok százalékos aránya|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár találati százalékos metrikájának használatával határozhatja meg, hogy szeretné-e bővíteni a további kapacitást, vagy futtassa újra a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|AdaptiveCacheUsedPercent|No|Adaptív gyorsítótár használt százaléka|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár használt százalékos metrikájának használatával határozhatja meg, hogy a további kapacitást szeretné-e méretezni, vagy újra kell futtatnia a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|Kapcsolatok|Yes|Kapcsolatok|Darabszám|Összesen|Az SQL-készletbe való teljes bejelentkezések száma|Eredmény|
|ConnectionsBlockedByFirewall|No|Tűzfal által letiltott kapcsolatok|Darabszám|Összesen|A tűzfalszabályok által letiltott kapcsolatok száma. Nyissa meg újra az SQL-készlethez tartozó hozzáférés-vezérlési szabályzatokat, és figyelje meg, hogy a kapcsolatok száma magas-e|Nincsenek méretek|
|CPUPercent|No|Felhasznált CPU százalékos aránya|Százalék|Maximum|CPU-kihasználtság az SQL-készlet összes csomópontja között|Nincsenek méretek|
|DWULimit|No|DWU korlátja|Darabszám|Maximum|Az SQL-készlet szolgáltatási szintjének célkitűzése|Nincsenek méretek|
|DWUUsed|No|Használt DWU|Darabszám|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. DWU-korláttal mérve * DWU százalék|Nincsenek méretek|
|DWUUsedPercent|No|DWU használt százalék|Százalék|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. A CPU-százalék és az adatio-százalék közötti maximális érték alapján mérhető|Nincsenek méretek|
|LocalTempDBUsedPercent|No|Helyi tempdb használt százalék|Százalék|Maximum|Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva|Nincsenek méretek|
|MemoryUsedPercent|No|Felhasznált memória százalékos aránya|Százalék|Maximum|Memória kihasználtsága az SQL-készlet összes csomópontján|Nincsenek méretek|
|QueuedQueries|No|Várólistán lévő lekérdezések|Darabszám|Összesen|Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát|IsUserDefined|
|WLGActiveQueries|No|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|A munkaterhelés csoporton belüli aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit. A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozás miatt)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|No|Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján|Százalék|Maximum|Megjeleníti az erőforrások százalékos kiosztását a munkaterhelési csoport tényleges maximális erőforrásához viszonyítva. Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások százalékos kiosztása a teljes rendszerrel viszonyítva|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|No|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport tényleges Cap-erőforrásának százaléka. Ha más munkaterhelés-csoportok min_percentage_resource > 0, a effective_cap_percentage_resource arányosan csökken|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|No|Effektív minimális erőforrás százaléka|Százalék|Maximum|A tényleges minimális erőforrás-százalékos beállítás a szolgáltatási szint és a munkaterhelés-csoport beállításainak figyelembevételével engedélyezett. A tényleges min_percentage_resource magasabb szinten állítható be alacsonyabb szolgáltatási szinteken|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az összes eseményforrás által olvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Yes|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Yes|Bejövő fogadott üzenetek|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Yes|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Yes|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Yes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Yes|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Yes|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Yes|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az eseményforrás által beolvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Yes|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Yes|Bejövő fogadott üzenetek|Darabszám|Összesen|Az eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Yes|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Yes|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Yes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Yes|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Yes|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Yes|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskReadBytesPerSecond|Yes|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|DiskReadLatency|Yes|Lemez olvasási késése|Ezredmásodpercben|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|Nincsenek méretek|
|DiskReadOperations|Yes|Lemezes olvasási műveletek|Darabszám|Összesen|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskWriteBytesPerSecond|Yes|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|Nincsenek méretek|
|DiskWriteLatency|Yes|Lemez írási késése|Ezredmásodpercben|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|Nincsenek méretek|
|DiskWriteOperations|Yes|Lemez írási műveletei|Darabszám|Összesen|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|MemoryActive|Yes|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|Nincsenek méretek|
|MemoryGranted|Yes|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|Nincsenek méretek|
|MemoryUsed|Yes|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|Nincsenek méretek|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A továbbított forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkInBytesPerSecond|Yes|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkOutBytesPerSecond|Yes|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|Nincsenek méretek|
|PercentageCpuReady|Yes|CPU-készültség százalékos aránya|Ezredmásodpercben|Összesen|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Aktív kérések (elavult)|Darabszám|Összesen|Aktív kérések|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő (elavult)|Másodperc|Átlag|A kérések kiszolgálásához szükséges átlagos idő másodpercben.|Példány|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|Az összes kezelőfelületen használt átlagos bejövő sávszélesség a MiB-ben.|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Az összes kezelőfelületen használt átlagos bejövő sávszélesség a MiB-ben.|Példány|
|CpuPercentage|Yes|Processzorhasználat (%)|Százalék|Átlag|Az előtér összes példányán használt átlagos processzor.|Példány|
|DiskQueueLength|Yes|Lemezvezérlő-várólista hossza|Darabszám|Átlag|A tárolás során várólistára vett olvasási és írási kérelmek átlagos száma. A lemez magas várólistájának hossza egy olyan alkalmazásra utal, amely a lemezes I/O-műveletek miatt lelassulhat.|Példány|
|Http101|Yes|Http 101|Darabszám|Összesen|Az 101-as HTTP-állapotkódot eredményező kérelmek száma.|Példány|
|Http2xx|Yes|Http-2xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 200, de < 300.|Példány|
|Http3xx|Yes|Http-3xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 300, de < 400.|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|A HTTP 401 állapotkódot eredményező kérések száma.|Példány|
|Http403|Yes|Http 403|Darabszám|Összesen|A HTTP 403 állapotkódot eredményező kérések száma.|Példány|
|Http404|Yes|Http 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérések száma.|Példány|
|Http406|Yes|Http 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérések száma.|Példány|
|Http4xx|Yes|Http-4xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 400, de < 500.|Példány|
|Http5xx|Yes|Http-kiszolgálói hibák|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 500, de < 600.|Példány|
|HttpQueueLength|Yes|Http-várólista hossza|Darabszám|Átlag|Azoknak a HTTP-kéréseknek az átlagos száma, amelyeket a várólistán kellett ülnie a teljesítése előtt. A magas vagy növekvő HTTP-várólista hossza a nagy terhelés alatt álló csomag tünete.|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|A kérések kiszolgálásához szükséges idő másodpercben.|Példány|
|LargeAppServicePlanInstances|Yes|Nagy App Service-csomag feldolgozói|Darabszám|Átlag|Nagy App Service-csomag feldolgozói|Nincsenek méretek|
|MediumAppServicePlanInstances|Yes|Közepes App Service-csomag feldolgozói|Darabszám|Átlag|Közepes App Service-csomag feldolgozói|Nincsenek méretek|
|MemoryPercentage|Yes|Memória százaléka|Százalék|Átlag|Az előtér összes példányán felhasznált átlagos memória.|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|A kérelmek teljes száma a létrejövő HTTP-állapotkódtől függetlenül.|Példány|
|SmallAppServicePlanInstances|Yes|Kisméretű App Service-csomag feldolgozói|Darabszám|Átlag|Kisméretű App Service-csomag feldolgozói|Nincsenek méretek|
|TotalFrontEnds|Yes|Összes előtér|Darabszám|Átlag|Összes előtér|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|Processzorhasználat (%)|Százalék|Átlag|A munkavégző készlet összes példányán használt átlagos CPU.|Példány|
|MemoryPercentage|Yes|Memória százaléka|Százalék|Átlag|A munkavégző készlet összes példányán használt átlagos memória.|Példány|
|WorkersAvailable|Yes|Rendelkezésre álló munkavégzők|Darabszám|Átlag|Rendelkezésre álló munkavégzők|Nincsenek méretek|
|WorkersTotal|Yes|Alkalmazottak összesen|Darabszám|Átlag|Alkalmazottak összesen|Nincsenek méretek|
|WorkersUsed|Yes|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Nincsenek méretek|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/kiszolgálófarmok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A csomag összes példányán használt átlagos bejövő sávszélesség.|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|A csomag összes példányán használt átlagos kimenő sávszélesség.|Példány|
|CpuPercentage|Yes|Processzorhasználat (%)|Százalék|Átlag|A csomag összes példányán használt átlagos CPU.|Példány|
|DiskQueueLength|Yes|Lemezvezérlő-várólista hossza|Darabszám|Átlag|A tárolás során várólistára vett olvasási és írási kérelmek átlagos száma. A lemez magas várólistájának hossza egy olyan alkalmazásra utal, amely a lemezes I/O-műveletek miatt lelassulhat.|Példány|
|HttpQueueLength|Yes|Http-várólista hossza|Darabszám|Átlag|Azoknak a HTTP-kéréseknek az átlagos száma, amelyeket a várólistán kellett ülnie a teljesítése előtt. A magas vagy növekvő HTTP-várólista hossza a nagy terhelés alatt álló csomag tünete.|Példány|
|MemoryPercentage|Yes|Memória százaléka|Százalék|Átlag|A csomag összes példányán használt átlagos memória.|Példány|
|SocketInboundAll|Yes|SocketInboundAll|Darabszám|Átlag|SocketInboundAll|Példány|
|SocketLoopback|Yes|SocketLoopback|Darabszám|Átlag|SocketLoopback|Példány|
|SocketOutboundAll|Yes|SocketOutboundAll|Darabszám|Átlag|SocketOutboundAll|Példány|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Darabszám|Átlag|SocketOutboundEstablished|Példány|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Darabszám|Átlag|SocketOutboundTimeWait|Példány|
|TcpCloseWait|Yes|TCP-bezárási várakozás|Darabszám|Átlag|TCP-bezárási várakozás|Példány|
|TcpClosing|Yes|TCP-zárás|Darabszám|Átlag|TCP-zárás|Példány|
|TcpEstablished|Yes|A TCP létrejött|Darabszám|Átlag|A TCP létrejött|Példány|
|TcpFinWait1|Yes|TCP fin WAIT 1|Darabszám|Átlag|TCP fin WAIT 1|Példány|
|TcpFinWait2|Yes|TCP fin WAIT 2|Darabszám|Átlag|TCP fin WAIT 2|Példány|
|TcpLastAck|Yes|TCP Last ACK|Darabszám|Átlag|TCP Last ACK|Példány|
|TcpSynReceived|Yes|TCP SYN kapott|Darabszám|Átlag|TCP SYN kapott|Példány|
|TcpSynSent|Yes|Eljuttatott TCP SYN|Darabszám|Átlag|Eljuttatott TCP SYN|Példány|
|TcpTimeWait|Yes|TCP-idő várakozása|Darabszám|Átlag|TCP-idő várakozása|Példány|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Kapcsolatok|Darabszám|Átlag|A homokozóban meglévő kötött szoftvercsatornák (w3wp.exe és annak alárendelt folyamatai) száma. A kötött szoftvercsatorna a kötési ()/Connect () API-k meghívásával jön létre, és addig marad, amíg az említett szoftvercsatorna le nem zárul a CloseHandle függvény hívásakor ()/closesocket () használatával.|Példány|
|AverageMemoryWorkingSet|Yes|Memória átlagos munkakészlete|Bájt|Átlag|Az alkalmazás által használt memória átlagos mérete (MB) (MiB).|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő (elavult)|Másodperc|Átlag|Az alkalmazásnak a kérelmek kiszolgálásához szükséges átlagos ideje másodpercben.|Példány|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|Az alkalmazás által felhasznált bejövő sávszélesség mennyisége a MiB-ben.|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Az alkalmazás által felhasznált kimenő sávszélesség mennyisége a MiB-ben.|Példány|
|CpuTime|Yes|CPU-idő|Másodperc|Összesen|Az alkalmazás által felhasznált CPU mennyisége másodpercben. További információ erről a metrikáról. Lásd: https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (CPU-idő vs CPU-százalék). Nem alkalmazható Azure Functionsra.|Példány|
|CurrentAssemblies|Yes|Aktuális szerelvények|Darabszám|Átlag|Az alkalmazás összes alkalmazástartományok betöltött szerelvények aktuális száma.|Példány|
|FileSystemUsage|Yes|Fájlrendszer használata|Bájt|Átlag|Az alkalmazás által felhasznált fájlrendszerbeli kvóta százalékaránya.|Nincsenek méretek|
|FunctionExecutionCount|Yes|Függvény végrehajtásának száma|Darabszám|Összesen|Függvények végrehajtásának száma. Csak Azure Functions esetén jelen van.|Példány|
|FunctionExecutionUnits|Yes|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei Csak Azure Functions esetén jelen van.|Példány|
|Gen0Collections|Yes|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|Az alkalmazási folyamat kezdete óta a 0. generációs objektumok számát gyűjti a rendszer. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen1Collections|Yes|1. generációs Garbage gyűjtemények|Darabszám|Összesen|Azon alkalmak száma, amikor az 1. generációs objektumok az alkalmazási folyamat kezdete óta beszedett szemetet gyűjtenek. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen2Collections|Yes|2. generációs Garbage gyűjtemények|Darabszám|Összesen|A 2. generációs objektumok száma az alkalmazási folyamat kezdete óta beszedett szemetet.|Példány|
|Kezeli|Yes|Leírók száma|Darabszám|Átlag|Az alkalmazási folyamat által jelenleg megnyitott kezelők teljes száma.|Példány|
|HealthCheckStatus|Yes|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Yes|Http 101|Darabszám|Összesen|Az 101-as HTTP-állapotkódot eredményező kérelmek száma.|Példány|
|Http2xx|Yes|Http-2xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 200, de < 300.|Példány|
|Http3xx|Yes|Http-3xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 300, de < 400.|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|A HTTP 401 állapotkódot eredményező kérések száma.|Példány|
|Http403|Yes|Http 403|Darabszám|Összesen|A HTTP 403 állapotkódot eredményező kérések száma.|Példány|
|Http404|Yes|Http 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérések száma.|Példány|
|Http406|Yes|Http 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérések száma.|Példány|
|Http4xx|Yes|Http-4xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 400, de < 500.|Példány|
|Http5xx|Yes|Http-kiszolgálói hibák|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 500, de < 600.|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|Az alkalmazás által a kérelmek kiszolgálásához szükséges idő (másodpercben).|Példány|
|IoOtherBytesPerSecond|Yes|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az adatokkal nem rendelkező I/O-műveletekhez (például vezérlési műveletekhez) tartozó bájtok kibocsátásának aránya.|Példány|
|IoOtherOperationsPerSecond|Yes|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olyan I/O-műveletek kiadására szolgál, amelyek nem rendelkeznek olvasási vagy írási műveletekkel.|Példány|
|IoReadBytesPerSecond|Yes|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az I/O-műveletek bájtjainak olvasási sebessége.|Példány|
|IoReadOperationsPerSecond|Yes|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olvasási I/O-műveletekre vonatkozó kiadási sebessége.|Példány|
|IoWriteBytesPerSecond|Yes|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|Az a sebesség, amellyel az alkalmazás a bájtok írását az I/O-műveletekhez.|Példány|
|IoWriteOperationsPerSecond|Yes|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata írási I/O-műveletek kiadásának gyakorisága.|Példány|
|MemoryWorkingSet|Yes|Memória munkakészlete|Bájt|Átlag|Az alkalmazás által a MiB-ben használt memória aktuális mennyisége.|Példány|
|PrivateBytes|Yes|Saját bájtok|Bájt|Átlag|A saját bájtok az alkalmazás által lefoglalt memória jelenlegi mérete (bájtban), amelyet más folyamatokkal nem lehet megosztani.|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|A kérelmek teljes száma a létrejövő HTTP-állapotkódtől függetlenül.|Példány|
|RequestsInApplicationQueue|Yes|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájában lévő kérelmek száma.|Példány|
|Szálak|Yes|Szálak száma|Darabszám|Átlag|Az alkalmazás folyamatában jelenleg aktív szálak száma.|Példány|
|TotalAppDomains|Yes|Alkalmazás összes tartománya|Darabszám|Átlag|Az alkalmazásban betöltött alkalmazástartományok aktuális száma.|Példány|
|TotalAppDomainsUnloaded|Yes|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Az alkalmazás indítása óta eltávolított alkalmazástartományok teljes száma.|Példány|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Kapcsolatok|Darabszám|Átlag|A homokozóban meglévő kötött szoftvercsatornák (w3wp.exe és annak alárendelt folyamatai) száma. A kötött szoftvercsatorna a kötési ()/Connect () API-k meghívásával jön létre, és addig marad, amíg az említett szoftvercsatorna le nem zárul a CloseHandle függvény hívásakor ()/closesocket () használatával.|Példány|
|AverageMemoryWorkingSet|Yes|Memória átlagos munkakészlete|Bájt|Átlag|Az alkalmazás által használt memória átlagos mérete (MB) (MiB).|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő (elavult)|Másodperc|Átlag|Az alkalmazásnak a kérelmek kiszolgálásához szükséges átlagos ideje másodpercben.|Példány|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|Az alkalmazás által felhasznált bejövő sávszélesség mennyisége a MiB-ben.|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Az alkalmazás által felhasznált kimenő sávszélesség mennyisége a MiB-ben.|Példány|
|CpuTime|Yes|CPU-idő|Másodperc|Összesen|Az alkalmazás által felhasznált CPU mennyisége másodpercben. További információ erről a metrikáról. Lásd: https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (CPU-idő vs CPU-százalék).|Példány|
|CurrentAssemblies|Yes|Aktuális szerelvények|Darabszám|Átlag|Az alkalmazás összes alkalmazástartományok betöltött szerelvények aktuális száma.|Példány|
|FileSystemUsage|Yes|Fájlrendszer használata|Bájt|Átlag|Az alkalmazás által felhasznált fájlrendszerbeli kvóta százalékaránya.|Nincsenek méretek|
|FunctionExecutionCount|Yes|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|FunctionExecutionUnits|Yes|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei|Példány|
|Gen0Collections|Yes|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|Az alkalmazási folyamat kezdete óta a 0. generációs objektumok számát gyűjti a rendszer. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen1Collections|Yes|1. generációs Garbage gyűjtemények|Darabszám|Összesen|Azon alkalmak száma, amikor az 1. generációs objektumok az alkalmazási folyamat kezdete óta beszedett szemetet gyűjtenek. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen2Collections|Yes|2. generációs Garbage gyűjtemények|Darabszám|Összesen|A 2. generációs objektumok száma az alkalmazási folyamat kezdete óta beszedett szemetet.|Példány|
|Kezeli|Yes|Leírók száma|Darabszám|Átlag|Az alkalmazási folyamat által jelenleg megnyitott kezelők teljes száma.|Példány|
|HealthCheckStatus|Yes|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Yes|Http 101|Darabszám|Összesen|Az 101-as HTTP-állapotkódot eredményező kérelmek száma.|Példány|
|Http2xx|Yes|Http-2xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 200, de < 300.|Példány|
|Http3xx|Yes|Http-3xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 300, de < 400.|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|A HTTP 401 állapotkódot eredményező kérések száma.|Példány|
|Http403|Yes|Http 403|Darabszám|Összesen|A HTTP 403 állapotkódot eredményező kérések száma.|Példány|
|Http404|Yes|Http 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérések száma.|Példány|
|Http406|Yes|Http 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérések száma.|Példány|
|Http4xx|Yes|Http-4xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 400, de < 500.|Példány|
|Http5xx|Yes|Http-kiszolgálói hibák|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 500, de < 600.|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|Az alkalmazás által a kérelmek kiszolgálásához szükséges idő (másodpercben).|Példány|
|IoOtherBytesPerSecond|Yes|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az adatokkal nem rendelkező I/O-műveletekhez (például vezérlési műveletekhez) tartozó bájtok kibocsátásának aránya.|Példány|
|IoOtherOperationsPerSecond|Yes|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olyan I/O-műveletek kiadására szolgál, amelyek nem rendelkeznek olvasási vagy írási műveletekkel.|Példány|
|IoReadBytesPerSecond|Yes|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az I/O-műveletek bájtjainak olvasási sebessége.|Példány|
|IoReadOperationsPerSecond|Yes|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olvasási I/O-műveletekre vonatkozó kiadási sebessége.|Példány|
|IoWriteBytesPerSecond|Yes|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|Az a sebesség, amellyel az alkalmazás a bájtok írását az I/O-műveletekhez.|Példány|
|IoWriteOperationsPerSecond|Yes|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata írási I/O-műveletek kiadásának gyakorisága.|Példány|
|MemoryWorkingSet|Yes|Memória munkakészlete|Bájt|Átlag|Az alkalmazás által a MiB-ben használt memória aktuális mennyisége.|Példány|
|PrivateBytes|Yes|Saját bájtok|Bájt|Átlag|A saját bájtok az alkalmazás által lefoglalt memória jelenlegi mérete (bájtban), amelyet más folyamatokkal nem lehet megosztani.|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|A kérelmek teljes száma a létrejövő HTTP-állapotkódtől függetlenül.|Példány|
|RequestsInApplicationQueue|Yes|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájában lévő kérelmek száma.|Példány|
|Szálak|Yes|Szálak száma|Darabszám|Átlag|Az alkalmazás folyamatában jelenleg aktív szálak száma.|Példány|
|TotalAppDomains|Yes|Alkalmazás összes tartománya|Darabszám|Átlag|Az alkalmazásban betöltött alkalmazástartományok aktuális száma.|Példány|
|TotalAppDomainsUnloaded|Yes|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Az alkalmazás indítása óta eltávolított alkalmazástartományok teljes száma.|Példány|


## <a name="microsoftwebstaticsites"></a>Microsoft. Web/staticSites

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|BytesSent|Példány|
|FunctionErrors|Yes|FunctionErrors|Darabszám|Összesen|FunctionErrors|Példány|
|FunctionHits|Yes|FunctionHits|Darabszám|Összesen|FunctionHits|Példány|
|SiteErrors|Yes|SiteErrors|Darabszám|Összesen|SiteErrors|Példány|
|SiteHits|Yes|SiteHits|Darabszám|Összesen|SiteHits|Példány|


## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Azure Monitor mérőszámokról](../data-platform.md)
- [Riasztások létrehozása metrikákhoz](../alerts/alerts-overview.md)
- [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](../essentials/platform-logs-overview.md)
