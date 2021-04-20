---
title: Azure Monitor (előzetes Azure Data Explorer)| Microsoft Docs
description: Ez a cikk a Azure Monitor fürtökre vonatkozó Azure Data Explorer ismerteti.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: ac147df90d3fa0c27bb4299c57ec79d9a9031710
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737592"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor (előzetes Azure Data Explorer)

Azure Monitor for Azure Data Explorer (előzetes verzió) átfogó monitorozást biztosít a fürtökről a fürtök teljesítményének, műveleteinek, használatának és meghibásodásának egységes áttekintésével.
Ez a cikk segít megérteni, hogyan lehet a Azure Monitor (előzetes verzió) Azure Data Explorer használni.

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>A Azure Monitor (előzetes Azure Data Explorer) bemutatása

Mielőtt belevetné magát a gyakorlatba, meg kell értenie, hogyan mutatja be és vizualizálja az információkat.
-    **Méretezési szempontból** a fürtök elsődleges metrika pillanatkép-nézetének megjelenítése a lekérdezések, a becslés és az exportálási műveletek teljesítményének egyszerű nyomon követéséhez.
-   **Egy adott fürt részletes** elemzésének Azure Data Explorer a részletes elemzéshez.
-    **Testreszabható,** ahol módosíthatja, hogy mely metrikákat szeretné látni, módosíthatja vagy beállíthatja a korlátokhoz igazodó küszöbértékeket, és mentheti saját egyéni munkafüzetét. A munkafüzet diagramjai rögzítheti az Azure-irányítópultokon.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>View from Azure Monitor (at scale perspective)

A Azure Monitor megtekintheti a fürt fő teljesítménymetrikákat, beleértve a lekérdezések, a bebecslés és az exportálási műveletek metrikákat az előfizetés több fürtjéről, és segít azonosítani a teljesítményproblémákat.

A fürtök teljesítményének az összes előfizetésben való megtekintéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/)

2. A **bal** oldali panelen válassza a Monitor Azure Portal, majd az Insights Hub szakaszban válassza a Azure Data Explorer **(előzetes verzió) lehetőséget.**

