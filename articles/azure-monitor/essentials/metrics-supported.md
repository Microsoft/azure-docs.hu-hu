---
title: Azure Monitor támogatott metrikák erőforrástípus szerint
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/01/2021
ms.author: robb
ms.openlocfilehash: 6f664450d5450782d9a01d75abfb5a96b3e0bba6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221194"
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

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ThrottledRequests|Nem|ThrottledRequests|Darabszám|Átlag|azureADMetrics típusú metrika|Nincsenek méretek|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Igen|Memória: tisztító – aktuális ár|Darabszám|Átlag|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|ServerResourceType|
|CleanerMemoryNonshrinkable|Igen|Memória: nem csökkenthető a tisztább memória|Bájt|Átlag|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|ServerResourceType|
|CleanerMemoryShrinkable|Igen|Memória: tisztító memória csökkenthető|Bájt|Átlag|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|ServerResourceType|
|CommandPoolBusyThreads|Igen|Szálak: a parancssori készlet foglalt szálak|Darabszám|Átlag|A parancsfájl-készletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Igen|Szálak: parancssori készlet üresjárati szálai|Darabszám|Átlag|Az üresjárati szálak száma a parancs szálának készletében.|ServerResourceType|
|CommandPoolJobQueueLength|Igen|Parancssori feladatok várólistájának hossza|Darabszám|Átlag|A parancsfájl-készlet várólistájában lévő feladatok száma.|ServerResourceType|
|Összege|Igen|Kapcsolat: aktuális kapcsolatok|Darabszám|Átlag|A létesített ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CurrentUserSessions|Igen|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrejött felhasználói munkamenetek aktuális száma.|ServerResourceType|
|LongParsingBusyThreads|Igen|Szálak: hosszú elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingIdleThreads|Igen|Szálak: tartós folyamatok elemzése – üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingJobQueueLength|Igen|Szálak: hosszú elemzési feladatok várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|mashup_engine_memory_metric|Igen|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|Igen|M motor saját bájtjai|Bájt|Átlag|Saját bájtok használata az adategyesítési motor folyamatai alapján.|ServerResourceType|
|mashup_engine_qpu_metric|Igen|M motor QPU|Darabszám|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Igen|M motor virtuális bájtjai|Bájt|Átlag|Az adategyesítési motor folyamatai által használt virtuális bájtok használata.|ServerResourceType|
|memory_metric|Igen|Memória|Bájt|Átlag|Memory. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|memory_thrashing_metric|Igen|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|MemoryLimitHard|Igen|Memória: rögzített memória|Bájt|Átlag|Rögzített memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Igen|Memória: magas a memória korlátozása|Bájt|Átlag|Magas memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Igen|Memória: kevés a memória korlátja|Bájt|Átlag|Kevés a memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Igen|Memória: VertiPaq korlátja|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|MemoryUsage|Igen|Memória: memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memória által leképezett adatmennyiséggel, figyelmen kívül hagyva azokat a memóriát, amelyeket a xVelocity memórián belüli elemzési motorja (VertiPaq) leképezett vagy lefoglalt, és amely meghaladja a xVelocity-memória korlátját.|ServerResourceType|
|private_bytes_metric|Igen|Saját bájtok|Bájt|Átlag|Saját bájtok.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Igen|Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai|Darabszám|Átlag|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Igen|Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva|Darabszám|Átlag|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Igen|Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Igen|Szálak: feldolgozási készlet üresjáratban nem I/O-szálai|Darabszám|Átlag|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Igen|Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Igen|Feldolgozási készlet nyomtatási várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|ServerResourceType|
|qpu_metric|Igen|QPU|Darabszám|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|QueryPoolBusyThreads|Igen|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szál készletében lévő foglalt szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Igen|Szálak: lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|QueryPoolJobQueueLength|Igen|Szálak: lekérdezési készlet – feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|Kvóta|Igen|Memória: kvóta|Bájt|Átlag|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|ServerResourceType|
|QuotaBlocked|Igen|Memória: blokkolt kvóta|Darabszám|Átlag|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|ServerResourceType|
|RowsConvertedPerSec|Igen|Feldolgozás: másodpercenként konvertált sorok száma|CountPerSecond|Átlag|A sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsReadPerSec|Igen|Feldolgozás: másodpercenként beolvasott sorok száma|CountPerSecond|Átlag|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|ServerResourceType|
|RowsWrittenPerSec|Igen|Feldolgozás: másodpercenként írt sorok száma|CountPerSecond|Átlag|A sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|ShortParsingBusyThreads|Igen|Szálak: rövid elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Igen|Szálak: rövid elemzési üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Igen|Szálak: rövid elemzési feladatok várólistájának hossza|Darabszám|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|SuccessfullConnectionsPerSec|Igen|Sikeres kapcsolatok másodpercenként|CountPerSecond|Átlag|A sikeres kapcsolatok gyakorisága.|ServerResourceType|
|TotalConnectionFailures|Igen|Összes sikertelen Kapcsolatfelvétel|Darabszám|Átlag|A sikertelen csatlakozási kísérletek teljes száma.|ServerResourceType|
|TotalConnectionRequests|Igen|Kapcsolatkérelmek teljes száma|Darabszám|Átlag|A kapcsolatkérelmek teljes száma. Ezek az érkezések.|ServerResourceType|
|VertiPaqNonpaged|Igen|Memória: VertiPaq, nem lapozható|Bájt|Átlag|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|ServerResourceType|
|VertiPaqPaged|Igen|Memória: VertiPaq lapozható|Bájt|Átlag|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|ServerResourceType|
|virtual_bytes_metric|Igen|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BackendDuration|Igen|A háttérbeli kérelmek időtartama|Ezredmásodpercben|Átlag|A háttérbeli kérelmek időtartama (ezredmásodpercben)|Hely, állomásnév|
|Kapacitás|Igen|Kapacitás|Százalék|Átlag|A ApiManagement szolgáltatás kihasználtsági metrikája|Hely|
|Időtartam|Igen|Az átjárók kéréseinek teljes időtartama|Ezredmásodpercben|Átlag|Az átjáró kérelmek teljes időtartama ezredmásodpercben|Hely, állomásnév|
|EventHubDroppedEvents|Igen|Eldobott EventHub események|Darabszám|Összesen|A várólista-méretkorlát elérte miatt kihagyott események száma|Hely|
|EventHubRejectedEvents|Igen|EventHub-események visszautasítva|Darabszám|Összesen|Visszautasított EventHub-események száma (helytelen konfiguráció vagy jogosulatlan)|Hely|
|EventHubSuccessfulEvents|Igen|Sikeres EventHub események|Darabszám|Összesen|Sikeres EventHub események száma|Hely|
|EventHubThrottledEvents|Igen|Szabályozott EventHub események|Darabszám|Összesen|A szabályozott EventHub események száma|Hely|
|EventHubTimedoutEvents|Igen|Időtúllépés a EventHub eseményeinél|Darabszám|Összesen|Az időtúllépés miatti EventHub események száma|Hely|
|EventHubTotalBytesSent|Igen|EventHub-események mérete|Bájt|Összesen|EventHub-események teljes mérete bájtban|Hely|
|EventHubTotalEvents|Igen|Összes EventHub esemény|Darabszám|Összesen|A EventHub eljuttatott események száma|Hely|
|EventHubTotalFailedEvents|Igen|Sikertelen EventHub események|Darabszám|Összesen|Sikertelen EventHub események száma|Hely|
|FailedRequests|Igen|Sikertelen átjáró-kérelmek (elavult)|Darabszám|Összesen|Hibák száma az átjáró kéréseiben – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|NetworkConnectivity|Igen|Erőforrások hálózati kapcsolati állapota (előzetes verzió)|Darabszám|Átlag|A API Management szolgáltatástól függő erőforrástípusok hálózati kapcsolati állapota|Hely, ResourceType|
|OtherRequests|Igen|Egyéb átjáró-kérelmek (elavult)|Darabszám|Összesen|Egyéb átjáró-kérelmek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|Több dimenzióval rendelkező átjáró-kérelmek metrikái|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Igen|Sikeres átjáró-kérelmek (elavult)|Darabszám|Összesen|Sikeres átjáró-kérések száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzió helyett|Hely, állomásnév|
|TotalRequests|Igen|Összes átjáró kérelme (elavult)|Darabszám|Összesen|Átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|UnauthorizedRequests|Igen|Nem engedélyezett átjáró-kérelmek (elavult)|Darabszám|Összesen|Jogosulatlan átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Igen|HttpIncomingRequestCount|Darabszám|Darabszám|A bejövő HTTP-kérelmek teljes száma.|StatusCode, hitelesítés|
|HttpIncomingRequestDuration|Igen|HttpIncomingRequestDuration|Darabszám|Átlag|Egy HTTP-kérelem késése.|StatusCode, hitelesítés|
|ThrottledHttpRequestCount|Igen|ThrottledHttpRequestCount|Darabszám|Darabszám|Szabályozott HTTP-kérelmek.|Nincsenek méretek|

## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|aktív – időzítő – darabszám|Igen|aktív – időzítő – darabszám|Darabszám|Átlag|Jelenleg aktív Időzítők száma|Üzembe helyezés, AppName, Pod|
|foglalási – arány|Igen|foglalási – arány|Bájt|Átlag|A felügyelt halomban lefoglalt bájtok száma|Üzembe helyezés, AppName, Pod|
|AppCpuUsage|Igen|Alkalmazás CPU-használata |Százalék|Átlag|Az alkalmazás legutóbbi CPU-használata|Üzembe helyezés, AppName, Pod|
|szerelvény – darabszám|Igen|szerelvény – darabszám|Darabszám|Átlag|Betöltött szerelvények száma|Üzembe helyezés, AppName, Pod|
|CPU – használat|Igen|CPU – használat|Százalék|Átlag|a folyamat%-os kihasználtsága (%)|Üzembe helyezés, AppName, Pod|
|aktuális – kérelmek|Igen|aktuális – kérelmek|Darabszám|Átlag|A folyamat teljes élettartamában folyamatban lévő kérelmek száma összesen|Üzembe helyezés, AppName, Pod|
|kivételek száma|Igen|kivételek száma|Darabszám|Összesen|Kivételek száma|Üzembe helyezés, AppName, Pod|
|Sikertelen – kérelmek|Igen|Sikertelen – kérelmek|Darabszám|Átlag|A sikertelen kérelmek teljes száma a folyamat élettartamában|Üzembe helyezés, AppName, Pod|
|GC-heap-size|Igen|GC-heap-size|Darabszám|Átlag|A GC által jelentett teljes halom mérete (MB)|Üzembe helyezés, AppName, Pod|
|Gen-0-GC-darabszám|Igen|Gen-0-GC-darabszám|Darabszám|Átlag|0. generációs GCs száma|Üzembe helyezés, AppName, Pod|
|Gen-0 – méret|Igen|Gen-0 – méret|Bájt|Átlag|0. generációs halom mérete|Üzembe helyezés, AppName, Pod|
|Gen-1 – GC-darabszám|Igen|Gen-1 – GC-darabszám|Darabszám|Átlag|1. generációs GCs száma|Üzembe helyezés, AppName, Pod|
|1. generációs méret|Igen|1. generációs méret|Bájt|Átlag|1. generációs halom mérete|Üzembe helyezés, AppName, Pod|
|Gen-2 – GC – darabszám|Igen|Gen-2 – GC – darabszám|Darabszám|Átlag|A 2. generációs GCs száma|Üzembe helyezés, AppName, Pod|
|2. generációs méret|Igen|2. generációs méret|Bájt|Átlag|2. generációs halom mérete|Üzembe helyezés, AppName, Pod|
|JVM. GC. Live. Resize|Igen|JVM. GC. Live. Resize|Bájt|Átlag|Régi generációs memória-készlet mérete teljes GC után|Üzembe helyezés, AppName, Pod|
|JVM. GC. max. az adatméret|Igen|JVM. GC. max. az adatméret|Bájt|Átlag|A régi generációs memória maximális mérete|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. lefoglalt|Igen|JVM. GC. Memory. lefoglalt|Bájt|Maximum|Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután egy GC a következőre kerül|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. előléptetve|Igen|JVM. GC. Memory. előléptetve|Bájt|Maximum|A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC a GC-re lenne|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Count|Igen|JVM. GC. pause. Total. Count|Darabszám|Összesen|GC-szüneteltetések száma|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Time|Igen|JVM. GC. pause. Total. Time|Ezredmásodpercben|Összesen|GC felfüggesztésének teljes ideje|Üzembe helyezés, AppName, Pod|
|JVM. Memory. véglegesített|Igen|JVM. Memory. véglegesített|Bájt|Átlag|JVM rendelt memória bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. max|Igen|JVM. Memory. max|Bájt|Maximum|A memória-kezeléshez használható memória maximális mennyisége bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. felhasználva|Igen|JVM. Memory. felhasználva|Bájt|Átlag|Az alkalmazás memóriája bájtban használatban|Üzembe helyezés, AppName, Pod|
|Kissné méret|Igen|Kissné méret|Bájt|Átlag|Halmozott halom mérete|Üzembe helyezés, AppName, Pod|
|figyelő – zárolási tartalom – darabszám|Igen|figyelő – zárolási tartalom – darabszám|Darabszám|Átlag|A figyelő zárolásának megkísérlése során megjelenő időpontok száma|Üzembe helyezés, AppName, Pod|
|Process. CPU. használat|Igen|Process. CPU. használat|Százalék|Átlag|A JVM folyamat legutóbbi CPU-használata|Üzembe helyezés, AppName, Pod|
|kérelmek/másodperc|Igen|kérelmek – arány|Darabszám|Átlag|Kérelmek gyakorisága|Üzembe helyezés, AppName, Pod|
|System. CPU. használat|Igen|System. CPU. használat|Százalék|Átlag|A legújabb CPU-használat a teljes rendszerhez|Üzembe helyezés, AppName, Pod|
|szálkészlet munkaszála belépett – befejezett elemek – darabszám|Igen|szálkészlet munkaszála belépett – befejezett elemek – darabszám|Darabszám|Átlag|A szálkészlet munkaszála belépett befejezte a munkaelemek darabszámát|Üzembe helyezés, AppName, Pod|
|szálkészlet munkaszála belépett – várólista hossza|Igen|szálkészlet munkaszála belépett – várólista hossza|Darabszám|Átlag|Szálkészlet munkaszála belépett munkaelemek várólistájának hossza|Üzembe helyezés, AppName, Pod|
|szálkészlet munkaszála belépett – szálak száma|Igen|szálkészlet munkaszála belépett – szálak száma|Darabszám|Átlag|Szálkészlet munkaszála belépett-szálak száma|Üzembe helyezés, AppName, Pod|
|idő – GC|Igen|idő – GC|Százalék|Átlag|%-os idő a GC-ben az utolsó GC óta|Üzembe helyezés, AppName, Pod|
|tomcat. Global. error|Igen|tomcat. Global. error|Darabszám|Összesen|Tomcat globális hiba|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Received|Igen|tomcat. Global. Received|Bájt|Összesen|Tomcat összesen fogadott bájtok|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. AVG. Time|Igen|tomcat. Global. Request. AVG. Time|Ezredmásodpercben|Átlag|Tomcat-kérelem átlagos ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. max|Igen|tomcat. Global. Request. max|Ezredmásodpercben|Maximum|Tomcat-kérelem maximális ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Count|Igen|tomcat. Global. Request. Total. Count|Darabszám|Összesen|Tomcat-kérelem összesített száma|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Time|Igen|tomcat. Global. Request. Total. Time|Ezredmásodpercben|Összesen|Tomcat-kérelem teljes ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. elküldve|Igen|tomcat. Global. elküldve|Bájt|Összesen|Tomcat összesen eljuttatott bájtok száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. Current|Igen|tomcat. Sessions. Active. Current|Darabszám|Összesen|Tomcat-munkamenet aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. max|Igen|tomcat. Sessions. Active. max|Darabszám|Összesen|Tomcat-munkamenet maximális aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Alive. max|Igen|tomcat. Sessions. Alive. max|Ezredmásodpercben|Maximum|Tomcat-munkamenet maximális élettartama|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. created|Igen|tomcat. Sessions. created|Darabszám|Összesen|Tomcat-munkamenet létrehozva szám|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. lejárt|Igen|tomcat. Sessions. lejárt|Darabszám|Összesen|A Tomcat-munkamenet lejárt|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. elutasítva|Igen|tomcat. Sessions. elutasítva|Darabszám|Összesen|A Tomcat-munkamenet elutasította a darabszámot|Üzembe helyezés, AppName, Pod|
|tomcat.threads.config. max|Igen|tomcat.threads.config. max|Darabszám|Összesen|Tomcat-konfiguráció maximális szálának száma|Üzembe helyezés, AppName, Pod|
|tomcat. Threads. Current|Igen|tomcat. Threads. Current|Darabszám|Összesen|Tomcat jelenlegi szálak száma|Üzembe helyezés, AppName, Pod|
|kérelmek összesen|Igen|kérelmek összesen|Darabszám|Átlag|A folyamat élettartamán belüli kérelmek teljes száma|Üzembe helyezés, AppName, Pod|
|munkakészlet|Igen|munkakészlet|Darabszám|Átlag|A folyamat által használt munkakészlet mennyisége (MB)|Üzembe helyezés, AppName, Pod|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|TotalJob|Igen|Feladatok összesen|Darabszám|Összesen|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentMachineRuns|Igen|Összes frissítés központi telepítési gép futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Igen|Összes frissítés központi telepítési futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, állapot|


