---
title: Az Azure Storage figyelése, diagnosztizálása és megoldása | Microsoft Docs
description: Az Azure Storage szolgáltatással kapcsolatos problémák azonosításához, diagnosztizálásához és hibaelhárításához olyan szolgáltatásokat használhat, mint a Storage Analytics, az ügyféloldali naplózás és más külső eszközök.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 5943a65d61dee4fcf610b8cc45139674362995cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600717"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Áttekintés
A felhőalapú környezetben üzemeltetett elosztott alkalmazások hibáinak diagnosztizálása és hibaelhárítása összetettebb lehet, mint a hagyományos környezetekben. Az alkalmazások a IaaS-infrastruktúrában, a helyszínen, a mobileszközön vagy a környezetek valamilyen kombinációjában is üzembe helyezhetők. Az alkalmazás hálózati forgalma általában nyilvános és magánhálózatok bejárására is alkalmas lehet, és az alkalmazás több tárolási technológiát is használhat, például a Microsoft Azure Storage táblákat, blobokat, várólistákat vagy fájlokat, valamint más adattárakon, például a viszonyítási és dokumentum-adatbázisokon kívül.

Az alkalmazások sikeres kezeléséhez proaktív módon kell figyelnie őket, és meg kell értenie, hogyan diagnosztizálhatja és elháríthatja az összes aspektusát és azok függő technológiáit. Az Azure Storage-szolgáltatások felhasználóinak folyamatosan figyelniük kell az alkalmazás által a működés közben fellépő váratlan változásokhoz (például a szokásos válaszidő-időpontnál lassabban) használt tárolási szolgáltatásokat, és a naplózás használatával részletesebb adatokat gyűjthetnek, és részletesen elemezheti a problémát. A figyelésből és a naplózásból beszerzett diagnosztikai információk segítségével meghatározható az alkalmazás által észlelt probléma kiváltó oka. Ezután elháríthatja a problémát, és meghatározhatja a megoldásához szükséges lépéseket. Az Azure Storage egy alapszintű Azure-szolgáltatás, amely az ügyfelek által az Azure-infrastruktúrára telepített megoldások többségének fontos részét képezi. Az Azure Storage olyan képességeket tartalmaz, amelyekkel egyszerűbbé válik a felhőalapú alkalmazások tárolási problémáinak figyelése, diagnosztizálása és hibaelhárítása.

* [Bevezetés]
  * [Az útmutató rendszerezése]
* [A tárolási szolgáltatás figyelése]
  * [Figyelési szolgáltatás állapota]
  * [Kapacitás figyelése]
  * [A rendelkezésre állás monitorozása]
  * [Teljesítmény monitorozása]
* [Tárolási problémák diagnosztizálása]
  * [A szolgáltatás állapotával kapcsolatos problémák]
  * [Teljesítményproblémák]
  * [Hibák diagnosztizálása]
  * [A Storage Emulator problémái]
  * [Storage-naplózási eszközök]
  * [Hálózati naplózási eszközök használata]
* [Végpontok közötti nyomkövetés]
  * [A naplófájlok korrelációjának összekötése]
  * [Ügyfélkérelem azonosítója]
  * [Kiszolgálói kérelem azonosítója]
  * [Időbélyegek]