![Képernyőkép az áttekintő élményről több diagrammal](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Áttekintés lap

A kiválasztott **előfizetés** Áttekintés lapján a táblázat interaktív metrikákat jelenít meg az Azure Data Explorer az előfizetésen belül csoportosított fürtökhöz. Az eredményeket az alábbi legördülő listákban kiválasztott beállítások alapján szűrheti:

* Előfizetések – csak azok az előfizetések listában Azure Data Explorer, amelyek Azure Data Explorer fürtök.

* Azure Data Explorer fürtök – alapértelmezés szerint legfeljebb öt fürt van előre kiválasztva. Ha kijelöli a hatókörválasztóban az összes vagy több fürtöt, legfeljebb 200 fürt lesz visszaadva.

* Időtartomány – alapértelmezés szerint a az elmúlt 24 óra adatait jeleníti meg a megfelelő beállítások alapján.

A számlálócsempe a legördülő listában összegzi a kiválasztott előfizetések Azure Data Explorer fürtök teljes számát, és azt mutatja, hogy hány van kiválasztva. Az oszlopokhoz feltételes színkódok is vannak: Életben tartás, CPU-használat, Bebeterhelési kihasználtság és Gyorsítótár-kihasználtság. Az narancssárga kódú cellák olyan értékekkel is vannak, amelyek nem fenntarthatók a fürt számára. 

Annak érdekében, hogy jobban megértsük, mit képviselnek az egyes metrikák, javasoljuk, hogy olvassa el a metrikákról [Azure Data Explorer dokumentációját.](/azure/data-explorer/using-metrics#cluster-metrics)

### <a name="query-performance-tab"></a>Lekérdezési teljesítmény lap

Ezen a lapon látható a lekérdezések időtartama, az egyidejű lekérdezések teljes száma és a korlátozott lekérdezések teljes száma.

![Képernyőkép a lekérdezési teljesítmény lapról](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Ingestion Performance (Beszúrás teljesítménye) lap

Ezen a lapon látható a beszúrási késés, a sikeres feldolgozás eredményei, a sikertelen feldolgozás eredményei, a beszúrási mennyiség, valamint az Event/IoT Hubs számára feldolgozott események.

[![Képernyőkép a beszúrási teljesítmény lapról](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Streamelési átvitel teljesítménye lap

Ez a lap az átlagos adatátviteli sebességről, az átlagos időtartamról és a kérelmek sebességére vonatkozó információkat tartalmaz.

### <a name="export-performance-tab"></a>Exportálási teljesítmény lap

Ez a lap az exportált rekordokkal, a késéssel, a függőben lévő számmal és a folyamatos exportálási műveletek kihasználtságával kapcsolatos információkat tartalmaz.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Nézet egy Azure Data Explorer-erőforrásból (részletezés elemzése)

A fürtök Azure Monitor hozzáférése Azure Data Explorer fürtökhöz közvetlenül egy Azure Data Explorer fürtből:

1. A Azure Portal válassza a **Fürtök Azure Data Explorer lehetőséget.**

2. A listából válasszon ki egy Azure Data Explorer fürtöt. A monitorozás szakaszban válassza az **Elemzések (előzetes verzió) lehetőséget.**

Ezek a nézetek úgy is elérhetők, hogy kiválasztják egy Azure Data Explorer fürt erőforrásnevét a Azure Monitor nézetben.

Azure Monitor a Azure Data Explorer naplókat és metrikákat kombinálva biztosít egy globális monitorozási megoldást. A naplóalapú vizualizációk felvételéhez a felhasználóknak engedélyezniük kell a Azure Data Explorer naplózását, és el kell küldeniük őket [egy Log Analytics-munkaterületre.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Az engedélyezni szükséges diagnosztikai naplók a következőek: **Parancs,** **Lekérdezés,** **TableDetails**, **és TableUsageStatistics.**

![A "Naplók monitorozásának engedélyezése" szöveget megjelenítő kék gomb képernyőképe](./media/data-explorer/enable-logs.png)


 Az **Áttekintés lapon** a következő látható:

- Metrikacsempék, amelyek a fürt rendelkezésre állását és általános állapotát emelik ki az állapot gyors felmérése érdekében.

- Az aktív [Advisor-javaslatok és az](/azure/data-explorer/azure-advisor) erőforrások [állapotának összegzése.](/azure/data-explorer/monitor-with-resource-health)

- Diagramok a processzor- és memóriafelhasználókról és az egyedi felhasználók számról az idő múlása alatt.


[![Képernyőkép egy fürterőforrás Azure Data Explorer nézetről](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

A **Fő** metrikák lap a fürt egyes metrikainak egyesített nézetét jeleníti meg a következő szerint csoportosítva: általános metrikák, lekérdezéssel kapcsolatos, adatbecsüléshez kapcsolódó és streambeszúrási adatokkal kapcsolatos metrikák.

[![Képernyőkép a hibák nézetről](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

A **Használat** lapon a felhasználók mélyebben is belemerülnek a fürt parancsaiba és lekérdezéseibe. Ezen a lapon a következőt használhatja:
 
 - Tekintse meg, hogy mely számítási feladatcsoportok, felhasználók és alkalmazások küldik a legtöbb lekérdezést, vagy melyek használják a legtöbb CPU-t és memóriát (hogy tudja, mely számítási feladatok küldik el a legnagyobb mennyiségű lekérdezést a fürt számára a feldolgozáshoz).
 - A legfontosabb számítási feladatok csoportjainak, felhasználóinak és alkalmazásának azonosítása sikertelen lekérdezések alapján.
 - Azonosítsa a lekérdezések számának legutóbbi változásait a számítási feladatcsoport, a felhasználó és az alkalmazás napi átlagához képest (az elmúlt 16 napban).
 - A lekérdezések, a memória és a processzorhasználat trendjeinek és csúcsértékének azonosítása számítási feladatcsoport, felhasználó, alkalmazás és parancstípus szerint.

[![Képernyőkép a műveleti nézetről, amely a felső alkalmazás fánkdiagramjaival parancs- és lekérdezésszám szerint, a legfontosabbak parancs- és lekérdezésszám szerint, valamint a legjobb parancsok parancstípusok szerint](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Képernyőkép a műveleti nézetről, amely vonaldiagramon mutatja a lekérdezések számát alkalmazás szerint, a teljes memóriát alkalmazás és a teljes CPU-t alkalmazás szerint](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

A **Táblák** lap a fürtben lévő táblák legújabb és előzménytulajdonságait jeleníti meg. Láthatja, hogy mely táblák foglalják a legtöbb helyet, nyomon követheti a növekedési előzményeket a tábla mérete, a forró adatok és a sorok száma alapján.

A **gyorsítótár** lapon a felhasználók elemezhetik a tényleges lekérdezések visszatekintő mintáit, és összehasonlíthatja őket a konfigurált gyorsítótár-szabályzatokkal (az egyes táblákhoz). Azonosíthatja a legtöbb lekérdezés és nem lekérdezett tábla által használt táblákat, és ennek megfelelően adaptálhatja a gyorsítótár-szabályzatot. Az Azure Advisor adott tábláira vonatkozóan konkrét gyorsítótár-szabályzati javaslatokat kap (jelenleg a gyorsítótárazási javaslatok csak a fő [Azure Advisor-irányítópultról](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)érhetők el), a tényleges lekérdezések elmúlt 30 napban történt visszatekintése és a lekérdezések legalább 95%-ára vonatkozó, nem optimalizált gyorsítótár-szabályzat alapján. A Azure Advisor gyorsítótárának csökkentésére vonatkozó javaslatok érhetők el az "adatok által határolt" fürtökhöz (azaz a fürt processzorhasználata alacsony és alacsony a bebecsülési kihasználtsága, de a nagy adatkapacitás miatt a fürt nem volt képes horizontális le- vagy leskálást használni).

[![A gyorsítótár részleteinek képernyőképe](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

A **fürthatárok lap** a használat alapján jeleníti meg a fürthatárokat. Ezen a lapon megvizsgálhatja a processzor, a bebeszúrás és a gyorsítótár kihasználtságát. Ezek a metrikák "Alacsony", "Közepes" vagy "Magas" pontszámmal vannak felszámulva. Ezek a metrikák és pontszámok fontosak a fürt optimális termékváltozatának és példányszámának meghatározásakor, és a termékváltozatra/méretre vonatkozó javaslatban Azure Advisor figyelembe. Ezen a lapon kiválaszthat egy metrikacsempét, és mélyebben is megértheti annak trendjét és a pontszámának kiválasztását. A fürthöz Azure Advisor termékváltozatra/méretre vonatkozó javaslatot is megtekintheti. Az alábbi képen például látható, hogy az összes metrika "Low" (Alacsony) pontszámmal van felmérőzve, így a fürt költségajánlott, amely lehetővé teszi a horizontális le- és leskálás, valamint a költségmegtakarítást.

> [!div class="mx-imgBorder"]
> [![A fürthatárok képernyőképe.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Rögzítés az Azure-irányítópulton

A metrika bármelyik szakaszát (a "nagy léptékű" perspektívát) rögzítheti egy Azure-irányítópulton, ha a szakasz jobb felső részében található leküldéses gombostű ikonra kattint.

![Képernyőkép a kijelölt rögzítés ikonról](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Fürt Azure Monitor testreszabása Azure Data Explorer fürthöz

Ez a szakasz gyakori forgatókönyveket mutat be a munkafüzet adatelemzési igényeknek megfelelő testreszabáshoz való szerkesztéséhez:
* A munkafüzet hatókörének beállítása egy adott előfizetés vagy Azure Data Explorer fürt(ök) kiválasztásához
* Metrikák módosítása a rácsban
* Küszöbértékek módosítása vagy színre renderelés/kódolás

A testreszabást a szerkesztési mód engedélyezésével, a felső eszköztár Testreszabás gombjának kiválasztásával megkezdheti. 

![A Testreszabás gomb képernyőképe](./media/data-explorer/customize.png)

A rendszer egyéni munkafüzetbe menti a testreszabásokat, hogy ne felülírja a közzétett munkafüzet alapértelmezett konfigurációját. A munkafüzetek egy erőforráscsoportban vannak mentve, az Ön számára privát Saját jelentések szakaszban vagy a Megosztott jelentések szakaszban, amely mindenki számára elérhető, aki hozzáféréssel rendelkezik az erőforráscsoporthoz. Miután menti az egyéni munkafüzetet, el kell indítania a munkafüzet-katalógust.

![Képernyőkép a munkafüzet-katalógusról](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Általános hibaelhárítási útmutatásért tekintse meg a dedikált munkafüzetalapú elemzések [hibaelhárítását bemutató cikket.](troubleshoot-workbooks.md)

Ez a szakasz segít diagnosztizálni és elhárítani néhány olyan gyakori problémát, amely a fürt Azure Monitor (előzetes verzió) Azure Data Explorer során merülhet fel. Az alábbi lista segítségével megkeresheti az adott problémához kapcsolódó információkat.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Miért nem látom az összes előfizetésemet az előfizetés-kiválasztóban?

Csak azok az előfizetések jelölve Azure Data Explorer, amelyek a kiválasztott előfizetési szűrőből kiválasztott fürtökből állnak, és amelyek a "Címtár és előfizetés" fejlécben vannak Azure Portal.

![Képernyőkép az előfizetés szűrőről](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Miért nem látok adatokat a Azure Data Explorer fürtömről a Használat, táblák vagy Gyorsítótár szakaszban?

A naplóalapú adatok megtekintéséhez engedélyeznie [](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) kell a diagnosztikai naplókat az Azure Data Explorer fürtök mindegyikéhez. Ez az egyes fürtök diagnosztikai beállításainál használhatja. Az adatokat egy Log Analytics-munkaterületre kell küldenie. Az engedélyezni szükséges diagnosztikai naplók a következőek: Parancs, Lekérdezés, TableDetails és TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Már engedélyeztem a naplókat a Azure Data Explorer fürthöz. Miért nem látom még mindig az adataimat a Parancsok és lekérdezések alatt?

A diagnosztikai naplók jelenleg visszamenőlegesen nem működnek, így az adatok csak akkor jelennek meg, ha már végre lett hozva a Azure Data Explorer. Ezért a fürt aktív állapotától függően akár óráktól akár egy napig is Azure Data Explorer is.


## <a name="next-steps"></a>Következő lépések

Az Interaktív jelentések létrehozása Azure Monitor munkafüzetekkel című cikkből megtudhatja, hogyan támogathatja a munkafüzeteket, hogyan hozhat létre új jelentéseket és szabhatja testre a [meglévő jelentéseket.](../visualize/workbooks-overview.md)