## <a name="microsoftavsprivateclouds"></a>Microsoft. AVS/privateClouds

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CapacityLatest|Igen|Adattár lemezének teljes kapacitása|Bájt|Átlag|Az adattárban lévő lemez teljes kapacitása|DSNAME|
|DiskUsedPercentage|Igen| Felhasznált adattároló-lemez százalékos aránya|Százalék|Átlag|Az adattárban használt szabad lemez százaléka|DSNAME|
|EffectiveCpuAverage|Igen|Százalékos processzorhasználat|Százalék|Átlag|A fürtben használt CPU-erőforrások százalékos aránya|clustername|
|EffectiveMemAverage|Igen|Átlagos tényleges memória|Bájt|Átlag|A fürtben rendelkezésre álló teljes memória mennyisége|clustername|
|OverheadAverage|Igen|Átlagos memória terhelése|Bájt|Átlag|A virtualizációs infrastruktúra által felhasznált fizikai memória üzemeltetése|clustername|
|TotalMbAverage|Igen|Teljes memória átlagos száma|Bájt|Átlag|Teljes memória a fürtben|clustername|
|UsageAverage|Igen|Memóriahasználat átlagos kihasználtsága|Százalék|Átlag|Memóriahasználat a teljes konfigurált vagy rendelkezésre álló memória százalékaként|clustername|
|UsedLatest|Igen|Felhasznált adattár-lemez|Bájt|Átlag|Az adattárban használt teljes lemez mennyisége|DSNAME|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CoreCount|Nem|Dedikált mag száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált magok teljes száma|Nincsenek méretek|
|CreatingNodeCount|Nem|Csomópontok számának létrehozása|Darabszám|Összesen|Létrehozandó csomópontok száma|Nincsenek méretek|
|IdleNodeCount|Nem|Tétlen csomópontok száma|Darabszám|Összesen|Üresjárati csomópontok száma|Nincsenek méretek|
|JobDeleteCompleteEvent|Igen|Feladatok törlése – befejezett események|Darabszám|Összesen|A sikeresen törölt feladatok teljes száma.|jobId|
|JobDeleteStartEvent|Igen|Feladatok törlésének indítási eseményei|Darabszám|Összesen|A törölni kívánt feladatok teljes száma.|jobId|
|JobDisableCompleteEvent|Igen|A feladatok letiltják a teljes eseményeket|Darabszám|Összesen|A sikeresen letiltott feladatok teljes száma.|jobId|
|JobDisableStartEvent|Igen|Feladatok letiltásának indítási eseményei|Darabszám|Összesen|A letiltani kívánt feladatok teljes száma.|jobId|
|JobStartEvent|Igen|Feladatok indítási eseményei|Darabszám|Összesen|A sikeresen elindított feladatok teljes száma.|jobId|
|JobTerminateCompleteEvent|Igen|A feladatokhoz tartozó befejezett események befejezése|Darabszám|Összesen|A sikeresen leállított feladatok teljes száma.|jobId|
|JobTerminateStartEvent|Igen|Feladatokból indított események leállítása|Darabszám|Összesen|A leállítani kívánt feladatok teljes száma.|jobId|
|LeavingPoolNodeCount|Nem|A készlet-csomópontok számának elhagyása|Darabszám|Összesen|A készletet elhagyó csomópontok száma|Nincsenek méretek|
|LowPriorityCoreCount|Nem|LowPriority mag száma|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincsenek méretek|
|OfflineNodeCount|Nem|Offline csomópontok száma|Darabszám|Összesen|Offline csomópontok száma|Nincsenek méretek|
|PoolCreateEvent|Igen|Készlet-létrehozási események|Darabszám|Összesen|A létrehozott készletek teljes száma|poolId|
|PoolDeleteCompleteEvent|Igen|Készlet törlése – befejezett események|Darabszám|Összesen|A befejezett készlet-törlések teljes száma|poolId|
|PoolDeleteStartEvent|Igen|Készlet törlése – indítási események|Darabszám|Összesen|Az elindított készlet-törlések száma összesen|poolId|
|PoolResizeCompleteEvent|Igen|Készlet átméretezése – befejezett események|Darabszám|Összesen|A készlet összes újraméretezésének száma|poolId|
|PoolResizeStartEvent|Igen|Készlet átméretezésének indítási eseményei|Darabszám|Összesen|A készlet elindított újraméretezésének teljes száma|poolId|
|PreemptedNodeCount|Nem|Előzik-csomópontok száma|Darabszám|Összesen|Előzik-csomópontok száma|Nincsenek méretek|
|RebootingNodeCount|Nem|Csomópontok számának újraindítása|Darabszám|Összesen|Újraindítási csomópontok száma|Nincsenek méretek|
|ReimagingNodeCount|Nem|Csomópontok rendszerképének alaphelyzetbe állítása|Darabszám|Összesen|Rendszerkép-csomópontok száma|Nincsenek méretek|
|RunningNodeCount|Nem|Csomópontok száma|Darabszám|Összesen|Futó csomópontok száma|Nincsenek méretek|
|StartingNodeCount|Nem|Csomópontok számának indítása|Darabszám|Összesen|Kiinduló csomópontok száma|Nincsenek méretek|
|StartTaskFailedNodeCount|Nem|Indítási feladat sikertelen csomópontok száma|Darabszám|Összesen|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|Nincsenek méretek|
|TaskCompleteEvent|Igen|Feladat teljes eseményei|Darabszám|Összesen|A Befejezett feladatok teljes száma|poolId, jobId|
|TaskFailEvent|Igen|Feladat sikertelen eseményei|Darabszám|Összesen|A meghiúsult állapotban Befejezett feladatok teljes száma|poolId, jobId|
|TaskStartEvent|Igen|Tevékenységek indítási eseményei|Darabszám|Összesen|Az elindított feladatok teljes száma|poolId, jobId|
|TotalLowPriorityNodeCount|Nem|Low-Priority csomópontok száma|Darabszám|Összesen|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincsenek méretek|
|TotalNodeCount|Nem|Dedikált csomópontok száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált csomópontok teljes száma|Nincsenek méretek|
|UnusableNodeCount|Nem|Nem használható csomópontok száma|Darabszám|Összesen|Használhatatlan csomópontok száma|Nincsenek méretek|
|WaitingForStartTaskNodeCount|Nem|Várakozás a feladat-csomópontok számának megkezdésére|Darabszám|Összesen|Az indítási tevékenység befejezésére váró csomópontok száma|Nincsenek méretek|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Igen|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Igen|Aktív csomópontok|Darabszám|Átlag|Futó csomópontok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Igen|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Igen|Tétlen csomópontok|Darabszám|Átlag|Üresjárati csomópontok száma|Forgatókönyv, ClusterName|
|A feladatok befejeződtek|Igen|A feladatok befejeződtek|Darabszám|Összesen|Befejezett feladatok száma|Forgatókönyv, ClusterName, ResultType|
|Feladatok elküldve|Igen|Feladatok elküldve|Darabszám|Összesen|Elküldött feladatok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Igen|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Igen|Csomópontok elhagyása|Darabszám|Átlag|Kilépő csomópontok száma|Forgatókönyv, ClusterName|
|Előzik magok|Igen|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Igen|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Igen|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Magok összesen|Igen|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Igen|Csomópontok összesen|Darabszám|Átlag|Összes csomópont száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Igen|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Igen|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma|Forgatókönyv, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Igen|BroadcastProcessedCountDisplayName|Darabszám|Átlag|A feldolgozott tranzakciók száma.|Csomópont, csatorna, típus, állapot|
|ChaincodeExecuteTimeouts|Igen|ChaincodeExecuteTimeoutsDisplayName|Darabszám|Átlag|Az időkorláttal rendelkező chaincode-végrehajtások (init vagy meghívás) száma.|Csomópont, chaincode|
|ChaincodeLaunchFailures|Igen|ChaincodeLaunchFailuresDisplayName|Darabszám|Átlag|A sikertelenül indított chaincode száma.|Csomópont, chaincode|
|ChaincodeLaunchTimeouts|Igen|ChaincodeLaunchTimeoutsDisplayName|Darabszám|Átlag|Az időtúllépés miatt elindított chaincode száma.|Csomópont, chaincode|
|ChaincodeShimRequestsCompleted|Igen|ChaincodeShimRequestsCompletedDisplayName|Darabszám|Átlag|A chaincode-kiigazítási kérelmek száma befejeződött.|Csomópont, típus, csatorna, chaincode, sikeres|
|ChaincodeShimRequestsReceived|Igen|ChaincodeShimRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott chaincode-kiigazítási kérelmek száma.|Csomópont, típus, csatorna, chaincode|
|ClusterCommEgressQueueCapacity|Igen|ClusterCommEgressQueueCapacityDisplayName|Darabszám|Átlag|A kimenő forgalom várólistájának kapacitása.|Csomópont, gazdagép, msg_type, csatorna|
|ClusterCommEgressQueueLength|Igen|ClusterCommEgressQueueLengthDisplayName|Darabszám|Átlag|A kimenő forgalom várólistájának hossza.|Csomópont, gazdagép, msg_type, csatorna|
|ClusterCommEgressQueueWorkers|Igen|ClusterCommEgressQueueWorkersDisplayName|Darabszám|Átlag|A kimenő várólista-feldolgozók száma.|Csomópont, csatorna|
|ClusterCommEgressStreamCount|Igen|ClusterCommEgressStreamCountDisplayName|Darabszám|Átlag|A többi csomóponthoz tartozó adatfolyamok száma.|Csomópont, csatorna|
|ClusterCommEgressTlsConnectionCount|Igen|ClusterCommEgressTlsConnectionCountDisplayName|Darabszám|Átlag|Más csomópontokhoz tartozó TLS-kapcsolatok száma.|Csomópont|
|ClusterCommIngressStreamCount|Igen|ClusterCommIngressStreamCountDisplayName|Darabszám|Átlag|A többi csomóponttól származó adatfolyamok száma.|Csomópont|
|ClusterCommMsgDroppedCount|Igen|ClusterCommMsgDroppedCountDisplayName|Darabszám|Átlag|Eldobott üzenetek száma.|Csomópont, állomás, csatorna|
|ConnectionAccepted|Igen|Elfogadott kapcsolatok|Darabszám|Összesen|Elfogadott kapcsolatok|Csomópont|
|ConnectionActive|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Csomópont|
|ConnectionHandled|Igen|Kezelt kapcsolatok|Darabszám|Összesen|Kezelt kapcsolatok|Csomópont|
|ConsensusEtcdraftActiveNodes|Igen|ConsensusEtcdraftActiveNodesDisplayName|Darabszám|Átlag|A csatorna aktív csomópontjainak száma.|Csomópont, csatorna|
|ConsensusEtcdraftClusterSize|Igen|ConsensusEtcdraftClusterSizeDisplayName|Darabszám|Átlag|A csatorna csomópontjainak száma.|Csomópont, csatorna|
|ConsensusEtcdraftCommittedBlockNumber|Igen|ConsensusEtcdraftCommittedBlockNumberDisplayName|Darabszám|Átlag|A legutóbb véglegesített blokk blokkjának száma.|Csomópont, csatorna|
|ConsensusEtcdraftConfigProposalsReceived|Igen|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Darabszám|Átlag|A konfigurációs típusú tranzakciókhoz fogadott javaslatok teljes száma.|Csomópont, csatorna|
|ConsensusEtcdraftIsLeader|Igen|ConsensusEtcdraftIsLeaderDisplayName|Darabszám|Átlag|Az aktuális csomópont vezetői állapota: 1, ha a vezető Else 0.|Csomópont, csatorna|
|ConsensusEtcdraftLeaderChanges|Igen|ConsensusEtcdraftLeaderChangesDisplayName|Darabszám|Átlag|A folyamat kezdete óta megjelenő vezető változások száma.|Csomópont, csatorna|
|ConsensusEtcdraftNormalProposalsReceived|Igen|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Darabszám|Átlag|A normál típusú tranzakciókhoz fogadott javaslatok teljes száma.|Csomópont, csatorna|
|ConsensusEtcdraftProposalFailures|Igen|ConsensusEtcdraftProposalFailuresDisplayName|Darabszám|Átlag|A javaslati hibák száma.|Csomópont, csatorna|
|ConsensusEtcdraftSnapshotBlockNumber|Igen|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Darabszám|Átlag|A legújabb pillanatkép letiltási száma.|Csomópont, csatorna|
|ConsensusKafkaBatchSize|Igen|ConsensusKafkaBatchSizeDisplayName|Darabszám|Átlag|Az átlagos batch-méret a témakörökbe eljuttatott bájtokban.|Csomópont, témakör|
|ConsensusKafkaCompressionRatio|Igen|ConsensusKafkaCompressionRatioDisplayName|Darabszám|Átlag|A témakörök átlagos tömörítési aránya (százalék).|Csomópont, témakör|
|ConsensusKafkaIncomingByteRate|Igen|ConsensusKafkaIncomingByteRateDisplayName|Darabszám|Átlag|Bájt/másodperc elolvasása brókerek.|Csomópont, broker_id|
|ConsensusKafkaLastOffsetPersisted|Igen|ConsensusKafkaLastOffsetPersistedDisplayName|Darabszám|Átlag|A legutóbb véglegesített blokk blokk metaadataiban megadott eltolás.|Csomópont, csatorna|
|ConsensusKafkaOutgoingByteRate|Igen|ConsensusKafkaOutgoingByteRateDisplayName|Darabszám|Átlag|A brókereknek írt bájtok másodpercenkénti száma.|Csomópont, broker_id|
|ConsensusKafkaRecordSendRate|Igen|ConsensusKafkaRecordSendRateDisplayName|Darabszám|Átlag|A témakörök számára másodpercenként elküldett rekordok száma.|Csomópont, témakör|
|ConsensusKafkaRecordsPerRequest|Igen|ConsensusKafkaRecordsPerRequestDisplayName|Darabszám|Átlag|A témakörökre vonatkozó kérelmek által elküldett rekordok átlagos száma.|Csomópont, témakör|
|ConsensusKafkaRequestLatency|Igen|ConsensusKafkaRequestLatencyDisplayName|Darabszám|Átlag|Az átlagos kérelmek késése az MS-ban a brókerek számára.|Csomópont, broker_id|
|ConsensusKafkaRequestRate|Igen|ConsensusKafkaRequestRateDisplayName|Darabszám|Átlag|A brókereknek küldött kérelmek/másodpercek.|Csomópont, broker_id|
|ConsensusKafkaRequestSize|Igen|ConsensusKafkaRequestSizeDisplayName|Darabszám|Átlag|Az átlagos kérelem mérete bájtban a brókerek számára.|Csomópont, broker_id|
|ConsensusKafkaResponseRate|Igen|ConsensusKafkaResponseRateDisplayName|Darabszám|Átlag|A brókereknek küldött kérelmek/másodpercek.|Csomópont, broker_id|
|ConsensusKafkaResponseSize|Igen|ConsensusKafkaResponseSizeDisplayName|Darabszám|Átlag|Az átlagos válasz mérete bájtban a brókerek között.|Csomópont, broker_id|
|CpuUsagePercentageInDouble|Igen|CPU-használat százaléka|Százalék|Maximum|CPU-használat százaléka|Csomópont|
|DeliverBlocksSent|Igen|DeliverBlocksSentDisplayName|Darabszám|Átlag|A kézbesítési szolgáltatás által eljuttatott blokkok száma.|Csomópont, csatorna, szűrt, data_type|
|DeliverRequestsCompleted|Igen|DeliverRequestsCompletedDisplayName|Darabszám|Átlag|A befejezett kézbesítési kérelmek száma.|Csomópont, csatorna, szűrt, data_type, sikeres|
|DeliverRequestsReceived|Igen|DeliverRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott kézbesítési kérelmek száma.|Csomópont, csatorna, szűrt, data_type|
|DeliverStreamsClosed|Igen|DeliverStreamsClosedDisplayName|Darabszám|Átlag|A kézbesítési szolgáltatás számára lezárt GRPC-adatfolyamok száma.|Csomópont|
|DeliverStreamsOpened|Igen|DeliverStreamsOpenedDisplayName|Darabszám|Átlag|A kézbesítési szolgáltatás számára megnyitott GRPC-adatfolyamok száma.|Csomópont|
|EndorserChaincodeInstantiationFailures|Igen|EndorserChaincodeInstantiationFailuresDisplayName|Darabszám|Átlag|A sikertelen chaincode-példányok vagy-verziófrissítések száma.|Csomópont, csatorna, chaincode|
|EndorserDuplicateTransactionFailures|Igen|EndorserDuplicateTransactionFailuresDisplayName|Darabszám|Átlag|A sikertelen javaslatok száma ismétlődő tranzakciós azonosító miatt.|Csomópont, csatorna, chaincode|
|EndorserEndorsementFailures|Igen|EndorserEndorsementFailuresDisplayName|Darabszám|Átlag|A sikertelen jóváhagyások száma.|Csomópont, csatorna, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Igen|EndorserProposalAclFailuresDisplayName|Darabszám|Átlag|Azon javaslatok száma, amelyek nem felelnek meg az ACL-ellenőrzéseknek.|Csomópont, csatorna, chaincode|
|EndorserProposalSimulationFailures|Igen|EndorserProposalSimulationFailuresDisplayName|Darabszám|Átlag|A sikertelen javaslat-szimulációk száma.|Csomópont, csatorna, chaincode|
|EndorserProposalsReceived|Igen|EndorserProposalsReceivedDisplayName|Darabszám|Átlag|A fogadott javaslatok száma.|Csomópont|
|EndorserProposalValidationFailures|Igen|EndorserProposalValidationFailuresDisplayName|Darabszám|Átlag|Azon javaslatok száma, amelyeken sikertelen volt a kezdeti ellenőrzés.|Csomópont|
|EndorserSuccessfulProposals|Igen|EndorserSuccessfulProposalsDisplayName|Darabszám|Átlag|A sikeres javaslatok száma.|Csomópont|
|FabricVersion|Igen|FabricVersionDisplayName|Darabszám|Átlag|A háló aktív verziója.|Csomópont, verzió|
|GossipCommMessagesReceived|Igen|GossipCommMessagesReceivedDisplayName|Darabszám|Átlag|Fogadott üzenetek száma.|Csomópont|
|GossipCommMessagesSent|Igen|GossipCommMessagesSentDisplayName|Darabszám|Átlag|Az elküldött üzenetek száma.|Csomópont|
|GossipCommOverflowCount|Igen|GossipCommOverflowCountDisplayName|Darabszám|Átlag|A kimenő várólista pufferének túlcsordult száma.|Csomópont|
|GossipLeaderElectionLeader|Igen|GossipLeaderElectionLeaderDisplayName|Darabszám|Átlag|A társ a vezető (1) vagy követő (0).|Csomópont, csatorna|
|GossipMembershipTotalPeersKnown|Igen|GossipMembershipTotalPeersKnownDisplayName|Darabszám|Átlag|Összes ismert társ.|Csomópont, csatorna|
|GossipPayloadBufferSize|Igen|GossipPayloadBufferSizeDisplayName|Darabszám|Átlag|A hasznos adatok pufferének mérete.|Csomópont, csatorna|
|GossipStateHeight|Igen|GossipStateHeightDisplayName|Darabszám|Átlag|Aktuális Főkönyv magassága|Csomópont, csatorna|
|GrpcCommConnClosed|Igen|GrpcCommConnClosedDisplayName|Darabszám|Átlag|gRPC-kapcsolatok lezárva. A nyitott mínusz lezárt érték a kapcsolatok aktív száma.|Csomópont|
|GrpcCommConnOpened|Igen|GrpcCommConnOpenedDisplayName|Darabszám|Átlag|a gRPC-kapcsolatok megnyitva. A nyitott mínusz lezárt érték a kapcsolatok aktív száma.|Csomópont|
|GrpcServerStreamMessagesReceived|Igen|GrpcServerStreamMessagesReceivedDisplayName|Darabszám|Átlag|A fogadott adatfolyam-üzenetek száma.|Csomópont, szolgáltatás, metódus|
|GrpcServerStreamMessagesSent|Igen|GrpcServerStreamMessagesSentDisplayName|Darabszám|Átlag|Az elküldött adatfolyam-üzenetek száma.|Csomópont, szolgáltatás, metódus|
|GrpcServerStreamRequestsCompleted|Igen|GrpcServerStreamRequestsCompletedDisplayName|Darabszám|Átlag|Az adatfolyam-kérelmek száma befejeződött.|Csomópont, szolgáltatás, metódus, kód|
|GrpcServerUnaryRequestsReceived|Igen|GrpcServerUnaryRequestsReceivedDisplayName|Darabszám|Átlag|A fogadott egyoperandusú kérelmek száma.|Csomópont, szolgáltatás, metódus|
|IOReadBytes|Igen|IO olvasási bájtok|Bájt|Összesen|IO olvasási bájtok|Csomópont|
|IOWriteBytes|Igen|IO írási bájtjai|Bájt|Összesen|IO írási bájtjai|Csomópont|
|LedgerBlockchainHeight|Igen|LedgerBlockchainHeightDisplayName|Darabszám|Átlag|A lánc magassága blokkokban.|Csomópont, csatorna|
|LedgerTransactionCount|Igen|LedgerTransactionCountDisplayName|Darabszám|Átlag|A feldolgozott tranzakciók száma.|Csomópont, csatorna, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Igen|LoggingEntriesCheckedDisplayName|Darabszám|Átlag|Az aktív naplózási szinttel ellenőrzött naplóbejegyzések száma.|Csomópont, szint|
|LoggingEntriesWritten|Igen|LoggingEntriesWrittenDisplayName|Darabszám|Átlag|Az írt naplóbejegyzések száma.|Csomópont, szint|
|MemoryLimit|Igen|Memória korlátja|Bájt|Átlag|Memória korlátja|Csomópont|
|MemoryUsage|Igen|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryUsagePercentageInDouble|Igen|Memóriahasználat százaléka|Százalék|Átlag|Memóriahasználat százaléka|Csomópont|
|PendingTransactions|Igen|Függőben lévő tranzakciók|Darabszám|Átlag|Függőben lévő tranzakciók|Csomópont|
|ProcessedBlocks|Igen|Feldolgozott blokkok|Darabszám|Összesen|Feldolgozott blokkok|Csomópont|
|ProcessedTransactions|Igen|Feldolgozott tranzakciók|Darabszám|Összesen|Feldolgozott tranzakciók|Csomópont|
|QueuedTransactions|Igen|Várólistán lévő tranzakciók|Darabszám|Átlag|Várólistán lévő tranzakciók|Csomópont|
|RequestHandled|Igen|Kezelt kérelmek|Darabszám|Összesen|Kezelt kérelmek|Csomópont|
|StorageUsage|Igen|Tárterület-használat|Bájt|Átlag|Tárterület-használat|Csomópont|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|RequestLatency|Igen|Kérelem késése|Ezredmásodpercben|Összesen|A kiszolgáló által a kérelem feldolgozásához szükséges idő|Művelet, hitelesítés, protokoll, DataCenter|
|RequestsTraffic|Igen|Forgalom kérése|Százalék|Darabszám|Benyújtott kérelmek száma|Művelet, hitelesítés, protokoll, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|allcachehits|Igen|Gyorsítótár-találatok (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allcachemisses|Igen|Gyorsítótár-lemaradás (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allcacheRead|Igen|Gyorsítótár olvasása (példány-alapú)|BytesPerSecond|Maximum||ShardId, port, elsődleges|
|allcacheWrite|Igen|Gyorsítótár-írás (példány-alapú)|BytesPerSecond|Maximum||ShardId, port, elsődleges|
|allconnectedclients|Igen|Csatlakoztatott ügyfelek (példány-alapú)|Darabszám|Maximum||ShardId, port, elsődleges|
|allevictedkeys|Igen|Kizárt kulcsok (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allexpiredkeys|Igen|Lejárt kulcsok (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|allgetcommands|Igen|Lekérdezi (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|alloperationsPerSecond|Igen|Másodpercenkénti műveletek (példány-alapú)|Darabszám|Maximum||ShardId, port, elsődleges|
|allserverLoad|Igen|Kiszolgáló terhelése (példány-alapú)|Százalék|Maximum||ShardId, port, elsődleges|
|allsetcommands|Igen|Készletek (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|alltotalcommandsprocessed|Igen|Összes művelet (példány-alapú)|Darabszám|Összesen||ShardId, port, elsődleges|
|alltotalkeys|Igen|Összes kulcs (példány-alapú)|Darabszám|Maximum||ShardId, port, elsődleges|
|allusedmemory|Igen|Felhasznált memória (példány-alapú)|Bájt|Maximum||ShardId, port, elsődleges|
|allusedmemorypercentage|Igen|Felhasznált memória százalékos aránya (példány-alapú)|Százalék|Maximum||ShardId, port, elsődleges|
|allusedmemoryRss|Igen|Használt memória RSS-je (példány-alapú)|Bájt|Maximum||ShardId, port, elsődleges|
|cachehits|Igen|Gyorsítótár-találatok|Darabszám|Összesen||ShardId|
|cachehits0|Igen|Gyorsítótárbeli találatok (szegmens 0)|Darabszám|Összesen||Nincsenek méretek|
|cachehits1|Igen|Gyorsítótárbeli találatok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits2|Igen|Gyorsítótárbeli találatok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachehits3|Igen|Gyorsítótárbeli találatok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits4|Igen|Gyorsítótár-találatok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits5|Igen|Gyorsítótárbeli találatok (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|cachehits6|Igen|Gyorsítótárbeli találatok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|cachehits7|Igen|Gyorsítótárbeli találatok (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|cachehits8|Igen|Gyorsítótárbeli találatok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|cachehits9|Igen|Gyorsítótárbeli találatok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|cacheLatency|Igen|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||ShardId|
|cachemisses|Igen|Gyorsítótár-tévesztések|Darabszám|Összesen||ShardId|
|cachemisses0|Igen|Gyorsítótár-kihagyás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses1|Igen|Gyorsítótár-lemaradás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses2|Igen|Gyorsítótár-lemaradás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses3|Igen|Gyorsítótár-lemaradás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses4|Igen|Gyorsítótár-lemaradás (4. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses5|Igen|Gyorsítótár-lemaradás (5. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses6|Igen|Gyorsítótár-lemaradás (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses7|Igen|Gyorsítótár-lemaradás (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses8|Igen|Gyorsítótár-lemaradás (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses9|Igen|Gyorsítótár-lemaradás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|cachemissrate|Igen|Gyorsítótár-kihagyás aránya|Százalék|cachemissrate||ShardId|
|cacheRead|Igen|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|cacheRead0|Igen|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead1|Igen|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead2|Igen|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead3|Igen|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead4|Igen|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead5|Igen|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead6|Igen|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead7|Igen|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead8|Igen|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead9|Igen|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite|Igen|Gyorsítótár-írás|BytesPerSecond|Maximum||ShardId|
|cacheWrite0|Igen|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite1|Igen|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite2|Igen|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite3|Igen|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite4|Igen|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite5|Igen|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite6|Igen|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite7|Igen|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite8|Igen|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite9|Igen|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|connectedclients|Igen|Csatlakoztatott ügyfelek|Darabszám|Maximum||ShardId|
|connectedclients0|Igen|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients1|Igen|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients2|Igen|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients3|Igen|Csatlakoztatott ügyfelek (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients4|Igen|Csatlakoztatott ügyfelek (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients5|Igen|Csatlakoztatott ügyfelek (5. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients6|Igen|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients7|Igen|Csatlakoztatott ügyfelek (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients8|Igen|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients9|Igen|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|Hibák|Igen|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|evictedkeys|Igen|Kizárt kulcsok|Darabszám|Összesen||ShardId|
|evictedkeys0|Igen|Kizárt kulcsok (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys1|Igen|Kizárt kulcsok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys2|Igen|Kizárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys3|Igen|Kizárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys4|Igen|Kizárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys5|Igen|Kizárt kulcsok (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys6|Igen|Kizárt kulcsok (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys7|Igen|Kizárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys8|Igen|Kizárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys9|Igen|Kizárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys|Igen|Lejárt kulcsok|Darabszám|Összesen||ShardId|
|expiredkeys0|Igen|Lejárt kulcsok (szilánk 0)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys1|Igen|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys2|Igen|Lejárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys3|Igen|Lejárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys4|Igen|Lejárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys5|Igen|Lejárt kulcsok (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys6|Igen|Lejárt kulcsok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys7|Igen|Lejárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys8|Igen|Lejárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys9|Igen|Lejárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|getcommands|Igen|Lekérések|Darabszám|Összesen||ShardId|
|getcommands0|Igen|Beolvasás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands1|Igen|Beolvasás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands2|Igen|Beolvasás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands3|Igen|Beolvasás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands4|Igen|Beolvasás (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|getcommands5|Igen|Beolvasás (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|getcommands6|Igen|Beolvasás (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|getcommands7|Igen|Beolvasás (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|getcommands8|Igen|Beolvasás (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|getcommands9|Igen|Beolvasás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|operationsPerSecond|Igen|Műveletek száma másodpercenként|Darabszám|Maximum||ShardId|
|operationsPerSecond0|Igen|Művelet/másodperc (0. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond1|Igen|Művelet/másodperc (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond2|Igen|Művelet/másodperc (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond3|Igen|Művelet/másodperc (3. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond4|Igen|Művelet/másodperc (4. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond5|Igen|Művelet/másodperc (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond6|Igen|Művelet/másodperc (szegmens 6)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond7|Igen|Művelet/másodperc (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond8|Igen|Művelet/másodperc (szegmens 8)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond9|Igen|Művelet/másodperc (szegmens 9)|Darabszám|Maximum||Nincsenek méretek|
|percentProcessorTime|Igen|CPU|Százalék|Maximum||ShardId|
|percentProcessorTime0|Igen|CPU (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime1|Igen|CPU (1. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime2|Igen|CPU (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime3|Igen|CPU (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime4|Igen|CPU (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime5|Igen|CPU (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime6|Igen|CPU (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime7|Igen|PROCESSZOR (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime8|Igen|CPU (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime9|Igen|CPU (szilánk 9)|Százalék|Maximum||Nincsenek méretek|
|serverLoad|Igen|Kiszolgáló terhelése|Százalék|Maximum||ShardId|
|serverLoad0|Igen|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad1|Igen|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad2|Igen|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad3|Igen|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad4|Igen|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad5|Igen|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad6|Igen|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|serverLoad7|Igen|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad8|Igen|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|serverLoad9|Igen|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||Nincsenek méretek|
|setcommands|Igen|Halmazok|Darabszám|Összesen||ShardId|
|setcommands0|Igen|Készletek (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands1|Igen|Készletek (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands2|Igen|Készletek (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands3|Igen|Készletek (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands4|Igen|Készletek (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|setcommands5|Igen|Készletek (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|setcommands6|Igen|Készletek (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|setcommands7|Igen|Készletek (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|setcommands8|Igen|Készletek (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|setcommands9|Igen|Készletek (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed|Igen|Műveletek összesen|Darabszám|Összesen||ShardId|
|totalcommandsprocessed0|Igen|Összes művelet (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed1|Igen|Összes művelet (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed2|Igen|Összes művelet (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed3|Igen|Összes művelet (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed4|Igen|Összes művelet (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed5|Igen|Összes művelet (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed6|Igen|Összes művelet (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed7|Igen|Összes művelet (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed8|Igen|Összes művelet (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed9|Igen|Összes művelet (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|totalkeys|Igen|Kulcsok összesen|Darabszám|Maximum||ShardId|
|totalkeys0|Igen|Összes kulcs (szilánk 0)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys1|Igen|Összes kulcs (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys2|Igen|Összes kulcs (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys3|Igen|Összes kulcs (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys4|Igen|Összes kulcs (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys5|Igen|Összes kulcs (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys6|Igen|Összes kulcs (szilánk 6)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys7|Igen|Összes kulcs (szegmens 7)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys8|Igen|Összes kulcs (szilánk 8)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys9|Igen|Összes kulcs (szilánk 9)|Darabszám|Maximum||Nincsenek méretek|
|usedmemory|Igen|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemory0|Igen|Felhasznált memória (0. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory1|Igen|Felhasznált memória (1. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory2|Igen|Felhasznált memória (2. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory3|Igen|Felhasznált memória (3. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory4|Igen|Felhasznált memória (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory5|Igen|Felhasznált memória (5. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory6|Igen|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory7|Igen|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory8|Igen|Felhasznált memória (8. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory9|Igen|Felhasznált memória (10. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemorypercentage|Igen|Felhasznált memória százalékos aránya|Százalék|Maximum||ShardId|
|usedmemoryRss|Igen|Felhasznált memória RSS-címe|Bájt|Maximum||ShardId|
|usedmemoryRss0|Igen|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss1|Igen|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss2|Igen|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss3|Igen|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss4|Igen|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss5|Igen|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss6|Igen|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss7|Igen|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss8|Igen|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss9|Igen|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||Nincsenek méretek|


## <a name="microsoftcacheredisenterprise"></a>Microsoft. cache/redisEnterprise

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|cachehits|Igen|Gyorsítótár-találatok|Darabszám|Összesen||Nincsenek méretek|
|cacheLatency|Igen|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||InstanceId|
|cachemisses|Igen|Gyorsítótár-tévesztések|Darabszám|Összesen||InstanceId|
|cacheRead|Igen|Gyorsítótár-olvasás|BytesPerSecond|Maximum||InstanceId|
|cacheWrite|Igen|Gyorsítótár-írás|BytesPerSecond|Maximum||InstanceId|
|connectedclients|Igen|Csatlakoztatott ügyfelek|Darabszám|Maximum||InstanceId|
|Hibák|Igen|Hibák|Darabszám|Maximum||InstanceId, ErrorType|
|evictedkeys|Igen|Kizárt kulcsok|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys|Igen|Lejárt kulcsok|Darabszám|Összesen||Nincsenek méretek|
|getcommands|Igen|Lekérések|Darabszám|Összesen||Nincsenek méretek|
|operationsPerSecond|Igen|Műveletek száma másodpercenként|Darabszám|Maximum||Nincsenek méretek|
|percentProcessorTime|Igen|CPU|Százalék|Maximum||InstanceId|
|serverLoad|Igen|Kiszolgáló terhelése|Százalék|Maximum||Nincsenek méretek|
|setcommands|Igen|Halmazok|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed|Igen|Műveletek összesen|Darabszám|Összesen||Nincsenek méretek|
|totalkeys|Igen|Kulcsok összesen|Darabszám|Maximum||Nincsenek méretek|
|usedmemory|Igen|Felhasznált memória|Bájt|Maximum||Nincsenek méretek|
|usedmemorypercentage|Igen|Felhasznált memória százalékos aránya|Százalék|Maximum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Igen|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profilok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ByteHitRatio|Igen|Bájtos találatok aránya|Százalék|Átlag|A gyorsítótárból kiszolgált teljes bájtok aránya a teljes válasz bájtjaihoz képest|Végpont|
|OriginHealthPercentage|Igen|Forrás állapotának százaléka|Százalék|Átlag|A AFDX és a háttér közötti sikeres állapot-mintavételek százalékos aránya.|Forrás, OriginGroup|
|OriginLatency|Igen|Forrás késése|Ezredmásodpercben|Átlag|A AFDX Edge által a háttérbe irányuló kérés elküldésekor kiszámított idő, amíg a AFDX nem kapta meg a háttér utolsó válaszának bájtját.|Forrás, végpont|
|OriginRequestCount|Igen|Származási kérelmek száma|Darabszám|Összesen|A AFDX és a forrás között küldött kérések száma.|HttpStatus, HttpStatusGroup, forrás, végpont|
|Percentage4XX|Igen|4XX százalékos aránya|Százalék|Átlag|Az összes olyan ügyfél-kérelem százaléka, amelynél a válasz állapotkód 4XX|Végpont, ClientRegion, ClientCountry|
|Percentage5XX|Igen|5XX százalékos aránya|Százalék|Átlag|Az összes olyan ügyfél-kérelem százaléka, amelynél a válasz állapotkód 5XX|Végpont, ClientRegion, ClientCountry|
|RequestCount|Igen|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|RequestSize|Igen|Kérelem mérete|Bájt|Összesen|Az ügyfelektől a AFDX küldött kérelmekként küldött bájtok száma.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|ResponseSize|Igen|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|TotalLatency|Igen|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, végpont|
|WebApplicationFirewallRequestCount|Igen|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|Nem|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|Nem|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|
|Bejövő hálózat|Igen|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Igen|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|Nem|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|Nincsenek méretek|
|Lemez írási sebessége (bájt/s)|Nem|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|Nincsenek méretek|
|Bejövő hálózat|Igen|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincsenek méretek|
|Kimenő hálózat|Igen|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|Nincsenek méretek|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|Nem|Felhasznált kapacitás|Bájt|Átlag|Felhasznált fiókkapacitás|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|Nem|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Nem|Blobok száma|Darabszám|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|Igen|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiók Blob service lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|Nem|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|Nincsenek méretek|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|Nem|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|Nem|Fájlok száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Nem|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareQuota|Nem|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareSnapshotCount|Nem|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Nem|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Igen|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|QueueCount|Igen|Várólista száma|Darabszám|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|Nincsenek méretek|
|QueueMessageCount|Igen|Üzenetsor-üzenetek száma|Darabszám|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|Nincsenek méretek|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Igen|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|TableCount|Igen|Táblák száma|Darabszám|Átlag|A Storage-fiók Table service található tábla száma.|Nincsenek méretek|
|TableEntityCount|Igen|Tábla entitások száma|Darabszám|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|Nincsenek méretek|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BlockedCalls|Igen|Blokkolt hívások|Darabszám|Összesen|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|CharactersTrained|Igen|Betanított karakterek|Darabszám|Összesen|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTranslated|Igen|Lefordított karakterek|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|ClientErrors|Igen|Ügyfél-hibák|Darabszám|Összesen|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Igen|Kimenő adatvesztés|Bájt|Összesen|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Igen|Késés|Ezredmásodpercben|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|LearnedEvents|Igen|Megtanult események|Darabszám|Összesen|A megtanult események száma.|IsMatchBaseline, mód, RunId|
|MatchedRewards|Igen|Egyeztetett jutalmak|Darabszám|Összesen| Egyeztetett jutalmak száma.|IsMatchBaseline, mód, RunId|
|ObservedRewards|Igen|Megfigyelt jutalmak|Darabszám|Összesen|Megfigyelt jutalmak száma.|IsMatchBaseline, mód, RunId|
|ProcessedCharacters|Igen|Feldolgozott karakterek|Darabszám|Összesen|Karakterek száma.|ApiName, FeatureName, UsageChannel, régió|
|ProcessedTextRecords|Igen|Feldolgozott szöveges rekordok|Darabszám|Összesen|Szöveges rekordok száma|ApiName, FeatureName, UsageChannel, régió|
|Kiszolgálóhibái|Igen|Kiszolgálói hibák|Darabszám|Összesen|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|SpeechSessionDuration|Igen|Beszédfelismerési munkamenet időtartama|Másodperc|Összesen|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|SuccessfulCalls|Igen|Sikeres hívások|Darabszám|Összesen|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalCalls|Igen|Hívások összesen|Darabszám|Összesen|A hívások száma összesen|ApiName, OperationName, régió|
|TotalErrors|Igen|Összes hiba|Darabszám|Összesen|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|TotalTokenCalls|Igen|Jogkivonat-hívások összesen|Darabszám|Összesen|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|
|TotalTransactions|Igen|Tranzakciók összesen|Darabszám|Összesen|A tranzakciók száma összesen|Nincsenek méretek|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|Nem|Hitelesítési API-kérelmek|Darabszám|Darabszám|A kommunikációs szolgáltatások hitelesítési végpontján megjelenő kérelmek száma.|Művelet, StatusCode, StatusCodeClass|
|APIRequestChat|Igen|Csevegési API-kérelmek|Darabszám|Darabszám|A kommunikációs szolgáltatások csevegési végpontján megjelenő kérelmek száma.|Művelet, StatusCode, StatusCodeClass|
|APIRequestSMS|Igen|SMS API-kérelmek|Darabszám|Darabszám|A kommunikációs szolgáltatások SMS-végpontján megjelenő kérelmek száma.|Művelet, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft. számítás/cloudServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|RoleInstanceId, Szerepkörazonosítónak|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|RoleInstanceId, Szerepkörazonosítónak|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft. számítási/cloudServices/szerepkörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|RoleInstanceId, Szerepkörazonosítónak|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|RoleInstanceId, Szerepkörazonosítónak|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|RoleInstanceId, Szerepkörazonosítónak|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|RoleInstanceId, Szerepkörazonosítónak|


## <a name="microsoftcomputedisks"></a>Microsoft. számítás/lemezek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Összetett lemez olvasási sebessége (bájt/s)|Nem|Lemez olvasási sebessége (bájt/mp) (előzetes verzió)|Bájt|Átlag|A figyelési időszak során lemezről beolvasott bájtok másodpercenkénti száma, Megjegyzés: Ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez olvasási művelete/mp|Nem|Lemez olvasási művelete/mp (előzetes verzió)|Bájt|Átlag|A figyelési időszak során a lemezen végrehajtott olvasási IOs-műveletek száma, vegye figyelembe, hogy ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez írási sebessége (bájt/s)|Nem|Lemez írási sebessége (bájt/s) (előzetes verzió)|Bájt|Átlag|A megfigyelési időszak során lemezre írt bájtok másodpercenkénti száma: Ez a metrika előzetes verzióban érhető el, és az általánosan elérhetővé válás előtt változhat.||
|Összetett lemez írási műveletei másodpercenként|Nem|Lemez írási műveletei/mp (előzetes verzió)|Bájt|Átlag|A figyelési időszak során egy lemezen végrehajtott írási IOs-szám (a mérőszám előzetes verzióban érhető el), és az általánosan elérhetővé válás előtt változhat.||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Igen|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma. Csak a B sorozatú feltört virtuális gépeken érhető el|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Igen|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma. Csak a B sorozatú feltört virtuális gépeken érhető el|Nincsenek méretek|
|Adatlemez-sávszélesség felhasznált százaléka|Igen|Adatlemez-sávszélesség felhasznált százaléka|Százalék|Átlag|A percenként felhasznált adatlemez-sávszélesség százalékos aránya|LUN|
|Adatlemez IOPS felhasznált százaléka|Igen|Adatlemez IOPS felhasznált százaléka|Százalék|Átlag|Az adatlemez I/o-kihasználtságának százalékos aránya percenként|LUN|
|Adatlemez maximális burst sávszélessége|Igen|Adatlemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége adatlemez esetén|LUN|
|Adatlemez maximális burst IOPS|Igen|Adatlemez maximális burst IOPS|Darabszám|Átlag|A maximális IOPS adatlemez|LUN|
|Adatlemez várakozási sorának mélysége|Igen|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Igen|Adatlemez olvasási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez céljának sávszélessége|Igen|Adatlemez céljának sávszélessége|Darabszám|Átlag|Az alapszintű bájtok másodpercenkénti átviteli sebessége adatlemez nélkül elérhető|LUN|
|Adatlemez céljának IOPS|Igen|Adatlemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS adatlemeze feltörtség nélkül is elérhető|LUN|
|Adatlemez – a burst BPS-kreditek százalékos aránya|Igen|Adatlemez – a burst BPS-kreditek százalékos aránya|Százalék|Átlag|A felhasznált adatlemezek százalékos aránya (%)|LUN|
|Adatlemez – feltört IO-kreditek százalékos aránya|Igen|Adatlemez – feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az eddig felhasznált adatlemez burst I/O-kreditének százalékos aránya|LUN|
|Adatlemez-írási sebesség (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Igen|Adatlemez írási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Igen|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Igen|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Igen|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Igen|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Igen|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Igen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Igen|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Igen|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Százalék|Átlag|Az operációsrendszer-lemez által felhasznált I/o-műveletek százalékos aránya percenként|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Igen|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége az operációsrendszer-lemezen elvégezhető|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Igen|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Darabszám|Átlag|A IOPS operációsrendszer-lemez maximális méretének elérése a burst használatával|LUN|
|OS-lemez várakozási sorának mélysége|Igen|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Igen|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Darabszám|Átlag|A másodpercenkénti átlagos átviteli sebesség (bájt/mp)|LUN|
|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Igen|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS operációsrendszer-lemeze feltörtség nélkül is elérhető|LUN|
|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Igen|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Százalék|Átlag|Eddig használt operációsrendszer-lemez burst sávszélességének aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Igen|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének eddig felhasznált I/O-kreditek százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Igen|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Igen|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Igen|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Igen|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Igen|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|
|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Igen|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Igen|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS százaléka|Nincsenek méretek|
|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Igen|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Igen|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemezek százalékos aránya IOPS|Nincsenek méretek|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Igen|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma. Csak a B sorozatú feltört virtuális gépeken érhető el|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Igen|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma. Csak a B sorozatú feltört virtuális gépeken érhető el|Nincsenek méretek|
|Adatlemez-sávszélesség felhasznált százaléka|Igen|Adatlemez-sávszélesség felhasznált százaléka|Százalék|Átlag|A percenként felhasznált adatlemez-sávszélesség százalékos aránya|LUN, VMName|
|Adatlemez IOPS felhasznált százaléka|Igen|Adatlemez IOPS felhasznált százaléka|Százalék|Átlag|Az adatlemez I/o-kihasználtságának százalékos aránya percenként|LUN, VMName|
|Adatlemez maximális burst sávszélessége|Igen|Adatlemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége adatlemez esetén|LUN, VMName|
|Adatlemez maximális burst IOPS|Igen|Adatlemez maximális burst IOPS|Darabszám|Átlag|A maximális IOPS adatlemez|LUN, VMName|
|Adatlemez várakozási sorának mélysége|Igen|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|Adatlemez-olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Igen|Adatlemez olvasási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez céljának sávszélessége|Igen|Adatlemez céljának sávszélessége|Darabszám|Átlag|Az alapszintű bájtok másodpercenkénti átviteli sebessége adatlemez nélkül elérhető|LUN, VMName|
|Adatlemez céljának IOPS|Igen|Adatlemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS adatlemeze feltörtség nélkül is elérhető|LUN, VMName|
|Adatlemez – a burst BPS-kreditek százalékos aránya|Igen|Adatlemez – a burst BPS-kreditek százalékos aránya|Százalék|Átlag|A felhasznált adatlemezek százalékos aránya (%)|LUN, VMName|
|Adatlemez – feltört IO-kreditek százalékos aránya|Igen|Adatlemez – feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az eddig felhasznált adatlemez burst I/O-kreditének százalékos aránya|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Igen|Adatlemez írási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Bejövő forgalomfolyamok|Igen|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Igen|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő hálózat|Igen|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|VMName|
|Teljes hálózat|Igen|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat|Igen|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|VMName|
|Kimenő hálózat összesen|Igen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|
|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Igen|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Igen|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Százalék|Átlag|Az operációsrendszer-lemez által felhasznált I/o-műveletek százalékos aránya percenként|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Igen|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége az operációsrendszer-lemezen elvégezhető|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Igen|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Darabszám|Átlag|A IOPS operációsrendszer-lemez maximális méretének elérése a burst használatával|LUN, VMName|
|OS-lemez várakozási sorának mélysége|Igen|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Igen|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Darabszám|Átlag|A másodpercenkénti átlagos átviteli sebesség (bájt/mp)|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Igen|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS operációsrendszer-lemeze feltörtség nélkül is elérhető|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Igen|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Százalék|Átlag|Eddig használt operációsrendszer-lemez burst sávszélességének aránya|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Igen|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének eddig felhasznált I/O-kreditek százalékos aránya|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Igen|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|Kimenő folyamatok|Igen|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Igen|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Igen|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Igen|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|
|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Igen|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez sávszélességének százalékos aránya|VMName|
|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Igen|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS százaléka|VMName|
|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Igen|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemez sávszélességének százalékos aránya|VMName|
|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Igen|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemezek százalékos aránya IOPS|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. számítás/virtualMachineScaleSets/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Igen|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma. Csak a B sorozatú feltört virtuális gépeken érhető el|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Igen|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma. Csak a B sorozatú feltört virtuális gépeken érhető el|Nincsenek méretek|
|Adatlemez-sávszélesség felhasznált százaléka|Igen|Adatlemez-sávszélesség felhasznált százaléka|Százalék|Átlag|A percenként felhasznált adatlemez-sávszélesség százalékos aránya|LUN|
|Adatlemez IOPS felhasznált százaléka|Igen|Adatlemez IOPS felhasznált százaléka|Százalék|Átlag|Az adatlemez I/o-kihasználtságának százalékos aránya percenként|LUN|
|Adatlemez maximális burst sávszélessége|Igen|Adatlemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége adatlemez esetén|LUN|
|Adatlemez maximális burst IOPS|Igen|Adatlemez maximális burst IOPS|Darabszám|Átlag|A maximális IOPS adatlemez|LUN|
|Adatlemez várakozási sorának mélysége|Igen|Adatlemez várakozási sorának mélysége|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Igen|Adatlemez olvasási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez céljának sávszélessége|Igen|Adatlemez céljának sávszélessége|Darabszám|Átlag|Az alapszintű bájtok másodpercenkénti átviteli sebessége adatlemez nélkül elérhető|LUN|
|Adatlemez céljának IOPS|Igen|Adatlemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS adatlemeze feltörtség nélkül is elérhető|LUN|
|Adatlemez – a burst BPS-kreditek százalékos aránya|Igen|Adatlemez – a burst BPS-kreditek százalékos aránya|Százalék|Átlag|A felhasznált adatlemezek százalékos aránya (%)|LUN|
|Adatlemez – feltört IO-kreditek százalékos aránya|Igen|Adatlemez – feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az eddig felhasznált adatlemez burst I/O-kreditének százalékos aránya|LUN|
|Adatlemez-írási sebesség (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s)|BytesPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Igen|Adatlemez írási műveletei (művelet/s)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Igen|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Igen|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Igen|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Igen|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Igen|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Igen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Igen|OPERÁCIÓSRENDSZER-lemez sávszélességének kihasználtsága (%)|Százalék|Átlag|Az operációs rendszer lemezének percenként felhasznált sávszélességének százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Igen|OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka|Százalék|Átlag|Az operációsrendszer-lemez által felhasznált I/o-műveletek százalékos aránya percenként|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Igen|OPERÁCIÓSRENDSZER-lemez maximális burst sávszélessége|Darabszám|Átlag|A másodpercenkénti bájtok maximális átviteli sebessége az operációsrendszer-lemezen elvégezhető|LUN|
|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Igen|OPERÁCIÓSRENDSZER-lemez maximális burst IOPS|Darabszám|Átlag|A IOPS operációsrendszer-lemez maximális méretének elérése a burst használatával|LUN|
|OS-lemez várakozási sorának mélysége|Igen|OS-lemez várakozási sorának mélysége|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Igen|OPERÁCIÓSRENDSZER-lemez célként megadott sávszélessége|Darabszám|Átlag|A másodpercenkénti átlagos átviteli sebesség (bájt/mp)|LUN|
|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Igen|OPERÁCIÓSRENDSZER-lemez céljának IOPS|Darabszám|Átlag|Az alapszintű IOPS operációsrendszer-lemeze feltörtség nélkül is elérhető|LUN|
|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Igen|OPERÁCIÓSRENDSZER-lemez a burst BPS-kreditek százalékát használta|Százalék|Átlag|Eddig használt operációsrendszer-lemez burst sávszélességének aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Igen|OPERÁCIÓSRENDSZER-lemez feltört IO-kreditek százalékos aránya|Százalék|Átlag|Az operációs rendszer lemezének eddig felhasznált I/O-kreditek százalékos aránya|LUN|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Igen|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Igen|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Igen|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Igen|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Igen|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|
|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Igen|A virtuális gép gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Igen|A virtuális gép gyorsítótárazott IOPS felhasznált százalék|Százalék|Átlag|A virtuális gép által felhasznált gyorsítótárazott lemez IOPS százaléka|Nincsenek méretek|
|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Igen|A virtuális gép nem gyorsítótárazott sávszélességének százalékos aránya|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemez sávszélességének százalékos aránya|Nincsenek méretek|
|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Igen|A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka|Százalék|Átlag|A virtuális gép által felhasznált nem gyorsítótárazott lemezek százalékos aránya IOPS|Nincsenek méretek|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CpuUsage|Igen|CPU-használat|Darabszám|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Igen|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Igen|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|Nincsenek méretek|
|NetworkBytesTransmittedPerSecond|Igen|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|Nincsenek méretek|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Igen|AgentPool CPU-idő|Másodperc|Összesen|AgentPool CPU-idő másodpercben|Nincsenek méretek|
|RunDuration|Igen|Futtatás időtartama|Ezredmásodpercben|Összesen|Futtatás időtartama (ezredmásodpercben)|Nincsenek méretek|
|SuccessfulPullCount|Igen|Sikeres lekérések száma|Darabszám|Átlag|Sikeres Képkeresések száma|Nincsenek méretek|
|SuccessfulPushCount|Igen|Sikeres leküldések száma|Darabszám|Átlag|Sikeres leküldések száma|Nincsenek méretek|
|TotalPullCount|Igen|Lekérések száma összesen|Darabszám|Átlag|A lekérések száma összesen|Nincsenek méretek|
|TotalPushCount|Igen|Leküldések száma összesen|Darabszám|Átlag|Leküldések száma összesen|Nincsenek méretek|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|Nem|Fedélzeti kérelmek|Darabszám|Átlag|A jelenleg felhasznált fedélzeti kérelmek maximális száma a apiserver az elmúlt másodpercben|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|Nem|Fürt állapota|Darabszám|Átlag|Meghatározza, hogy a fürt automatikusan méretezhető-e a fürtön.||
|cluster_autoscaler_scale_down_in_cooldown|Nem|Leskálázási hűtő|Darabszám|Átlag|Meghatározza, hogy a leskálázás van-e a hűtőben – ebben az időkeretben egyetlen csomópont sem lesz eltávolítva||
|cluster_autoscaler_unneeded_nodes_count|Nem|Szükségtelen csomópontok|Darabszám|Átlag|A fürt auotscaler megjelöli ezeket a csomópontokat törlésre jelöltként, és végül törölve lesz||
|cluster_autoscaler_unschedulable_pods_count|Nem|Unschedulable hüvelyek|Darabszám|Átlag|A fürtben aktuálisan unschedulable hüvelyek száma||
|kube_node_status_allocatable_cpu_cores|Nem|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Darabszám|Átlag|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma||
|kube_node_status_allocatable_memory_bytes|Nem|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége||
|kube_node_status_condition|Nem|Különböző csomóponti feltételek állapota|Darabszám|Átlag|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|Nem|Hüvelyek száma fázis szerint|Darabszám|Átlag|Hüvelyek száma fázis szerint|fázis, névtér, Pod|
|kube_pod_status_ready|Nem|A hüvelyek száma üzemkész állapotban|Darabszám|Átlag|A hüvelyek száma üzemkész állapotban|névtér, Pod, Condition|
|node_cpu_usage_millicores|Igen|CPU-használat Millicores|MilliCores|Átlag|A CPU-kihasználtság összesített mérése a millicores-ben a fürtön keresztül|csomópont, nodepool|
|node_cpu_usage_percentage|Igen|CPU-használat százaléka|Százalék|Átlag|A fürt összesített átlagos CPU-kihasználtsága százalékban kifejezve|csomópont, nodepool|
|node_disk_usage_bytes|Igen|Felhasznált lemez bájtjai|Bájt|Átlag|Az eszköz által bájtban használt lemezterület|csomópont, nodepool, eszköz|
|node_disk_usage_percentage|Igen|Felhasznált lemez százaléka|Százalék|Átlag|Az eszköz által százalékban használt lemezterület|csomópont, nodepool, eszköz|
|node_memory_rss_bytes|Igen|Memória RSS-bájtjai|Bájt|Átlag|Bájtban használt tároló RSS-memória|csomópont, nodepool|
|node_memory_rss_percentage|Igen|Memória RSS-százaléka|Százalék|Átlag|A tárolóban használt RSS-memória százalékban|csomópont, nodepool|
|node_memory_working_set_bytes|Igen|Memória munkakészletének bájtjai|Bájt|Átlag|A tároló munkakészletének memóriában használt mérete|csomópont, nodepool|
|node_memory_working_set_percentage|Igen|Memória munkakészletének százalékos aránya|Százalék|Átlag|A tároló munkakészletének%-ban használt memóriája|csomópont, nodepool|
|node_network_in_bytes|Igen|Hálózati bájtban|Bájt|Átlag|Hálózati fogadott bájtok|csomópont, nodepool|
|node_network_out_bytes|Igen|Hálózati kimenő bájtok|Bájt|Átlag|Hálózati továbbított bájtok|csomópont, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRequests|Igen|Sikertelen kérelmek|Darabszám|Összesen|A rendelkezésre álló naplók beolvasása egyéni erőforrás-szolgáltatók számára|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Igen|Sikeres kérelmek|Darabszám|Összesen|Az egyéni szolgáltató által kezdeményezett sikeres kérések|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Igen|Rendelkezésre álló kapacitás|Bájt|Átlag|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|Nincsenek méretek|
|BytesUploadedToCloud|Igen|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|Nincsenek méretek|
|BytesUploadedToCloudPerShare|Igen|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Átlag|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|CloudReadThroughput|Igen|Felhőbeli letöltési sebesség|BytesPerSecond|Átlag|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudReadThroughputPerShare|Igen|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughput|Igen|Felhőbeli feltöltési sebesség|BytesPerSecond|Átlag|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudUploadThroughputPerShare|Igen|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|HyperVMemoryUtilization|Igen|Edge-számítás – memóriahasználat|Százalék|Átlag|Használatban lévő RAM mennyisége|InstanceName|
|HyperVVirtualProcessorUtilization|Igen|Edge-számítás – százalékos CPU|Százalék|Átlag|CPU-használat százalékos aránya|InstanceName|
|NICReadThroughput|Igen|Olvasási sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Igen|Írási átviteli sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|TotalCapacity|Igen|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|Nincsenek méretek|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. DataCollaboration/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DataAssetCount|Igen|Adategységek létrehozva|Darabszám|Maximum|Létrehozott adategységek száma|DataAssetName|
|PipelineCount|Igen|Létrehozott folyamatok|Darabszám|Maximum|Létrehozott folyamatok száma|PipelineName|
|ProposalCount|Igen|Létrehozott javaslatok|Darabszám|Maximum|Létrehozott javaslatok száma|ProposalName|
|ScriptCount|Igen|Létrehozott parancsfájlok|Darabszám|Maximum|Létrehozott parancsfájlok száma|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRuns|Igen|Sikertelen futtatások|Darabszám|Összesen||pipelineName, activityName|
|SuccessfulRuns|Igen|Sikeres futtatások|Darabszám|Összesen||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Igen|A megszakított tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivityFailedRuns|Igen|Sikertelen tevékenység-futtatási metrikák|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|Igen|A sikeres tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|FactorySizeInGbUnits|Igen|Gyári méret összesen (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|IntegrationRuntimeAvailableMemory|Igen|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableNodeNumber|Igen|Integrációs modul elérhető csomópontok száma|Darabszám|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Igen|Integration Runtime-várólista időtartama|Másodperc|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Igen|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeQueueLength|Igen|Integration Runtime-várólista hossza|Darabszám|Átlag||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Igen|Maximálisan engedélyezett gyári méret (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|MaxAllowedResourceCount|Igen|Engedélyezett entitások maximális száma|Darabszám|Maximum||Nincsenek méretek|
|PipelineCancelledRuns|Igen|Megszakított folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineElapsedTimeRuns|Igen|Az eltelt idő folyamata metrikákat futtat|Darabszám|Összesen||RunId, név|
|PipelineFailedRuns|Igen|Sikertelen folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineSucceededRuns|Igen|A folyamat sikeresen futtatja a metrikákat|Darabszám|Összesen||FailureType, név|
|ResourceCount|Igen|Entitások száma összesen|Darabszám|Maximum||Nincsenek méretek|
|SSISIntegrationRuntimeStartCancel|Igen|Megszakított SSIS Integration Runtime Start mérőszámai|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Igen|Nem sikerült SSIS az Integration Runtime indítási mérőszámait|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Igen|Sikeres SSIS Integration Runtime indítási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|Igen|Beragadt SSIS Integration Runtime leállítási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Igen|Sikeres SSIS integrációs modul leállítási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Igen|SSIS-csomag végrehajtási metrikáinak megszakítva|Darabszám|Összesen||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Igen|Sikertelen SSIS-csomag végrehajtási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Igen|Sikeres SSIS-csomag végrehajtási metrikái|Darabszám|Összesen||IntegrationRuntimeName|
|TriggerCancelledRuns|Igen|Megszakított trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerFailedRuns|Igen|Sikertelen trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerSucceededRuns|Igen|A sikeres trigger metrikákat futtat|Darabszám|Összesen||Név, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Igen|Megszakított AU-idő|Másodperc|Összesen|A megszakított feladatok teljes AU-ideje.|Nincsenek méretek|
|JobAUEndedFailure|Igen|Sikertelen AU-idő|Másodperc|Összesen|A sikertelen feladatok összes AU-ideje.|Nincsenek méretek|
|JobAUEndedSuccess|Igen|Sikeres AU-idő|Másodperc|Összesen|A sikeres feladatok teljes AU-ideje.|Nincsenek méretek|
|JobEndedCancelled|Igen|Megszakított feladatok|Darabszám|Összesen|Megszakított feladatok száma.|Nincsenek méretek|
|JobEndedFailure|Igen|Sikertelen feladatok|Darabszám|Összesen|Sikertelen feladatok száma.|Nincsenek méretek|
|JobEndedSuccess|Igen|Sikeres feladatok|Darabszám|Összesen|A sikeres feladatok száma.|Nincsenek méretek|
|JobStage|Igen|Feladatok a fázisban|Darabszám|Összesen|A feladatok száma az egyes fázisokban.|Nincsenek méretek|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DataRead|Igen|Olvasott információk|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|Nincsenek méretek|
|DataWritten|Igen|Írt adatértékek|Bájt|Összesen|A fiókba írt összes adatmennyiség.|Nincsenek méretek|
|ReadRequests|Igen|Olvasási kérelmek|Darabszám|Összesen|A fióknak küldött adatolvasási kérelmek száma.|Nincsenek méretek|
|TotalStorage|Igen|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|Nincsenek méretek|
|WriteRequests|Igen|Írási kérelmek|Darabszám|Összesen|A fiókra vonatkozó adatírási kérelmek száma.|Nincsenek méretek|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Igen|Fogadott megosztás sikertelen Pillanatképek|Darabszám|Darabszám|A fogadott megosztások száma sikertelen Pillanatképek a fiókban|Nincsenek méretek|
|FailedShareSynchronizations|Igen|Nem sikerült elküldeni a megosztást pillanatképeket|Darabszám|Darabszám|A fiókban sikertelenül továbbított megosztási Pillanatképek száma|Nincsenek méretek|
|ShareCount|Igen|Eljuttatott megosztások|Darabszám|Maximum|A fiókban eljuttatott megosztások száma|ShareName|
|ShareSubscriptionCount|Igen|Fogadott megosztások|Darabszám|Maximum|A fiókban fogadott megosztások száma|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Igen|A kapott megosztás sikeres pillanatképei|Darabszám|Darabszám|A fogadott megosztás sikeres pillanatképei a fiókban|Nincsenek méretek|
|SucceededShareSynchronizations|Igen|Az eljuttatott megosztás pillanatképei sikeresen megtörténtek|Darabszám|Darabszám|A fiókban sikeresen elvégezte a sikeres megosztási Pillanatképek számát|Nincsenek méretek|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Igen|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Igen|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Igen|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Igen|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Igen|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Igen|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|aborted_connections|Igen|Megszakított kapcsolatok|Darabszám|Összesen|Megszakított kapcsolatok|Nincsenek méretek|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Maximum|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Maximum|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|cpu_percent|Igen|Gazda CPU-százaléka|Százalék|Maximum|Gazda CPU-százaléka|Nincsenek méretek|
|io_consumption_percent|Igen|IO-százalék|Százalék|Maximum|IO-százalék|Nincsenek méretek|
|memory_percent|Igen|Gazdagép memória százaléka|Százalék|Maximum|Gazdagép memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Gazda hálózat kimenő|Bájt|Összesen|A gazda hálózati kimenő forgalom bájtban|Nincsenek méretek|
|network_bytes_ingress|Igen|Gazdagép hálózata|Bájt|Összesen|Gazdagép hálózati bejövő forgalom (bájt)|Nincsenek méretek|
|Lekérdezések|Igen|Lekérdezések|Darabszám|Összesen|Lekérdezések|Nincsenek méretek|
|replication_lag|Igen|Replikálás késése másodpercben|Másodperc|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|storage_limit|Igen|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Maximum|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Maximum|Felhasznált tárterület|Nincsenek méretek|
|total_connections|Igen|Összes kapcsolat|Darabszám|Összesen|Összes kapcsolat|Nincsenek méretek|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Igen|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Igen|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Igen|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Igen|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Igen|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Igen|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connections_succeeded|Igen|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_credits_consumed|Igen|Felhasznált CPU-kreditek|Darabszám|Átlag|Az adatbázis-kiszolgáló által felhasznált kreditek teljes száma|Nincsenek méretek|
|cpu_credits_remaining|Igen|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|disk_queue_depth|Igen|Lemez várólistájának mélysége|Darabszám|Átlag|Az adatlemezre vonatkozó függőben lévő I/O-műveletek száma|Nincsenek méretek|
|IOPS|Igen|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|maximum_used_transactionIDs|Igen|Maximálisan használt tranzakciós azonosítók|Darabszám|Átlag|Maximálisan használt tranzakciós azonosítók|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|read_iops|Igen|IOPS olvasása|Darabszám|Átlag|Adatlemez I/O-olvasási műveleteinek másodpercenkénti száma|Nincsenek méretek|
|read_throughput|Igen|Olvasási sebesség sebessége (bájt/s)|Darabszám|Átlag|A megfigyelési időszak során az adatlemezről másodpercenként olvasott bájtok száma|Nincsenek méretek|
|storage_free|Igen|Szabad tárterület|Bájt|Átlag|Szabad tárterület|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|
|txlogs_storage_used|Igen|A tranzakciós napló tárterülete használatban van|Bájt|Átlag|A tranzakciós napló tárterülete használatban van|Nincsenek méretek|
|write_iops|Igen|IOPS írása|Darabszám|Átlag|Adatlemez I/O-írási műveleteinek másodpercenkénti száma|Nincsenek méretek|
|write_throughput|Igen|Írási átviteli sebesség (bájt/s)|Darabszám|Átlag|A megfigyelési időszak során az adatlemezre másodpercenként írt bájtok száma|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft. DBForPostgreSQL/serverGroupsv2

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|ServerName|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|ServerName|
|IOPS|Igen|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|ServerName|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|ServerName|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|ServerName|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|ServerName|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|ServerName|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Igen|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|pg_replica_log_delay_in_bytes|Igen|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|Nincsenek méretek|
|pg_replica_log_delay_in_seconds|Igen|Replika késése|Másodperc|Maximum|Replika késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Igen|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Igen|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Igen|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Igen|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|IOPS|Igen|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdeviceselasticpools"></a>Microsoft. Devices/ElasticPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Igen|kért használati arány|Százalék|Átlag|kért használati arány|Nincsenek méretek|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. commands. kimenő. elhagyása. sikeres|Igen|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. Complete. success|Igen|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. elutasítás. sikeres|Igen|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. Methods. failure|Igen|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincsenek méretek|
|C2D. Methods. requestSize|Igen|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. responseSize|Igen|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. success|Igen|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincsenek méretek|
|C2D. Twin. Read. failure|Igen|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. size|Igen|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Read. success|Igen|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Update. failure|Igen|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. size|Igen|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|C2D. Twin. Update. success|Igen|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2DMessagesExpired|Igen|C2D üzenetek lejártak|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|Nincsenek méretek|
|konfigurációk|Igen|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|Nincsenek méretek|
|connectedDeviceCount|Igen|Csatlakoztatott eszközök|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. endpoints. kimenő. beépített. események|Igen|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. kimenő. eventHubs|Igen|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusQueues|Igen|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusTopics|Igen|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage|Igen|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Blobok|Igen|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Bytes|Igen|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincsenek méretek|
|D2C. endpoints. késleltetés. beépített. események|Igen|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. késleltetés. eventHubs|Igen|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusQueues|Igen|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusTopics|Igen|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. látencia. Storage|Igen|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincsenek méretek|
|D2C. telemetria. kimenő. eldobott|Igen|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincsenek méretek|
|D2C. telemetria. kimenő. tartalék|Igen|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincsenek méretek|
|D2C. telemetria. kimenő. érvénytelen|Igen|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincsenek méretek|
|D2C. telemetria. kimenő. árva|Igen|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincsenek méretek|
|D2C. telemetria. kimenő. sikeres|Igen|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincsenek méretek|
|D2C. telemetria. beáramló. allProtocol|Igen|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sendThrottle|Igen|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sikeres|Igen|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincsenek méretek|
|D2C. Twin. Read. failure|Igen|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. size|Igen|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|D2C. Twin. Read. success|Igen|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|D2C. Twin. Update. failure|Igen|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|D2C. Twin. Update. size|Igen|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|D2C. Twin. Update. success|Igen|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincsenek méretek|
|dailyMessageQuotaUsed|Igen|A felhasznált üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt összes üzenet száma|Nincsenek méretek|
|deviceDataUsage|Igen|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|deviceDataUsageV2|Igen|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|Devices. connectedDevices. allProtocol|Igen|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|Devices. totalDevices|Igen|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|EventGridDeliveries|Igen|Event Grid kézbesítések|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|Eredmény, EventType|
|EventGridLatency|Igen|Event Grid késés|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|feladatok. cancelJob. hiba|Igen|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. sikeres|Igen|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. kész|Igen|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. hiba|Igen|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. sikeres|Igen|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. hiba|Igen|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. sikeres|Igen|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. sikertelen|Igen|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek méretek|
|feladatok. listJobs. hiba|Igen|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincsenek méretek|
|feladatok. listJobs. sikeres|Igen|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. queryJobs. hiba|Igen|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincsenek méretek|
|feladatok. queryJobs. sikeres|Igen|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek méretek|
|RoutingDataSizeInBytesDelivered|Igen|Továbbítási üzenetek mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT hub által egy végpontra küldött üzenetek teljes mérete bájtban megadva. A Végpontneve és a EndpointType dimenzió használatával megtekintheti az üzenetek méretét a különböző végpontokra küldött bájtokban. A metrika értéke minden leküldött üzenetnél növekszik, beleértve azt is, hogy az üzenetet több végpontra is továbbítják-e, vagy ha az üzenet többször is ugyanarra a végpontra érkezik.|EndpointType, Végpontneve, RoutingSource|
|RoutingDeliveries|Igen|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Azon időpontok száma, IoT Hub az összes végpont felé irányuló üzenetek továbbítására történt kísérlet az Útválasztás használatával. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az eredmény dimenziót. Ha szeretné megtekinteni a hiba okát (például érvénytelen, eldobott vagy árva), használja az FailureReasonCategory dimenziót. A Végpontneve és a EndpointType dimenzióval is megtudhatja, hogy hány üzenet lett továbbítva a különböző végpontoknak. A metrika értéke eggyel nő az egyes kézbesítési kísérleteknél, beleértve azt is, hogy az üzenetet több végpontra kézbesítik-e, vagy ha az üzenet többször is ugyanarra a végpontra van kézbesítve.|EndpointType, Végpontneve, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Igen|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodpercben|Átlag|Az üzenet bejövő értékének átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria az üzeneteket a végpontba. A Végpontneve és a EndpointType méretek segítségével megismerheti a különböző végpontok késését.|EndpointType, Végpontneve, RoutingSource|
|tenantHub.requestedUsageRate|Igen|kért használati arány|Százalék|Átlag|kért használati arány|Nincsenek méretek|
|totalDeviceCount|Igen|Összes eszköz|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|twinQueries. hiba|Igen|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries.resultSize|Igen|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincsenek méretek|
|twinQueries. success|Igen|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincsenek méretek|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. commands. kimenő. elhagyása. sikeres|Igen|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. Complete. success|Igen|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. elutasítás. sikeres|Igen|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. Methods. failure|Igen|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincsenek méretek|
|C2D. Methods. requestSize|Igen|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. responseSize|Igen|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. success|Igen|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincsenek méretek|
|C2D. Twin. Read. failure|Igen|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. size|Igen|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Read. success|Igen|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Update. failure|Igen|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. size|Igen|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|C2D. Twin. Update. success|Igen|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2DMessagesExpired|Igen|C2D üzenetek lejártak|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|Nincsenek méretek|
|konfigurációk|Igen|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|Nincsenek méretek|
|connectedDeviceCount|Nem|Csatlakoztatott eszközök|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. endpoints. kimenő. beépített. események|Igen|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. kimenő. eventHubs|Igen|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusQueues|Igen|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusTopics|Igen|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage|Igen|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Blobok|Igen|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Bytes|Igen|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincsenek méretek|
|D2C. endpoints. késleltetés. beépített. események|Igen|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. késleltetés. eventHubs|Igen|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusQueues|Igen|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusTopics|Igen|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. látencia. Storage|Igen|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincsenek méretek|
|D2C. telemetria. kimenő. eldobott|Igen|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincsenek méretek|
|D2C. telemetria. kimenő. tartalék|Igen|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincsenek méretek|
|D2C. telemetria. kimenő. érvénytelen|Igen|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincsenek méretek|
|D2C. telemetria. kimenő. árva|Igen|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincsenek méretek|
|D2C. telemetria. kimenő. sikeres|Igen|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincsenek méretek|
|D2C. telemetria. beáramló. allProtocol|Igen|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sendThrottle|Igen|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sikeres|Igen|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincsenek méretek|
|D2C. Twin. Read. failure|Igen|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. size|Igen|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|D2C. Twin. Read. success|Igen|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|D2C. Twin. Update. failure|Igen|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|D2C. Twin. Update. size|Igen|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|D2C. Twin. Update. success|Igen|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincsenek méretek|
|dailyMessageQuotaUsed|Igen|A felhasznált üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt összes üzenet száma|Nincsenek méretek|
|deviceDataUsage|Igen|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|deviceDataUsageV2|Igen|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|Devices. connectedDevices. allProtocol|Igen|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|Devices. totalDevices|Igen|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|EventGridDeliveries|Igen|Event Grid kézbesítések|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|Eredmény, EventType|
|EventGridLatency|Igen|Event Grid késés|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|feladatok. cancelJob. hiba|Igen|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. sikeres|Igen|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. kész|Igen|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. hiba|Igen|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. sikeres|Igen|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. hiba|Igen|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. sikeres|Igen|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. sikertelen|Igen|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek méretek|
|feladatok. listJobs. hiba|Igen|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincsenek méretek|
|feladatok. listJobs. sikeres|Igen|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. queryJobs. hiba|Igen|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincsenek méretek|
|feladatok. queryJobs. sikeres|Igen|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek méretek|
|RoutingDataSizeInBytesDelivered|Igen|Továbbítási üzenetek mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT hub által egy végpontra küldött üzenetek teljes mérete bájtban megadva. A Végpontneve és a EndpointType dimenzió használatával megtekintheti az üzenetek méretét a különböző végpontokra küldött bájtokban. A metrika értéke minden leküldött üzenetnél növekszik, beleértve azt is, hogy az üzenetet több végpontra is továbbítják-e, vagy ha az üzenet többször is ugyanarra a végpontra érkezik.|EndpointType, Végpontneve, RoutingSource|
|RoutingDeliveries|Igen|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Azon időpontok száma, IoT Hub az összes végpont felé irányuló üzenetek továbbítására történt kísérlet az Útválasztás használatával. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az eredmény dimenziót. Ha szeretné megtekinteni a hiba okát (például érvénytelen, eldobott vagy árva), használja az FailureReasonCategory dimenziót. A Végpontneve és a EndpointType dimenzióval is megtudhatja, hogy hány üzenet lett továbbítva a különböző végpontoknak. A metrika értéke eggyel nő az egyes kézbesítési kísérleteknél, beleértve azt is, hogy az üzenetet több végpontra kézbesítik-e, vagy ha az üzenet többször is ugyanarra a végpontra van kézbesítve.|EndpointType, Végpontneve, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Igen|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodpercben|Átlag|Az üzenet bejövő értékének átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria az üzeneteket a végpontba. A Végpontneve és a EndpointType méretek segítségével megismerheti a különböző végpontok késését.|EndpointType, Végpontneve, RoutingSource|
|totalDeviceCount|Nem|Összes eszköz|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|twinQueries. hiba|Igen|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries.resultSize|Igen|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincsenek méretek|
|twinQueries. success|Igen|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincsenek méretek|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AttestationAttempts|Igen|Igazolási kísérletek|Darabszám|Összesen|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, állapot, protokoll|
|DeviceAssignments|Igen|Hozzárendelt eszközök|Darabszám|Összesen|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Igen|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, állapot|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ApiRequests|Igen|API-kérelmek|Darabszám|Összesen|A digitális Twins olvasási, írási, törlési és lekérdezési műveleteire vonatkozó API-kérések száma.|Művelet, hitelesítés, protokoll, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Igen|API-kérelmek meghibásodási aránya|Százalék|Átlag|A szolgáltatás által a példányhoz kapott API-kérelmek százalékos aránya, amely belső hibát (500) ad vissza a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteihez.|Művelet, hitelesítés, protokoll|
|ApiRequestsLatency|Igen|API-kérelmek késése|Ezredmásodpercben|Átlag|Az API-kérések válaszideje, ha a kérést az Azure digitális ikrek fogadják el, amíg a szolgáltatás nem küld sikeres/sikertelen eredményt a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteinek.|Művelet, hitelesítés, protokoll, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Igen|Számlázási API-műveletek|Darabszám|Összesen|Az Azure Digital Twins szolgáltatásban végrehajtott API-kérelmek számának számlázási mérőszáma.|MeterId|
|BillingMessagesProcessed|Igen|Feldolgozott számlázási üzenetek|Darabszám|Összesen|Számlázási metrika az Azure digitális Twins-ból külső végpontokra küldött üzenetek számának megadásához.|MeterId|
|BillingQueryUnits|Igen|Számlázási lekérdezési egységek|Darabszám|Összesen|A lekérdezési egységek száma, a szolgáltatások erőforrás-felhasználásának belsőleg számított mértéke, a lekérdezések végrehajtásához felhasználva.|MeterId|
|IngressEvents|Igen|Bejövő események|Darabszám|Összesen|A bejövő telemetria események száma az Azure digitális Twins-ban.|Eredmény|
|IngressEventsFailureRate|Igen|Bejövő események meghibásodási aránya|Százalék|Átlag|Azon bejövő telemetria-események százalékos aránya, amelyek esetében a szolgáltatás belső hibát (500) ad vissza.|Nincsenek méretek|
|IngressEventsLatency|Igen|Bejövő események késése|Ezredmásodpercben|Átlag|Az az idő, amikor egy esemény megérkezik, amikor készen áll az Azure digitális ikrek általi egressed, és ekkor a szolgáltatás sikeres/sikertelen eredményt küld.|Eredmény|
|ModelCount|Igen|Modellek száma|Darabszám|Összesen|Az Azure Digital Twins-példány modelljeinek száma összesen. Ezzel a metrikával meghatározhatja, hogy az adott példányon engedélyezett modellek maximális száma eléri-e a szolgáltatási korlátot.|Nincsenek méretek|
|Útválasztás|Igen|Átirányított üzenetek|Darabszám|Összesen|Az Azure-szolgáltatásokhoz (például Event hub, Service Bus vagy Event Grid) továbbított üzenetek száma.|EndpointType, eredmény|
|RoutingFailureRate|Igen|Útválasztási hibák aránya|Százalék|Átlag|Az olyan események százalékos aránya, amelyek az Azure digitális ikrekről egy végponti Azure-szolgáltatásba (például az Event hub, Service Bus vagy Event Grid) való továbbítás során hibát eredményeznek.|EndpointType|
|RoutingLatency|Igen|Útválasztási késés|Ezredmásodpercben|Átlag|Az Azure Digital Twins-ból átirányított esemény között eltelt idő a végponti Azure-szolgáltatásba (például Event hub, Service Bus vagy Event Grid) való közzétételkor.|EndpointType, eredmény|
|TwinCount|Igen|Dupla darabszám|Darabszám|Összesen|Az ikrek teljes száma az Azure Digital Twins-példányban. Ezzel a metrikával meghatározhatja, hogy a szolgáltatás legfeljebb hány ikrek számára engedélyezett.|Nincsenek méretek|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AddRegion|Igen|Régió hozzáadva|Darabszám|Darabszám|Régió hozzáadva|Region|
|AutoscaleMaxThroughput|Nem|Maximális átviteli sebesség|Darabszám|Maximum|Maximális átviteli sebesség|DatabaseName, CollectionName|
|AvailableStorage|Nem|elavult Rendelkezésre álló tár|Bájt|Összesen|A "rendelkezésre álló tár" el lesz távolítva a Azure Monitorról a 2023 szeptember végén. Cosmos DB a gyűjtemény tárterületének mérete mostantól korlátlan. Az egyetlen korlátozás az, hogy az egyes logikai partíciós kulcsok tárolási mérete 20 GB. A PartitionKeyStatistics a diagnosztikai naplóban engedélyezheti a felső partíciós kulcsok tárolási felhasználásának megismeréséhez. A Cosmos DB tárolási kvótával kapcsolatos további információkért tekintse meg ezt a dokumentációt https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Az elavulás után a fennmaradó riasztási szabályok az elavult metrika esetében még mindig le lesznek tiltva az elavult dátum után.|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Nem|Cassandra-kapcsolatok bezárása|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|Régió, ClosureReason|
|CassandraConnectorAvgReplicationLatency|Nem|Cassandra-összekötő átlagos ReplicationLatency|Ezredmásodpercben|Átlag|Cassandra-összekötő átlagos ReplicationLatency|Nincsenek méretek|
|CassandraConnectorReplicationHealthStatus|Nem|Cassandra Connector replikációs állapotának állapota|Darabszám|Darabszám|Cassandra Connector replikációs állapotának állapota|NotStarted, ReplicationInProgress, hiba|
|CassandraKeyspaceCreate|Nem|Cassandra-terület létrehozva|Darabszám|Darabszám|Cassandra-terület létrehozva|ResourceName |
|CassandraKeyspaceDelete|Nem|Cassandra szóköz törölve|Darabszám|Darabszám|Cassandra szóköz törölve|ResourceName |
|CassandraKeyspaceThroughputUpdate|Nem|Cassandra Space-átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Space-átviteli sebesség frissítve|ResourceName |
|CassandraKeyspaceUpdate|Nem|Cassandra Space frissítve|Darabszám|Darabszám|Cassandra Space frissítve|ResourceName |
|CassandraRequestCharges|Nem|Cassandra-kérelmek díjai|Darabszám|Összesen|A Cassandra-kérelmekhez felhasznált RUs|DatabaseName, CollectionName, régió, OperationType, ResourceType|
|CassandraRequests|Nem|Cassandra-kérelmek|Darabszám|Darabszám|A végrehajtott Cassandra-kérelmek száma|DatabaseName, CollectionName, régió, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Nem|Cassandra-tábla létrehozva|Darabszám|Darabszám|Cassandra-tábla létrehozva|ResourceName, ChildResourceName, |
|CassandraTableDelete|Nem|Cassandra-tábla törölve|Darabszám|Darabszám|Cassandra-tábla törölve|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|Nem|Cassandra Table átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Table átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|CassandraTableUpdate|Nem|Cassandra-tábla frissítve|Darabszám|Darabszám|Cassandra-tábla frissítve|ResourceName, ChildResourceName, |
|CreateAccount|Igen|Fiók létrehozva|Darabszám|Darabszám|Fiók létrehozva|Nincsenek méretek|
|DataUsage|Nem|Adathasználat|Bájt|Összesen|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DedicatedGatewayRequests|Igen|DedicatedGatewayRequests|Darabszám|Darabszám|Kérések a dedikált átjárón|DatabaseName, CollectionName, CacheExercised, OperationName, régió|
|DeleteAccount|Igen|Fiók törölve|Darabszám|Darabszám|Fiók törölve|Nincsenek méretek|
|DocumentCount|Nem|Dokumentumok száma|Darabszám|Összesen|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|Nem|Dokumentum kvótája|Bájt|Összesen|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|GremlinDatabaseCreate|Nem|Gremlin-adatbázis létrehozva|Darabszám|Darabszám|Gremlin-adatbázis létrehozva|ResourceName |
|GremlinDatabaseDelete|Nem|Gremlin-adatbázis törölve|Darabszám|Darabszám|Gremlin-adatbázis törölve|ResourceName |
|GremlinDatabaseThroughputUpdate|Nem|Gremlin adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin adatbázis átviteli sebessége frissítve|ResourceName |
|GremlinDatabaseUpdate|Nem|Gremlin-adatbázis frissítve|Darabszám|Darabszám|Gremlin-adatbázis frissítve|ResourceName |
|GremlinGraphCreate|Nem|Gremlin gráf létrehozva|Darabszám|Darabszám|Gremlin gráf létrehozva|ResourceName, ChildResourceName, |
|GremlinGraphDelete|Nem|Gremlin gráf törölve|Darabszám|Darabszám|Gremlin gráf törölve|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|Nem|Gremlin gráf átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin gráf átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|Nem|Gremlin gráf frissítve|Darabszám|Darabszám|Gremlin gráf frissítve|ResourceName, ChildResourceName, |
|IndexUsage|Nem|Indexhasználat|Bájt|Összesen|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|IntegratedCacheEvictedEntriesSize|Nem|IntegratedCacheEvictedEntriesSize|Bájt|Átlag|Az integrált gyorsítótárból kizárt bejegyzések mérete|CacheType, régió|
|IntegratedCacheHitRate|Nem|IntegratedCacheHitRate|Százalék|Átlag|Gyorsítótár-találatok aránya az integrált gyorsítótárak esetében|CacheType, régió|
|IntegratedCacheSize|Nem|IntegratedCacheSize|Bájt|Átlag|A dedikált átjáró-kérelmekhez tartozó integrált gyorsítótárak mérete|CacheType, régió|
|IntegratedCacheTTLExpirationCount|Nem|IntegratedCacheTTLExpirationCount|Darabszám|Átlag|Az integrált gyorsítótárból a TTL lejárta miatt eltávolított bejegyzések száma|CacheType, régió|
|MetadataRequests|Nem|Metaadat-kérelmek|Darabszám|Darabszám|A metaadat-kérelmek száma. Cosmos DB az egyes fiókok rendszermetaadat-gyűjteményét kezeli, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, |
|MongoCollectionCreate|Nem|Mongo-gyűjtemény létrehozva|Darabszám|Darabszám|Mongo-gyűjtemény létrehozva|ResourceName, ChildResourceName, |
|MongoCollectionDelete|Nem|Mongo-gyűjtemény törölve|Darabszám|Darabszám|Mongo-gyűjtemény törölve|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|Nem|A Mongo-gyűjtési átviteli sebesség frissítve|Darabszám|Darabszám|A Mongo-gyűjtési átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|Nem|Mongo-gyűjtemény frissítve|Darabszám|Darabszám|Mongo-gyűjtemény frissítve|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|Nem|Mongo-adatbázis törölve|Darabszám|Darabszám|Mongo-adatbázis törölve|ResourceName |
|MongoDatabaseThroughputUpdate|Nem|Mongo adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Mongo adatbázis átviteli sebessége frissítve|ResourceName |
|MongoDBDatabaseCreate|Nem|Mongo-adatbázis létrehozva|Darabszám|Darabszám|Mongo-adatbázis létrehozva|ResourceName |
|MongoDBDatabaseUpdate|Nem|Mongo-adatbázis frissítve|Darabszám|Darabszám|Mongo-adatbázis frissítve|ResourceName |
|MongoRequestCharge|Igen|Mongo-kérelem díja|Darabszám|Összesen|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequests|Igen|Mongo kérelmek|Darabszám|Darabszám|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequestsCount|Nem|elavult Mongo kérelmek gyakorisága|CountPerSecond|Átlag|Mongo kérelmek száma másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsDelete|Nem|elavult Mongo-törlési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-törlési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsInsert|Nem|elavult Mongo-beszúrási kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-beszúrási szám másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsQuery|Nem|elavult Mongo-lekérdezési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-lekérdezési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsUpdate|Nem|elavult Mongo-frissítési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo frissítési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|NormalizedRUConsumption|Nem|Normalizált RU-felhasználás|Százalék|Maximum|Maximális RU-százalék/perc|CollectionName, DatabaseName, régió, PartitionKeyRangeId|
|ProvisionedThroughput|Nem|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|DatabaseName, CollectionName|
|RegionFailover|Igen|A régió feladatátvétele megtörtént|Darabszám|Darabszám|A régió feladatátvétele megtörtént|Nincsenek méretek|
|RemoveRegion|Igen|Régió eltávolítva|Darabszám|Darabszám|Régió eltávolítva|Region|
|ReplicationLatency|Igen|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion, TargetRegion|
|ServerSideLatency|Nem|Kiszolgálóoldali késés|Ezredmásodpercben|Átlag|Kiszolgálóoldali késés|DatabaseName, CollectionName, régió, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Nem|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|Nincsenek méretek|
|SqlContainerCreate|Nem|SQL-tároló létrehozva|Darabszám|Darabszám|SQL-tároló létrehozva|ResourceName, ChildResourceName, |
|SqlContainerDelete|Nem|SQL-tároló törölve|Darabszám|Darabszám|SQL-tároló törölve|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|Nem|SQL-tároló átviteli sebessége frissítve|Darabszám|Darabszám|SQL-tároló átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|SqlContainerUpdate|Nem|SQL-tároló frissítve|Darabszám|Darabszám|SQL-tároló frissítve|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|Nem|SQL-adatbázis létrehozva|Darabszám|Darabszám|SQL-adatbázis létrehozva|ResourceName |
|SqlDatabaseDelete|Nem|SQL-adatbázis törölve|Darabszám|Darabszám|SQL-adatbázis törölve|ResourceName |
|SqlDatabaseThroughputUpdate|Nem|Az SQL Database átviteli sebessége frissítve|Darabszám|Darabszám|Az SQL Database átviteli sebessége frissítve|ResourceName |
|SqlDatabaseUpdate|Nem|SQL-adatbázis frissítve|Darabszám|Darabszám|SQL-adatbázis frissítve|ResourceName |
|TableTableCreate|Nem|AzureTable tábla létrehozva|Darabszám|Darabszám|AzureTable tábla létrehozva|ResourceName |
|TableTableDelete|Nem|AzureTable-tábla törölve|Darabszám|Darabszám|AzureTable-tábla törölve|ResourceName |
|TableTableThroughputUpdate|Nem|AzureTable-tábla átviteli sebessége frissítve|Darabszám|Darabszám|AzureTable-tábla átviteli sebessége frissítve|ResourceName |
|TableTableUpdate|Nem|AzureTable tábla frissítve|Darabszám|Darabszám|AzureTable tábla frissítve|ResourceName |
|TotalRequests|Igen|Összes kérelem|Darabszám|Darabszám|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|TotalRequestUnits|Igen|Kérelmek összes egysége|Darabszám|Összesen|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|UpdateAccountKeys|Igen|A fiók kulcsai frissítve|Darabszám|Darabszám|A fiók kulcsai frissítve|KeyType|
|UpdateAccountNetworkSettings|Igen|A fiók hálózati beállításai frissítve|Darabszám|Darabszám|A fiók hálózati beállításai frissítve|Nincsenek méretek|
|UpdateAccountReplicationSettings|Igen|A fiók replikációs beállításainak frissítése megtörtént|Darabszám|Darabszám|A fiók replikációs beállításainak frissítése megtörtént|Nincsenek méretek|
|UpdateDiagnosticsSettings|Nem|Fiók diagnosztikai beállításainak frissítése|Darabszám|Darabszám|Fiók diagnosztikai beállításainak frissítése|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Igen|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincsenek méretek|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincsenek méretek|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek méretek|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Igen|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Igen|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Igen|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/fürtök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.||
|AvailableMemory|Nem|Szabad memória|Százalék|Maximum|Az Event hub-fürt rendelkezésre álló memóriája a teljes memória százalékában.|Szerepkör|
|CaptureBacklog|Nem|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.||
|CapturedBytes|Nem|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.||
|CapturedMessages|Nem|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.||
|ConnectionsClosed|Nem|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.||
|ConnectionsOpened|Nem|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.||
|CPU|Nem|CPU|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|IncomingBytes|Igen|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.||
|IncomingMessages|Igen|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.||
|IncomingRequests|Igen|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.||
|OutgoingBytes|Igen|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.||
|OutgoingMessages|Igen|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.||
|QuotaExceededErrors|Nem|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|Operationresult tevékenységen|
|Kiszolgálóhibái|Nem|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|Operationresult tevékenységen|
|Méret|Nem|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|Szerepkör|
|SuccessfulRequests|Nem|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|Operationresult tevékenységen|
|ThrottledRequests|Nem|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|Operationresult tevékenységen|
|UserErrors|Nem|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|Operationresult tevékenységen|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.||
|CaptureBacklog|Nem|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedBytes|Nem|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|CapturedMessages|Nem|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|ConnectionsClosed|Nem|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|Nem|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|EHABL|Igen|Archivált várakozó üzenetek (elavult)|Darabszám|Összesen|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)||
|EHAMBS|Igen|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összesen|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)||
|EHAMSGS|Igen|Archivált üzenetek (elavult)|Darabszám|Összesen|Event hub archivált üzenetek egy névtérben (elavult)||
|EHINBYTES|Igen|Bejövő bájtok (elavult)|Bájt|Összesen|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)||
|EHINMBS|Igen|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)||
|EHINMSGS|Igen|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtér összes bejövő üzenete (elavult)||
|EHOUTBYTES|Igen|Kimenő bájtok (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)||
|EHOUTMBS|Igen|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)||
|EHOUTMSGS|Igen|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér összes kimenő üzenete (elavult)||
|FAILREQ|Igen|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)||
|IncomingBytes|Igen|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|IncomingMessages|Igen|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|IncomingRequests|Igen|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|EntityName|
|INMSGS|Igen|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)||
|INREQS|Igen|Bejövő kérelmek (elavult)|Darabszám|Összesen|Egy névtér összes bejövő küldési kérelme (elavult)||
|INTERer|Igen|Belső kiszolgálói hibák (elavult)|Darabszám|Összesen|Névtér összes belső kiszolgálóhiba (elavult)||
|MISCERR|Igen|Egyéb hibák (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)||
|OutgoingBytes|Igen|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|OutgoingMessages|Igen|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|OUTMSGS|Igen|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)||
|QuotaExceededErrors|Nem|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName, Operationresult tevékenységen|
|Kiszolgálóhibái|Nem|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName, Operationresult tevékenységen|
|Méret|Nem|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|EntityName|
|SuccessfulRequests|Nem|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName, Operationresult tevékenységen|
|SUCCREQ|Igen|Sikeres kérések (elavult)|Darabszám|Összesen|A névtér összes sikeres kérelme (elavult)||
|SVRBSY|Igen|Kiszolgáló által foglalt hibák (elavult)|Darabszám|Összesen|Egy névtér összes kiszolgáló által foglalt hibája (elavult)||
|ThrottledRequests|Nem|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|UserErrors|Nem|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|EntityName, Operationresult tevékenységen|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/fürtök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Igen|Kategorizált átjáró-kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Igen|Átjáróra vonatkozó kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Igen|REST proxy fogyasztói RequestThroughput|CountPerSecond|Összesen|A Kafka REST proxy iránti fogyasztói kérelmek száma|Gép, témakör|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Igen|REST proxy fogyasztó sikertelen kérelmek|CountPerSecond|Összesen|Fogyasztói kérelmekre vonatkozó kivételek|Gép, témakör|
|KafkaRestProxy.ConsumerRequestTime.p95|Igen|REST proxy fogyasztói RequestLatency|Ezredmásodpercben|Átlag|Üzenet késése egy fogyasztói kérelemben a Kafka REST proxyn keresztül|Gép, témakör|
|KafkaRestProxy.ConsumerRequestWaitingInQueueTime.p95|Igen|REST-proxy fogyasztói kérelmének várakozó fájlok|Ezredmásodpercben|Átlag|A fogyasztói REST proxy várólistájának hossza|Gép, témakör|
|KafkaRestProxy.MessagesIn.m1_delta|Igen|REST proxy gyártó MessageThroughput|CountPerSecond|Összesen|Termelői üzenetek száma a Kafka REST proxyn keresztül|Gép, témakör|
|KafkaRestProxy.MessagesOut.m1_delta|Igen|REST proxy fogyasztói MessageThroughput|CountPerSecond|Összesen|Fogyasztói üzenetek száma a Kafka REST proxyn keresztül|Gép, témakör|
|KafkaRestProxy.OpenConnections|Igen|REST-proxy ConcurrentConnections|Darabszám|Összesen|A Kafka REST proxyn keresztüli egyidejű kapcsolatok száma|Gép, témakör|
|KafkaRestProxy.ProducerRequest.m1_delta|Igen|REST proxy gyártó RequestThroughput|CountPerSecond|Összesen|A Kafka REST proxyhoz tartozó termelői kérelmek száma|Gép, témakör|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Igen|REST-proxy előállítójának sikertelen kérelmei|CountPerSecond|Összesen|Termelői kérelmekre vonatkozó kivételek|Gép, témakör|
|KafkaRestProxy.ProducerRequestTime.p95|Igen|REST proxy gyártó RequestLatency|Ezredmásodpercben|Átlag|Üzenet késése egy gyártói kérelemben a Kafka REST proxyn keresztül|Gép, témakör|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|Igen|REST-proxy gyártói kérelmének várakozó fájlok|Ezredmásodpercben|Átlag|Gyártó REST proxy-várólista hossza|Gép, témakör|
|NumActiveWorkers|Igen|Aktív feldolgozók száma|Darabszám|Maximum|Aktív feldolgozók száma|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/szolgáltatások

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A szolgáltatás rendelkezésre állási sebessége.|Nincsenek méretek|
|CosmosDbCollectionSize|Igen|Cosmos DB gyűjtemény mérete|Bájt|Összesen|A háttérbeli Cosmos DB gyűjtemény mérete bájtban megadva.|Nincsenek méretek|
|CosmosDbIndexSize|Igen|Cosmos DB index mérete|Bájt|Összesen|A Cosmos DB gyűjtemény indexének mérete bájtban kifejezve.|Nincsenek méretek|
|CosmosDbRequestCharge|Igen|RU-használat Cosmos DB|Darabszám|Összesen|A szolgáltatásra irányuló kérések RU-használata Cosmos DB.|Művelet, ResourceType|
|CosmosDbRequests|Igen|Szolgáltatás-Cosmos DB kérelmek|Darabszám|Sum|A szolgáltatás által Cosmos DBre küldött kérelmek teljes száma.|Művelet, ResourceType|
|CosmosDbThrottleRate|Igen|Szolgáltatási Cosmos DB szabályozásának sebessége|Darabszám|Sum|A szolgáltatás Cosmos DBi biztonsági mentésére adott válaszok teljes 429 száma.|Művelet, ResourceType|
|IoTConnectorDeviceEvent|Igen|Bejövő üzenetek száma|Darabszám|Sum|Az Azure IoT-összekötő által az FHIR számára fogadott üzenetek teljes száma a normalizálás előtt.|Művelet, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Igen|Átlagos normalizálás szakasz késése|Ezredmásodpercben|Átlag|Egy esemény betöltési idejének és a normalizálás során az esemény feldolgozásának időpontjának átlagos ideje.|Művelet, ConnectorName|
|IoTConnectorMeasurement|Igen|Mérések száma|Darabszám|Sum|A FHIR Azure IoT-összekötő FHIR átalakítási szakasza által fogadott Normalizált érték-olvasási értékek száma.|Művelet, ConnectorName|
|IoTConnectorMeasurementGroup|Igen|Üzenetszöveg-csoportok száma|Darabszám|Sum|A FHIR-átalakítási fázis által generált típusok, eszközök, páciensek és beállított időszakok egyedi csoportosítási csoportjainak teljes száma.|Művelet, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Igen|Csoportosítási szakasz átlagos késése|Ezredmásodpercben|Átlag|Az az időtartam, amikor az IoT-összekötő megkapta az eszközre vonatkozó adatgyűjtést, és amikor az FHIR-átalakítási fázis dolgozza fel az adatfeldolgozást.|Művelet, ConnectorName|
|IoTConnectorNormalizedEvent|Igen|Normalizált üzenetek száma|Darabszám|Sum|A FHIR-hez készült Azure IoT-összekötő normalizálás szakasza által leképezett megfeleltetett normalizált értékek teljes száma.|Művelet, ConnectorName|
|IoTConnectorTotalErrors|Igen|Hibák száma összesen|Darabszám|Sum|Az Azure IoT-összekötő által a FHIR számára naplózott hibák teljes száma|Név, művelet, ErrorType, ErrorSeverity, ConnectorName|
|TotalErrors|Igen|Összes hiba|Darabszám|Sum|A szolgáltatás által észlelt belső kiszolgálói hibák teljes száma.|Protokoll, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Igen|Teljes késés|Ezredmásodpercben|Átlag|A szolgáltatás válaszának késése.|Protokoll|
|TotalRequests|Igen|Összes kérelem|Darabszám|Sum|A szolgáltatás által fogadott kérelmek teljes száma.|Protokoll|


## <a name="microsofthybridnetworknetworkfunctions"></a>Microsoft. hybridnetwork/networkfunctions

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Igen|CPU átlagos kihasználtsága|Százalék|Átlag|A virtuális processzor kihasználtságának átlagos százalékos aránya egy percen belül. A virtuális CPU teljes száma az SKU-definícióban a felhasználó által konfigurált értéken alapul. A további szűrést az SKU-ban definiált RoleName alapján lehet alkalmazni.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>Microsoft. hybridnetwork/virtualnetworkfunctions

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Igen|CPU átlagos kihasználtsága|Százalék|Átlag|A virtuális processzor kihasználtságának átlagos százalékos aránya egy percen belül. A virtuális CPU teljes száma az SKU-definícióban a felhasználó által konfigurált értéken alapul. A további szűrést az SKU-ban definiált RoleName alapján lehet alkalmazni.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/autoscalesettings

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|MetricThreshold|Igen|Metrika küszöbértéke|Darabszám|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Igen|Megfigyelt kapacitás|Darabszám|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.||
|ObservedMetricValue|Igen|Megfigyelt metrika értéke|Darabszám|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|ScaleActionsInitiated|Igen|Elindított skálázási műveletek|Darabszám|Összesen|A skálázási művelet iránya.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Igen|Rendelkezésre állás|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|Nem|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Igen|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Igen|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|Nincsenek méretek|
|browserTimings/processingDuration|Igen|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|Nincsenek méretek|
|browserTimings/receiveDuration|Igen|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|Nincsenek méretek|
|browserTimings/sendDuration|Igen|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincsenek méretek|
|browserTimings/totalDuration|Igen|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|Nincsenek méretek|
|függőségek/darabszám|Nem|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/időtartam|Igen|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/sikertelen|Nem|Függőségi hívások hibái|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|függőség/típus, függőség/performanceBucket, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kivételek/böngésző|Nem|Böngészőkivételek|Darabszám|Darabszám|A böngészőben fellépő nem kezelt kivételek száma.|felhő/roleName|
|kivételek/darabszám|Igen|Kivételek|Darabszám|Darabszám|Az összes nem kezelt kivétel összesített száma.|felhő/roleName, felhő/roleInstance, ügyfél/típus|
|kivételek/kiszolgáló|Nem|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|felhő/roleName, felhő/roleInstance|
|Oldalmegtekintések/darabszám|Igen|Lapok nézetei|Darabszám|Darabszám|Lapok nézeteinek száma.|művelet/szintetikus, felhő/roleName|
|Oldalmegtekintések/időtartam|Igen|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus, felhő/roleName|
|performanceCounters/exceptionsPerSecond|Igen|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Igen|Igénybe vehető memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Igen|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Igen|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Igen|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Igen|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|performanceCounters/requestExecutionTime|Igen|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|Igen|HTTP-kérelmek az alkalmazás-várólistában|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájának hossza|felhő/roleInstance|
|performanceCounters/requestsPerSecond|Igen|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|felhő/roleInstance|
|kérelmek/darabszám|Nem|Kiszolgálói kérelmek|Darabszám|Darabszám|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/időtartam|Igen|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|Nem|Sikertelen kérelmek|Darabszám|Darabszám|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések >= 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|Nem|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|nyomkövetés/darabszám|Igen|Hívásláncok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|nyomkövetés/severityLevel, művelet/szintetikus, felhő/roleName, felhő/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. Property. Read. failure|Igen|Nem sikerült beolvasni az eszköz tulajdonságát IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok összes olvasásának száma|Nincsenek méretek|
|C2D. Property. Read. success|Igen|Az eszköz tulajdonságainak olvasása IoT Central|Darabszám|Összesen|Az IoT Centralról kezdeményezett sikeres tulajdonságok száma|Nincsenek méretek|
|C2D. Property. Update. hiba|Igen|Nem sikerült frissíteni az eszköz tulajdonságait IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok frissítéseinek száma|Nincsenek méretek|
|C2D. Property. Update. success|Igen|Az eszköz tulajdonságainak frissítése IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett összes sikeres tulajdonság-frissítés száma|Nincsenek méretek|
|connectedDeviceCount|Nem|Csatlakoztatott eszközök összesen|Darabszám|Átlag|IoT Centralhoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. Property. Read. failure|Igen|Nem sikerült beolvasni az eszköz tulajdonságait az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikertelen tulajdonságok összes olvasásának száma|Nincsenek méretek|
|D2C. Property. Read. success|Igen|Az eszköz tulajdonságainak olvasása az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikeres tulajdonságok összes olvasásának száma|Nincsenek méretek|
|D2C. Property. Update. hiba|Igen|Nem sikerült az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről indított összes sikertelen tulajdonság-frissítés száma|Nincsenek méretek|
|D2C. Property. Update. success|Igen|Az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikeres tulajdonság-frissítés száma|Nincsenek méretek|
|dataExport. error|Igen|Adatexportálási hibák|Darabszám|Összesen|Az adatexportálás során észlelt hibák száma|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport. messages. FILTERED|Igen|Adatexportálási üzenetek szűrve|Darabszám|Összesen|Az adatexportálási szűrőkkel továbbított üzenetek száma|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport. messages. Received|Igen|Fogadott adatexportálási üzenetek|Darabszám|Összesen|Az adatexportálásba bejövő üzenetek száma a szűrés és a dúsítás feldolgozása előtt|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport. messages. írásos|Igen|Az adatexportálási üzenetek írása|Darabszám|Összesen|Célhelyre írt üzenetek száma|exportId, exportDisplayName, destinationId, destinationDisplayName|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. kulcstartó/managedhsms

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Nem|Teljes szolgáltatás rendelkezésre állása|Százalék|Átlag|Szolgáltatási kérelmek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Igen|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Nem|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Igen|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Tár teljes rendelkezésre állása|Százalék|Átlag|Tár kéréseinek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Nem|Tár teljes telítettsége|Százalék|Átlag|Tár kapacitásának használata|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Igen|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Igen|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Igen|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>Microsoft. kubernetes/connectedClusters

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Igen|A csatlakoztatott fürtben található CPU-magok teljes száma|Darabszám|Összesen|A csatlakoztatott fürtben található CPU-magok teljes száma||


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BatchBlobCount|Igen|Köteg Blobok száma|Darabszám|Átlag|Adatforrások száma egy összesített kötegben a betöltéshez.|Adatbázis|
|BatchDuration|Igen|Köteg időtartama|Másodperc|Átlag|A betöltési folyamat összesítési fázisának időtartama.|Adatbázis|
|BatchesProcessed|Igen|Feldolgozott kötegek|Darabszám|Összesen|A betöltéshez összesített kötegek száma. Kötegelt feldolgozás típusa: azt határozza meg, hogy a köteg elérte-e a kötegelt feldolgozás időpontját, az adatméretet vagy a fájlokra vonatkozó korlátot.|Adatbázis, SealReason|
|BatchSize|Igen|Köteg mérete|Bájt|Átlag|Tömörítetlen várt adatméret egy összesített kötegben a betöltéshez.|Adatbázis|
|BlobsDropped|Igen|Blobok elvetve|Darabszám|Összesen|Egy összetevő által véglegesen visszautasított Blobok száma.|Adatbázis, ComponentType, ComponentName|
|BlobsProcessed|Igen|Feldolgozott Blobok|Darabszám|Összesen|Egy összetevő által feldolgozott Blobok száma.|Adatbázis, ComponentType, ComponentName|
|BlobsReceived|Igen|Blobok fogadva|Darabszám|Összesen|Egy összetevő által a bemeneti streamből fogadott Blobok száma.|Adatbázis, ComponentType, ComponentName|
|CacheUtilization|Igen|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|Nincsenek méretek|
|ContinuousExportMaxLatenessMinutes|Igen|Folyamatos exportálás maximális késői|Darabszám|Maximum|A fürt folyamatos exportálási feladatai által jelentett késői idő (percben)|Nincsenek méretek|
|ContinuousExportNumOfRecordsExported|Igen|Folyamatos exportálás – számú exportált rekord|Darabszám|Összesen|Az exportált rekordok száma az exportálási művelet során írt összes tárolási összetevő esetében|ContinuousExportName, adatbázis|
|ContinuousExportPendingCount|Igen|Folyamatos exportálás függőben lévő darabszáma|Darabszám|Maximum|A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma|Nincsenek méretek|
|ContinuousExportResult|Igen|Folyamatos exportálás eredménye|Darabszám|Darabszám|Azt jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e|ContinuousExportName, eredmény, adatbázis|
|CPU|Igen|CPU|Százalék|Átlag|CPU-kihasználtsági szint|Nincsenek méretek|
|DiscoveryLatency|Igen|Felderítés késése|Másodperc|Átlag|Adatkapcsolatok jelentik (ha vannak ilyenek). Az az idő másodpercben, amikor egy üzenet várólistán lévő vagy esemény jön létre, amíg az adatkapcsolat fel nem deríti. Ezt az időt nem tartalmazza az Azure Adatkezelő teljes betöltési időtartama.|ComponentType, ComponentName|
|EventsDropped|Igen|Eldobott események|Darabszám|Összesen|Az adatkapcsolatok által véglegesen eldobott események száma. A rendszer elküld egy betöltési eredmény metrikáját a hiba okának megfelelően.|ComponentType, ComponentName|
|EventsProcessed|Igen|Feldolgozott események|Darabszám|Összesen|A fürt által feldolgozott események száma|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Igen|Feldolgozott események (Event/IoT hubok esetében)|Darabszám|Összesen|A fürt által az esemény/IoT Hub betöltéskor feldolgozott események száma|EventStatus|
|EventsReceived|Igen|Fogadott események|Darabszám|Összesen|Az adatkapcsolatok által fogadott események száma.|ComponentType, ComponentName|
|ExportUtilization|Igen|Exportálás kihasználtsága|Százalék|Maximum|Exportálás kihasználtsága|Nincsenek méretek|
|IngestionLatencyInSeconds|Igen|Betöltési késés|Másodperc|Átlag|A betöltött adatok késése, az adatok fürtbeli fogadásától a lekérdezésre való előkészítésükig. A betöltési késés időtartama a betöltési forgatókönyvtől függ.|Nincsenek méretek|
|IngestionResult|Igen|Betöltés eredménye|Darabszám|Összesen|Betöltési műveletek száma|IngestionResultDetails|
|IngestionUtilization|Igen|Betöltési kihasználtság|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|Nincsenek méretek|
|IngestionVolumeInMB|Igen|Betöltési mennyiség|Bájt|Összesen|Teljes mennyiségű betöltött adatot a fürtbe|Adatbázis|
|InstanceCount|Igen|Példányszám|Darabszám|Átlag|Példányok száma összesen|Nincsenek méretek|
|KeepAlive|Igen|Életben tartása|Darabszám|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|Nincsenek méretek|
|MaterializedViewAgeMinutes|Igen|Jelentős nézet kora|Darabszám|Átlag|Az anyag nézet életkora percben kifejezve|Adatbázis, MaterializedViewName|
|MaterializedViewDataLoss|Igen|Jelentős adatvesztés|Darabszám|Maximum|A lehetséges adatvesztést jelzi a jelentős nézetben|Adatbázis, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|Igen|Az anyagbeli megtekintési egységek újraépítése|Darabszám|Átlag|Egységek számának újraépítése|Adatbázis, MaterializedViewName|
|MaterializedViewHealth|Igen|Anyagbeli nézet állapota|Darabszám|Átlag|Az anyagbeli nézet állapota (1 – kifogástalan, 0 – nem kifogástalan)|Adatbázis, MaterializedViewName|
|MaterializedViewRecordsInDelta|Igen|A különbözeti rekordok megtekintése|Darabszám|Átlag|A nézet nem anyagként szolgáló részében lévő rekordok száma|Adatbázis, MaterializedViewName|
|MaterializedViewResult|Igen|Anyagbeli nézet eredménye|Darabszám|Átlag|A megvalósításának gyakorlatban folyamat eredménye|Adatbázis, MaterializedViewName, eredmény|
|QueryDuration|Igen|Lekérdezés időtartama|Ezredmásodpercben|Átlag|Lekérdezések időtartama másodpercben|QueryStatus|
|QueryResult|Nem|Lekérdezés eredménye|Darabszám|Darabszám|A lekérdezések teljes száma.|QueryStatus|
|QueueLength|Igen|Processzor-várólista hossza|Darabszám|Átlag|Egy összetevő várólistájában várakozó üzenetek száma.|ComponentType|
|QueueOldestMessage|Igen|Várólista legrégebbi üzenete|Darabszám|Átlag|Az az idő másodpercben, amikor a rendszer beszúrta a legrégebbi üzenetet a várólistába.|ComponentType|
|ReceivedDataSizeBytes|Igen|Fogadott adatméret bájtjai|Bájt|Átlag|Az adatkapcsolatok által fogadott adatmennyiség. Ez az adatfolyam mérete, vagy ha meg van adni a nyers adatméret.|ComponentType, ComponentName|
|StageLatency|Igen|Szakasz késése|Másodperc|Átlag|Kumulatív idő, amikor egy üzenet fel lett derítve, amíg meg nem érkezik a jelentéskészítési összetevő a feldolgozáshoz (a felderítési idő be van állítva, ha az üzenet várólistán lévő a betöltési sorhoz, vagy amikor az adatcsatlakozás során felderíti az üzenetet).|Adatbázis, ComponentType|
|SteamingIngestRequestRate|Igen|Streamelési betöltési kérelmek gyakorisága|Darabszám|RateRequestsPerSecond|Folyamatos átviteli kérelmek aránya (kérelmek/másodperc)|Nincsenek méretek|
|StreamingIngestDataRate|Igen|Streamelés adatbetöltési sebessége|Darabszám|Átlag|Adatforgalom adatátviteli sebessége (MB/s)|Nincsenek méretek|
|StreamingIngestDuration|Igen|Streamelés betöltési időtartama|Ezredmásodpercben|Átlag|Folyamatos átvitel időtartama (ezredmásodperc)|Nincsenek méretek|
|StreamingIngestResults|Igen|Streamelés betöltési eredménye|Darabszám|Átlag|Folyamatos átvitel eredménye|Eredmény|
|TotalNumberOfConcurrentQueries|Igen|Az egyidejű lekérdezések teljes száma|Darabszám|Maximum|Az egyidejű lekérdezések teljes száma|Nincsenek méretek|
|TotalNumberOfExtents|Igen|Egységek teljes száma|Darabszám|Összesen|Adategységek teljes száma|Nincsenek méretek|
|TotalNumberOfThrottledCommands|Igen|A szabályozott parancsok teljes száma|Darabszám|Összesen|A szabályozott parancsok teljes száma|CommandType|
|TotalNumberOfThrottledQueries|Igen|A szabályozott lekérdezések teljes száma|Darabszám|Maximum|A szabályozott lekérdezések teljes száma|Nincsenek méretek|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Igen|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Igen|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Igen|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Igen|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Igen|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Igen|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Igen|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Igen|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Igen|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Igen|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Igen|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Igen|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|Nincsenek méretek|
|RunFailurePercentage|Igen|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Igen|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Igen|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Igen|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Igen|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Igen|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Igen|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Igen|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Igen|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Igen|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TriggerFireLatency|Igen|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Igen|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Igen|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Igen|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Igen|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Igen|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Igen|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Igen|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Igen|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Igen|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Igen|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Igen|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Igen|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Igen|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Igen|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Igen|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Igen|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Igen|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|BillableActionExecutions|Igen|Számlázható műveletek végrehajtása|Darabszám|Összesen|A számlázott munkafolyamat-műveletek végrehajtásának száma.|Nincsenek méretek|
|BillableTriggerExecutions|Igen|Számlázható trigger-végrehajtások|Darabszám|Összesen|A számlázott munkafolyamat-trigger-végrehajtások száma.|Nincsenek méretek|
|BillingUsageNativeOperation|Igen|A natív művelet végrehajtásához használt számlázási használat|Darabszám|Összesen|A számlázott natív művelet-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStandardConnector|Igen|A standard szintű összekötők végrehajtásának számlázási használata|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStorageConsumption|Igen|Számlázási használat a tárolási felhasználás végrehajtásához|Darabszám|Összesen|A számlázható tárterület-használat végrehajtásának száma.|Nincsenek méretek|
|RunFailurePercentage|Igen|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Igen|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Igen|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Igen|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Igen|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Igen|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Igen|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Igen|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Igen|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Igen|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TotalBillableExecutions|Igen|Számlázandó végrehajtások összesen|Darabszám|Összesen|A számlázandó munkafolyamat-végrehajtások száma.|Nincsenek méretek|
|TriggerFireLatency|Igen|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Igen|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Igen|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Igen|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Igen|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Igen|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Igen|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Igen|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Igen|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Igen|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Igen|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Igen|Aktív csomópontok|Darabszám|Átlag|Acitve-csomópontok száma Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Forgatókönyv, ClusterName|
|A kért futtatások megszakítása|Igen|A kért futtatások megszakítása|Darabszám|Összesen|Azon futtatások száma, amelyek esetében a rendszer a megszakítást kérelmezte ehhez a munkaterülethez. A Count akkor frissül, ha a lemondási kérelem érkezett a futtatáshoz.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Megszakított futtatások|Igen|Megszakított futtatások|Darabszám|Összesen|A munkaterülethez megszakított futtatások száma. A rendszer a Futtatás sikeres megszakítása után frissíti a darabszámot.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Befejezett futtatások|Igen|Befejezett futtatások|Darabszám|Összesen|A munkaterületen sikeresen befejeződött a futtatások száma. A számláló a Futtatás befejeződése után frissül, és a rendszer a kimenetet gyűjtötte.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuUtilization|Igen|CpuUtilization|Darabszám|Átlag|A CPU-csomópont kihasználtságának százalékos értéke. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, ClusterName|
|Hibák|Igen|Hibák|Darabszám|Összesen|A munkaterületen futtatott hibák száma. A Count frissítése akkor történik meg, amikor a Futtatás hibát észlel.|Eset|
|Sikertelen futtatások|Igen|Sikertelen futtatások|Darabszám|Összesen|A munkaterületen nem sikerült a futtatások száma. A Count a Futtatás meghiúsulása esetén frissül.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Futtatások véglegesítése|Igen|Futtatások véglegesítése|Darabszám|Összesen|A munkaterületre vonatkozó véglegesítési állapotba lépett futtatások száma. A Count akkor frissül, ha egy Futtatás befejeződött, de a kimeneti gyűjtemény még folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Igen|GpuMemoryUtilization|Darabszám|Átlag|A memória kihasználtságának százalékos aránya egy GPU-csomóponton. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, DeviceId, ClusterName|
|GpuUtilization|Igen|GpuUtilization|Darabszám|Átlag|A GPU-csomópont kihasználtságának százalékos értéke. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, DeviceId, ClusterName|
|Üresjárati magok|Igen|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Igen|Tétlen csomópontok|Darabszám|Átlag|Az üresjárati csomópontok száma. Az üresjárati csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de az elérhetővé tehetik az új feladatot.|Forgatókönyv, ClusterName|
|Magok kihagyása|Igen|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Igen|Csomópontok elhagyása|Darabszám|Átlag|A csomópontok elhagyásának száma. A csomópontok elhagyják azokat a csomópontokat, amelyek éppen befejezték a feladatok feldolgozását, és az inaktív állapotba kerülnek.|Forgatókönyv, ClusterName|
|Modell-üzembehelyezés sikertelen|Igen|Modell-üzembehelyezés sikertelen|Darabszám|Összesen|A munkaterületen sikertelen telepítési modellek száma|Forgatókönyv, StatusCode|
|Modell-üzembehelyezés elindítva|Igen|Modell-üzembehelyezés elindítva|Darabszám|Összesen|A munkaterületen elindított modellek központi telepítésének száma|Eset|
|Modell-üzembehelyezés sikerült|Igen|Modell-üzembehelyezés sikerült|Darabszám|Összesen|A munkaterületen sikeres központi telepítési modellek száma|Eset|
|A modell regisztrálása nem sikerült|Igen|A modell regisztrálása nem sikerült|Darabszám|Összesen|A munkaterületen meghiúsult modell-regisztrációk száma|Forgatókönyv, StatusCode|
|A modell regisztrálása sikerült|Igen|A modell regisztrálása sikerült|Darabszám|Összesen|A munkaterületen sikeres modell-regisztrációk száma|Eset|
|Nem válaszoló futtatások|Igen|Nem válaszoló futtatások|Darabszám|Összesen|A munkaterületre nem válaszoló futtatások száma. A Count akkor frissül, ha a Futtatás nem válaszol.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Nem indult el a futtatások|Igen|Nem indult el a futtatások|Darabszám|Összesen|A nem elindított futtatások száma ehhez a munkaterülethez. A Count frissítése akkor történik meg, ha egy futtatási kérelem érkezik, de a futtatási adatok még nem lettek feltöltve. |Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Előzik magok|Igen|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Igen|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma Ezek a csomópontok az alacsony prioritású csomópontok, amelyek a rendelkezésre álló csomópont-készletből származnak.|Forgatókönyv, ClusterName|
|Futtatások előkészítése|Igen|Futtatások előkészítése|Darabszám|Összesen|A munkaterületre felkészülő futtatások száma. A Count akkor frissül, ha egy Futtatás előkészítési állapotba kerül, amíg a futtatási környezet előkészítése folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kiépítési futtatások|Igen|Kiépítési futtatások|Darabszám|Összesen|A munkaterülethez kiépített futtatások száma. A Count frissítése akkor történik meg, ha egy Futtatás a számítási cél létrehozására vagy kiépítési célra vár.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Várólistán lévő futtatások|Igen|Várólistán lévő futtatások|Darabszám|Összesen|Azon futtatások száma, amelyek várólistára kerülnek ehhez a munkaterülethez. A Count akkor frissül, ha egy Futtatás várólistára kerül a számítási célra. Akkor fordulhat elő, ha a szükséges számítási csomópontok üzemkész állapotra várnak.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kvóta kihasználtsága (%)|Igen|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Elindított futtatások|Igen|Elindított futtatások|Darabszám|Összesen|A munkaterületen futó futtatások száma. A Count akkor frissül, ha a Futtatás a szükséges erőforrásokon fut.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Futtatások indítása|Igen|Futtatások indítása|Darabszám|Összesen|A munkaterülethez elindított futtatások száma. A Count frissítése a futtatási és futtatási adatok (például a futtatási azonosító) létrehozási kérelme alapján történt.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Magok összesen|Igen|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Igen|Csomópontok összesen|Darabszám|Átlag|A csomópontok száma összesen. Ez az összeg magában foglalja az aktív csomópontok, a tétlen csomópontok, a nem használható csomópontok, a Premepted csomópontok és a csomópontok elhagyását|Forgatókönyv, ClusterName|
|Használhatatlan magok|Igen|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Igen|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma Néhány feloldhatatlan probléma miatt nem használható csomópontok nem működőképesek. Az Azure újrahasznosítja ezeket a csomópontokat.|Forgatókönyv, ClusterName|
|Figyelmeztetések|Igen|Figyelmeztetések|Darabszám|Összesen|A futtatási figyelmeztetések száma ebben a munkaterületen. A darabszám akkor frissül, amikor egy Futtatás figyelmeztetést észlel.|Eset|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|
|Használat|Nem|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AssetCount|Igen|Eszközök száma|Darabszám|Átlag|Az aktuális Media Service-fiókban már létrehozott eszközök száma|Nincsenek méretek|
|AssetQuota|Igen|Eszköz kvótája|Darabszám|Átlag|Hány eszköz engedélyezett a jelenlegi Media Service-fiókhoz|Nincsenek méretek|
|AssetQuotaUsedPercentage|Igen|Eszköz kvótájának kihasználtsága (%)|Százalék|Átlag|Az eszköz által használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|
|ChannelsAndLiveEventsCount|Igen|Élő események száma|Darabszám|Átlag|Az aktuális Media Services-fiókban élő események teljes száma|Nincsenek méretek|
|ContentKeyPolicyCount|Igen|Tartalmi kulcsokra vonatkozó szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejött a tartalmi kulcs házirendjeinek száma|Nincsenek méretek|
|ContentKeyPolicyQuota|Igen|Tartalmi kulcs házirend-kvótája|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó tartalmi kulcsokra vonatkozó házirendek száma|Nincsenek méretek|
|ContentKeyPolicyQuotaUsedPercentage|Igen|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|Százalék|Átlag|A tartalmi kulcsra vonatkozó házirend százaléka a jelenlegi Media Service-fiókban|Nincsenek méretek|
|MaxChannelsAndLiveEventsCount|Igen|Élő esemény kvótájának maximális száma|Darabszám|Maximum|Az aktuális Media Services-fiókban engedélyezett élő események maximális száma|Nincsenek méretek|
|MaxRunningChannelsAndLiveEventsCount|Igen|Élő esemény kvótájának maximális száma|Darabszám|Maximum|Az aktuális Media Services-fiókban engedélyezett élő események maximális száma|Nincsenek méretek|
|RunningChannelsAndLiveEventsCount|Igen|Élő események számának futtatása|Darabszám|Átlag|Az aktuális Media Services-fiókban futó élő események teljes száma|Nincsenek méretek|
|StreamingPolicyCount|Igen|Folyamatos átviteli szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejöttek a folyamatos átviteli házirendek|Nincsenek méretek|
|StreamingPolicyQuota|Igen|Streaming Policy-kvóta|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó folyamatos átviteli házirendek száma|Nincsenek méretek|
|StreamingPolicyQuotaUsedPercentage|Igen|Adatfolyam-házirend kvótájának kihasználtsága (%)|Százalék|Átlag|A streaming Policy használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft. Media/Mediaservices/liveEvents

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|IngestBitrate|Igen|Élő esemény betöltési bitrátája|BitsPerSecond|Átlag|Egy élő eseményhez (bit/mp) betöltött bejövő bitráta.|TrackName|
|IngestDriftValue|Igen|Élő esemény betöltésének drift értéke|Másodperc|Maximum|A betöltött tartalom és a rendszeróra időbélyege közötti eltolódás a percenkénti másodpercben kifejezve. A nullától eltérő érték azt jelzi, hogy a betöltött tartalom lassabban érkezik a rendszeridőt jelző időpontig.|TrackName|
|IngestLastTimestamp|Igen|Élő esemény betöltésének utolsó időbélyeg|Ezredmásodpercben|Maximum|Élő esemény utolsó időbélyege.|TrackName|
|LiveOutputLastTimestamp|Igen|Utolsó kimeneti időbélyeg|Ezredmásodpercben|Maximum|Az élő esemény kimenetére a tárterületre feltöltött utolsó töredék időbélyegzője.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Mediaservices/streamingEndpoints

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CPU|Igen|Processzorhasználat|Százalék|Átlag|A prémium szintű streaming-végpontok CPU-használata. Ezek az adatátviteli végpontok nem érhetők el.|Nincsenek méretek|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve.|OutputFormat|
|EgressBandwidth|Nem|Kimenő sávszélesség|BitsPerSecond|Átlag|Kimenő forgalom (bit/mp).|Nincsenek méretek|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|Továbbítási végpontra irányuló kérelmek.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Igen|A Befejezés végének késése|Ezredmásodpercben|Átlag|A sikeres kérelmek átlagos késése ezredmásodpercben.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Igen|Aktív renderelési munkamenetek|Darabszám|Átlag|Aktív renderelési munkamenetek száma összesen|SessionType, SDKVersion|
|AssetsConverted|Igen|Átalakított eszközök|Darabszám|Összesen|Átalakított eszközök teljes száma|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft. MixedReality/spatialAnchorsAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AnchorsCreated|Igen|Létrehozott horgonyok|Darabszám|Összesen|Létrehozott horgonyok száma|DeviceFamily, SDKVersion|
|AnchorsDeleted|Igen|Törölt horgonyok|Darabszám|Összesen|Törölt horgonyok száma|DeviceFamily, SDKVersion|
|AnchorsQueried|Igen|Lekérdezett horgonyok|Darabszám|Összesen|Lekérdezett térbeli horgonyok száma|DeviceFamily, SDKVersion|
|AnchorsUpdated|Igen|Frissített horgonyok|Darabszám|Összesen|Frissített horgonyok száma|DeviceFamily, SDKVersion|
|PosesFound|Igen|Talált eredmény|Darabszám|Összesen|Visszaadott eredmény száma|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Igen|Napi horgonyok összesen|Darabszám|Átlag|A horgonyok teljes száma – naponta|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Igen|Készlet lefoglalt mérete|Bájt|Átlag|A készlet kiépített mérete|Nincsenek méretek|
|VolumePoolAllocatedToVolumeThroughput|Igen|Lefoglalt készlet átviteli sebessége|BytesPerSecond|Átlag|A készlethez tartozó összes kötet átviteli sebességének összege|Nincsenek méretek|
|VolumePoolAllocatedUsed|Igen|A kötet méretéhez lefoglalt készlet|Bájt|Átlag|A készlet lefoglalt felhasznált mérete|Nincsenek méretek|
|VolumePoolProvisionedThroughput|Igen|Kiosztott átviteli sebesség a készlethez|BytesPerSecond|Átlag|A készlet kiépített átviteli sebessége|Nincsenek méretek|
|VolumePoolTotalLogicalSize|Igen|Készlet felhasznált mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincsenek méretek|
|VolumePoolTotalSnapshotSize|Igen|A készlethez tartozó pillanatképek teljes mérete|Bájt|Átlag|A készletben lévő összes kötet pillanatkép-méretének összege|Nincsenek méretek|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/kötetek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageReadLatency|Igen|Olvasási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|Nincsenek méretek|
|AverageWriteLatency|Igen|Írási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|Nincsenek méretek|
|CbsVolumeBackupActive|Igen|A kötet biztonsági mentése fel van függesztve|Darabszám|Átlag|Felfüggesztette a biztonsági mentési szabályzatot a kötetre? 0 Ha igen, akkor 1, ha nem.|Nincsenek méretek|
|CbsVolumeLogicalBackupBytes|Igen|Kötet biztonsági másolatának bájtjai|Bájt|Átlag|A köteten biztonsági mentéssel elkészített bájtok száma.|Nincsenek méretek|
|CbsVolumeOperationComplete|Igen|A kötet biztonsági mentési művelete befejeződött|Darabszám|Átlag|Sikerült befejezni az utolsó kötet biztonsági mentési vagy visszaállítási műveletét? 1 Ha igen, 0, ha nem.|Nincsenek méretek|
|CbsVolumeOperationTransferredBytes|Igen|Kötet biztonsági mentése utoljára továbbított bájtok|Bájt|Átlag|A legutóbbi biztonsági mentési vagy visszaállítási művelet során átvitt bájtok teljes száma.|Nincsenek méretek|
|CbsVolumeProtected|Igen|Engedélyezve van-e a kötet biztonsági mentése|Darabszám|Átlag|Engedélyezve van a biztonsági másolat a köteten? 1 Ha igen, 0, ha nem.|Nincsenek méretek|
|OtherThroughput|Igen|Egyéb átviteli sebesség|BytesPerSecond|Átlag|Egyéb átviteli sebesség (amely nem írható vagy írható) bájt/másodpercben|Nincsenek méretek|
|ReadIops|Igen|IOPS olvasása|CountPerSecond|Átlag|Olvasási/kimenő műveletek másodpercenként|Nincsenek méretek|
|ReadThroughput|Igen|Olvasási sebesség|BytesPerSecond|Átlag|Olvasási sebesség (bájt/s)|Nincsenek méretek|
|TotalThroughput|Igen|Teljes átviteli sebesség|BytesPerSecond|Átlag|Az összes átviteli sebesség (bájt/s) összege másodpercenként|Nincsenek méretek|
|VolumeAllocatedSize|Igen|Kötet lefoglalt mérete|Bájt|Átlag|Kötet kiépített mérete|Nincsenek méretek|
|VolumeConsumedSizePercentage|Igen|Százalékos mennyiség felhasznált mérete|Százalék|Átlag|A felhasznált kötet százalékos aránya, beleértve a pillanatképeket is.|Nincsenek méretek|
|VolumeLogicalSize|Igen|Kötet felhasznált mérete|Bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|Nincsenek méretek|
|VolumeSnapshotSize|Igen|Kötet pillanatképének mérete|Bájt|Átlag|A köteten található összes pillanatkép mérete|Nincsenek méretek|
|WriteIops|Igen|IOPS írása|CountPerSecond|Átlag|Írási/kimenő műveletek másodpercenként|Nincsenek méretek|
|WriteThroughput|Igen|Írási sebesség|BytesPerSecond|Átlag|Írási sebesség (bájt/s) másodpercenként|Nincsenek méretek|
|XregionReplicationHealthy|Igen|A kötet replikálási állapota Kifogástalan|Darabszám|Átlag|A kapcsolat feltétele, 1 vagy 0.|Nincsenek méretek|
|XregionReplicationLagTime|Igen|Kötet replikációs késési ideje|Másodperc|Átlag|Az az időtartam másodpercben, ameddig a tükrözött adatmennyiség a forrás mögött marad.|Nincsenek méretek|
|XregionReplicationLastTransferDuration|Igen|Kötet replikálásának utolsó átvitelének időtartama|Másodperc|Átlag|Az utolsó átvitel befejezéséhez szükséges idő másodpercben.|Nincsenek méretek|
|XregionReplicationLastTransferSize|Igen|Kötet replikálásának utolsó átvitelének mérete|Bájt|Átlag|Az utolsó átvitel részeként átvitt bájtok teljes száma.|Nincsenek méretek|
|XregionReplicationRelationshipProgress|Igen|Kötet replikálásának folyamata|Bájt|Átlag|Az aktuális adatátviteli művelethez továbbított adatok teljes mennyisége.|Nincsenek méretek|
|XregionReplicationRelationshipTransferring|Igen|A kötet-replikálás átadása|Darabszám|Átlag|Azt jelzi, hogy a kötet replikálásának állapota "átvitel".|Nincsenek méretek|
|XregionReplicationTotalTransferBytes|Igen|Kötet replikálásának teljes átvitele|Bájt|Átlag|A kapcsolathoz továbbított összesített bájtok száma.|Nincsenek méretek|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Nem|Application Gateway teljes idő|Ezredmásodpercben|Átlag|A kérelem feldolgozásának és a válasz elküldésekor elvégezhető átlagos idő. Ez az időszak átlaga, amikor a Application Gateway megkapja egy HTTP-kérelem első bájtját, amikor a válasz küldése művelet befejeződik. Fontos megjegyezni, hogy ez általában a Application Gateway feldolgozási időt, az időpontot, amikor a kérés és a válasz csomagjai a hálózaton keresztül utaznak, valamint a háttér-kiszolgáló válaszának időpontját.|Figyelő|
|AvgRequestCountPerHealthyHost|Nem|Percenkénti kérelmek száma kifogástalan állapotú gazdagépen|Darabszám|Átlag|Kérelmek átlagos száma percenként, a készletben lévő kifogástalan állapotú gazdagépek esetében|BackendSettingsPool|
|BackendConnectTime|Nem|Háttérbeli kapcsolat ideje|Ezredmásodpercben|Átlag|A háttér-kiszolgálóval létesített kapcsolatok létrehozásához töltött idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendFirstByteResponseTime|Nem|Háttérbeli első bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz fejléc első bájtjának fogadása között eltelt idő, a háttér-kiszolgáló feldolgozási idejének megközelítő időpontja|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendLastByteResponseTime|Nem|Háttérbeli utolsó bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz törzse utolsó bájtjának fogadása között eltelt idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendResponseStatus|Igen|Háttérbeli válasz állapota|Darabszám|Összesen|A háttérbeli tagok által létrehozott HTTP-válasz kódok száma. Ez nem tartalmazza a Application Gateway által létrehozott válasz-kódokat.|BackendServer, Háttérkészletek, Backendhttpsetting értékre, HttpStatusGroup|
|BlockedCount|Igen|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Szerepkörcsoportot, RuleId|
|BlockedReqCount|Igen|Webalkalmazási tűzfal letiltott kérelmek száma|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek száma|Nincsenek méretek|
|BytesReceived|Igen|Fogadott bájtok száma|Bájt|Összesen|A Application Gateway által az ügyfelektől fogadott bájtok teljes száma|Figyelő|
|BytesSent|Igen|Eljuttatott bájtok|Bájt|Összesen|A Application Gateway által az ügyfeleknek eljuttatott bájtok teljes száma|Figyelő|
|CapacityUnits|Nem|Aktuális kapacitási egységek|Darabszám|Átlag|Felhasznált kapacitási egységek|Nincsenek méretek|
|ClientRtt|Nem|Ügyfél-RTT|Ezredmásodpercben|Átlag|Az ügyfelek és a Application Gateway közötti átlagos menetidő. Ez a metrika azt jelzi, hogy mennyi időt vesz igénybe a kapcsolatok és a visszaküldési visszaigazolás|Figyelő|
|ComputeUnits|Nem|Aktuális számítási egységek|Darabszám|Átlag|Felhasznált számítási egységek|Nincsenek méretek|
|CpuUtilization|Nem|Processzor kihasználtsága|Százalék|Átlag|A Application Gateway aktuális CPU-kihasználtsága|Nincsenek méretek|
|Összege|Igen|Aktuális kapcsolatok|Darabszám|Összesen|Application Gatewaysal létesített aktuális kapcsolatok száma|Nincsenek méretek|
|EstimatedBilledCapacityUnits|Nem|Becsült számlázott kapacitási egységek|Darabszám|Átlag|A felszámított becsült kapacitási egységek|Nincsenek méretek|
|FailedRequests|Igen|Sikertelen kérelmek|Darabszám|Összesen|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|FixedBillableCapacityUnits|Nem|Fix számlázandó kapacitásegységek|Darabszám|Átlag|Minimálisan felszámított kapacitási egységek|Nincsenek méretek|
|HealthyHostCount|Igen|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Kifogástalan állapotú háttérbeli gazdagépek száma|BackendSettingsPool|
|MatchedCount|Igen|Webalkalmazási tűzfal teljes szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal – teljes szabály eloszlása a bejövő forgalomhoz|Szerepkörcsoportot, RuleId|
|NewConnectionsPerSecond|Nem|Új kapcsolatok másodpercenként|CountPerSecond|Átlag|Új kapcsolatok másodpercenként létesített Application Gateway|Nincsenek méretek|
|ResponseStatus|Igen|Válasz állapota|Darabszám|Összesen|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Teljesítmény|Nem|Teljesítmény|BytesPerSecond|Átlag|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|Nincsenek méretek|
|TlsProtocol|Igen|Ügyfél TLS protokoll|Darabszám|Összesen|Azoknak a TLS-és nem TLS-kérelmeknek a száma, amelyek az ügyfél által kezdeményezett Application Gatewaykal létesített kapcsolatban állnak. A TLS protokoll terjesztésének megtekintéséhez szűrje a Dimension TLS protokollt.|Figyelő, TlsProtocol|
|TotalRequests|Igen|Összes kérelem|Darabszám|Összesen|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|UnhealthyHostCount|Igen|Nem kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Nem kifogástalan állapotú háttérbeli gazdagépek száma|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Igen|Alkalmazás-szabályok találatok száma|Darabszám|Összesen|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|DataProcessed|Igen|Feldolgozott adatok|Bájt|Összesen|A tűzfal által feldolgozott adatmennyiség teljes mennyisége|Nincsenek méretek|
|FirewallHealth|Igen|Tűzfal állapota|Százalék|Átlag|A tűzfal általános állapotát jelzi|Állapot, ok|
|NetworkRuleHit|Igen|Hálózati szabályok találatok száma|Darabszám|Összesen|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|
|SNATPortUtilization|Igen|SNAT-portok kihasználtsága|Százalék|Átlag|Jelenleg használatban lévő kimenő SNAT-portok százalékos aránya|Protokoll|
|Teljesítmény|Nem|Teljesítmény|BitsPerSecond|Átlag|A tűzfal által feldolgozott átviteli sebesség|Nincsenek méretek|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|pingmesh|Nem|Megerősített kommunikációs állapot|Darabszám|Átlag|A kommunikációs állapot 1 értéket jelenít meg, ha az összes kommunikáció jó, és 0, ha a hibás.||
|munkamenetek|Nem|Session Count|Darabszám|Összesen|A megerősített munkamenetek száma. Megtekintés összegben és példányban.|gazda|
|összesen|Igen|Teljes memória|Darabszám|Átlag|Memória teljes statisztikája.|gazda|
|usage_user|Nem|Használt processzor|Darabszám|Átlag|CPU-használati statisztika.|CPU, gazdagép|
|protokollok|Igen|Felhasznált memória|Darabszám|Átlag|Memóriahasználat statisztikái.|gazda|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Igen|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Igen|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume|Nem|Lekérdezési kötet|Darabszám|Összesen|A DNS-zónák számára kiszolgált lekérdezések száma|Nincsenek méretek|
|RecordSetCapacityUtilization|Nem|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|Nincsenek méretek|
|RecordSetCount|Nem|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ArpAvailability|Igen|ARP rendelkezésre állása|Százalék|Átlag|Az ARP és a MSEE közötti rendelkezésre állás az összes társ felé.|PeeringType, társ|
|BgpAvailability|Igen|BGP rendelkezésre állása|Százalék|Átlag|A BGP rendelkezésre állása a MSEE az összes társ felé.|PeeringType, társ|
|BitsInPerSecond|Nem|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeringType, DeviceRole|
|BitsOutPerSecond|Nem|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|Nem|GlobalReachBitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Nem|GlobalReachBitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Igen|DroppedInBitsPerSecond|BitsPerSecond|Átlag|Bejövő adatforgalom (bit/mp)|Nincsenek méretek|
|QosDropBitsOutPerSecond|Igen|DroppedOutBitsPerSecond|BitsPerSecond|Átlag|Kimenő adatforgalom másodpercenkénti száma|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/társak

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Igen|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Igen|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Nem|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Kapcsolatnév|
|ErGatewayConnectionBitsOutPerSecond|Nem|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Kapcsolatnév|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Igen|A társnak hirdetett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által hirdetett útvonalak száma|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Igen|A társtól megszerzett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által megszerzett útvonalak száma|roleInstance|
|ExpressRouteGatewayCpuUtilization|Igen|Processzorhasználat|Darabszám|Átlag|A ExpressRoute-átjáró CPU-kihasználtsága|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|Nem|Útvonalak változásának gyakorisága (előzetes verzió)|Darabszám|Összesen|Útvonalak változásának gyakorisága a ExpressRoute-átjáróban|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|Nem|Virtuális gépek száma a Virtual Networkban (előzetes verzió)|Darabszám|Maximum|A Virtual Networkban lévő virtuális gépek száma|Nincsenek méretek|
|ExpressRouteGatewayPacketsPerSecond|Nem|Másodpercenkénti csomagok száma|CountPerSecond|Átlag|ExpressRoute-átjáró csomagjainak száma|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AdminState|Igen|AdminState|Darabszám|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol|Igen|LineProtocol|Darabszám|Átlag|A port protokolljának állapota|Hivatkozás|
|PortBitsInPerSecond|Igen|BitsInPerSecond|BitsPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Hivatkozás|
|PortBitsOutPerSecond|Igen|BitsOutPerSecond|BitsPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Hivatkozás|
|RxLightLevel|Igen|RxLightLevel|Darabszám|Átlag|Rx-fény szintje dBm-ben|Hivatkozás, sáv|
|TxLightLevel|Igen|TxLightLevel|Darabszám|Átlag|TX-fény szintje dBm-ben|Hivatkozás, sáv|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Igen|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|BackendRequestCount|Igen|Háttérbeli kérelmek száma|Darabszám|Összesen|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Igen|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Háttérrendszer|
|BillableResponseSize|Igen|Számlázható válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött számlázandó bájtjainak (minimális 2KB) száma az ügyfeleknek.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Igen|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Igen|Kérelem mérete|Bájt|Összesen|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Igen|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Igen|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Igen|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Nem|Lefoglalt SNAT-portok|Darabszám|Átlag|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Igen|Bájtok száma|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, irány|
|DipAvailability|Igen|Állapotadat-mintavétel|Darabszám|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Igen|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SnatConnectionCount|Igen|SNAT-kapcsolatok száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Igen|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|UsedSnatPorts|Nem|Használt SNAT-portok|Darabszám|Átlag|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Igen|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft. Network/natGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount|Igen|Bájt|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Protokoll, irány|
|DatapathAvailability|Igen|DataPath elérhetősége (előzetes verzió)|Darabszám|Átlag|NAT-átjáró DataPath rendelkezésre állása|Nincsenek méretek|
|PacketCount|Igen|Csomagok|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Protokoll, irány|
|PacketDropCount|Igen|Eldobott csomagok|Darabszám|Összesen|Eldobott csomagok száma|Nincsenek méretek|
|SNATConnectionCount|Igen|SNAT-kapcsolatok száma|Darabszám|Összesen|Egyidejű aktív kapcsolatok összesen|Protokoll, ConnectionState|
|TotalConnectionCount|Igen|SNAT-kapcsolatok száma összesen|Darabszám|Összesen|Aktív SNAT-kapcsolatok teljes száma|Protokoll|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Igen|Fogadott bájtok száma|Bájt|Összesen|A hálózati adapter által fogadott bájtok száma|Nincsenek méretek|
|BytesSentRate|Igen|Eljuttatott bájtok|Bájt|Összesen|A hálózati adapter által eljuttatott bájtok száma|Nincsenek méretek|
|PacketsReceivedRate|Igen|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|Nincsenek méretek|
|PacketsSentRate|Igen|Küldött csomagok|Darabszám|Összesen|A hálózati adapter által küldött csomagok száma|Nincsenek méretek|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Igen|Átlagos menetidő (MS) (klasszikus)|Ezredmásodpercben|Átlag|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|Nincsenek méretek|
|ChecksFailedPercent|Igen|Sikertelen ellenőrzések százalékos aránya|Százalék|Átlag|a kapcsolat figyelési ellenőrzése nem sikerült|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Igen|%-Os mintavétel sikertelen (klasszikus)|Százalék|Átlag|a kapcsolat figyelési mintavételének%-a meghiúsult|Nincsenek méretek|
|RoundTripTimeMs|Igen|Round-Trip idő (MS)|Ezredmásodpercben|Átlag|A kapcsolat figyelési ellenőrzésének időkorlátja ezredmásodpercben|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|Igen|Teszt eredménye|Darabszám|Átlag|A Csatlakozáskezelő teszt eredménye|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|P2SBandwidth|Igen|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SConnectionCount|Igen|P2S-kapcsolatok száma|Darabszám|Összesen|Az átjáró P2S-kapcsolatainak száma|Protokoll|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume|Igen|Lekérdezési kötet|Darabszám|Összesen|Egy saját DNS zónában kiszolgált lekérdezések száma|Nincsenek méretek|
|RecordSetCapacityUtilization|Nem|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy saját DNS zóna által használt rekordazonosító-kapacitás százalékos aránya|Nincsenek méretek|
|RecordSetCount|Igen|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy saját DNS zónában|Nincsenek méretek|
|VirtualNetworkLinkCapacityUtilization|Nem|Virtual Network kapcsolat kapacitásának kihasználtsága|Százalék|Maximum|saját DNS zóna által használt Virtual Network kapcsolat kapacitásának százaléka|Nincsenek méretek|
|VirtualNetworkLinkCount|Igen|Virtual Network kapcsolatok száma|Darabszám|Maximum|saját DNS zónához csatolt virtuális hálózatok száma|Nincsenek méretek|
|VirtualNetworkWithRegistrationCapacityUtilization|Nem|Virtual Network regisztrációs kapcsolat kapacitásának kihasználtsága|Százalék|Maximum|Virtual Network-hivatkozás százaléka, amely egy saját DNS zóna által használt automatikus regisztrációs kapacitással rendelkezik|Nincsenek méretek|
|VirtualNetworkWithRegistrationLinkCount|Igen|Virtual Network regisztrációs kapcsolatok száma|Darabszám|Maximum|Egy saját DNS zónához kapcsolt virtuális hálózatok száma, amelyeken engedélyezve van az automatikus regisztráció|Nincsenek méretek|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft. Network/privateEndpoints

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PEBytesIn|Igen|Bájtok itt|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateEndpointId|
|PEBytesOut|Igen|Kimenő bájtok|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft. Network/privateLinkServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PLSBytesIn|Igen|Bájtok itt|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateLinkServiceId|
|PLSBytesOut|Igen|Kimenő bájtok|Darabszám|Összesen|Kimenő bájtok teljes száma|PrivateLinkServiceId|
|PLSNatPortsUsage|Igen|NAT-portok használata|Százalék|Átlag|NAT-portok használata|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount|Igen|Bájtok száma|Bájt|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|BytesDroppedDDoS|Igen|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|Nincsenek méretek|
|BytesForwardedDDoS|Igen|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|Nincsenek méretek|
|BytesInDDoS|Igen|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincsenek méretek|
|DDoSTriggerSYNPackets|Igen|DDoS elleni védelem aktiválására szolgáló bejövő SYN-csomagok|CountPerSecond|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő SYN-csomagok|Nincsenek méretek|
|DDoSTriggerTCPPackets|Igen|DDoS elleni védelem aktiválására szolgáló bejövő TCP-csomagok|CountPerSecond|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő TCP-csomagok|Nincsenek méretek|
|DDoSTriggerUDPPackets|Igen|DDoS elleni védelem aktiválására szolgáló bejövő UDP-csomagok|CountPerSecond|Maximum|DDoS elleni védelem aktiválására szolgáló bejövő UDP-csomagok|Nincsenek méretek|
|IfUnderDDoSAttack|Igen|DDoS-támadás alatt vagy nem|Darabszám|Maximum|DDoS-támadás alatt vagy nem|Nincsenek méretek|
|PacketCount|Igen|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|PacketsDroppedDDoS|Igen|Bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő csomagok eldobott DDoS|Nincsenek méretek|
|PacketsForwardedDDoS|Igen|Bejövő csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő csomagok továbbított DDoS|Nincsenek méretek|
|PacketsInDDoS|Igen|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|Nincsenek méretek|
|SynCount|Igen|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|
|TCPBytesDroppedDDoS|Igen|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|Nincsenek méretek|
|TCPBytesForwardedDDoS|Igen|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|Nincsenek méretek|
|TCPBytesInDDoS|Igen|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincsenek méretek|
|TCPPacketsDroppedDDoS|Igen|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincsenek méretek|
|TCPPacketsForwardedDDoS|Igen|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|Nincsenek méretek|
|TCPPacketsInDDoS|Igen|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|Nincsenek méretek|
|UDPBytesDroppedDDoS|Igen|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|Nincsenek méretek|
|UDPBytesForwardedDDoS|Igen|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|Nincsenek méretek|
|UDPBytesInDDoS|Igen|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincsenek méretek|
|UDPPacketsDroppedDDoS|Igen|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincsenek méretek|
|UDPPacketsForwardedDDoS|Igen|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincsenek méretek|
|UDPPacketsInDDoS|Igen|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|Nincsenek méretek|
|VipAvailability|Igen|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Igen|Végponti állapot végpont szerint|Darabszám|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|
|QpsByEndpoint|Igen|Visszaadott végponti lekérdezések|Darabszám|Összesen|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth|Igen|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincsenek méretek|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Igen|A társnak hirdetett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által hirdetett útvonalak száma|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Igen|A társtól megszerzett útvonalak száma (előzetes verzió)|Darabszám|Maximum|A társ által a ExpressRouteGateway által megszerzett útvonalak száma|roleInstance|
|ExpressRouteGatewayCpuUtilization|Igen|Processzorhasználat|Darabszám|Átlag|A ExpressRoute-átjáró CPU-kihasználtsága|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|Nem|Útvonalak változásának gyakorisága (előzetes verzió)|Darabszám|Összesen|Útvonalak változásának gyakorisága a ExpressRoute-átjáróban|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|Nem|Virtuális gépek száma a Virtual Networkban (előzetes verzió)|Darabszám|Maximum|A Virtual Networkban lévő virtuális gépek száma|Nincsenek méretek|
|ExpressRouteGatewayPacketsPerSecond|Nem|Másodpercenkénti csomagok száma|CountPerSecond|Átlag|ExpressRoute-átjáró csomagjainak száma|roleInstance|
|P2SBandwidth|Igen|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SConnectionCount|Igen|P2S-kapcsolatok száma|Darabszám|Maximum|Az átjáró P2S-kapcsolatainak száma|Protokoll|
|TunnelAverageBandwidth|Igen|Alagút sávszélessége|BytesPerSecond|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Igen|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Igen|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Igen|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Igen|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Igen|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Igen|Bújtatási bejövő csomagok|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelNatAllocations|Nem|Bújtatási NAT-foglalások|Darabszám|Összesen|Egy alagúton lévő NAT-szabályhoz tartozó foglalások száma|NatRule, kapcsolatnév, RemoteIP|
|TunnelNatedBytes|Nem|Bújtatási címfordításos bájtok|Bájt|Összesen|Egy NAT-szabály által az alagúton mért bájtok száma |NatRule, kapcsolatnév, RemoteIP|
|TunnelNatedPackets|Nem|Bújtatási címfordításos csomagok|Darabszám|Összesen|Egy NAT-szabály által az alagúton lezárt csomagok száma|NatRule, kapcsolatnév, RemoteIP|
|TunnelNatFlowCount|Nem|Bújtatási NAT-folyamatok|Darabszám|Összesen|NAT-folyamatok száma az alagúton a flow típusa és a NAT-szabály szerint|NatRule, kapcsolatnév, RemoteIP, FlowType|
|TunnelNatPacketDrop|Nem|Alagút NAT-csomagjainak elejtése|Darabszám|Összesen|Az alagúton a drop Type és a NAT-szabály által eldobott felhúzott csomagok száma|NatRule, kapcsolatnév, RemoteIP, DropType|
|TunnelReverseNatedBytes|Nem|Bújtatási fordított, visszaadott bájtok|Bájt|Összesen|Az alagúton egy NAT-szabály által fordítottan fordított bájtok száma|NatRule, kapcsolatnév, RemoteIP|
|TunnelReverseNatedPackets|Nem|Bújtatási fordított, visszaadott csomagok|Darabszám|Összesen|Egy NAT-szabály által fordított, egy alagúton lévő csomagok száma|NatRule, kapcsolatnév, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Igen|A pingelések időpontjának kerekítése egy virtuális géphez|Ezredmásodpercben|Átlag|A célként megadott virtuális gépre eljuttatott pingelések menetének időpontja|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Igen|Sikertelen pingelések egy virtuális géphez|Százalék|Átlag|A hibás pingelések száma a cél virtuális gép összes elküldéses pingelésének százalékában|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft. Network/virtualRouters

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PeeringAvailability|Igen|BGP rendelkezésre állása|Százalék|Átlag|A BGP rendelkezésre állása a VirtualRouter és a távoli társak között|Társ|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth|Igen|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincsenek méretek|
|TunnelAverageBandwidth|Igen|Alagút sávszélessége|BytesPerSecond|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Igen|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Igen|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Igen|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Igen|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Igen|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Igen|Bújtatási bejövő csomagok|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelNatAllocations|Nem|Bújtatási NAT-foglalások|Darabszám|Összesen|Egy alagúton lévő NAT-szabályhoz tartozó foglalások száma|NatRule, kapcsolatnév, RemoteIP|
|TunnelNatedBytes|Nem|Bújtatási címfordításos bájtok|Bájt|Összesen|Egy NAT-szabály által az alagúton mért bájtok száma |NatRule, kapcsolatnév, RemoteIP|
|TunnelNatedPackets|Nem|Bújtatási címfordításos csomagok|Darabszám|Összesen|Egy NAT-szabály által az alagúton lezárt csomagok száma|NatRule, kapcsolatnév, RemoteIP|
|TunnelNatFlowCount|Nem|Bújtatási NAT-folyamatok|Darabszám|Összesen|NAT-folyamatok száma az alagúton a flow típusa és a NAT-szabály szerint|NatRule, kapcsolatnév, RemoteIP, FlowType|
|TunnelNatPacketDrop|Nem|Alagút NAT-csomagjainak elejtése|Darabszám|Összesen|Az alagúton a drop Type és a NAT-szabály által eldobott felhúzott csomagok száma|NatRule, kapcsolatnév, RemoteIP, DropType|
|TunnelReverseNatedBytes|Nem|Bújtatási fordított, visszaadott bájtok|Bájt|Összesen|Az alagúton egy NAT-szabály által fordítottan fordított bájtok száma|NatRule, kapcsolatnév, RemoteIP|
|TunnelReverseNatedPackets|Nem|Bújtatási fordított, visszaadott csomagok|Darabszám|Összesen|Egy NAT-szabály által fordított, egy alagúton lévő csomagok száma|NatRule, kapcsolatnév, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|bejövő|Igen|Bejövő üzenetek|Darabszám|Összesen|A sikeres küldési API-hívások száma. |Nincsenek méretek|
|bejövő. ALL. failedrequests|Igen|Minden bejövő sikertelen kérelem|Darabszám|Összesen|Értesítési központ bejövő sikertelen kéréseinek száma|Nincsenek méretek|
|bejövő. ALL. requests|Igen|Minden bejövő kérelem|Darabszám|Összesen|Értesítési központ összes bejövő kérelme|Nincsenek méretek|
|bejövő. ütemezett|Igen|Ütemezett leküldéses értesítések elküldve|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|bejövő. ütemezett. Mégse|Igen|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|a telepítés. All|Igen|Telepítési felügyeleti műveletek|Darabszám|Összesen|Telepítési felügyeleti műveletek|Nincsenek méretek|
|telepítés. Törlés|Igen|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|Nincsenek méretek|
|telepítés. Get|Igen|Telepítési műveletek beolvasása|Darabszám|Összesen|Telepítési műveletek beolvasása|Nincsenek méretek|
|Installation. patch|Igen|Javítások telepítési műveletei|Darabszám|Összesen|Javítások telepítési műveletei|Nincsenek méretek|
|telepítési. upsert|Igen|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|Nincsenek méretek|
|notificationhub. leküldések|Igen|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|Nincsenek méretek|
|kimenő. allpns. badorexpiredchannel|Igen|Rossz vagy lejárt csatorna-hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|Nincsenek méretek|
|kimenő. allpns. channelerror|Igen|Csatorna hibái|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|Nincsenek méretek|
|kimenő. allpns. invalidpayload|Igen|Hasznos adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|Nincsenek méretek|
|kimenő. allpns. pnserror|Igen|Külső értesítési rendszerhibák|Darabszám|Összesen|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|Nincsenek méretek|
|kimenő. allpns. sikeres|Igen|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. apns. badchannel|Igen|APNS hibás csatorna hiba|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS: 8).|Nincsenek méretek|
|kimenő. apns. expiredchannel|Igen|APNS lejárt csatorna hibája|Darabszám|Összesen|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|Nincsenek méretek|
|kimenő. apns. invalidcredentials|Igen|APNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. apns. invalidnotificationsize|Igen|A APNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|Nincsenek méretek|
|kimenő. apns. pnserror|Igen|APNS hibák|Darabszám|Összesen|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. apns. sikeres|Igen|APNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. authenticationerror|Igen|GCM-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|Nincsenek méretek|
|kimenő. GCM. badchannel|Igen|GCM hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|Nincsenek méretek|
|kimenő. GCM. expiredchannel|Igen|GCM lejárt csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|Nincsenek méretek|
|kimenő. GCM. invalidcredentials|Igen|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. GCM. invalidnotificationformat|Igen|GCM – érvénytelen értesítési formátum|Darabszám|Összesen|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincsenek méretek|
|kimenő. GCM. invalidnotificationsize|Igen|A GCM érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|Nincsenek méretek|
|kimenő. GCM. pnserror|Igen|GCM hibák|Darabszám|Összesen|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. GCM. sikeres|Igen|GCM sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. szabályozva|Igen|GCM-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|Nincsenek méretek|
|kimenő. GCM. wrongchannel|Igen|GCM rossz csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|Nincsenek méretek|
|kimenő. mpns. authenticationerror|Igen|MPNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. badchannel|Igen|MPNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. mpns. channeldisconnected|Igen|MPNS csatorna leválasztva|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|Nincsenek méretek|
|kimenő. mpns. Dropped|Igen|MPNS eldobott értesítések|Darabszám|Összesen|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|Nincsenek méretek|
|kimenő. mpns. invalidcredentials|Igen|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. invalidnotificationformat|Igen|MPNS – érvénytelen értesítési formátum|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|Nincsenek méretek|
|kimenő. mpns. pnserror|Igen|MPNS hibák|Darabszám|Összesen|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. mpns. sikeres|Igen|MPNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. mpns. szabályozva|Igen|MPNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|Nincsenek méretek|
|kimenő. wns. authenticationerror|Igen|WNS-hitelesítési hibák|Darabszám|Összesen|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincsenek méretek|
|kimenő. wns. badchannel|Igen|WNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. wns. channeldisconnected|Igen|WNS csatorna leválasztva|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. channelthrottled|Igen|WNS csatorna szabályozása|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|Nincsenek méretek|
|kimenő. wns. Dropped|Igen|WNS eldobott értesítések|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. expiredchannel|Igen|WNS lejárt csatorna hibája|Darabszám|Összesen|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|Nincsenek méretek|
|kimenő. wns. invalidcredentials|Igen|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincsenek méretek|
|kimenő. wns. invalidnotificationformat|Igen|WNS – érvénytelen értesítési formátum|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|Nincsenek méretek|
|kimenő. wns. invalidnotificationsize|Igen|A WNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az értesítési tartalom túl nagy (WNS állapot: 413).|Nincsenek méretek|
|kimenő. wns. invalidtoken|Igen|WNS-hitelesítési hibák (érvénytelen token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|Nincsenek méretek|
|kimenő. wns. pnserror|Igen|WNS hibák|Darabszám|Összesen|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|Nincsenek méretek|
|kimenő. wns. sikeres|Igen|WNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. wns. szabályozva|Igen|WNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|Nincsenek méretek|
|kimenő. wns. tokenproviderunreachable|Igen|WNS-hitelesítési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem érhető el.|Nincsenek méretek|
|kimenő. wns. wrongtoken|Igen|WNS-hitelesítési hibák (hibás token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincsenek méretek|
|regisztráció. All|Igen|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |Nincsenek méretek|
|regisztráció. Create|Igen|Regisztráció-létrehozási műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs létrehozás száma.|Nincsenek méretek|
|regisztráció. Delete|Igen|Regisztrációs törlési műveletek|Darabszám|Összesen|A regisztráció sikeres törléseinak száma.|Nincsenek méretek|
|regisztráció. Get|Igen|Regisztrálási olvasási műveletek|Darabszám|Összesen|A sikeres regisztrációs lekérdezések száma.|Nincsenek méretek|
|regisztráció. frissítés|Igen|Regisztrációs frissítési műveletek|Darabszám|Összesen|A sikeres regisztrációs frissítések száma.|Nincsenek méretek|
|ütemezett. függőben|Igen|Függőben lévő ütemezett értesítések|Darabszám|Összesen|Függőben lévő ütemezett értesítések|Nincsenek méretek|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre álló memória Average_%-ban|Igen|Rendelkezésre álló memória%-ban|Darabszám|Átlag|Rendelkezésre álló memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Igen|Rendelkezésre álló swap-terület (%)|Darabszám|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Igen|Előjegyzett memória%-ban használatban|Darabszám|Átlag|Average_%-os előjegyzett bájtok használatban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% DPC idő|Igen|% DPC idő|Darabszám|Átlag|Average_% DPC idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad inode (%)|Igen|Szabad inode%-ban|Darabszám|Átlag|Average_ szabad inode (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Igen|Szabad terület (%)|Darabszám|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Igen|Üresjáratban eltöltött időhányad (%)|Darabszám|Átlag|Average_ üresjárati idő%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Igen|Megszakítási idő%-ban|Darabszám|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% IO várakozási idő|Igen|I/o várakozási idő%-ban|Darabszám|Átlag|Average_% IO várakozási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os szép idő|Igen|% Nice idő|Darabszám|Átlag|Average_%-os szép idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|Igen|%-Os privilegizált idő|Darabszám|Átlag|Average_%-os privilegizált idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|Igen|A processzor kihasználtsága (%)|Darabszám|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ használt inode|Igen|Felhasznált inode%-ban|Darabszám|Átlag|Average_ használt inode|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Igen|Felhasznált memória (%)|Darabszám|Átlag|Felhasznált memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Igen|Felhasznált terület (%)|Darabszám|Átlag|Average_ felhasznált terület%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Igen|Felhasznált swap-terület%-ban|Darabszám|Átlag|Average_ felhasznált lapozófájl-terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Igen|Felhasználói idő%-ban|Darabszám|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban|Igen|Rendelkezésre álló memória (megabájt)|Darabszám|Átlag|Average_Available MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB memória|Igen|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Igen|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban való swap|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Igen|Átlagos írási idő (mp/olvasás)|Darabszám|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Lemez (mp/átvitel)|Igen|Átlagos műveleti idő (mp/átvitel)|Darabszám|Átlag|Average_Avg. Lemez (mp/átvitel)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Igen|Átlagos írási idő (mp/írás)|Darabszám|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Igen|Fogadott bájtok/s|Darabszám|Átlag|Fogadott Average_Bytes/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Igen|Küldési sebesség (bájt/s)|Darabszám|Átlag|Küldési Average_Bytes másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Igen|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összesen/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Igen|Lemez aktuális várólistájának hossza|Darabszám|Átlag|Average_Current a lemez várólistájának hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Igen|Lemez olvasási sebessége (bájt/s)|Darabszám|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Igen|Olvasási sebesség (lemez/mp)|Darabszám|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Igen|Lemez átvitele másodpercenként|Darabszám|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Igen|Lemez írási sebessége (bájt/s)|Darabszám|Átlag|Írási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Igen|Írási sebesség (írás/mp)|Darabszám|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Igen|Szabad terület (MB)|Darabszám|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Igen|Szabad fizikai memória|Darabszám|Átlag|Fizikai memória Average_Free|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Igen|Szabad terület a Lapozófájlokban|Darabszám|Átlag|Average_Free lemezterület a Lapozófájlokban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free virtuális memória|Igen|Szabad virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Igen|Logikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Logical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Igen|Olvasott lap/mp|Darabszám|Átlag|Olvasási Average_Page másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Page|Igen|Írási idő/mp|Darabszám|Átlag|Írás/mp Average_Page|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pages előállított/másodperc|Igen|Lap/mp|Darabszám|Átlag|Average_Pages előállított/másodperc|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|Igen|PCT rendszerjogosultságú idő|Darabszám|Átlag|Average_Pct emelt szintű idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|Igen|PCT felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Igen|Fizikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Physical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processes|Igen|Folyamatok|Darabszám|Átlag|Average_Processes|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Igen|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Igen|Lapozófájlokban tárolt méret|Darabszám|Átlag|Lapozófájlokban tárolt Average_Size|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total bájtok|Igen|Bájtok összesen|Darabszám|Átlag|Average_Total bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Total bájtok száma|Igen|Fogadott bájtok összesen|Darabszám|Átlag|Fogadott Average_Total bájtok száma|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított bájtok|Igen|Továbbított bájtok összesen|Darabszám|Átlag|Average_Total továbbított bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Ütközések Average_Total|Igen|Ütközések összesen|Darabszám|Átlag|Ütközések Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Igen|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Igen|Továbbított csomagok összesen|Darabszám|Átlag|Average_Total továbbított csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rx-hibák Average_Total|Igen|Rx-hibák összesen|Darabszám|Átlag|Rx-hibák Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total TX-hibák|Igen|TX-hibák összesen|Darabszám|Átlag|Average_Total TX-hibák|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Uptime|Igen|Üzemidő|Darabszám|Átlag|Average_Uptime|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used MB-ban felcserélt terület|Igen|Felhasznált memória (MB) – lapozófájl|Darabszám|Átlag|Average_Used MB-ban felcserélt terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Igen|Felhasznált memória (kilobájt)|Darabszám|Átlag|Average_Used memória kilobájtban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Igen|Felhasznált memória (MB)|Darabszám|Átlag|Average_Used memória MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Users|Igen|Felhasználók|Darabszám|Átlag|Average_Users|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Igen|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Esemény|Igen|Esemény|Darabszám|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|
|Szívverés|Igen|Szívverés|Darabszám|Összesen|Szívverés|Számítógép, OSType, verzió, SourceComputerId|
|Frissítés|Igen|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/társaik

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Igen|Kimenő forgalom aránya|BitsPerSecond|Átlag|Kimenő forgalom sebessége (bit/mp)|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Igen|Bejövő forgalom aránya|BitsPerSecond|Átlag|Bejövő forgalom sebessége bit/másodpercben|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability|Igen|Munkamenet rendelkezésre állása|Darabszám|Átlag|A társ-munkamenet rendelkezésre állása|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PrefixLatency|Igen|Előtag-késés|Ezredmásodpercben|Átlag|Medián előtag késése|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|memory_metric|Igen|Memória (Gen1)|Bájt|Átlag|Memory. Tartomány 0-3 GB a1, 0-5 GB az a2, 0-10 GB, a3, 0-25 GB, a4, 0-50 GB, a5 és 0-100 GB, A6. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|memory_thrashing_metric|Igen|Memória-Kiverés (adatkészletek) (Gen1)|Százalék|Átlag|Memória átlagos kiverése. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|qpu_high_utilization_metric|Igen|QPU magas kihasználtsága (Gen1)|Darabszám|Összesen|A QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|QueryDuration|Igen|Lekérdezés időtartama (adatkészletek) (Gen1)|Ezredmásodpercben|Átlag|A DAX-lekérdezés időtartama az utolsó intervallumban. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|
|QueryPoolJobQueueLength|Igen|Lekérdezési készlet feladatok várólistájának hossza (adatkészletek) (Gen1)|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma. Csak Power BI Embedded 1. generációs erőforrások esetén támogatott.|Nincsenek méretek|


## <a name="microsoftpurviewaccounts"></a>Microsoft. hatáskörébe/fiókok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ScanCancelled|Igen|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított ellenőrzések számát jelzi.||
|ScanCompleted|Igen|A vizsgálat befejeződött|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma sikeresen befejeződött.||
|ScanFailed|Igen|Sikertelen vizsgálat|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma nem sikerült.||
|ScanTimeTaken|Igen|Vizsgálat ideje elvégezve|Másodperc|Összesen|A teljes vizsgálati időt jelzi másodpercben.||


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|ActiveListeners|Nem|ActiveListeners|Darabszám|Összesen|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|BytesTransferred|Igen|BytesTransferred|Bájt|Összesen|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|ListenerConnections-ClientError|Nem|ListenerConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|ListenerConnections-ServerError|Nem|ListenerConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|ListenerConnections-Success|Nem|ListenerConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName, Operationresult tevékenységen|
|ListenerConnections-TotalRequests|Nem|ListenerConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|Bontásai|Nem|Bontásai|Darabszám|Összesen|A Microsoft. Relay bontásai összesen.|EntityName|
|SenderConnections-ClientError|Nem|SenderConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|SenderConnections-ServerError|Nem|SenderConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|SenderConnections-Success|Nem|SenderConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName, Operationresult tevékenységen|
|SenderConnections-TotalRequests|Nem|SenderConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|SenderDisconnects|Nem|SenderDisconnects|Darabszám|Összesen|A Microsoft. Relay SenderDisconnects összesen.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>Microsoft. Resources/előfizetések

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Késés|Igen|Bejövő HTTP-kérelmek késésének adatvédelme|Darabszám|Átlag|Bejövő HTTP-kérelmek késésének adatvédelme|Metódus, névtér, RequestRegion, ResourceType, Microsoft. SubscriptionId|
|Adatforgalom|Igen|Adatforgalom|Darabszám|Átlag|Http-forgalom|RequestRegion, StatusCode, StatusCodeClass, ResourceType, névtér, Microsoft. SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|SearchLatency|Igen|Keresési késés|Másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|Nincsenek méretek|
|SearchQueriesPerSecond|Igen|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincsenek méretek|
|ThrottledSearchQueriesPercentage|Igen|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|Nincsenek méretek|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. ServiceBus összes aktív kapcsolata.||
|ActiveMessages|Nem|Üzenetsor vagy témakör aktív üzeneteinek száma.|Darabszám|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma.|EntityName|
|ConnectionsClosed|Nem|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|Nem|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus megnyitott kapcsolatainak száma.|EntityName|
|CPUXNS|Nem|CPU (elavult)|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája. Ez a metrika depricated. Használja helyette a CPU-metrikát (NamespaceCpuUsage).|Replika|
|DeadletteredMessages|Nem|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|Darabszám|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|EntityName|
|IncomingMessages|Igen|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő üzenetei.|EntityName|
|IncomingRequests|Igen|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő kérései.|EntityName|
|Üzenetek|Nem|Üzenetsor/téma üzeneteinek száma.|Darabszám|Átlag|Üzenetsor/téma üzeneteinek száma.|EntityName|
|NamespaceCpuUsage|Nem|CPU|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája.|Replika|
|NamespaceMemoryUsage|Nem|Memóriahasználat|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma.|Replika|
|OutgoingMessages|Igen|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus kimenő üzenetei.|EntityName|
|ScheduledMessages|Nem|Az üzenetsor/témakör ütemezett üzeneteinek száma.|Darabszám|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma.|EntityName|
|Kiszolgálóhibái|Nem|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. ServiceBus esetében.|EntityName, Operationresult tevékenységen|
|Méret|Nem|Méret|Bájt|Átlag|Várólista/témakör mérete bájtban.|EntityName|
|SuccessfulRequests|Nem|Sikeres kérelmek|Darabszám|Összesen|Névtér összes sikeres kérelme|EntityName, Operationresult tevékenységen|
|ThrottledRequests|Nem|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. ServiceBus által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|UserErrors|Nem|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. ServiceBus felhasználói hibái.|EntityName, Operationresult tevékenységen|
|WSXNS|Nem|Memóriahasználat (elavult)|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma. Ez a metrika elavult. Használja helyette a memóriahasználat (NamespaceMemoryUsage) metrikáját.|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/alkalmazások

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActualCpu|Nem|ActualCpu|Darabszám|Átlag|Tényleges CPU-használat a Millon magokban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualMemory|Nem|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedCpu|Nem|AllocatedCpu|Darabszám|Átlag|A tárolóhoz a Millet magokban lefoglalt CPU|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedMemory|Nem|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ApplicationStatus|Nem|ApplicationStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|Tároló állapota:|Nem|Tároló állapota:|Darabszám|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName, állapot|
|CpuUtilization|Nem|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|MemoryUtilization|Nem|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|RestartCount|Nem|RestartCount|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, status, szolgáltatásnév, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|Nem|ServiceReplicaStatus|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, status, szolgáltatásnév, ServiceReplicaName|
|ServiceStatus|Nem|ServiceStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, állapot, szolgáltatásnév|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ConnectionCount|Igen|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|InboundTraffic|Igen|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincsenek méretek|
|MessageCount|Igen|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|Nincsenek méretek|
|OutboundTraffic|Igen|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincsenek méretek|
|SystemErrors|Igen|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|Nincsenek méretek|
|UserErrors|Igen|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|Nincsenek méretek|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft. SignalRService/WebPubSub

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ConnectionCount|Igen|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolatok mennyisége.|Nincsenek méretek|
|InboundTraffic|Igen|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincsenek méretek|
|OutboundTraffic|Igen|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincsenek méretek|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Igen|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|Nincsenek méretek|
|io_bytes_read|Igen|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|Nincsenek méretek|
|io_bytes_written|Igen|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|Nincsenek méretek|
|io_requests|Igen|IO-kérelmek száma|Darabszám|Átlag|IO-kérelmek száma|Nincsenek méretek|
|reserved_storage_mb|Igen|Tárterület fenntartva|Darabszám|Átlag|Tárterület fenntartva|Nincsenek méretek|
|storage_space_used_mb|Igen|Felhasznált tárterület|Darabszám|Átlag|Felhasznált tárterület|Nincsenek méretek|
|virtual_core_count|Igen|Virtuális mag száma|Darabszám|Átlag|Virtuális mag száma|Nincsenek méretek|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_queries|Igen|Aktív lekérdezések|Darabszám|Összesen|Aktív lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|allocated_data_storage|Igen|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|app_cpu_billed|Igen|Az alkalmazás CPU-számlázása|Darabszám|Összesen|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_cpu_percent|Igen|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_memory_percent|Igen|Az alkalmazás memóriájának százaléka|Százalék|Átlag|Az alkalmazás memóriájának százalékos aránya. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|base_blob_size_bytes|Igen|Az alap blob tárterületének mérete|Bájt|Maximum|Az alap blob tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|blocked_by_firewall|Igen|A tűzfal blokkolja|Darabszám|Összesen|A tűzfal blokkolja|Nincsenek méretek|
|cache_hit_percent|Igen|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|cache_used_percent|Igen|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|connection_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connection_successful|Igen|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_limit|Igen|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|cpu_percent|Igen|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Igen|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|holtpont|Igen|Holtpontok|Darabszám|Összesen|Holtpontok. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|diff_backup_size_bytes|Igen|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|dtu_consumption_percent|Igen|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_limit|Igen|DTU korlátja|Darabszám|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_used|Igen|Használt DTU|Darabszám|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dwu_consumption_percent|Igen|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_limit|Igen|DWU korlátja|Darabszám|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_used|Igen|Használt DWU|Darabszám|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|full_backup_size_bytes|Igen|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|local_tempdb_usage_percent|Igen|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|log_backup_size_bytes|Igen|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. A virtuális mag-alapú és a nagy kapacitású adatbázisokra vonatkozik.|Nincsenek méretek|
|log_write_percent|Igen|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|memory_usage_percent|Igen|Memória százaléka|Százalék|Maximum|Memória százalékos aránya Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|physical_data_read_percent|Igen|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|queued_queries|Igen|Várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|sessions_percent|Igen|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|snapshot_backup_size_bytes|Igen|Pillanatkép biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő pillanatkép biztonsági mentési tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|sqlserver_process_core_percent|Igen|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|sqlserver_process_memory_percent|Igen|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage|Igen|Felhasznált adatterület|Bájt|Maximum|Felhasznált adatterület. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage_percent|Igen|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatterület felhasznált százaléka százalékban. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|tempdb_data_size|Igen|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|A tempdb adatfájljaiban használt lemezterület kilobájtban. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_size|Igen|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|A tempdb tranzakciós naplófájljában használt lemezterület kilobájtban megadva. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_used_percent|Igen|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb tranzakciós naplófájlban használt százalékos érték. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|wlg_active_queries|Igen|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|Aktív lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Igen|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Azok a lekérdezések, amelyek túllépték a munkaterhelés-csoport időtúllépését. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Igen|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások a teljes rendszerterhelési csoporthoz viszonyított százalékos aránya. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Igen|Munkaterhelés-csoport lefoglalása a Cap-erőforrás százaléka alapján|Százalék|Maximum|Az erőforrások megadott százalékos aránya a munkaterhelési csoportok meghatározott erőforrásaihoz viszonyítva. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Igen|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport számára engedélyezett erőforrások százalékos arányának korlátozása, figyelembe véve a más munkaterhelés-csoportok számára kiosztott tényleges minimális erőforrás-százalékos arányt. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Igen|Effektív minimális erőforrás százaléka|Százalék|Maximum|A munkaterhelés-csoport számára fenntartott és elkülönített erőforrások minimális százalékos aránya, a szolgáltatási szint minimumának figyelembevételével. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Igen|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|workers_percent|Igen|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|xtp_storage_percent|Igen|In-Memory OLTP-tárolási százalék|Százalék|Átlag|In-Memory OLTP a tárolási százalékot. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/kiszolgálók/elasticPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|allocated_data_storage|Igen|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|Nincsenek méretek|
|allocated_data_storage_percent|Igen|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|Nincsenek méretek|
|cpu_limit|Igen|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|cpu_percent|Igen|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Igen|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|database_allocated_data_storage|Nem|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId|
|database_cpu_limit|Nem|CPU-korlát|Darabszám|Átlag|CPU-korlát|DatabaseResourceId|
|database_cpu_percent|Nem|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId|
|database_cpu_used|Nem|Felhasznált CPU|Darabszám|Átlag|Felhasznált CPU|DatabaseResourceId|
|database_dtu_consumption_percent|Nem|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId|
|database_eDTU_used|Nem|használt eDTU|Darabszám|Átlag|használt eDTU|DatabaseResourceId|
|database_log_write_percent|Nem|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|DatabaseResourceId|
|database_physical_data_read_percent|Nem|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId|
|database_sessions_percent|Nem|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId|
|database_storage_used|Nem|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId|
|database_workers_percent|Nem|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|DatabaseResourceId|
|dtu_consumption_percent|Igen|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_limit|Igen|eDTU korlátja|Darabszám|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_used|Igen|használt eDTU|Darabszám|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|log_write_percent|Igen|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|Nincsenek méretek|
|physical_data_read_percent|Igen|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|sessions_percent|Igen|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincsenek méretek|
|sqlserver_process_core_percent|Igen|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|sqlserver_process_memory_percent|Igen|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|storage_limit|Igen|Maximális adatméret|Bájt|Átlag|Maximális adatméret|Nincsenek méretek|
|storage_percent|Igen|Felhasznált adatterület százalékos aránya|Százalék|Átlag|Felhasznált adatterület százalékos aránya|Nincsenek méretek|
|storage_used|Igen|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|Nincsenek méretek|
|tempdb_data_size|Igen|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|A tempdb adatfájljaiban használt lemezterület kilobájtban.|Nincsenek méretek|
|tempdb_log_size|Igen|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|A tempdb tranzakciós naplófájljában használt lemezterület kilobájtban megadva.|Nincsenek méretek|
|tempdb_log_used_percent|Igen|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb tranzakciós naplófájlban használt százalékos arány|Nincsenek méretek|
|workers_percent|Igen|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|Nincsenek méretek|
|xtp_storage_percent|Igen|In-Memory OLTP-tárolási százalék|Százalék|Átlag|In-Memory OLTP-tárolási százalék|Nincsenek méretek|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|Igen|Felhasznált kapacitás|Bájt|Átlag|A Storage-fiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. A Premium Storage-fiókok és a blob Storage-fiókok esetében ugyanaz, mint a BlobCapacity vagy a FileCapacity.|Nincsenek méretek|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|Nem|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Nem|Blobok száma|Darabszám|Átlag|A Storage-fiókban tárolt blob-objektumok száma.|BlobType, szintű|
|BlobProvisionedSize|Nem|BLOB kiépített mérete|Bájt|Átlag|A Storage-fiók Blob serviceban kiépített tárterület mérete bájtban megadva.|BlobType, szintű|
|ContainerCount|Igen|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiókban lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|Nem|Index kapacitása|Bájt|Átlag|A Azure Data Lake Storage Gen2 hierarchikus index által használt tárterület mennyisége.|Nincsenek méretek|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|Nem|Fájl kapacitása|Bájt|Átlag|A Storage-fiók által használt file Storage mennyisége.|Fájlmegosztás|
|FileCount|Nem|Fájlok száma|Darabszám|Átlag|A Storage-fiókban található fájlok száma.|Fájlmegosztás|
|FileShareCapacityQuota|Nem|Fájlmegosztás kapacitásának kvótája|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareCount|Nem|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiókban lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareProvisionedIOPS|Nem|Fájlmegosztási kiépített IOPS|Bájt|Átlag|A Premium file Storage-fiókban a prémium fájlmegosztás kiépített IOPS alapszáma. Ezt a számot a megosztási kapacitás kiosztott mérete (kvóta) alapján számítjuk ki.|Fájlmegosztás|
|FileShareSnapshotCount|Nem|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Nem|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Igen|Várólista kapacitása|Bájt|Átlag|A Storage-fiók által használt üzenetsor-tároló mennyisége.|Nincsenek méretek|
|QueueCount|Igen|Várólista száma|Darabszám|Átlag|A Storage-fiókban lévő várólisták száma.|Nincsenek méretek|
|QueueMessageCount|Igen|Üzenetsor-üzenetek száma|Darabszám|Átlag|A nem lejárt üzenetsor-üzenetek száma a Storage-fiókban.|Nincsenek méretek|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Igen|Tábla kapacitása|Bájt|Átlag|A Storage-fiók által használt Table Storage mennyisége.|Nincsenek méretek|
|TableCount|Igen|Táblák száma|Darabszám|Átlag|A Storage-fiókban lévő táblák száma.|Nincsenek méretek|
|TableEntityCount|Igen|Tábla entitások száma|Darabszám|Átlag|A Storage-fiókban lévő tábla entitások száma.|Nincsenek méretek|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/gyorsítótárak

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ClientIOPS|Igen|Ügyfél teljes IOPS|Darabszám|Átlag|A gyorsítótár által feldolgozott ügyféloldali fájlok műveleteinek aránya.|Nincsenek méretek|
|ClientLatency|Igen|Ügyfél átlagos késése|Ezredmásodpercben|Átlag|Az ügyféloldali fájl műveleteinek átlagos késése a gyorsítótárban.|Nincsenek méretek|
|ClientLockIOPS|Igen|Ügyfél-zárolási IOPS|CountPerSecond|Átlag|Az ügyfél fájljának zárolási műveletei másodpercenként.|Nincsenek méretek|
|ClientMetadataReadIOPS|Igen|Ügyfél metaadatainak olvasása IOPS|CountPerSecond|Átlag|Az adatolvasások kivételével a gyorsítótárba elküldett ügyféloldali műveletek sebessége, amely nem módosítja az állandó állapotot.|Nincsenek méretek|
|ClientMetadataWriteIOPS|Igen|Ügyfél metaadatainak írási IOPS|CountPerSecond|Átlag|A gyorsítótárba küldendő, az adatírásokat nem tartalmazó, az állandó állapotot módosító ügyfél-fájl műveleteinek aránya.|Nincsenek méretek|
|ClientReadIOPS|Igen|Ügyfél olvasási IOPS|CountPerSecond|Átlag|Az ügyfél olvasási műveletei másodpercenként.|Nincsenek méretek|
|ClientReadThroughput|Igen|Gyorsítótár átlagos olvasási átviteli sebessége|BytesPerSecond|Átlag|Az ügyfél olvasási adatátviteli sebessége.|Nincsenek méretek|
|ClientWriteIOPS|Igen|Ügyfél írási IOPS|CountPerSecond|Átlag|Az ügyfél írási műveletei másodpercenként.|Nincsenek méretek|
|ClientWriteThroughput|Igen|Gyorsítótár átlagos írási sebessége|BytesPerSecond|Átlag|Az ügyfél írási adatátviteli sebessége.|Nincsenek méretek|
|StorageTargetAsyncWriteThroughput|Igen|StorageTarget aszinkron írási átviteli sebesség|BytesPerSecond|Átlag|A gyorsítótárnak az adatokat egy adott StorageTarget való aszinkron módon írási sebessége. Ezek olyan alkalmi írások, amelyek nem okozzák az ügyfelek blokkolását.|StorageTarget|
|StorageTargetFillThroughput|Igen|StorageTarget-kitöltési sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár beolvassa az adatokat a StorageTarget a gyorsítótár-kihagyás kezelésére.|StorageTarget|
|StorageTargetHealth|Igen|Tárolási cél állapota|Darabszám|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a tárolási célok között.|Nincsenek méretek|
|StorageTargetIOPS|Igen|Összes StorageTarget-IOPS|Darabszám|Átlag|Az összes fájl műveletének sebessége, amelyet a gyorsítótár küld egy adott StorageTarget.|StorageTarget|
|StorageTargetLatency|Igen|StorageTarget késés|Ezredmásodpercben|Átlag|A gyorsítótár által a partricular-StorageTarget küldött összes fájl műveletének átlagos ciklikus késése.|StorageTarget|
|StorageTargetMetadataReadIOPS|Igen|StorageTarget-metaadatok IOPS olvasása|CountPerSecond|Átlag|A nem állandó állapotot módosító, valamint az olvasási művelet kihagyása után a gyorsítótár egy adott StorageTarget küldi az adatokat.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Igen|StorageTarget-metaadatok írási IOPS|CountPerSecond|Átlag|Az állandó állapotot módosító és az írási műveletet nem tartalmazó, a gyorsítótár által egy adott StorageTarget küldött fájl-műveletek aránya.|StorageTarget|
|StorageTargetReadAheadThroughput|Igen|StorageTarget – olvasási sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár a StorageTarget beolvassa az adatokat.|StorageTarget|
|StorageTargetReadIOPS|Igen|StorageTarget olvasási IOPS|CountPerSecond|Átlag|A fájl olvasási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|StorageTargetSyncWriteThroughput|Igen|StorageTarget szinkron írási sebessége|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár szinkron módon ír adatokat egy adott StorageTarget. Ezek az írások, amelyek az ügyfelek blokkolását okozzák.|StorageTarget|
|StorageTargetTotalReadThroughput|Igen|StorageTarget összesen olvasási átviteli sebesség|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat olvas egy adott StorageTarget.|StorageTarget|
|StorageTargetTotalWriteThroughput|Igen|StorageTarget összes írási sebessége|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat ír egy adott StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Igen|StorageTarget írási IOPS|Darabszám|Átlag|A fájl írási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|Üzemidő|Igen|Üzemidő|Darabszám|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a figyelési rendszer között.|Nincsenek méretek|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Igen|Szinkronizálási munkamenet eredménye|Darabszám|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Igen|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecallComputedSuccessRate|Igen|Felhőbeli rétegek felidézésének sikerességi aránya|Százalék|Átlag|A sikeres visszahívások százalékos aránya|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Igen|Felhőbeli rétegek felidézésének mérete alkalmazás szerint|Bájt|Összesen|Az alkalmazás által visszahívott adatmennyiség|SyncGroupName, kiszolgálónév, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Igen|Felhőbeli rétegek felidézésének mérete|Bájt|Összesen|Visszahívott adatmennyiség|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Igen|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Igen|Felhőbeli rétegek felidézésének átviteli sebessége|BytesPerSecond|Átlag|Adatvisszahívási sebesség mérete|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Igen|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Igen|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Igen|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Igen|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Igen|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Igen|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Igen|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Igen|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Igen|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Igen|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Igen|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Igen|Sikertelen függvények kérései|Darabszám|Összesen|Sikertelen függvények kérései|LogicalName, PartitionId|
|AMLCalloutInputEvents|Igen|Függvények eseményei|Darabszám|Összesen|Függvények eseményei|LogicalName, PartitionId|
|AMLCalloutRequests|Igen|Függvények kérései|Darabszám|Összesen|Függvények kérései|LogicalName, PartitionId|
|ConversionErrors|Igen|Adatátalakítási hibák|Darabszám|Összesen|Adatátalakítási hibák|LogicalName, PartitionId|
|DeserializationError|Igen|Bemeneti deszerializálási hibák|Darabszám|Összesen|Bemeneti deszerializálási hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Igen|Megrendelési események|Darabszám|Összesen|Megrendelési események|LogicalName, PartitionId|
|EarlyInputEvents|Igen|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|LogicalName, PartitionId|
|Hibák|Igen|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|LogicalName, PartitionId|
|InputEventBytes|Igen|Bemeneti esemény bájtjai|Bájt|Összesen|Bemeneti esemény bájtjai|LogicalName, PartitionId|
|InputEvents|Igen|Bemeneti események|Darabszám|Összesen|Bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Igen|Várakozó bemeneti eseményei|Darabszám|Maximum|Várakozó bemeneti eseményei|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Igen|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|LogicalName, PartitionId|
|LateInputEvents|Igen|Késői bemeneti események|Darabszám|Összesen|Késői bemeneti események|LogicalName, PartitionId|
|OutputEvents|Igen|Kimeneti események|Darabszám|Összesen|Kimeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Igen|Vízjel késleltetése|Másodperc|Maximum|Vízjel késleltetése|LogicalName, PartitionId|
|ProcessCPUUsagePercentage|Igen|CPU-kihasználtság%-ban (előzetes verzió)|Százalék|Maximum|CPU-kihasználtság%-ban (előzetes verzió)|LogicalName, PartitionId|
|ResourceUtilization|Igen|SU% kihasználtsága|Százalék|Maximum|SU% kihasználtsága|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|Nem|Feldolgozott adatmennyiség (bájt)|Bájt|Összesen|Lekérdezések által feldolgozott adatmennyiség|Nincsenek méretek|
|BuiltinSqlPoolLoginAttempts|Nem|Bejelentkezési kísérletek|Darabszám|Összesen|A sikerül vagy sikertelen bejelentkezési kísérletek száma|Eredmény|
|BuiltinSqlPoolRequestsEnded|Nem|Befejezett kérelmek|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított kérelmek száma|Eredmény|
|IntegrationActivityRunsEnded|Nem|A tevékenység futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított integrációs tevékenységek száma|Eredmény, FailureType, tevékenység, ActivityType, folyamat|
|IntegrationPipelineRunsEnded|Nem|A folyamat futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított integrációs folyamat-futtatások száma|Eredmény, FailureType, folyamat|
|IntegrationTriggerRunsEnded|Nem|A trigger futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított integrációs eseményindítók száma|Eredmény, FailureType, trigger|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|Nem|lefoglalt virtuális mag|Darabszám|Maximum|Apache Spark készlethez lefoglalt virtuális mag|SubmitterId|
|BigDataPoolAllocatedMemory|Nem|Lefoglalt memória (GB)|Darabszám|Maximum|Lefoglalt memória a Apach Spark-készlethez (GB)|SubmitterId|
|BigDataPoolApplicationsActive|Nem|Aktív Apache Spark alkalmazások|Darabszám|Maximum|Aktív Apache Spark készlet összes alkalmazása|JobState|
|BigDataPoolApplicationsEnded|Nem|Lejárt Apache Spark alkalmazások|Darabszám|Összesen|A Apache Spark készlet-alkalmazások száma befejeződött|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveQueries|Nem|Aktív lekérdezések|Darabszám|Összesen|Az aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|IsUserDefined|
|AdaptiveCacheHitPercent|Nem|Adaptív gyorsítótár-találatok százalékos aránya|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár találati százalékos metrikájának használatával határozhatja meg, hogy szeretné-e bővíteni a további kapacitást, vagy futtassa újra a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|AdaptiveCacheUsedPercent|Nem|Adaptív gyorsítótár használt százaléka|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár használt százalékos metrikájának használatával határozhatja meg, hogy a további kapacitást szeretné-e méretezni, vagy újra kell futtatnia a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|Kapcsolatok|Igen|Kapcsolatok|Darabszám|Összesen|Az SQL-készletbe való teljes bejelentkezések száma|Eredmény|
|ConnectionsBlockedByFirewall|Nem|Tűzfal által letiltott kapcsolatok|Darabszám|Összesen|A tűzfalszabályok által letiltott kapcsolatok száma. Nyissa meg újra az SQL-készlethez tartozó hozzáférés-vezérlési szabályzatokat, és figyelje meg, hogy a kapcsolatok száma magas-e|Nincsenek méretek|
|CPUPercent|Nem|Felhasznált CPU százalékos aránya|Százalék|Maximum|CPU-kihasználtság az SQL-készlet összes csomópontja között|Nincsenek méretek|
|DWULimit|Nem|DWU korlátja|Darabszám|Maximum|Az SQL-készlet szolgáltatási szintjének célkitűzése|Nincsenek méretek|
|DWUUsed|Nem|Használt DWU|Darabszám|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. DWU-korláttal mérve * DWU százalék|Nincsenek méretek|
|DWUUsedPercent|Nem|DWU használt százalék|Százalék|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. A CPU-százalék és az adatio-százalék közötti maximális érték alapján mérhető|Nincsenek méretek|
|LocalTempDBUsedPercent|Nem|Helyi tempdb használt százalék|Százalék|Maximum|Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva|Nincsenek méretek|
|MemoryUsedPercent|Nem|Felhasznált memória százalékos aránya|Százalék|Maximum|Memória kihasználtsága az SQL-készlet összes csomópontján|Nincsenek méretek|
|QueuedQueries|Nem|Várólistán lévő lekérdezések|Darabszám|Összesen|Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát|IsUserDefined|
|WLGActiveQueries|Nem|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|A munkaterhelés csoporton belüli aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Nem|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit. A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozás miatt)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|Nem|Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján|Százalék|Maximum|Megjeleníti az erőforrások százalékos kiosztását a munkaterhelési csoport tényleges maximális erőforrásához viszonyítva. Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Nem|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások százalékos kiosztása a teljes rendszerrel viszonyítva|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Nem|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport tényleges Cap-erőforrásának százaléka. Ha más munkaterhelés-csoportok min_percentage_resource > 0, a effective_cap_percentage_resource arányosan csökken|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|Nem|Effektív minimális erőforrás százaléka|Százalék|Maximum|A tényleges minimális erőforrás-százalékos beállítás a szolgáltatási szint és a munkaterhelés-csoport beállításainak figyelembevételével engedélyezett. A tényleges min_percentage_resource magasabb szinten állítható be alacsonyabb szolgáltatási szinteken|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Nem|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Igen|Bejövő fogadott bájtok száma|Bájt|Összesen|Az összes eseményforrás által olvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Igen|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Igen|Bejövő fogadott üzenetek|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Igen|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Igen|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Igen|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Igen|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Igen|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Igen|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Igen|Bejövő fogadott bájtok száma|Bájt|Összesen|Az eseményforrás által beolvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Igen|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Igen|Bejövő fogadott üzenetek|Darabszám|Összesen|Az eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Igen|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Igen|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Igen|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Igen|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Igen|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Igen|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskReadBytesPerSecond|Igen|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|DiskReadLatency|Igen|Lemez olvasási késése|Ezredmásodpercben|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|Nincsenek méretek|
|DiskReadOperations|Igen|Lemezes olvasási műveletek|Darabszám|Összesen|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskWriteBytesPerSecond|Igen|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|Nincsenek méretek|
|DiskWriteLatency|Igen|Lemez írási késése|Ezredmásodpercben|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|Nincsenek méretek|
|DiskWriteOperations|Igen|Lemez írási műveletei|Darabszám|Összesen|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|MemoryActive|Igen|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|Nincsenek méretek|
|MemoryGranted|Igen|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|Nincsenek méretek|
|MemoryUsed|Igen|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|Nincsenek méretek|
|Bejövő hálózat|Igen|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|Kimenő hálózat|Igen|Kimenő hálózat|Bájt|Összesen|A továbbított forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkInBytesPerSecond|Igen|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkOutBytesPerSecond|Igen|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|Nincsenek méretek|
|PercentageCpuReady|Igen|CPU-készültség százalékos aránya|Ezredmásodpercben|Összesen|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRequests|Igen|Aktív kérések (elavult)|Darabszám|Összesen|Aktív kérések|Példány|
|AverageResponseTime|Igen|Átlagos válaszidő (elavult)|Másodperc|Átlag|A kérések kiszolgálásához szükséges átlagos idő másodpercben.|Példány|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|Az összes kezelőfelületen használt átlagos bejövő sávszélesség a MiB-ben.|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Az összes kezelőfelületen használt átlagos bejövő sávszélesség a MiB-ben.|Példány|
|CpuPercentage|Igen|Processzorhasználat (%)|Százalék|Átlag|Az előtér összes példányán használt átlagos processzor.|Példány|
|DiskQueueLength|Igen|Lemezvezérlő-várólista hossza|Darabszám|Átlag|A tárolás során várólistára vett olvasási és írási kérelmek átlagos száma. A lemez magas várólistájának hossza egy olyan alkalmazásra utal, amely a lemezes I/O-műveletek miatt lelassulhat.|Példány|
|Http101|Igen|Http 101|Darabszám|Összesen|Az 101-as HTTP-állapotkódot eredményező kérelmek száma.|Példány|
|Http2xx|Igen|Http-2xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 200, de < 300.|Példány|
|Http3xx|Igen|Http-3xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 300, de < 400.|Példány|
|Http401|Igen|HTTP 401|Darabszám|Összesen|A HTTP 401 állapotkódot eredményező kérések száma.|Példány|
|Http403|Igen|Http 403|Darabszám|Összesen|A HTTP 403 állapotkódot eredményező kérések száma.|Példány|
|Http404|Igen|Http 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérések száma.|Példány|
|Http406|Igen|Http 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérések száma.|Példány|
|Http4xx|Igen|Http-4xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 400, de < 500.|Példány|
|Http5xx|Igen|Http-kiszolgálói hibák|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 500, de < 600.|Példány|
|HttpQueueLength|Igen|Http-várólista hossza|Darabszám|Átlag|Azoknak a HTTP-kéréseknek az átlagos száma, amelyeket a várólistán kellett ülnie a teljesítése előtt. A magas vagy növekvő HTTP-várólista hossza a nagy terhelés alatt álló csomag tünete.|Példány|
|HttpResponseTime|Igen|Válaszidő|Másodperc|Átlag|A kérések kiszolgálásához szükséges idő másodpercben.|Példány|
|LargeAppServicePlanInstances|Igen|Nagy App Service-csomag feldolgozói|Darabszám|Átlag|Nagy App Service-csomag feldolgozói|Nincsenek méretek|
|MediumAppServicePlanInstances|Igen|Közepes App Service-csomag feldolgozói|Darabszám|Átlag|Közepes App Service-csomag feldolgozói|Nincsenek méretek|
|MemoryPercentage|Igen|Memória százaléka|Százalék|Átlag|Az előtér összes példányán felhasznált átlagos memória.|Példány|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|A kérelmek teljes száma a létrejövő HTTP-állapotkódtől függetlenül.|Példány|
|SmallAppServicePlanInstances|Igen|Kisméretű App Service-csomag feldolgozói|Darabszám|Átlag|Kisméretű App Service-csomag feldolgozói|Nincsenek méretek|
|TotalFrontEnds|Igen|Összes előtér|Darabszám|Átlag|Összes előtér|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CpuPercentage|Igen|Processzorhasználat (%)|Százalék|Átlag|A munkavégző készlet összes példányán használt átlagos CPU.|Példány|
|MemoryPercentage|Igen|Memória százaléka|Százalék|Átlag|A munkavégző készlet összes példányán használt átlagos memória.|Példány|
|WorkersAvailable|Igen|Rendelkezésre álló munkavégzők|Darabszám|Átlag|Rendelkezésre álló munkavégzők|Nincsenek méretek|
|WorkersTotal|Igen|Alkalmazottak összesen|Darabszám|Átlag|Alkalmazottak összesen|Nincsenek méretek|
|WorkersUsed|Igen|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Nincsenek méretek|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/kiszolgálófarmok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A csomag összes példányán használt átlagos bejövő sávszélesség.|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|A csomag összes példányán használt átlagos kimenő sávszélesség.|Példány|
|CpuPercentage|Igen|Processzorhasználat (%)|Százalék|Átlag|A csomag összes példányán használt átlagos CPU.|Példány|
|DiskQueueLength|Igen|Lemezvezérlő-várólista hossza|Darabszám|Átlag|A tárolás során várólistára vett olvasási és írási kérelmek átlagos száma. A lemez magas várólistájának hossza egy olyan alkalmazásra utal, amely a lemezes I/O-műveletek miatt lelassulhat.|Példány|
|HttpQueueLength|Igen|Http-várólista hossza|Darabszám|Átlag|Azoknak a HTTP-kéréseknek az átlagos száma, amelyeket a várólistán kellett ülnie a teljesítése előtt. A magas vagy növekvő HTTP-várólista hossza a nagy terhelés alatt álló csomag tünete.|Példány|
|MemoryPercentage|Igen|Memória százaléka|Százalék|Átlag|A csomag összes példányán használt átlagos memória.|Példány|
|SocketInboundAll|Igen|SocketInboundAll|Darabszám|Átlag|SocketInboundAll|Példány|
|SocketLoopback|Igen|SocketLoopback|Darabszám|Átlag|SocketLoopback|Példány|
|SocketOutboundAll|Igen|SocketOutboundAll|Darabszám|Átlag|SocketOutboundAll|Példány|
|SocketOutboundEstablished|Igen|SocketOutboundEstablished|Darabszám|Átlag|SocketOutboundEstablished|Példány|
|SocketOutboundTimeWait|Igen|SocketOutboundTimeWait|Darabszám|Átlag|SocketOutboundTimeWait|Példány|
|TcpCloseWait|Igen|TCP-bezárási várakozás|Darabszám|Átlag|TCP-bezárási várakozás|Példány|
|TcpClosing|Igen|TCP-zárás|Darabszám|Átlag|TCP-zárás|Példány|
|TcpEstablished|Igen|A TCP létrejött|Darabszám|Átlag|A TCP létrejött|Példány|
|TcpFinWait1|Igen|TCP fin WAIT 1|Darabszám|Átlag|TCP fin WAIT 1|Példány|
|TcpFinWait2|Igen|TCP fin WAIT 2|Darabszám|Átlag|TCP fin WAIT 2|Példány|
|TcpLastAck|Igen|TCP Last ACK|Darabszám|Átlag|TCP Last ACK|Példány|
|TcpSynReceived|Igen|TCP SYN kapott|Darabszám|Átlag|TCP SYN kapott|Példány|
|TcpSynSent|Igen|Eljuttatott TCP SYN|Darabszám|Átlag|Eljuttatott TCP SYN|Példány|
|TcpTimeWait|Igen|TCP-idő várakozása|Darabszám|Átlag|TCP-idő várakozása|Példány|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Igen|Kapcsolatok|Darabszám|Átlag|A homokozóban meglévő kötött szoftvercsatornák (w3wp.exe és annak alárendelt folyamatai) száma. A kötött szoftvercsatorna a kötési ()/Connect () API-k meghívásával jön létre, és addig marad, amíg az említett szoftvercsatorna le nem zárul a CloseHandle függvény hívásakor ()/closesocket () használatával.|Példány|
|AverageMemoryWorkingSet|Igen|Memória átlagos munkakészlete|Bájt|Átlag|Az alkalmazás által használt memória átlagos mérete (MB) (MiB).|Példány|
|AverageResponseTime|Igen|Átlagos válaszidő (elavult)|Másodperc|Átlag|Az alkalmazásnak a kérelmek kiszolgálásához szükséges átlagos ideje másodpercben.|Példány|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|Az alkalmazás által felhasznált bejövő sávszélesség mennyisége a MiB-ben.|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Az alkalmazás által felhasznált kimenő sávszélesség mennyisége a MiB-ben.|Példány|
|CpuTime|Igen|CPU-idő|Másodperc|Összesen|Az alkalmazás által felhasznált CPU mennyisége másodpercben. További információ erről a metrikáról. Nem alkalmazható Azure Functionsra. Lásd: https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-idő vs CPU-százalék).|Példány|
|CurrentAssemblies|Igen|Aktuális szerelvények|Darabszám|Átlag|Az alkalmazás összes alkalmazástartományok betöltött szerelvények aktuális száma.|Példány|
|FileSystemUsage|Igen|Fájlrendszer használata|Bájt|Átlag|Az alkalmazás által felhasznált fájlrendszerbeli kvóta százalékaránya.|Nincsenek méretek|
|FunctionExecutionCount|Igen|Függvény végrehajtásának száma|Darabszám|Összesen|Függvények végrehajtásának száma. Csak Azure Functions esetén jelen van.|Példány|
|FunctionExecutionUnits|Igen|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei Csak Azure Functions esetén jelen van.|Példány|
|Gen0Collections|Igen|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|Az alkalmazási folyamat kezdete óta a 0. generációs objektumok számát gyűjti a rendszer. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen1Collections|Igen|1. generációs Garbage gyűjtemények|Darabszám|Összesen|Azon alkalmak száma, amikor az 1. generációs objektumok az alkalmazási folyamat kezdete óta beszedett szemetet gyűjtenek. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen2Collections|Igen|2. generációs Garbage gyűjtemények|Darabszám|Összesen|A 2. generációs objektumok száma az alkalmazási folyamat kezdete óta beszedett szemetet.|Példány|
|Kezeli|Igen|Leírók száma|Darabszám|Átlag|Az alkalmazási folyamat által jelenleg megnyitott kezelők teljes száma.|Példány|
|HealthCheckStatus|Igen|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Igen|Http 101|Darabszám|Összesen|Az 101-as HTTP-állapotkódot eredményező kérelmek száma.|Példány|
|Http2xx|Igen|Http-2xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 200, de < 300.|Példány|
|Http3xx|Igen|Http-3xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 300, de < 400.|Példány|
|Http401|Igen|HTTP 401|Darabszám|Összesen|A HTTP 401 állapotkódot eredményező kérések száma.|Példány|
|Http403|Igen|Http 403|Darabszám|Összesen|A HTTP 403 állapotkódot eredményező kérések száma.|Példány|
|Http404|Igen|Http 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérések száma.|Példány|
|Http406|Igen|Http 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérések száma.|Példány|
|Http4xx|Igen|Http-4xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 400, de < 500.|Példány|
|Http5xx|Igen|Http-kiszolgálói hibák|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 500, de < 600.|Példány|
|HttpResponseTime|Igen|Válaszidő|Másodperc|Átlag|Az alkalmazás által a kérelmek kiszolgálásához szükséges idő (másodpercben).|Példány|
|IoOtherBytesPerSecond|Igen|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az adatokkal nem rendelkező I/O-műveletekhez (például vezérlési műveletekhez) tartozó bájtok kibocsátásának aránya.|Példány|
|IoOtherOperationsPerSecond|Igen|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olyan I/O-műveletek kiadására szolgál, amelyek nem rendelkeznek olvasási vagy írási műveletekkel.|Példány|
|IoReadBytesPerSecond|Igen|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az I/O-műveletek bájtjainak olvasási sebessége.|Példány|
|IoReadOperationsPerSecond|Igen|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olvasási I/O-műveletekre vonatkozó kiadási sebessége.|Példány|
|IoWriteBytesPerSecond|Igen|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|Az a sebesség, amellyel az alkalmazás a bájtok írását az I/O-műveletekhez.|Példány|
|IoWriteOperationsPerSecond|Igen|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata írási I/O-műveletek kiadásának gyakorisága.|Példány|
|MemoryWorkingSet|Igen|Memória munkakészlete|Bájt|Átlag|Az alkalmazás által a MiB-ben használt memória aktuális mennyisége.|Példány|
|PrivateBytes|Igen|Saját bájtok|Bájt|Átlag|A saját bájtok az alkalmazás által lefoglalt memória jelenlegi mérete (bájtban), amelyet más folyamatokkal nem lehet megosztani.|Példány|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|A kérelmek teljes száma a létrejövő HTTP-állapotkódtől függetlenül.|Példány|
|RequestsInApplicationQueue|Igen|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájában lévő kérelmek száma.|Példány|
|Szálak|Igen|Szálak száma|Darabszám|Átlag|Az alkalmazás folyamatában jelenleg aktív szálak száma.|Példány|
|TotalAppDomains|Igen|Alkalmazás összes tartománya|Darabszám|Átlag|Az alkalmazásban betöltött alkalmazástartományok aktuális száma.|Példány|
|TotalAppDomainsUnloaded|Igen|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Az alkalmazás indítása óta eltávolított alkalmazástartományok teljes száma.|Példány|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Igen|Kapcsolatok|Darabszám|Átlag|A homokozóban meglévő kötött szoftvercsatornák (w3wp.exe és annak alárendelt folyamatai) száma. A kötött szoftvercsatorna a kötési ()/Connect () API-k meghívásával jön létre, és addig marad, amíg az említett szoftvercsatorna le nem zárul a CloseHandle függvény hívásakor ()/closesocket () használatával.|Példány|
|AverageMemoryWorkingSet|Igen|Memória átlagos munkakészlete|Bájt|Átlag|Az alkalmazás által használt memória átlagos mérete (MB) (MiB).|Példány|
|AverageResponseTime|Igen|Átlagos válaszidő (elavult)|Másodperc|Átlag|Az alkalmazásnak a kérelmek kiszolgálásához szükséges átlagos ideje másodpercben.|Példány|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|Az alkalmazás által felhasznált bejövő sávszélesség mennyisége a MiB-ben.|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Az alkalmazás által felhasznált kimenő sávszélesség mennyisége a MiB-ben.|Példány|
|CpuTime|Igen|CPU-idő|Másodperc|Összesen|Az alkalmazás által felhasznált CPU mennyisége másodpercben. További információ erről a metrikáról. Nem alkalmazható Azure Functionsra. Lásd: https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-idő vs CPU-százalék).|Példány|
|CurrentAssemblies|Igen|Aktuális szerelvények|Darabszám|Átlag|Az alkalmazás összes alkalmazástartományok betöltött szerelvények aktuális száma.|Példány|
|FileSystemUsage|Igen|Fájlrendszer használata|Bájt|Átlag|Az alkalmazás által felhasznált fájlrendszerbeli kvóta százalékaránya.|Nincsenek méretek|
|FunctionExecutionCount|Igen|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|FunctionExecutionUnits|Igen|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei|Példány|
|Gen0Collections|Igen|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|Az alkalmazási folyamat kezdete óta a 0. generációs objektumok számát gyűjti a rendszer. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen1Collections|Igen|1. generációs Garbage gyűjtemények|Darabszám|Összesen|Azon alkalmak száma, amikor az 1. generációs objektumok az alkalmazási folyamat kezdete óta beszedett szemetet gyűjtenek. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|Példány|
|Gen2Collections|Igen|2. generációs Garbage gyűjtemények|Darabszám|Összesen|A 2. generációs objektumok száma az alkalmazási folyamat kezdete óta beszedett szemetet.|Példány|
|Kezeli|Igen|Leírók száma|Darabszám|Átlag|Az alkalmazási folyamat által jelenleg megnyitott kezelők teljes száma.|Példány|
|HealthCheckStatus|Igen|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Igen|Http 101|Darabszám|Összesen|Az 101-as HTTP-állapotkódot eredményező kérelmek száma.|Példány|
|Http2xx|Igen|Http-2xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 200, de < 300.|Példány|
|Http3xx|Igen|Http-3xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 300, de < 400.|Példány|
|Http401|Igen|HTTP 401|Darabszám|Összesen|A HTTP 401 állapotkódot eredményező kérések száma.|Példány|
|Http403|Igen|Http 403|Darabszám|Összesen|A HTTP 403 állapotkódot eredményező kérések száma.|Példány|
|Http404|Igen|Http 404|Darabszám|Összesen|A HTTP 404 állapotkódot eredményező kérések száma.|Példány|
|Http406|Igen|Http 406|Darabszám|Összesen|A HTTP 406 állapotkódot eredményező kérések száma.|Példány|
|Http4xx|Igen|Http-4xx|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 400, de < 500.|Példány|
|Http5xx|Igen|Http-kiszolgálói hibák|Darabszám|Összesen|A kérelmek száma, ami a HTTP-állapotkód = 500, de < 600.|Példány|
|HttpResponseTime|Igen|Válaszidő|Másodperc|Átlag|Az alkalmazás által a kérelmek kiszolgálásához szükséges idő (másodpercben).|Példány|
|IoOtherBytesPerSecond|Igen|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az adatokkal nem rendelkező I/O-műveletekhez (például vezérlési műveletekhez) tartozó bájtok kibocsátásának aránya.|Példány|
|IoOtherOperationsPerSecond|Igen|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olyan I/O-műveletek kiadására szolgál, amelyek nem rendelkeznek olvasási vagy írási műveletekkel.|Példány|
|IoReadBytesPerSecond|Igen|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|Az alkalmazási folyamat által az I/O-műveletek bájtjainak olvasási sebessége.|Példány|
|IoReadOperationsPerSecond|Igen|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata olvasási I/O-műveletekre vonatkozó kiadási sebessége.|Példány|
|IoWriteBytesPerSecond|Igen|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|Az a sebesség, amellyel az alkalmazás a bájtok írását az I/O-műveletekhez.|Példány|
|IoWriteOperationsPerSecond|Igen|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|Az alkalmazás folyamata írási I/O-műveletek kiadásának gyakorisága.|Példány|
|MemoryWorkingSet|Igen|Memória munkakészlete|Bájt|Átlag|Az alkalmazás által a MiB-ben használt memória aktuális mennyisége.|Példány|
|PrivateBytes|Igen|Saját bájtok|Bájt|Átlag|A saját bájtok az alkalmazás által lefoglalt memória jelenlegi mérete (bájtban), amelyet más folyamatokkal nem lehet megosztani.|Példány|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|A kérelmek teljes száma a létrejövő HTTP-állapotkódtől függetlenül.|Példány|
|RequestsInApplicationQueue|Igen|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájában lévő kérelmek száma.|Példány|
|Szálak|Igen|Szálak száma|Darabszám|Átlag|Az alkalmazás folyamatában jelenleg aktív szálak száma.|Példány|
|TotalAppDomains|Igen|Alkalmazás összes tartománya|Darabszám|Átlag|Az alkalmazásban betöltött alkalmazástartományok aktuális száma.|Példány|
|TotalAppDomainsUnloaded|Igen|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Az alkalmazás indítása óta eltávolított alkalmazástartományok teljes száma.|Példány|


## <a name="microsoftwebstaticsites"></a>Microsoft. Web/staticSites

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|BytesSent|Példány|
|FunctionErrors|Igen|FunctionErrors|Darabszám|Összesen|FunctionErrors|Példány|
|FunctionHits|Igen|FunctionHits|Darabszám|Összesen|FunctionHits|Példány|
|SiteErrors|Igen|SiteErrors|Darabszám|Összesen|SiteErrors|Példány|
|SiteHits|Igen|SiteHits|Darabszám|Összesen|SiteHits|Példány|

## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Azure Monitor mérőszámokról](../data-platform.md)
- [Riasztások létrehozása metrikákhoz](../alerts/alerts-overview.md)
- [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](../essentials/platform-logs-overview.md)