* [Hibaelhárítási útmutató]
  * [A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]
  * [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
  * [A mérőszámok magas AverageServerLatency értéket mutatnak]
  * [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]
  * [A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]
  * [A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]
  * [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]
  * [Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap]
  * [Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap]
  * [Az ügyfél HTTP 409 (Ütközés) hibaüzeneteket kap]
  * [A metrikák az alacsony PercentSuccess vagy az elemzési naplóbejegyzések esetében a ClientOtherErrors tranzakciós állapotú műveleteit mutatják]
  * [A kapacitási mérőszámok váratlan növekedést mutatnak a tárolási kapacitás kihasználtsága terén]
  * [A probléma a Storage Emulator fejlesztési vagy tesztelési célú használatból ered]
  * [Problémákba ütközik a .NET-hez készült Azure SDK telepítésekor]
  * [Más probléma van a tárolási szolgáltatással]
  * [VHD-k hibaelhárítása Windows rendszerű virtuális gépeken](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)   
  * [VHD-k hibaelhárítása Linux rendszerű virtuális gépeken](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
  * [A Windows problémáinak elhárítása Azure Files](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [A Linux Azure Files kapcsolatos problémák elhárítása](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Függelékek]
  * [1. függelék: a Hegedűs használata a HTTP-és HTTPS-forgalom rögzítéséhez]
  * [2. függelék: hálózati forgalom rögzítése a Wireshark használatával]
  * [4. függelék: az Excel használata a metrikák és a naplózási adatok megtekintéséhez]
  * [5. függelék: az Azure DevOps Application Insights figyelése]

## <a name="introduction"></a><a name="introduction"></a>Bevezetés
Ez az útmutató bemutatja, hogyan használhatók olyan szolgáltatások, mint például az Azure Storage Analytics, az ügyféloldali naplózás az Azure Storage ügyféloldali Kódtáraban, valamint más, harmadik féltől származó eszközök az Azure Storage-hoz kapcsolódó problémák azonosításához, diagnosztizálásához és hibaelhárításához.

![Az ügyfélalkalmazások és az Azure Storage-szolgáltatások közötti információáramlást ábrázoló diagram.][1]

Ez az útmutató elsősorban olyan online szolgáltatások fejlesztői számára készült, akik az Azure Storage-szolgáltatásokat és az ilyen online szolgáltatások kezeléséért felelős informatikai szakembereket használják. Az útmutató célja:

* Az Azure Storage-fiókok állapotának és teljesítményének fenntartása érdekében.
* A szükséges folyamatok és eszközök megadásával eldöntheti, hogy egy alkalmazás hibája vagy problémája kapcsolódik-e az Azure Storage-hoz.
* Az Azure Storage szolgáltatással kapcsolatos problémák megoldásához használható útmutatást nyújt.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Az útmutató rendszerezése
A "[tárolási szolgáltatás figyelése]" szakasz azt ismerteti, hogyan figyelhetők meg az Azure Storage-szolgáltatások állapota és teljesítménye Azure Storage Analytics metrikák (tárolási metrikák) használatával.

A "[tárolási problémák diagnosztizálása]" szakasz azt ismerteti, hogyan diagnosztizálhatja a problémákat Azure Storage Analytics naplózás (Storage-naplózás) használatával. Azt is leírja, hogyan engedélyezhető az ügyféloldali naplózás az egyik ügyfél-függvénytárban, például a .NET-hez készült Storage ügyféloldali kódtára vagy a Javához készült Azure SDK használatával.

A "[végpontok közötti nyomkövetés]" szakasz azt ismerteti, hogyan lehet összekapcsolni a különböző naplófájlokban és mérőszámokban tárolt adatokat.

A "[hibaelhárítási útmutató]" című szakasz hibaelhárítási útmutatást nyújt az esetlegesen felmerülő gyakori, a tárterülettel kapcsolatos problémákhoz.

A "[függelékek]" információkkal szolgálnak más eszközök, például a Wireshark és a netmon használatával a hálózati csomagok adatainak elemzéséhez, és a Hegedűs a HTTP/HTTPS-üzenetek elemzéséhez.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>A tárolási szolgáltatás figyelése
Ha ismeri a Windows teljesítményfigyelőjét, a tárolási metrikákat úgy tekintheti meg, hogy a Windows teljesítményfigyelő számlálóinak megfelelő Azure Storage-kompatibilis. A tárolási Mérőszámokban átfogó mérőszámokat (számlálókat a Windows Teljesítményfigyelő terminológiájában) talál, például a szolgáltatás elérhetőségét, a szolgáltatásra irányuló kérelmek teljes számát vagy a sikeres kérelmek százalékos arányát. Az elérhető metrikák teljes listáját itt tekintheti meg: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema). Megadhatja, hogy a tárolási szolgáltatás óránként, vagy percenként összesítse-e a metrikákat. A metrikák engedélyezésével és a tárolási fiókok figyelésével kapcsolatos további információkért lásd: [a tárolási mérőszámok engedélyezése és a metrikák adatainak megtekintése](../blobs/monitor-blob-storage.md).

Kiválaszthatja, hogy mely óradíjak jelenjenek meg a [Azure Portalban](https://portal.azure.com) , és hogyan konfigurálhat olyan szabályokat, amelyek e-mailben értesítik a rendszergazdát, amikor egy óradíj meghaladja az adott küszöbértéket. További információt a [Riasztási értesítések fogadása](../../azure-monitor/alerts/alerts-overview.md)című témakörben talál.

Javasoljuk, hogy tekintse át [Azure monitor a Storage szolgáltatáshoz](../../azure-monitor/insights/storage-insights-overview.md) (előzetes verzió). Ez az Azure Monitor szolgáltatása, amely átfogóan figyeli az Azure Storage-fiókokat az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes áttekintésével. Nem igényli, hogy bármit engedélyezzen vagy konfiguráljan, és azonnal megtekintheti ezeket a metrikákat az előre definiált interaktív diagramokon és egyéb vizualizációkban is.

A tárolási szolgáltatás a legjobb erőfeszítéssel gyűjt metrikákat, de nem rögzíti az összes tárolási műveletet.

A Azure Portalban megtekintheti a tárolási fiókok metrikáit, például a rendelkezésre állást, a kérelmek teljes számát és az átlagos késési számokat. Egy értesítési szabály is be lett állítva, hogy figyelmeztessen egy rendszergazdát, ha a rendelkezésre állás egy bizonyos szint alá csökken. Az adatok megtekintésekor az egyik lehetséges terület a Table Service sikerességi aránya a 100% alatt (További információ: "a[mérőszámok alacsony PercentSuccess-vagy elemzési naplóbejegyzések használata a ClientOtherErrors tranzakciós állapotával]").

Folyamatosan figyelje az Azure-alkalmazásokat, hogy azok kifogástalan állapotú legyenek, és a következő módon végezzék el a feladatokat:

* Hozzon létre néhány alapvető mérőszámot az alkalmazáshoz, amely lehetővé teszi a jelenlegi adatok összehasonlítását, valamint az Azure Storage és az alkalmazás működésében bekövetkezett jelentős változások azonosítását. Az alapvető mérőszámok értékei sok esetben alkalmazásspecifikusek lesznek, és az alkalmazás teljesítményének tesztelésekor kell őket létrehoznia.
* Percenkénti mérőszámok rögzítése és azok használata a váratlan hibák és rendellenességek, például a hibák száma vagy a kérelmek díjszabása alapján történő aktív figyeléshez.
* Óránkénti mérőszámok rögzítése és használata az átlagos értékek, például az átlagos hibák száma és a kérelmek díjszabásának figyeléséhez.
* A diagnosztikai eszközökkel kapcsolatos lehetséges problémák kivizsgálása a "[tárolási problémák diagnosztizálása]" szakasz későbbi részében leírtak szerint.

Az alábbi ábrán látható diagramok azt szemléltetik, hogy az óránkénti metrikák átlagosan előforduló mérőszámai elrejtik-e a tevékenységben lévő tüskéket. Az óránkénti mérőszámok úgy jelennek meg, hogy állandó kérelmeket jelenítenek meg, míg a percenkénti mérőszámok felfedik a ténylegesen zajló ingadozásokat.

![Azok a diagramok, amelyek bemutatják, hogy az óránkénti mérőszámok átlagosan előforduló átlaga Hogyan rejthető el a tevékenységekben.][3]

A szakasz további része a figyelni kívánt metrikákat és azok okát ismerteti.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Figyelési szolgáltatás állapota
A [Azure Portal](https://portal.azure.com) használatával megtekintheti a Storage szolgáltatás (és más Azure-szolgáltatások) állapotát a világ összes Azure-régiójában. A figyelés lehetővé teszi, hogy azonnal megjelenjen, ha a vezérlőn kívüli probléma hatással van az alkalmazáshoz használt régió tárolási szolgáltatására.

A [Azure Portal](https://portal.azure.com) a különböző Azure-szolgáltatásokra vonatkozó incidensekről szóló értesítéseket is megadhat.
Megjegyzés: ez az információ korábban elérhető volt az [Azure-szolgáltatás irányítópultján](https://status.azure.com), a korábbi adatokkal együtt.
Az Azure-DevOps Application Insightsáról további információt az "[5. függelék: az Azure DevOps Application Insights figyelése](#appendix-5)" című függelékben talál.

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Kapacitás figyelése
A tárolási metrikák csak a blob szolgáltatás kapacitás-metrikáit tárolják, mivel a Blobok jellemzően a tárolt adatok legnagyobb hányadára vonatkoznak (az írás során nem lehetséges a tárolási mérőszámok használata a táblák és a várólisták kapacitásának figyelésére). Ha engedélyezte a Blob service figyelését, a **$MetricsCapacityBlob** táblában is megtalálhatja ezeket az adatfájlokat. A tárolási metrikák naponta egyszer rögzítik ezeket az adatokat, és a **RowKey** értékével meghatározhatja, hogy a sor tartalmaz-e olyan entitást, amely a felhasználói adatok (érték **adatai**) vagy az elemzési adatok (Value **Analytics**) esetében kapcsolódik. Minden tárolt entitás információt tartalmaz a felhasznált tárterület mennyiségéről (bájtban mért **kapacitás** ), valamint a Storage-fiókban használt tárolók (**ContainerCount**) és Blobok (**ObjectCount**-EK) aktuális számáról. A **$MetricsCapacityBlob** táblában tárolt kapacitási metrikákkal kapcsolatos további információkért lásd: [Storage Analytics mérőszámok táblázat sémája](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> Ezeket az értékeket egy korai figyelmeztetéssel kell figyelnie, amikor közeledik a Storage-fiók kapacitási korlátaihoz. A Azure Portal riasztási szabályokat adhat hozzá, amelyekkel értesítheti, ha az összesített tárterület-használat meghaladja a megadott küszöbértéket, vagy alacsonyabbra csökken.
>
>

A különböző tárolási objektumok, például a Blobok méretének becsléséhez tekintse meg az [Azure Storage számlázási szolgáltatás – sávszélesség, tranzakciók és kapacitás – ismertetését](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)ismertető blogbejegyzést.

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>A rendelkezésre állás monitorozása
Figyelje meg a Storage-fiók tárolási szolgáltatásainak rendelkezésre állását úgy, hogy a **rendelkezésre állási** oszlopban lévő értéket figyeli az óránkénti vagy perc mérőszámok táblázatában: **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. A **rendelkezésre állási** oszlop olyan százalékos értéket tartalmaz, amely jelzi a szolgáltatás rendelkezésre állását vagy a sor által jelzett API-műveletet (a **RowKey** azt jelzi, hogy a sor tartalmaz-e metrikákat a szolgáltatás egészére vagy egy adott API-műveletre vonatkozóan).

A 100%-nál kisebb érték azt jelzi, hogy egyes tárolási kérelmek meghiúsulnak. Azt is megtudhatja, hogy miért nem sikerül, ha megvizsgálja a metrikák más oszlopait, amelyek a különböző típusú (például **ServerTimeoutError**) kérelmek számát jelenítik meg. A **rendelkezésre állást** úgy kell megtekinteni, hogy az átmeneti kiszolgáló időtúllépése miatt átmenetileg a 100% alá esik, amíg a szolgáltatás a partíciókat áthelyezi a terheléselosztási kérelmek jobb terhelése érdekében. az ügyfélalkalmazás újrapróbálkozási logikájának képesnek kell lennie az ilyen időszakos feltételek kezelésére. A [naplózott műveletek és állapotüzenetek Storage Analytics](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages) cikk felsorolja a tárolási metrikák által a **rendelkezésre állási** számításban foglalt tranzakciótípusok listáját.

A [Azure Portal](https://portal.azure.com)riasztási szabályokat adhat hozzá, amelyek értesítik, ha a szolgáltatás **rendelkezésre állása** a megadott küszöbérték alá esik.

A jelen útmutató "[hibaelhárítási útmutató]" szakasza a rendelkezésre állással kapcsolatos gyakori tárolási szolgáltatásokkal kapcsolatos problémákat ismerteti.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Teljesítményfigyelés
A tárolási szolgáltatások teljesítményének figyeléséhez a következő metrikákat használhatja az óránkénti és a perc mérőszámok táblázatból.

* A **AverageE2ELatency** és a **averageserverlatency értéket mutatnak** oszlop értékei azt mutatják, hogy a tárolási szolgáltatás vagy az API-művelet típusa milyen átlagosan vesz igénybe kérelmek feldolgozásához. A **AverageE2ELatency** olyan végpontok közötti késés mértéke, amely magában foglalja a kérelem olvasásának idejét és a válasz elküldését a kérelem feldolgozásához szükséges idő mellett (ezért magában foglalja a hálózati késést, ha a kérelem eléri a tárolási szolgáltatást); A **averageserverlatency értéket mutatnak** csak a feldolgozási idő mértékét jelenti, ezért kizárja az ügyféllel folytatott kommunikációhoz kapcsolódó hálózati késéseket. Tekintse meg az útmutató későbbi, "[magas AverageE2ELatency és alacsony averageserverlatency értéket mutatnak]" című szakaszát, amelyből megtudhatja, miért lehet jelentős különbség a két érték között.
* A **TotalIngress** és a **TotalEgress** oszlopban lévő értékek az adatok teljes mennyiségét jelenítik meg (bájtban), amely a tárolási szolgáltatástól vagy egy adott API-műveleti típustól érkezik.
* A **TotalRequests** oszlopban szereplő értékek az API-művelet tárolási szolgáltatása által fogadott kérelmek teljes számát jelenítik meg. A **TotalRequests** a tárolási szolgáltatás által fogadott kérelmek teljes száma.

Az ilyen értékek bármelyikének váratlan változásait általában a vizsgálathoz szükséges problémákkal figyelheti.

A [Azure Portal](https://portal.azure.com)riasztási szabályokat adhat hozzá, amelyek értesítik, ha a szolgáltatás bármelyik teljesítmény-mérőszáma alá esik, vagy meghaladja a megadott küszöbértéket.

A jelen útmutató "[hibaelhárítási útmutatója]" című szakasza a teljesítménygel kapcsolatos gyakori tárolási szolgáltatásokkal kapcsolatos problémákat ismerteti.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Tárolási problémák diagnosztizálása
Több módon is megismerheti az alkalmazás hibáját vagy problémáját, többek között a következőket:

* Jelentős hiba történt, amely miatt az alkalmazás összeomlik, vagy leáll.
* Jelentős változások származnak az alapértékek közül a figyelt mérőszámokban az előző szakaszban "a[tárolási szolgáltatás figyelése]" részben leírtak szerint.
* Az alkalmazás felhasználóitól érkező jelentések arról, hogy egy adott művelet nem a várt módon fejeződött be, vagy hogy egyes szolgáltatások nem működnek.
* A naplófájlokban vagy más értesítési módszerekben megjelenő, az alkalmazáson belül létrehozott hibák.

Az Azure Storage-szolgáltatásokkal kapcsolatos problémák általában a következő négy kategóriába sorolhatók:

* Az alkalmazás teljesítménybeli hibával rendelkezik, vagy a felhasználók által jelentett, vagy a teljesítmény metrikáinak változásai alapján feltárt.
* Egy vagy több régióban probléma van az Azure Storage-infrastruktúrával.
* Az alkalmazás egy hibát észlel, amelyet a felhasználók jelentettek, vagy amelyek a figyelt hibák számának növekedésével derültek ki.
* A fejlesztés és a tesztelés során előfordulhat, hogy a helyi Storage emulatort használja; Előfordulhat, hogy olyan problémák merülnek fel, amelyek kifejezetten a Storage Emulator használatára vonatkoznak.

A következő szakaszokban ismertetjük azokat a lépéseket, amelyeket követnie kell az alábbi négy kategória hibáinak diagnosztizálásához és elhárításához. Az útmutató későbbi, "[hibaelhárítási útmutató]" szakasza részletesebben ismerteti az esetlegesen felmerülő gyakori problémákat.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>A szolgáltatás állapotával kapcsolatos problémák
A szolgáltatás állapotával kapcsolatos problémák általában a vezérlőn kívül esnek. A [Azure Portal](https://portal.azure.com) információt nyújt az Azure-szolgáltatásokkal kapcsolatos folyamatos problémákról, beleértve a tárolási szolgáltatásokat is. Ha úgy döntött, hogy Read-Access Geo-Redundant tárterületet a Storage-fiók létrehozásakor, akkor ha az adatai nem lesznek elérhetők az elsődleges helyen, az alkalmazás a másodlagos helyen lévő írásvédett másolatra átmenetileg átválthat. A másodlagosból való olvasáshoz az alkalmazásnak képesnek kell lennie az elsődleges és a másodlagos tárolóhelyek közötti váltásra, és képesnek kell lennie csökkentett funkcionalitású módban dolgozni a csak olvasható adatokkal. Az Azure Storage ügyféloldali kódtárai lehetővé teszik az újrapróbálkozási szabályzat megadását, amely képes a másodlagos tárolóból olvasni, ha az elsődleges tárolóból való olvasás meghiúsul. Emellett az alkalmazásnak is tisztában kell lennie azzal, hogy a másodlagos helyen lévő adatai végül konzisztensek. További információkért tekintse meg az [Azure Storage redundancia lehetőségeinek és az olvasási hozzáférés földrajzi redundáns tárolásának](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)feladatait ismertető blogot.

### <a name="performance-issues"></a><a name="performance-issues"></a>Teljesítményproblémák
Egy alkalmazás teljesítményének megítélése szubjektív lehet, főképp a felhasználó szemszögéből. Ezért fontos, hogy rendelkezzen olyan alapmértékekkel, amelyek segíthetnek a teljesítménnyel kapcsolatos problémák azonosításában. Számos tényező hatással lehet az Azure Storage szolgáltatás teljesítményére az ügyfélalkalmazás szemszögéből. Ezek a tényezők működhetnek a Storage szolgáltatásban, az ügyfélen vagy a hálózati infrastruktúrában; Ezért fontos, hogy a teljesítménnyel kapcsolatos probléma eredetének azonosítására szolgáló stratégia legyen.

Miután azonosította a teljesítménnyel kapcsolatos probléma valószínű helyét a mérőszámokból, a naplófájlok segítségével részletes információkat találhat a probléma diagnosztizálásához és elhárításához.

Az útmutató későbbi, "[hibaelhárítási útmutató]" szakasza további információkat tartalmaz az esetlegesen felmerülő gyakori teljesítménnyel kapcsolatos problémákról.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Hibák diagnosztizálása
Az alkalmazás felhasználói értesítést kaphatnak az ügyfélalkalmazás által jelentett hibákról. A tárolási metrikák a tárolási szolgáltatásokban (például **NetworkError**, **ClientTimeoutError** vagy **AuthorizationError**) lévő különböző típusú hibák számát is rögzítik. Míg a tárolási metrikák csak a különböző típusú hibák számát rögzítik, a kiszolgálóoldali, az ügyféloldali és a hálózati naplók vizsgálatával további részleteket tudhat meg az egyes kérelmekről. A tárolási szolgáltatás által visszaadott HTTP-állapotkód általában azt jelzi, hogy miért nem sikerült a kérelem végrehajtása.

> [!NOTE]
> Ne feledje, hogy néhány időnként előforduló hibát kell látnia: például átmeneti hálózati feltételek vagy alkalmazáshiba miatti hibák.
>
>

Az alábbi erőforrások hasznosak a tárolási szolgáltatásokkal kapcsolatos állapot- és hibakódok megértéséhez:

* [Gyakori REST API hibakódok](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [BLOB Service-hibakódok](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Üzenetsor-szolgáltatási hibakódok](/rest/api/storageservices/Queue-Service-Error-Codes)
* [Table Service-hibakódok](/rest/api/storageservices/Table-Service-Error-Codes)
* [File Service-hibakódok](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>A Storage Emulator problémái
Az Azure SDK tartalmaz egy fejlesztői munkaállomáson futtatható tároló-emulátort. Ez az emulátor szimulálja az Azure Storage-szolgáltatások legtöbb viselkedését, és a fejlesztés és a tesztelés során hasznos, lehetővé téve az Azure Storage-szolgáltatásokat használó alkalmazások futtatását anélkül, hogy Azure-előfizetésre és Azure Storage-fiókra lenne szükség.

Az útmutató "[hibaelhárítási útmutató]" szakasza a Storage Emulator használatával kapcsolatos gyakori problémákat ismerteti.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Storage-naplózási eszközök
A tárolási naplózás a Storage-kérelmek kiszolgálóoldali naplózását biztosítja az Azure Storage-fiókban. A kiszolgálóoldali naplózás engedélyezésével és a naplózási adatok elérésével kapcsolatos további információkért lásd: a [tárolási naplózás engedélyezése és a naplózási adatok elérése](./storage-analytics-logging.md).

A .NET-hez készült Storage ügyféloldali kódtára lehetővé teszi az alkalmazás által végrehajtott tárolási műveletekhez kapcsolódó ügyféloldali naplózási adatok gyűjtését. További információt a [.NET-es Storage-ügyfélkódtárral történő ügyféloldali naplózást](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library) ismertető szakaszban találhat.

> [!NOTE]
> Bizonyos esetekben (például SAS-engedélyezési hibák esetén) a felhasználó hibát jelenthet, ha a kiszolgálóoldali tárolóhelyek naplóiban nem találhatók kérelem-adat. A Storage ügyféloldali kódtár naplózási funkciói segítségével megvizsgálhatja, hogy a probléma oka az ügyfélen van-e, vagy hálózati figyelési eszközöket használ a hálózat vizsgálatához.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Hálózati naplózási eszközök használata
Az ügyfél és a kiszolgáló közötti forgalmat rögzítheti az ügyfél és a kiszolgáló közötti adatcsere és a mögöttes hálózati feltételek részletes adatainak biztosításához. Hasznos hálózati naplózási eszközök a következők:

* A [Hegedűs](https://www.telerik.com/fiddler) egy ingyenes webes hibakeresési proxy, amely lehetővé teszi a http-és HTTPS-kérések és-válaszüzenetek fejlécének és hasznos adatainak vizsgálatát. További információ: [1. függelék: a Hegedűs használata a http-és HTTPS-forgalom rögzítéséhez](#appendix-1).
* A [Microsoft Hálózatfigyelő (netmon)](https://download.cnet.com/s/network-monitor/) és a [Wireshark](https://www.wireshark.org/) olyan ingyenes hálózati protokoll-elemzők, amelyek lehetővé teszik a hálózati protokollok széles körének részletes csomagjainak megtekintését. A Wireshark kapcsolatos további információkért lásd a "[2. függelék: a hálózati forgalom rögzítése a Wireshark használatával](#appendix-2)" című szakaszt.
* Ha alapszintű kapcsolati tesztet szeretne végezni annak ellenőrzéséhez, hogy az ügyfélszámítógép képes-e csatlakozni az Azure Storage szolgáltatáshoz a hálózaton keresztül, ezt a standard **ping** eszközzel nem végezheti el az ügyfélen. A [ **tcping** eszközzel](https://www.elifulkerson.com/projects/tcping.php) azonban ellenőrizheti a kapcsolatot.

Sok esetben a tárolási adatok naplózása és a Storage ügyféloldali kódtára elegendő lesz a probléma diagnosztizálásához, de bizonyos helyzetekben szükség lehet a hálózati naplózási eszközök által biztosított részletesebb információkra. A Hegedűs a HTTP-és HTTPS-üzenetek megtekintéséhez például lehetővé teszi, hogy megtekintse a tárolási szolgáltatásokban küldött fejléc-és hasznos adatokat, ami lehetővé teszi annak vizsgálatát, hogy az ügyfélalkalmazás hogyan próbálkozzon újra a tárolási műveletekkel. A protokollok elemzői, például a Wireshark a csomagok szintjén működnek, lehetővé téve a TCP-adatok megtekintését, ami lehetővé teszi az elveszett csomagok és csatlakozási problémák elhárítását. 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Végpontok közötti nyomkövetés
A különböző naplófájlokat használó teljes körű nyomkövetés hasznos módszer a lehetséges problémák kivizsgálásához. A metrikák adatai alapján a dátum-és időinformációk segítségével megtekintheti, hogy hol érdemes megkeresni a naplófájlokban a probléma megoldásához segítséget nyújtó részletes információkat.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>A naplófájlok korrelációjának összekötése
Amikor az ügyfélalkalmazások, a hálózati Nyomkövetések és a kiszolgálóoldali tárolás naplóit tekinti meg, kritikus fontosságú, hogy összekapcsolja a kérelmeket a különböző naplófájlok között. A naplófájlok több különböző mezőt tartalmaznak, amelyek korrelációs azonosítóként használhatók. Az ügyfél-kérelem azonosítója a leghasznosabb mező, amely a különböző naplók bejegyzéseinek összekapcsolására használható. Esetenként azonban hasznos lehet a kiszolgálói kérelmek AZONOSÍTÓjának vagy időbélyegének használata. A következő szakaszokban további információkat talál ezekről a lehetőségekről.

### <a name="client-request-id"></a><a name="client-request-id"></a>Ügyfélkérelem azonosítója
A Storage ügyféloldali kódtára automatikusan létrehoz egy egyedi ügyfélalkalmazás-azonosítót minden kérelemhez.

* Az ügyféloldali naplófájlban, amelyet a Storage ügyféloldali kódtára hoz létre, az ügyfél-kérelem azonosítója megjelenik a kérelemhez kapcsolódó összes naplóbejegyzés **ügyfél-kérés azonosítója** mezőjében.
* Egy olyan hálózati nyomkövetésben, mint például a Hegedűs által rögzített, az ügyfél-kérelem azonosítója a kérelmek üzeneteiben az **x-MS-Client-Request-ID** HTTP-fejléc értéke jelenik meg.
* A kiszolgálóoldali tároló naplózási naplójában az ügyfélalkalmazás azonosítója megjelenik az ügyfél-kérelem azonosítója oszlopban.

> [!NOTE]
> Több kérelem is megosztható ugyanazzal az ügyfél-kérelem-AZONOSÍTÓval, mert az ügyfél hozzárendelheti ezt az értéket (bár a Storage ügyféloldali kódtár automatikusan új értéket rendel hozzá). Az ügyfél újrapróbálkozásakor az összes kísérlet ugyanazzal az ügyfél-kérelem-AZONOSÍTÓval rendelkezik. Az ügyféltől érkező kötegek esetében a köteg egyetlen ügyfél-kérelem-AZONOSÍTÓval rendelkezik.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>Kiszolgálói kérelem azonosítója
A Storage szolgáltatás automatikusan létrehozza a kiszolgálói kérelmek azonosítóit.

* A kiszolgálóoldali tároló naplózási naplójában a kiszolgálói kérelem azonosítója megjelenik a kérelem- **azonosító fejléce** oszlopban.
* Egy olyan hálózati nyomkövetésben, mint például a Hegedűs által rögzített, a kiszolgálói kérelem azonosítója a válaszüzenetekben az **x-MS-Request-ID** HTTP-fejléc értéke jelenik meg.
* Az ügyféloldali naplófájlban, amely a Storage ügyféloldali kódtárat hozza létre, a kiszolgálói kérelem azonosítója jelenik meg a **műveleti szöveg** oszlopban, amely a kiszolgáló válaszának részleteit jeleníti meg.

> [!NOTE]
> A tárolási szolgáltatás mindig egyedi kiszolgálói kérés-azonosítót rendel minden fogadott kérelemhez, így az ügyfél minden újrapróbálkozási kísérlete és a kötegben szereplő összes művelet egyedi kiszolgálói kérelem-AZONOSÍTÓval rendelkezik.
>
>

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Az alábbi mintakód azt mutatja be, hogyan használható egyéni ügyfél-kérelem-azonosító. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Ha a Storage ügyféloldali kódtár egy **StorageException** dob az ügyfélen, a **RequestInformation** tulajdonság olyan **RequestResult** objektumot tartalmaz, amely tartalmaz egy **ServiceRequestID** tulajdonságot. Egy **RequestResult** objektum egy **OperationContext** -példányból is elérhető.

Az alábbi mintakód azt mutatja be, hogyan lehet egyéni **ügyfélkérelem** értéket beállítani egy **OperationContext** objektumnak a Storage szolgáltatáshoz való csatolásával. Azt is bemutatja, hogyan kérhető le a **ServerRequestId** értéke a válaszüzenetből.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

---

### <a name="timestamps"></a><a name="timestamps"></a>Időbélyegek
Időbélyegeket is használhat a kapcsolódó naplóbejegyzések megkereséséhez, de ügyeljen arra, hogy az ügyfél és a kiszolgáló közötti összes óra döntse. Keressen plusz vagy mínusz 15 percet a kiszolgálóoldali bejegyzések egyeztetéséhez az ügyfél időbélyege alapján. Ne feledje, hogy a metrikákat tartalmazó Blobok metaadatai a blobban tárolt mérőszámok időtartományát jelzik. Ez az időtartomány akkor hasznos, ha sok metrikai blobtal rendelkezik ugyanahhoz a perchez vagy órára.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató
Ez a szakasz az Azure Storage-szolgáltatások használata során felmerülő gyakori problémák diagnosztizálását és hibaelhárítását ismerteti. Az alábbi lista segítségével megkeresheti az adott hibához kapcsolódó információkat.

**A döntési fa hibaelhárítása**

---
Kapcsolódik a probléma az egyik tárolási szolgáltatás teljesítményéhez?

* [A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]
* [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
* [A mérőszámok magas AverageServerLatency értéket mutatnak]
* [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]

---
A probléma az egyik tárolási szolgáltatás elérhetőségével kapcsolatos?

* [A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]
* [A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]
* [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]

---
 Az ügyfélalkalmazás HTTP-4XX (például 404) kap a tárolási szolgáltatástól?

* [Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap]
* [Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap]
* [Az ügyfél HTTP 409 (Ütközés) hibaüzeneteket kap]

---
[A metrikák az alacsony PercentSuccess vagy az elemzési naplóbejegyzések esetében a ClientOtherErrors tranzakciós állapotú műveleteit mutatják]

---
[A kapacitási mérőszámok váratlan növekedést mutatnak a tárolási kapacitás kihasználtsága terén]

---
[A nagy számú csatlakoztatott VHD-vel rendelkező Virtual Machines váratlan újraindítását tapasztalja.

---
[A probléma a Storage Emulator fejlesztési vagy tesztelési célú használatból ered]

---
[Problémákba ütközik a .NET-hez készült Azure SDK telepítésekor]

---
[Más probléma van a tárolási szolgáltatással]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak
Az [Azure Portal](https://portal.azure.com) monitorozási eszköz alább látható ábrája egy példát mutat be, ahol a **AverageE2ELatency** jelentősen nagyobb, mint a **averageserverlatency értéket mutatnak**.

![A Azure Portal illusztrációja, amely egy példát mutat be, ahol a AverageE2ELatency jelentősen nagyobb, mint a Averageserverlatency értéket mutatnak.][4]

A Storage szolgáltatás csak a sikeres kérések metrikai **AverageE2ELatency** számítja ki, és a **averageserverlatency értéket mutatnak**-től eltérően azt az időpontot is tartalmazza, amikor az ügyfél az adatok elküldését és a tárolási szolgáltatástól való nyugtát fogadja. Ezért a **AverageE2ELatency** és a **averageserverlatency értéket mutatnak** közötti különbség oka az lehet, hogy az ügyfélalkalmazás nem válaszol, vagy a hálózat feltételei miatt.

> [!NOTE]
> A tárolási naplózási napló adataiban a **E2ELatency** és a **ServerLatency** is megtekintheti az egyes tárolási műveletekhez.
>
>

#### <a name="investigating-client-performance-issues"></a>Az ügyfél teljesítményével kapcsolatos problémák kivizsgálása
Az ügyfélnek a lassú válaszadás lehetséges okai a következők lehetnek: korlátozott számú elérhető kapcsolat vagy szál, illetve kevés erőforrás, például CPU-, memória-vagy hálózati sávszélesség. Előfordulhat, hogy a probléma megoldásához módosítania kell az ügyfél kódját, hogy hatékonyabb legyen (például aszinkron hívásokat használ a tárolási szolgáltatáshoz), vagy egy nagyobb virtuális gép (több maggal és több memóriával) használatával.

A tábla-és üzenetsor-szolgáltatások esetében a Nyéki algoritmus magas **AverageE2ELatency** eredményezhet a **averageserverlatency értéket mutatnak** képest: további információért lásd a [nyár utáni algoritmust, amely nem csupán a kis kérések elérésére szolgál](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests). A **System.net** -névtér **ServicePointManager** osztályának használatával letilthatja a Nyéki algoritmust a kódban. Ezt csak akkor hajtsa végre, ha az alkalmazásban meghívja a Table vagy a üzenetsor-szolgáltatást, mivel ez nem befolyásolja a már megnyitott kapcsolatokat. A következő példa egy feldolgozói szerepkör **Application_Start** metódusára mutat.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

Tekintse át az ügyféloldali naplókat, és ellenőrizze, hogy az ügyfélalkalmazás hány kérelmét küldi el, és keresse meg a .NET-sel kapcsolatos általános teljesítménybeli szűk keresztmetszeteket az ügyfélben, például a PROCESSZORt, a .NET-alapú adatgyűjtést, a hálózati kihasználtságot vagy a memóriát. A .NET-ügyfélalkalmazások hibaelhárításának kiindulási pontként lásd: [hibakeresés, nyomkövetés és profilkészítés](/dotnet/framework/debug-trace-profile/).

#### <a name="investigating-network-latency-issues"></a>Hálózati késéssel kapcsolatos problémák kivizsgálása
Általában a hálózat által okozott magas végpontok közötti késés átmeneti körülmények miatt. Az átmeneti és az állandó hálózati problémákat, például az eldobott csomagokat olyan eszközökkel is vizsgálhatja, mint például a Wireshark.

A hálózati problémák Wireshark használatával kapcsolatos további információkért lásd a "[2. függelék: a Wireshark használata a hálózati forgalom rögzítéséhez]" című szakaszt.

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal
Ebben a forgatókönyvben a legvalószínűbb ok az a tárolási szolgáltatás elérésére irányuló kérelmek késése. Meg kell vizsgálnia, hogy az ügyféltől érkező kérések miért nem teszik át a blob szolgáltatásba.

Az ügyfél a küldési kérelmek késleltetésének egyik lehetséges oka, hogy korlátozott számú elérhető kapcsolat vagy szál van.

Ellenőrizze azt is, hogy az ügyfél több újrapróbálkozást is végrehajt-e, és ha igen, vizsgálja meg az okát. Annak megállapításához, hogy az ügyfél több újrapróbálkozást hajt végre, a következőket teheti:

* Vizsgálja meg a Storage Analytics naplókat. Ha több újrapróbálkozás történik, több műveletet is látni fog ugyanazzal az ügyfél-kérelem-AZONOSÍTÓval, de eltérő kiszolgálói kérelmek azonosítói alapján.
* Ellenőrizze az ügyfél naplóit. A részletes naplózás azt jelzi, hogy az Újrapróbálkozás megtörtént.
* Végezzen hibakeresést a kódban, és keresse meg a kérelemhez társított **OperationContext** objektum tulajdonságait. Ha a művelet újrapróbálkozik, a **RequestResults** tulajdonság több egyedi kiszolgálói kérelem azonosítóját fogja tartalmazni. Az egyes kérések kezdési és befejezési időpontját is megtekintheti. További információ: a [kiszolgálói kérelem azonosítója]című szakasz kód mintája.

Ha nincsenek problémák az ügyfélben, vizsgálja meg a lehetséges hálózati problémákat, például a csomagok elvesztését. A hálózati problémák kivizsgálásához olyan eszközöket használhat, mint például a Wireshark.

A hálózati problémák Wireshark használatával kapcsolatos további információkért lásd a "[2. függelék: a Wireshark használata a hálózati forgalom rögzítéséhez]" című szakaszt.

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>A mérőszámok magas AverageServerLatency értéket mutatnak
A blob letöltési kéréseinek magas **averageserverlatency értéket mutatnak** esetén a tárolási naplózási naplók segítségével ellenőrizze, hogy vannak-e ismétlődő kérelmek ugyanahhoz a blobhoz (vagy Blobok készletéhez). A Blobok feltöltésére vonatkozó kérelmek esetében meg kell vizsgálni, hogy az ügyfél által használt blokkolási méret (például az 64 K-nál kevesebb blokk) csak akkor vezethet, ha az olvasások kisebbek, mint 64 K-es adattömböknél, és ha több ügyfél is feltölti a blokkokat ugyanarra a blobra párhuzamosan. Azt is ellenőriznie kell, hogy a másodpercenkénti kérések száma a másodpercenkénti méretezhetőségi célok meghaladása esetén hány percenkénti várakozási[arányt eredményez.]

Ha a Blobok letöltési kéréseinek nagy **averageserverlatency értéket mutatnak** jelenik meg, ha ismétlődő kérelmek ugyanazt a blobot vagy blobot használják, érdemes megfontolnia a Blobok gyorsítótárazását az Azure cache vagy az Azure Content DELIVERY Network (CDN) használatával. A feltöltési kérelmek esetében nagyobb méretű blokk használatával javítható az átviteli sebesség. A táblákra irányuló lekérdezések esetében az ügyféloldali gyorsítótárazás is megvalósítható az ugyanazon lekérdezési műveleteket végző ügyfeleken, és az adatok nem változnak gyakran.

A magas **averageserverlatency értéket mutatnak** értékek olyan, rosszul tervezett táblák vagy lekérdezések tünetét is okozhatják, amelyek vizsgálati műveleteket eredményeznek, vagy amelyek a Hozzáfűzés/előtag ellenes mintát követik. További információ: "a[mérőszámok a percentthrottlingerror értéket mutatnak növekedését mutatják]".

> [!NOTE]
> Az átfogó ellenőrzőlista teljesítményére vonatkozó ellenőrzőlista itt található: [Microsoft Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlista](../blobs/storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor
Ha késlelteti az alkalmazás egy várólistához való hozzáadását, valamint a várólistából való olvasáshoz szükséges időt, akkor az alábbi lépéseket kell elvégeznie a probléma diagnosztizálásához:

* Győződjön meg arról, hogy az alkalmazás sikeresen hozzáadja az üzeneteket a várólistához. Győződjön meg arról, hogy az alkalmazás nem próbálkozik újra többször a **AddMessage** metódussal a sikeres művelet előtt. A Storage ügyféloldali kódtár naplóiban megjelennek a tárolási műveletek ismételt újrapróbálkozásai.
* Ellenőrizze, hogy a feldolgozói szerepkör nem rendelkezik-e az üzenetet a várólistához, valamint a várólistából beolvasott üzenetet olvasó feldolgozói szerepkörhöz, amely úgy tűnik, mintha a feldolgozás késése van.
* Ellenőrizze, hogy a várólista üzeneteit olvasó feldolgozói szerepkör meghibásodik-e. Ha egy üzenetsor-ügyfél meghívja a **GetMessage** metódust, de nem válaszol a visszaigazolásra, az üzenet mindaddig láthatatlan marad a várólistán, amíg lejár a **invisibilityTimeout** időszak. Ezen a ponton az üzenet újbóli feldolgozásra válik elérhetővé.
* Ellenőrizze, hogy a várólista hossza növekszik-e az idő múlásával. Ez akkor fordulhat elő, ha nem áll rendelkezésre elegendő feldolgozója ahhoz, hogy feldolgozzák az összes olyan üzenetet, amelyet a többi alkalmazott a várólistára helyez. Tekintse meg a metrikákat is, és ellenőrizze, hogy a törlési kérések sikertelenek-e, és hogy a várólistán lévő üzenetek száma megtörténjen-e az üzenetekben, ami azt jelzi, hogy ismételt sikertelen
* Vizsgálja meg a tárolási naplózási naplókat minden olyan üzenetsor-művelet esetében, amely a vártnál nagyobb **E2ELatency** és **ServerLatency** értékkel rendelkezik a szokásosnál hosszabb ideig.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>A mérőszámok emelkedő PercentThrottlingError értéket mutatnak
Sávszélesség-szabályozási hibák léphetnek fel, ha túllépi a tárolási szolgáltatás skálázhatósági céljait. A tárolási szolgáltatás szabályozása biztosítja, hogy egyetlen ügyfél vagy bérlő sem használhatja a szolgáltatást mások rovására. További információ: a [méretezhetőségi és teljesítményi célok a standard szintű Storage-fiókok](scalability-targets-standard-account.md) esetében a Storage-fiókok méretezhetőségi céljainak és a Storage-fiókokban lévő partíciók teljesítményi céljainak részleteiről.

Ha a **percentthrottlingerror értéket mutatnak** -metrika a kérelmek százalékos arányának növekedését mutatja a sávszélesség-szabályozási hiba miatt, meg kell vizsgálnia két forgatókönyv egyikét:

* [Átmeneti növekedés a Percentthrottlingerror értéket mutatnak]
* [Percentthrottlingerror értéket mutatnak-hiba állandó növekedése]

A **percentthrottlingerror értéket mutatnak** növekedése gyakran fordul elő a tárolási kérelmek számának növekedésekor, vagy amikor először tölti be az alkalmazás tesztelését. Ez az ügyfél "503 Server foglalt" vagy "500 művelet időtúllépése" HTTP-állapotüzenetek tárolására is felhasználható a tárolási műveletekben.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Átmeneti növekedés a Percentthrottlingerror értéket mutatnak
Ha a **percentthrottlingerror értéket mutatnak** értékében olyan csúcsokat lát, amelyek egybeesnek az alkalmazás magas aktivitási idejével, egy exponenciális (nem lineáris) visszalépési stratégiát kell megvalósítani az ügyfélen végzett újrapróbálkozásokhoz. A back-off újrapróbálkozások csökkentik a partíciók azonnali terhelését, és segítenek az alkalmazásnak a forgalomban lévő tüskék kisimításában. További információ az újrapróbálkozási házirendek a Storage ügyféloldali kódtár használatával történő megvalósításáról: [Microsoft. Azure. Storage. RetryPolicies névtér](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> A **percentthrottlingerror értéket mutatnak** olyan értékeit is láthatja, amelyek nem egyeznek az alkalmazás magas aktivitási idejével: Ez a legvalószínűbb ok, ha a Storage szolgáltatás partíciókat helyez át a terheléselosztás javítására.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Percentthrottlingerror értéket mutatnak-hiba állandó növekedése
Ha következetesen magas értéket lát a **percentthrottlingerror értéket mutatnak** a tranzakciós kötetek állandó növekedése után, vagy ha a kezdeti terhelési teszteket az alkalmazáson hajtja végre, ki kell értékelnie, hogy az alkalmazás hogyan használja a tárolási partíciókat, és hogy közeledik-e a tárolási fiók skálázhatósági céljaihoz. Ha például egy várólistán (amely egyetlen partícióként számít) a hibák szabályozása, akkor érdemes további várólistákat használni a tranzakciók több partíción keresztüli elterjesztéséhez. Ha a hibák szabályozásával kapcsolatos hibákat lát egy táblán, érdemes egy másik particionálási sémát használnia, hogy a tranzakciókat több partíción keresztül terjessze, a partíciós kulcsok szélesebb körének használatával. A probléma egyik gyakori oka az a művelet, amely a (z) előtag/Hozzáfűzés Anti-pattern, ahol kiválaszthatja a dátumot a partíciós kulcsként, majd egy adott napon lévő összes adatot egy partícióba írja: a terhelés alatt ez írási szűk keresztmetszetet eredményezhet. Fontolja meg egy másik particionálási terv használatát, vagy annak kiértékelését, hogy a blob Storage használata jobb megoldás-e. Azt is vizsgálja meg, hogy a szabályozás a forgalomban előforduló tüskék miatt következik-e be, és vizsgálja meg a kérelmek kisimításának módszereit.

Ha több partícióra terjeszti a tranzakciókat, akkor továbbra is tisztában kell lennie a Storage-fiókhoz beállított skálázhatósági korlátokkal. Ha például tíz várólistát használt a másodpercenként legfeljebb 2 000 1KB-üzenet feldolgozásához, a Storage-fiókhoz másodpercenként legfeljebb 20 000 üzenet jelenik meg. Ha másodpercenként több mint 20 000 entitást kell feldolgoznia, érdemes több Storage-fiókot használni. Azt is figyelembe kell vennie, hogy a kérések és az entitások mérete hatással van arra, hogy a tárolási szolgáltatás hogyan szabályozza az ügyfeleket: Ha nagyobb kérelmek és entitások vannak, akkor lehet, hogy hamarabb szabályozni kell.

A nem hatékony lekérdezési kialakítás azt is lehetővé teszi, hogy elérje a tábla partícióinak skálázhatósági korlátait. Egy olyan lekérdezéssel például, amely csak az entitások egy százalékát választja ki egy partícióban, de egy partíció összes entitását megvizsgálja, minden entitáshoz hozzá kell férnie. Az összes beolvasott entitás az adott partíción belüli tranzakciók teljes száma felé fog számítani. Ezért könnyedén elérheti a méretezhetőségi célokat.

> [!NOTE]
> A teljesítménytesztnek fel kell fednie a nem hatékony lekérdezési terveket az alkalmazásban.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>A mérőszámok emelkedő PercentTimeoutError értéket mutatnak
A metrikák az egyik tárolási szolgáltatás **percenttimeouterror értéket mutatnak** növekedését mutatják be. Ugyanakkor az ügyfél nagy mennyiségű "500 művelet időtúllépése" HTTP-állapotüzenetek fogadását kapja a tárolási műveletekben.

> [!NOTE]
> Előfordulhat, hogy az időtúllépési hibák átmenetileg megjelennek a Storage szolgáltatás terheléselosztási kérelmei között, ha áthelyez egy partíciót egy új kiszolgálóra.
>
>

A **percenttimeouterror értéket mutatnak** metrika a következő metrikák összesítése: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** és **SASServerTimeoutError**.

A kiszolgáló időtúllépéseit a kiszolgáló hibája okozza. Az ügyfél időtúllépései történnek, mert a kiszolgálón végrehajtott művelet túllépte az ügyfél által megadott időkorlátot. a Storage ügyféloldali kódtárat használó ügyfelek például a **QueueRequestOptions** osztály **ServerTimeout** tulajdonságának használatával állíthatnak be időtúllépést egy művelethez.

A kiszolgáló időtúllépései a további vizsgálatot igénylő tárolási szolgáltatással kapcsolatos problémát jeleznek. A metrikák használatával megtekintheti, hogy a szolgáltatás skálázhatósági korlátait és a hibát okozó forgalomban észlelt összes adatugrást azonosítja. Ha a probléma időszakos, előfordulhat, hogy a szolgáltatás terheléselosztási tevékenysége miatt. Ha a probléma tartósan fennáll, és nem az alkalmazása okozza a szolgáltatás méretezhetőségi korlátait, akkor támogatási problémát kell megadnia. Az ügyfél-időtúllépések esetében el kell döntenie, hogy az időtúllépés megfelelő értékre van-e állítva az ügyfélen, illetve hogy módosítja-e az ügyfélen beállított időtúllépési értéket, vagy megvizsgálhatja, hogy miként javítható a tárolási szolgáltatás műveleteinek teljesítménye, például a tábla lekérdezésének optimalizálásával vagy az üzenetek méretének csökkentésével.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>A mérőszámok emelkedő PercentNetworkError értéket mutatnak
A metrikák az egyik tárolási szolgáltatás **percentnetworkerror értéket mutatnak** növekedését mutatják be. A **percentnetworkerror értéket mutatnak** metrika a következő metrikák összesítése: **NetworkError**, **AnonymousNetworkError** és **SASNetworkError**. Ezek akkor fordulnak elő, ha a tárolási szolgáltatás hálózati hibát észlel, amikor az ügyfél tárolási kérelmet készít.

Ennek a hibának a leggyakoribb oka az ügyfél leválasztása, mielőtt lejár az időkorlát a Storage szolgáltatásban. Vizsgálja meg az ügyfél kódját, hogy megtudja, miért és mikor szakad meg az ügyfél a Storage szolgáltatással. A Wireshark vagy a Tcping segítségével is ellenőrizheti a hálózati kapcsolattal kapcsolatos problémákat az ügyfélről. Ezek az eszközök a [függelékekben]olvashatók.

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap
Ha az ügyfélalkalmazás HTTP 403 (Tiltott) hibákat jelez, annak egyik valószínű oka lehet, hogy az ügyfél egy lejárt közös hozzáférésű jogosultságkódot (SAS-t) használ, amikor tárolási kérelmet küld (egyéb lehetséges okok lehetnek még az óraeltérés, az érvénytelen kulcsok és az üres fejlécek). Ha egy lejárt SAS-kulcs a hiba oka, akkor nem fog bejegyzéseket látni a kiszolgálóoldali Storage naplózási szolgáltatásának naplóadataiban. Az alábbi táblázat a Storage ügyféloldali kódtár által létrehozott ügyféloldali naplóból származó mintát mutatja be, amely a problémát szemlélteti:

| Forrás | Részletesség | Részletesség | Ügyfélkérelem azonosítója | Művelet szövege |
| --- | --- | --- | --- | --- |
| Microsoft. Azure. Storage |Tájékoztatás |3 |85d077ab-... |A művelet megkezdése az elsődleges hellyel (Location Mode) PrimaryOnly. |
| Microsoft. Azure. Storage |Tájékoztatás |3 |85d077ab-... |Szinkron kérelem indítása <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft. Azure. Storage |Tájékoztatás |3 |85d077ab-... |Várakozás a válaszra. |
| Microsoft. Azure. Storage |Figyelmeztetés |2 |85d077ab-... |Kivétel történt a válaszra való várakozás közben: a távoli kiszolgáló a következő hibát adta vissza: (403) tiltott. |
| Microsoft. Azure. Storage |Tájékoztatás |3 |85d077ab-... |Válasz érkezett. Állapotkód = 403, kérelem azonosítója = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, tartalom-MD5 =, ETag =. |
| Microsoft. Azure. Storage |Figyelmeztetés |2 |85d077ab-... |Kivétel történt a művelet során: a távoli kiszolgáló a következő hibát adta vissza: (403) tiltott.. |
| Microsoft. Azure. Storage |Tájékoztatás |3 |85d077ab-... |Annak ellenőrzése, hogy a műveletet újra kell-e próbálni. Újrapróbálkozás száma = 0, HTTP-állapotkód = 403, kivétel = a távoli kiszolgáló hibát adott vissza: (403) tiltott.. |
| Microsoft. Azure. Storage |Tájékoztatás |3 |85d077ab-... |A következő hely az elsődleges értékre van állítva, a hely mód alapján. |
| Microsoft. Azure. Storage |Hiba |1 |85d077ab-... |Az újrapróbálkozási szabályzat nem engedélyezte az újrapróbálkozást. A távoli kiszolgáló meghibásodása hibát adott vissza: (403) tiltott. |

Ebben az esetben meg kell vizsgálnia, hogy miért jár le az SAS-token, mielőtt az ügyfél elküldi a jogkivonatot a kiszolgálónak:

* Általában nem kell beállítania a kezdési időt, amikor létrehoz egy SAS-t egy ügyfél számára azonnali használat céljából. Ha kis óraeltérések vannak az SAS-t a jelenlegi idő használatával létrehozó gazdagép és a tárolási szolgáltatás között, akkor előfordulhat, hogy a tárolási szolgáltatás olyan SAS-t fogad, amely még nem érvényes.
* Ne állítson be nagyon rövid lejárati időt az SAS-hoz. Az SAS-t létrehozó gazdagép és a tárolási szolgáltatás közötti kis óraeltérések tehát a vártnál látszólag korábban lejáró SAS-t eredményezhetnek.
* Az SAS-kulcs Version paramétere (például az **ÜKtgE = 2015-04-05**) egyezik-e az Ön által használt Storage ügyféloldali kódtár verziójával? Javasoljuk, hogy mindig a [Storage ügyféloldali kódtár](https://www.nuget.org/packages/WindowsAzure.Storage/)legújabb verzióját használja.
* Ha újra létrehozza a tárelérési kulcsot, a meglévő SAS-jogkivonatok érvénytelenné válhatnak. Ez a probléma akkor léphet fel, ha hosszú lejárati idejű SAS-jogkivonatokat hoz létre az ügyfélalkalmazások számára gyorsítótárazás céljából.

Ha a Storage ügyféloldali kódtárának használatával hozza létre az SAS-jogkivonatokat, akkor könnyedén hozhat létre érvényes jogkivonatot. Ha azonban a tárolót használja REST API és az SAS-tokeneket kézzel hozza létre, tekintse meg a [hozzáférés delegálása közös hozzáférési aláírással](/rest/api/storageservices/delegate-access-with-shared-access-signature)című témakört.

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap
Ha az ügyfélalkalmazás egy HTTP 404 (Nem található) üzenetet kap a kiszolgálótól, akkor ez azt jelenti, hogy az objektum, amelyet az ügyfél használni próbált (például egy entitás, egy táblázat, egy blob, egy tároló vagy egy üzenetsor) nem létezik a tárolási szolgáltatásban. Ennek számos oka lehet, például:

* [Az ügyfél vagy egy másik folyamat korábban törölte az objektumot]
* [Egy közös hozzáférésű jogosultságkód (SAS) engedélyezési problémája]
* [Az ügyféloldali JavaScript-kód nem rendelkezik engedéllyel az objektumhoz való hozzáféréshez]
* [Hálózati hiba]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Az ügyfél vagy egy másik folyamat korábban törölte az objektumot.
Olyan esetekben, amikor az ügyfél egy tárolási szolgáltatásban lévő adatok olvasását, frissítését vagy törlését kísérli meg, általában könnyen azonosítható a kiszolgálóoldali naplók egy korábbi művelet, amely törölte a szóban forgó objektumot a Storage szolgáltatásból. A naplófájlok gyakran azt mutatják, hogy egy másik felhasználó vagy folyamat törölte az objektumot. A kiszolgálóoldali tároló naplózási naplójában a művelet típusa és a kért-Object-Key oszlopok azt mutatják be, hogy egy ügyfél törölte az objektumot.

Abban az esetben, ha az ügyfél egy objektumot próbál beszúrni, előfordulhat, hogy nem azonnal nyilvánvaló, hogy ez az eredmény olyan HTTP 404 (nem található) választ eredményez, amely miatt az ügyfél új objektumot hoz létre. Ha azonban az ügyfél blobot hoz létre, akkor képesnek kell lennie a blob-tároló megtalálására, ha az ügyfél egy üzenetet hoz létre, és képesnek kell lennie a várólista megkeresésére, és ha az ügyfél egy sor hozzáadásával képesnek kell lennie a tábla megkeresésére.

A Storage ügyféloldali kódtár ügyféloldali naplójának használatával részletesebben megtekintheti, hogy az ügyfél milyen kéréseket küld a Storage szolgáltatásnak.

A Storage ügyféloldali kódtár által létrehozott következő ügyféloldali napló szemlélteti a problémát, ha az ügyfél nem találja a létrehozandó blob tárolóját. Ez a napló a következő tárolási műveletek részleteit tartalmazza:

| Kérés azonosítója | Művelet |
| --- | --- |
| 07b26a5d-... |**Deleteifexists paranccsal** metódus a blob-tároló törléséhez. Vegye figyelembe, hogy ez a művelet egy **Head** kérést tartalmaz a tároló létezésének ellenőrzéséhez. |
| e2d06d78... |**Createifnotexists metódust** metódus a blob-tároló létrehozásához. Vegye figyelembe, hogy ez a művelet egy **Head** kérést tartalmaz, amely ellenőrzi a tároló létezését. A **fej** 404 üzenetet ad vissza, de folytatja. |
| de8b1c3c-... |**UploadFromStream** metódus a blob létrehozásához. A **put** kérelem 404 üzenettel meghiúsul |

Naplóbejegyzések:

| Kérés azonosítója | Művelet szövege |
| --- | --- |
| 07b26a5d-... |Szinkron kérelem indítása a következőhöz: `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = HEAD.............. x-MS-Client-Request-ID: 07b26a5d-.... x-MS-Date: kedd, 03 jún 2014 10:33:11 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. RESTYPE: tároló. |
| 07b26a5d-... |Várakozás a válaszra. |
| 07b26a5d-... |Válasz érkezett. Állapotkód = 200, kérelem azonosítója = eeead849-... Content-MD5 =, ETag =    &quot; 0x8D14D2DC63D059B &quot; . |
| 07b26a5d-... |A válasz fejlécei sikeresen feldolgozva, a művelet többi részével folytatva. |
| 07b26a5d-... |Válasz törzsének letöltése. |
| 07b26a5d-... |A művelet sikeresen befejeződött. |
| 07b26a5d-... |Szinkron kérelem indítása a következőhöz: `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = DELETE......... x-MS-Client-Request-ID: 07b26a5d-.... x-MS-Date: kedd, 03 jún 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. RESTYPE: tároló. |
| 07b26a5d-... |Várakozás a válaszra. |
| 07b26a5d-... |Válasz érkezett. Állapotkód = 202, kérelem azonosítója = 6ab2a4cf-..., Content-MD5 =, ETag =. |
| 07b26a5d-... |A válasz fejlécei sikeresen feldolgozva, a művelet többi részével folytatva. |
| 07b26a5d-... |Válasz törzsének letöltése. |
| 07b26a5d-... |A művelet sikeresen befejeződött. |
| e2d06d78-... |Aszinkron kérelem indítása a következőhöz: `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` .</td> |
| e2d06d78-... |StringToSign = HEAD.............. x-MS-Client-Request-ID: e2d06d78-.... x-MS-Date: kedd, 03 jún 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. RESTYPE: tároló. |
| e2d06d78-... |Várakozás a válaszra. |
| de8b1c3c-... |Szinkron kérelem indítása a következőhöz: `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt` . |
| de8b1c3c-... |StringToSign = PUT... 64. qCmF + TQLPhq/YYK50mP9ZQ = =........ x-MS-blob-Type: BlockBlob. x-MS-Client-Request-ID: de8b1c3c-.... x-MS-Date: kedd, 03 jún 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |A kérések adatbevitelének előkészítése. |
| e2d06d78-... |Kivétel történt a válaszra való várakozás közben: a távoli kiszolgáló a következő hibát adta vissza: (404) nem található.. |
| e2d06d78-... |Válasz érkezett. Állapotkód = 404, kérelem azonosítója = 353ae3bc-..., Content-MD5 =, ETag =. |
| e2d06d78-... |A válasz fejlécei sikeresen feldolgozva, a művelet többi részével folytatva. |
| e2d06d78-... |Válasz törzsének letöltése. |
| e2d06d78-... |A művelet sikeresen befejeződött. |
| e2d06d78-... |Aszinkron kérelem indítása a következőhöz: `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| e2d06d78-... |StringToSign = PUT... 0......... x-MS-Client-Request-ID: e2d06d78-.... x-MS-Date: kedd, 03 jún 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. RESTYPE: tároló. |
| e2d06d78-... |Várakozás a válaszra. |
| de8b1c3c-... |Kérelemre vonatkozó adatírás. |
| de8b1c3c-... |Várakozás a válaszra. |
| e2d06d78-... |Kivétel történt a válaszra való várakozás közben: a távoli kiszolgáló hibát adott vissza: (409) ütközés.. |
| e2d06d78-... |Válasz érkezett. Állapotkód = 409, kérelem azonosítója = c27da20e-..., Content-MD5 =, ETag =. |
| e2d06d78-... |A hiba-válasz törzsének letöltése. |
| de8b1c3c-... |Kivétel történt a válaszra való várakozás közben: a távoli kiszolgáló a következő hibát adta vissza: (404) nem található.. |
| de8b1c3c-... |Válasz érkezett. Állapotkód = 404, kérelem azonosítója = 0eaeab3e-..., Content-MD5 =, ETag =. |
| de8b1c3c-... |Kivétel történt a művelet során: a távoli kiszolgáló a következő hibát adta vissza: (404) nem található.. |
| de8b1c3c-... |Az újrapróbálkozási szabályzat nem engedélyezte az újrapróbálkozást. A távoli kiszolgáló meghibásodása hibát adott vissza: (404) nem található.. |
| e2d06d78-... |Az újrapróbálkozási szabályzat nem engedélyezte az újrapróbálkozást. A távoli kiszolgáló meghibásodása hibát adott vissza: (409) ütközés.. |

Ebben a példában a napló azt mutatja, hogy az ügyfél a **createifnotexists metódust** metódustól érkező kéréseket (e2d06d78...) kéri a **UploadFromStream** metódustól érkező kérésekkel (de8b1c3c-...). Ez az elhagyás azért történik, mert az ügyfélalkalmazás aszinkron módon hívja meg ezeket a metódusokat. Módosítsa az ügyfél aszinkron kódját annak biztosításához, hogy az létrehozza a tárolót, mielőtt az adott tárolóban lévő blobba feltölthet bármilyen adatfeltöltés előtt. Ideális esetben minden tárolót előre kell létrehoznia.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>A közös hozzáférésű aláírás (SAS) engedélyezési hibája
Ha az ügyfélalkalmazás olyan SAS-kulcsot próbál használni, amely nem tartalmazza a művelethez szükséges engedélyeket, a Storage szolgáltatás HTTP 404 (nem található) üzenetet ad vissza az ügyfélnek. Ugyanakkor a mérőszámokban nem nulla értéket is láthat a **SASAuthorizationError** .

A következő táblázat a tárolási naplózási naplófájlban található példa kiszolgálóoldali naplófájlt jeleníti meg:

| Name | Érték |
| --- | --- |
| Kérelem kezdési ideje | 2014-05-30T06:17:48.4473697 Z |
| Művelettípus     | GetBlobProperties            |
| Kérelem állapota     | SASAuthorizationError        |
| HTTP-állapotkód   | 404                            |
| Hitelesítéstípus| Sas                          |
| Szolgáltatás típusa       | Blob                         |
| URL-cím kérése         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ? SV = 2014-02-14&SR = c&si = mypolicy&SIG = XXXXX &; API-Version = 2014-02-14 |
| Kérelem AZONOSÍTÓjának fejléce  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Ügyfélkérelem azonosítója  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Vizsgálja meg, hogy az ügyfélalkalmazás miért próbálkozik olyan művelet végrehajtásával, amelyhez nem adta meg az engedélyeket.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>Az ügyféloldali JavaScript-kód nem jogosult az objektum elérésére
Ha JavaScript-ügyfelet használ, és a Storage szolgáltatás HTTP 404-üzeneteket ad vissza, a böngészőben a következő JavaScript-hibákat kell megkeresni:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Az Internet Explorerben az F12 Fejlesztői eszközök segítségével nyomon követheti a böngésző és a Storage szolgáltatás között cserélt üzeneteket az ügyféloldali JavaScript-problémák elhárítása során.
>
>

Ezek a hibák azért fordulnak elő, mert a webböngésző [ugyanazt](https://www.w3.org/Security/wiki/Same_Origin_Policy) a biztonsági korlátozást valósítja meg, amely megakadályozza, hogy egy weblap egy másik tartományba tartozó API-t hívjon le abból a tartományból, ahonnan a lap származik.

A JavaScript-probléma megkerüléséhez konfigurálhatja a CORS-t az ügyfélhez hozzáférő tárolási szolgáltatáshoz. További információ: [az CORS-támogatás az Azure Storage szolgáltatásokhoz](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Az alábbi mintakód bemutatja, hogyan konfigurálhatja a blob Service-t, hogy engedélyezze a contoso-tartományban futó JavaScriptet a blob Storage szolgáltatásban található blob eléréséhez:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

---

#### <a name="network-failure"></a><a name="network-failure"></a>Hálózati hiba
Bizonyos esetekben a hálózati csomagok elvesztése miatt a Storage szolgáltatás HTTP 404-üzeneteket ad vissza az ügyfélnek. Ha például az ügyfélalkalmazás töröl egy entitást a Table szolgáltatásból, láthatja, hogy az ügyfél olyan tárolási kivételt jelez, amely "HTTP 404 (nem található)" állapotüzenetek bejelentését jeleníti meg a Table szolgáltatásból. Amikor megvizsgálja a táblázatot a Table Storage szolgáltatásban, láthatja, hogy a szolgáltatás a kért módon törölte az entitást.

Az ügyfél kivétel részletei közé tartozik a kérelem azonosítója (7e84f12d...), amelyet a kéréshez a Table Service rendelt: ezt az információt felhasználva megkeresheti a kérelem részleteit a kiszolgálóoldali tárolóhelyek naplófájljaiban a naplófájlban található **Request-ID-header** oszlopban. A metrikák használatával is meghatározhatja, hogy mikor fordulnak elő hibák, majd a naplófájlok alapján megkeresheti a hibát. Ez a naplóbejegyzés azt mutatja, hogy a törlés meghiúsult "HTTP (404) Client other Error" állapotüzenetek üzenet. Ugyanez a naplóbejegyzés is magában foglalja az ügyfél által a **Client-Request-ID** oszlopban (813ea74f...) létrehozott kérelem azonosítóját is.

A kiszolgálóoldali napló egy másik, ugyanazzal az **ügyfél-kérelmi azonosító** értékkel (813ea74f...) rendelkező bejegyzést is tartalmaz egy sikeres törlési művelethez ugyanahhoz az entitáshoz, és ugyanabból az ügyfélből. Ez a sikeres törlési művelet nagyon rövid idő alatt zajlott a sikertelen törlési kérelem előtt.

Ennek a forgatókönyvnek a legvalószínűbb oka az, hogy az ügyfél törlési kérelmet küld az entitásnak a Table szolgáltatásnak, amely sikeres volt, de nem kapott nyugtát a kiszolgálóról (például egy ideiglenes hálózati probléma miatt). Az ügyfél ezután automatikusan újrapróbálkozik a művelettel (ugyanazzal az **ügyfél-kérelem-azonosítóval**), és az újrapróbálkozás sikertelen volt, mert az entitás már törölve lett.

Ha ez a probléma gyakran előfordul, vizsgálja meg, hogy az ügyfél miért nem tud nyugtákat fogadni a Table szolgáltatástól. Ha a probléma időnként időszakos, akkor a "HTTP (404) nem található" hibaüzenetet kell felhasználnia, és be kell jelentkeznie az ügyfélbe, de az ügyfél továbbra is elérhetővé válik.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Az ügyfél HTTP 409 (ütközés) üzeneteket kap
Az alábbi táblázat a kiszolgálóoldali napló kivonatát mutatja be két ügyfél-művelethez: a **deleteifexists paranccsal** a **createifnotexists metódust** által azonnal követte a blob-tároló nevét használva. Minden ügyfél-művelet két kérelmet küld a kiszolgálónak, először egy **GetContainerProperties** -kérést, hogy ellenőrizze, hogy létezik-e a tároló, majd kövesse a **DeleteContainer** vagy a **CreateContainer** kérelmet.

| Időbélyeg | Művelet | Eredmény | Tárolónév | Ügyfélkérelem azonosítója |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

Az ügyfélalkalmazás kódja törli, majd azonnal újból létrehozza a BLOB-tárolót ugyanazzal a névvel: a **createifnotexists metódust** metódus (az ügyfél-kérelem azonosítója bc881924-...) végül a http 409 (ütközés) hibával meghiúsul. Ha egy ügyfél blobtárolókat, táblázatokat vagy üzenetsorokat töröl, egy rövid ideig nem lesz elérhető ugyanaz a név.

Ha a törlési/ismételt létrehozási minta gyakran előfordul, az ügyfélalkalmazásnak egyedi tárolóneveket kell használnia, valahányszor új tárolókat hoz létre.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>A metrikák az alacsony PercentSuccess vagy az elemzési naplóbejegyzések esetében a ClientOtherErrors tranzakciós állapotú műveleteit mutatják
A **PercentSuccess** metrika rögzíti a http-állapotkód alapján sikeresen végrehajtott műveletek százalékos arányát. A 2XX állapot-kódjaival rendelkező műveletek sikeresek, míg az 3XX-ben, a 4XX és a 5XX-tartományban található állapotkódok nem sikeresnek számítanak, és csökkentik a **PercentSuccess** metrika értékét. A kiszolgálóoldali tárolási naplófájlokban ezek a műveletek a **ClientOtherErrors** tranzakciós állapotával lesznek rögzítve.

Fontos megjegyezni, hogy ezek a műveletek sikeresen befejeződtek, ezért nem befolyásolják más metrikákat, például a rendelkezésre állást. Néhány példa a sikeresen végrehajtott műveletekre, de a sikertelen HTTP-állapotkódok például a következők lehetnek:

* A **ResourceNotFound** (nem található 404), például egy Get kérelemből olyan blobra, amely nem létezik.
* **ResourceAlreadyExists** (ütközés 409), például egy olyan **CreateIfNotExist** -műveletből, amelyben az erőforrás már létezik.
* **ConditionNotMet** (nem módosított 304), például egy feltételes műveletből, például amikor egy ügyfél egy **ETAG** -értéket küld, valamint egy http **If-None-Match** fejlécet, hogy csak akkor igényeljen képet, ha az a legutóbbi művelet óta frissült.

Megtalálhatja az általános REST API hibakódok listáját, amelyeket a tárolási szolgáltatások az oldal [általános REST API hibakódok](/rest/api/storageservices/Common-REST-API-Error-Codes)esetén adnak vissza.

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>A kapacitási mérőszámok váratlan növekedést mutatnak a tárolási kapacitás kihasználtsága terén
Ha a Storage-fiókban hirtelen, váratlan változásokat tapasztal, akkor a rendelkezésre állási metrikák megtekintésével megvizsgálhatja az okokat. Előfordulhat például, hogy a sikertelen törlési kérelmek számának növekedése növelheti az alkalmazás-specifikus törlési műveletként használt BLOB Storage mennyiségét, ezért előfordulhat, hogy a lemezterület felszabadítása valószínűleg nem a várt módon működik (például azért, mert a lemezterület felszabadításához használt SAS-jogkivonatok lejártak).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>A probléma a Storage Emulator fejlesztési vagy tesztelési célú használatból ered
A Storage emulatort általában a fejlesztés és a tesztelés során használja, hogy elkerülje az Azure Storage-fiókra vonatkozó követelményt. A Storage Emulator használatakor előforduló gyakori problémák a következők:

* [Az "X" funkció nem működik a Storage-emulátorban.]
* [Hiba: "a HTTP-fejlécek egyikének értéke nem megfelelő formátumú" a Storage Emulator használata esetén]
* [A Storage-emulátor futtatásához rendszergazdai jogosultságok szükségesek]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Az "X" funkció nem működik a Storage-emulátorban.
A Storage Emulator nem támogatja az Azure Storage-szolgáltatások, például a file Service összes funkcióját. További információ: [Az Azure Storage Emulator használata fejlesztéshez és teszteléshez](storage-use-emulator.md).

Azokhoz a funkciókhoz, amelyeket a Storage Emulator nem támogat, használja az Azure Storage szolgáltatást a felhőben.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Hiba: "a HTTP-fejlécek egyikének értéke nem megfelelő formátumú" a Storage Emulator használata esetén
Teszteli az alkalmazást, amely a Storage ügyféloldali kódtárat használja a helyi tároló-Emulátorra, és a metódusok (például a **createifnotexists metódust** ) a következő hibaüzenettel meghiúsulnak: "a HTTP-fejlécek egyikének értéke nem megfelelő formátumú." Ez azt jelzi, hogy a használt Storage-emulátor verziója nem támogatja a Storage ügyféloldali kódtár verzióját, amelyet Ön használ. A Storage ügyféloldali kódtár hozzáadja az **x-MS-Version** fejlécet az összes olyan kéréshez, amelyet az tesz. Ha a Storage Emulator nem ismeri fel az **x-MS-Version** fejlécben szereplő értéket, a rendszer elutasítja a kérelmet.

A tár ügyféloldali naplófájljaival megtekintheti az elküldött **x-MS-Version fejléc** értékét. Az **x-MS-Version fejléc** értékét is megtekintheti, ha a Hegedűs használatával követi nyomon az ügyfélalkalmazás kérelmeit.

Ez a forgatókönyv általában akkor fordul elő, ha a Storage ügyféloldali kódtár legújabb verzióját telepíti és használja a Storage Emulator frissítése nélkül. Telepítenie kell a Storage Emulator legújabb verzióját, vagy a fejlesztéshez és a teszteléshez az emulátor helyett a felhőalapú tárolást kell használnia.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>A Storage-emulátor futtatásához rendszergazdai jogosultságok szükségesek
A Storage Emulator futtatásakor a rendszer rendszergazdai hitelesítő adatokat kér. Ez csak akkor történik meg, ha első alkalommal inicializálja a Storage-emulátort. A Storage Emulator inicializálását követően nincs szükség rendszergazdai jogosultságokra a futtatásához.

További információ: [Az Azure Storage Emulator használata fejlesztéshez és teszteléshez](storage-use-emulator.md). A Storage emulatort a Visual Studióban is inicializálhatja, amely rendszergazdai jogosultságokat is igényel.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Problémákba ütközik a .NET-hez készült Azure SDK telepítésekor
Amikor megpróbálja telepíteni az SDK-t, a nem kísérli meg telepíteni a Storage emulatort a helyi gépre. A telepítési napló a következő üzenetek egyikét tartalmazza:

* CAQuietExec: hiba: az SQL-példány nem érhető el
* CAQuietExec: hiba: nem sikerült létrehozni az adatbázist

Az ok a meglévő LocalDB-telepítéssel kapcsolatos probléma. Alapértelmezés szerint a Storage Emulator a LocalDB használatával tartja az adatmegőrzést az Azure Storage szolgáltatásainak szimulálása során. A LocalDB-példány alaphelyzetbe állításához futtassa a következő parancsokat egy parancssori ablakban, mielőtt megpróbálja telepíteni az SDK-t.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

A **delete** parancs eltávolítja a régi adatbázisfájlok a Storage Emulator korábbi példányaiból.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Más probléma van a tárolási szolgáltatással
Ha az előző hibaelhárítási szakaszban nem szerepel a tárolási szolgáltatással kapcsolatos probléma, a következő módszert kell alkalmaznia a probléma diagnosztizálásához és hibaelhárításához.

* Ellenőrizze, hogy van-e változás a várt Base-line viselkedéstől. Előfordulhat, hogy a mérőszámok alapján meg tudja határozni, hogy a probléma átmeneti vagy állandó, és hogy a probléma milyen tárolási műveleteket érint.
* A metrikák információ segítségével a kiszolgálóoldali naplózási adatokon keresheti meg az esetlegesen előforduló hibákkal kapcsolatos részletesebb információkat. Ezek az információk segíthetnek a probléma elhárításában és megoldásában.
* Ha a kiszolgálóoldali naplókban található információk nem elégségesek a probléma megoldásához, használhatja a Storage ügyféloldali kódtár ügyféloldali naplóit az ügyfélalkalmazás működésének vizsgálatához, valamint az olyan eszközökhöz, mint a Hegedűs, a Wireshark a hálózat kivizsgálásához.

A Hegedűs használatával kapcsolatos további információkért tekintse meg az[1. függelék: a Hegedűs használata a http-és HTTPS-forgalom rögzítéséhez]című témakört.

További információ a Wireshark használatáról: "[2. függelék: a Wireshark használata a hálózati forgalom rögzítéséhez]".

## <a name="appendices"></a><a name="appendices"></a>Függelékek
A függelékekben számos olyan eszközt ismertetünk, amelyek hasznosak lehetnek az Azure Storage (és más szolgáltatások) problémáinak diagnosztizálásához és hibaelhárításához. Ezek az eszközök nem részei az Azure Storage-nak, és némelyikük harmadik féltől származó termék. Ennek megfelelően az ezekben a függelékekben tárgyalt eszközökre nem vonatkozik a Microsoft Azure vagy az Azure Storage szolgáltatással esetlegesen támogatott támogatási szerződés, ezért az értékelési folyamat részeként meg kell vizsgálnia az eszközök szolgáltatói által elérhető licencelési és támogatási lehetőségeket.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>1. függelék: a Hegedűs használata a HTTP-és HTTPS-forgalom rögzítéséhez
A [Hegedűs](https://www.telerik.com/fiddler) hasznos eszköz a http-és HTTPS-forgalom elemzéséhez az ügyfélalkalmazás és az Ön által használt Azure Storage szolgáltatás között.

> [!NOTE]
> A Hegedűs képes a HTTPS-forgalom dekódolására; olvassa el figyelmesen a Hegedűs dokumentációját, és Ismerje meg, hogy ez hogyan működik, és hogy megértse a biztonsági szempontokat.
>
>

Ez a függelék röviden ismerteti, hogyan konfigurálhatja a hegedűst a forgalom rögzítésére a helyi gép között, ahol a Hegedűs és az Azure Storage szolgáltatásokat telepítette.

A Hegedűs elindítása után megkezdi a HTTP-és HTTPS-forgalom rögzítését a helyi gépen. A következő néhány hasznos parancs a Hegedűs vezérléséhez:

* A forgalom rögzítésének leállítása és elindítása. A főmenüben lépjen a **fájl** pontra, majd kattintson a **forgalom rögzítése** lehetőségre a rögzítés be-és kikapcsolásához.
* Rögzített forgalmi adatok mentése. A főmenüben válassza a **fájl**, majd a **Mentés** lehetőséget, majd kattintson a **minden munkamenet** elemre: Ez lehetővé teszi, hogy egy munkamenet-archív fájlba mentse a forgalmat. A munkamenet-archívumot később is újratöltheti elemzés céljából, vagy elküldheti, ha a Microsoft támogatási szolgálata kéri.

A Hegedűs által rögzített forgalom mennyiségének korlátozásához használhatja a **szűrők** lapon konfigurált szűrőket. Az alábbi képernyőfelvételen egy szűrő látható, amely csak az **contosoemaildist.table.Core.Windows.net** -tároló végpontjának továbbított forgalmat rögzíti:

![Képernyőkép, amely egy olyan szűrőt mutat be, amely csak a contosoemaildist.table.core.windows.net-tároló végpontjának továbbított forgalmat rögzíti.][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>2. függelék: hálózati forgalom rögzítése a Wireshark használatával
A [Wireshark](https://www.wireshark.org/) egy hálózati protokoll-elemző, amely lehetővé teszi a különböző hálózati protokollok részletes csomagjainak megtekintését.

Az alábbi eljárás bemutatja, hogyan rögzíthet részletes csomagokat a helyi gépről érkező forgalomra vonatkozóan, ahol a Wireshark az Azure Storage-fiókban lévő Table Service-be telepítette.

1. Indítsa el a Wireshark a helyi gépen.
2. A **Start** szakaszban válassza ki az internethez csatlakozó helyi hálózati adaptert vagy adaptereket.
3. Kattintson a **rögzítési beállítások** elemre.
4. Vegyen fel egy szűrőt a **rögzítési szűrő** szövegmezőbe. A **gazdagép contosoemaildist.table.Core.Windows.net** például úgy konfigurálja a Wireshark-t, hogy csak a **contosoemaildist** -fiókban lévő Table Service-végponton vagy onnan küldött csomagokat rögzítsen. Tekintse meg a [rögzítési szűrők teljes listáját](https://wiki.wireshark.org/CaptureFilters).

   ![Képernyőkép, amely bemutatja, hogyan adhat hozzá szűrőt a rögzítési szűrő szövegmezőhöz.][6]
5. Kattintson a **Start** gombra. A Wireshark most rögzíti az összes olyan csomagot, amelyet a tábla szolgáltatási végpontja küld, vagy amelyről az ügyfélalkalmazás a helyi gépen való használatakor kerül.
6. Ha elkészült, a főmenün kattintson a **rögzítés** , majd a **Leállítás** lehetőségre.
7. A rögzített Wireshark-rögzítési fájlban lévő fájlok mentéséhez a főmenüben kattintson a **fájl** , majd a **Mentés** elemre.

A WireShark kiemel minden, a **packetlist** ablakban található hibát. A hibák és figyelmeztetések összefoglalásának megtekintéséhez használhatja a **szakértői információs** ablakot is (kattintson az **elemzés**, majd a **szakértői adatok** elemre).

![Képernyőkép a szakértői információk ablakról, amelyen megtekintheti a hibák és figyelmeztetések összegzését.][7]

Azt is megteheti, hogy megtekinti a TCP-adatforrást, mivel az alkalmazási réteg úgy látja, hogy a jobb gombbal a TCP-adatforrásra kattint, és kiválasztja a **TCP stream** Ez akkor hasznos, ha rögzítési szűrő nélkül rögzítette a memóriaképet. További információért lásd a [következő TCP-streameket](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![Képernyőkép, amely bemutatja, hogyan tekintheti meg a TCP-adatértékeket az alkalmazás rétege számára.][8]

> [!NOTE]
> A Wireshark használatával kapcsolatos további információkért tekintse meg a [Wireshark felhasználói útmutatóját](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>4. függelék: az Excel használata a metrikák és a naplózási adatok megtekintéséhez
Számos eszköz lehetővé teszi, hogy egy tagolt formátumban töltse le a tárolási metrikai adatokat az Azure Table Storage-ból, így a megtekintés és elemzés érdekében egyszerűen betöltheti az adatokat az Excelbe. Az Azure Blob Storage Storage-naplózási adatai már olyan tagolt formátumban vannak, amelyet betölthet az Excelbe. Azonban a megfelelő oszlopfejlécek hozzáadására van szükség az információk alapján [Storage Analytics a naplózási formátumot](/rest/api/storageservices/Storage-Analytics-Log-Format) , és Storage Analytics a [metrikák tábla sémáját](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

A tároló naplózási adatainak importálása az Excel programba a blob Storage-ból való letöltés után:

* Az **adatok** menüben kattintson a **szövegből** elemre.
* Keresse meg a megtekinteni kívánt naplófájlt, és kattintson az **Importálás** gombra.
* A **szöveg importálása varázsló** 1. lépésében válassza a **tagolt** elemet.

A **szövegfájl importálása varázsló** 1. lépésében válassza a **pontosvessző** lehetőséget az egyetlen határolójelként, és válassza a dupla idézőjelet **szöveges minősítőként**. Ezután kattintson a **Befejezés** gombra, és válassza ki, hová kívánja helyezni az adatait a munkafüzetben.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>5. függelék: az Azure DevOps Application Insights figyelése
A teljesítmény és a rendelkezésre állás monitorozásának részeként használhatja az Azure DevOps Application Insights funkcióját is. Az eszköz a következőket teheti:

* Győződjön meg arról, hogy a webszolgáltatás elérhető és rugalmas. Függetlenül attól, hogy az alkalmazás egy webszolgáltatást használó webhely vagy alkalmazás, amely néhány percenként tesztelheti az URL-címet a világ különböző helyein, és megtudhatja, hogy van-e probléma.
* Gyorsan diagnosztizálhatja a webszolgáltatás teljesítményével kapcsolatos problémákat vagy kivételeket. Annak megállapítása, hogy a CPU vagy más erőforrások szűkösek-e, verem-nyomkövetést kap a kivételek közül, és könnyen kereshetők a naplók nyomkövetése. Ha az alkalmazás teljesítménye az elfogadható határértékek alá csökken, a Microsoft e-mailt küldhet. A .NET-és a Java-webszolgáltatásokat egyaránt nyomon követheti.

További információ: [Mi a Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Következő lépések

Az Azure Storage-beli elemzéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Tárfiók monitorozása az Azure Portalon](./manage-storage-analytics-logs.md)
* [Storage Analytics](storage-analytics.md)
* [Storage Analytics-metrikák](storage-analytics-metrics.md)
* [Storage Analytics mérőszámok táblázatának sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Storage Analytics-naplók](storage-analytics-logging.md)
* [Storage Analytics-napló formátuma](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Bevezetés]: #introduction
[Az útmutató rendszerezése]: #how-this-guide-is-organized

[A tárolási szolgáltatás figyelése]: #monitoring-your-storage-service
[Figyelési szolgáltatás állapota]: #monitoring-service-health
[Kapacitás figyelése]: #monitoring-capacity
[A rendelkezésre állás monitorozása]: #monitoring-availability
[Teljesítmény monitorozása]: #monitoring-performance

[Tárolási problémák diagnosztizálása]: #diagnosing-storage-issues
[A szolgáltatás állapotával kapcsolatos problémák]: #service-health-issues
[Teljesítményproblémák]: #performance-issues
[Hibák diagnosztizálása]: #diagnosing-errors
[A Storage Emulator problémái]: #storage-emulator-issues
[Storage-naplózási eszközök]: #storage-logging-tools
[Hálózati naplózási eszközök használata]: #using-network-logging-tools

[Végpontok közötti nyomkövetés]: #end-to-end-tracing
[A naplófájlok korrelációjának összekötése]: #correlating-log-data
[Ügyfélkérelem azonosítója]: #client-request-id
[Kiszolgálói kérelem azonosítója]: #server-request-id
[Időbélyegek]: #timestamps

[Hibaelhárítási útmutató]: #troubleshooting-guidance
[A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok magas AverageServerLatency értéket mutatnak]: #metrics-show-high-AverageServerLatency
[Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]: #you-are-experiencing-unexpected-delays-in-message-delivery

[A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]: #metrics-show-an-increase-in-PercentThrottlingError
[Átmeneti növekedés a Percentthrottlingerror értéket mutatnak]: #transient-increase-in-PercentThrottlingError
[Percentthrottlingerror értéket mutatnak-hiba állandó növekedése]: #permanent-increase-in-PercentThrottlingError
[A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]: #metrics-show-an-increase-in-PercentTimeoutError
[A mérőszámok emelkedő PercentNetworkError értéket mutatnak]: #metrics-show-an-increase-in-PercentNetworkError

[Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap]: #the-client-is-receiving-403-messages
[Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap]: #the-client-is-receiving-404-messages
[Az ügyfél vagy egy másik folyamat korábban törölte az objektumot]: #client-previously-deleted-the-object
[Egy közös hozzáférésű jogosultságkód (SAS) engedélyezési problémája]: #SAS-authorization-issue
[Az ügyféloldali JavaScript-kód nem rendelkezik engedéllyel az objektumhoz való hozzáféréshez]: #JavaScript-code-does-not-have-permission
[Hálózati hiba]: #network-failure
[Az ügyfél HTTP 409 (Ütközés) hibaüzeneteket kap]: #the-client-is-receiving-409-messages

[A metrikák az alacsony PercentSuccess vagy az elemzési naplóbejegyzések esetében a ClientOtherErrors tranzakciós állapotú műveleteit mutatják]: #metrics-show-low-percent-success
[A kapacitási mérőszámok váratlan növekedést mutatnak a tárolási kapacitás kihasználtsága terén]: #capacity-metrics-show-an-unexpected-increase
[A probléma a Storage Emulator fejlesztési vagy tesztelési célú használatból ered]: #your-issue-arises-from-using-the-storage-emulator
[Az "X" funkció nem működik a Storage-emulátorban.]: #feature-X-is-not-working
[Hiba: "a HTTP-fejlécek egyikének értéke nem megfelelő formátumú" a Storage Emulator használata esetén]: #error-HTTP-header-not-correct-format
[A Storage-emulátor futtatásához rendszergazdai jogosultságok szükségesek]: #storage-emulator-requires-administrative-privileges
[Problémákba ütközik a .NET-hez készült Azure SDK telepítésekor]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Más probléma van a tárolási szolgáltatással]: #you-have-a-different-issue-with-a-storage-service

[Függelékek]: #appendices
[1. függelék: a Hegedűs használata a HTTP-és HTTPS-forgalom rögzítéséhez]: #appendix-1
[2. függelék: hálózati forgalom rögzítése a Wireshark használatával]: #appendix-2
[4. függelék: az Excel használata a metrikák és a naplózási adatok megtekintéséhez]: #appendix-4
[5. függelék: az Azure DevOps Application Insights figyelése]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png