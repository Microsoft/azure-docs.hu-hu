---
title: Azure Adatkezelő Azure Monitor (előzetes verzió) | Microsoft Docs
description: Ez a cikk Azure Monitor Azure Adatkezelő-fürtökkel kapcsolatos információkat ismerteti.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: dcfe12b30e336863c8e112d9ad675a2f57fe48f4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179136"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Adatkezelő Azure Monitor (előzetes verzió)

Azure Monitor for Azure Adatkezelő (előzetes verzió) – a fürtök teljesítményének, működésének, használatának és meghibásodásának egységes áttekintésével biztosítja a fürtök átfogó figyelését.
Ez a cikk segítséget nyújt az Azure Adatkezelő (előzetes verzió) Azure Monitor bevezetésének és használatának megismerésében.

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Az Azure Adatkezelő Azure Monitor bemutatása (előzetes verzió)

Mielőtt beugrott a felületre, tisztában kell lennie azzal, hogyan mutatja be és jeleníti meg az információkat.
-    **Méretezési szempontból** a fürtök elsődleges metrikáinak pillanatkép-nézetét ábrázolva egyszerűen nyomon követheti a lekérdezések, a betöltések és az exportálási műveletek teljesítményét.
-   Egy adott Azure Adatkezelő-fürt **elemzésének részletezése** a részletes elemzések elvégzése érdekében.
-    **Testreszabható** , ahol megváltoztathatja, hogy mely metrikákat szeretné megtekinteni, módosítani vagy beállítani a határértékekhez igazított küszöbértékeket, és mentheti a saját egyéni munkafüzeteit. A munkafüzetben található diagramok az Azure-irányítópultokon rögzíthetők.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Megtekintés Azure Monitor (méretezési szempontból)

Azure Monitorból megtekintheti a fürt fő teljesítménymutatóit, beleértve a lekérdezésekhez, a betöltéshez és az exportálási műveletekhez tartozó mérőszámokat az előfizetésben található több fürtről, valamint a teljesítménnyel kapcsolatos problémák azonosításához.

Ha szeretné megtekinteni a fürtök teljesítményét az összes előfizetésben, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)

2. Válassza a **figyelő** elemet a Azure Portal bal oldali paneljén, majd az áttekintési központ szakaszban válassza az **Azure adatkezelő-fürtök (előzetes verzió)** lehetőséget.

![Képernyőkép az áttekintő élményről több gráfmal](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Áttekintés lap

A kiválasztott előfizetés **Áttekintés** lapján a táblázat az Azure adatkezelő-fürtökhöz tartozó interaktív metrikákat jeleníti meg az előfizetésen belül csoportosítva. Az eredményeket az alábbi legördülő listából kiválasztott beállítások alapján szűrheti:

* Előfizetések – csak az Azure Adatkezelő-fürtökkel rendelkező előfizetések jelennek meg.

* Azure Adatkezelő-fürtök – alapértelmezés szerint csak legfeljebb öt fürt lehet előre kijelölve. Ha a hatókör-választóban az összes vagy több fürtöt választja, a rendszer legfeljebb 200 fürtöt ad vissza.

* Időtartomány – alapértelmezés szerint az utolsó 24 órában jeleníti meg az adatokat a megfelelő beállítások alapján.

A számláló csempéje a legördülő listában a kiválasztott előfizetésekben lévő Azure Adatkezelő-fürtök teljes számát összesíti, és azt, hogy hány van kiválasztva. Az oszlopokhoz feltételes színkódok tartoznak: életben tartás, CPU, betöltés kihasználtsága és gyorsítótár kihasználtsága. Az narancssárga kódolású cellák olyan értékekkel rendelkeznek, amelyek nem fenntarthatók a fürt számára. 

Ahhoz, hogy jobban megértse az egyes mérőszámok körét, javasoljuk, hogy olvassa el az [Azure adatkezelő mérőszámok](/azure/data-explorer/using-metrics#cluster-metrics)dokumentációját.

### <a name="query-performance-tab"></a>Lekérdezési teljesítmény lap

Ezen a lapon látható a lekérdezés időtartama, az egyidejű lekérdezések teljes száma és a szabályozott lekérdezések teljes száma.

![A lekérdezési teljesítmény lap képernyőképe](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Betöltési teljesítmény lap

Ezen a lapon látható a betöltési késés, a sikeres betöltési eredmények, a sikertelen betöltési eredmények, a betöltési mennyiség és az Event/IoT-hubhoz feldolgozott események.

[![Képernyőfelvétel a betöltési teljesítmény lapról](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Adatfolyam-betöltési teljesítmény lap

Ezen a lapon az átlagos adatsebességre, az átlagos időtartamra és a kérelmekre vonatkozó adatok szerepelnek.

### <a name="export-performance-tab"></a>Teljesítmény exportálása lap

Ezen a lapon megtalálhatók a folyamatos exportálási műveletekhez tartozó exportált rekordok, a késői, a függőben lévő darabszámok és a kihasználtság százalékos aránya.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Megtekintés Azure Adatkezelő fürterőforrás-erőforrásból (részletezés elemzése)

Azure Adatkezelő-fürtök Azure Monitorának elérése közvetlenül egy Azure Adatkezelő-fürtből:

1. A Azure Portal válassza az **Azure adatkezelő-fürtök** lehetőséget.

2. Válasszon ki egy Azure Adatkezelő-fürtöt a listából. A figyelés szakaszban válassza az **áttekintések (előzetes verzió)** lehetőséget.

Ezek a nézetek az Azure Adatkezelő-fürt erőforrás-nevének kiválasztásával is elérhetők a Azure Monitor-megállapítások nézetben.

Az Azure Adatkezelő Azure Monitor a naplók és a metrikák összekapcsolása révén globális figyelési megoldást biztosít. A naplókon alapuló vizualizációk belefoglalása megköveteli, hogy a felhasználók [engedélyezzék az Azure adatkezelő-fürt diagnosztikai naplózását, és elküldjék őket egy log Analytics munkaterületre.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Az engedélyezni kívánt diagnosztikai naplók a következők: **parancs**, **lekérdezés**, **TableDetails** és **TableUsageStatistics**.

![A "figyelési naplók engedélyezése" szöveget megjelenítő kék gomb képernyőképe](./media/data-explorer/enable-logs.png)


 Az **Áttekintés** lap a következőket jeleníti meg:

- A metrikák csempéi kiemelik a fürt rendelkezésre állását és általános állapotát, hogy gyorsan értékeljék az állapotukat.

- Az aktív [Advisor ajánlásainak](/azure/data-explorer/azure-advisor) és az [erőforrás](/azure/data-explorer/monitor-with-resource-health) állapotának összefoglalása.

- A legfontosabb CPU-és memóriahasználat, valamint az egyedi felhasználók számának időbeli alakulását bemutató diagramok.


[![Képernyőkép egy Azure Adatkezelő fürterőforrás-erőforrásról](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

A **fő mérőszámok** lap a fürt egyes metrikáinak egységes nézetét jeleníti meg: általános mérőszámok, lekérdezésekkel kapcsolatos, betöltéssel kapcsolatos és folyamatos átvitelsel kapcsolatos metrikák.

[![A hibák nézet képernyőképe](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

A **használat** lapon a felhasználók mélyen megismerhetik a fürt parancsainak és lekérdezésének teljesítményét. Ezen az oldalon a következőket teheti:
 
 - Megtudhatja, hogy mely munkaterhelési csoportok, felhasználók és alkalmazások küldik a legtöbb lekérdezést, vagy a legtöbb PROCESSZORt és memóriát használják (így megtudhatja, hogy mely munkaterhelések küldik el a fürt legsúlyosabb lekérdezéseit a feldolgozáshoz).
 - Azonosítsa a leggyakoribb munkaterhelés-csoportokat, a felhasználókat és az alkalmazásokat a sikertelen lekérdezések alapján.
 - Azonosítsa a lekérdezések számának legutóbbi változásait az átlagos napi átlag (az elmúlt 16 nap során), a munkaterhelés-csoport, a felhasználó és az alkalmazás alapján.
 - Azonosíthatja a trendek és a csúcsok számát a lekérdezések, a memória és a CPU-felhasználás alapján a munkaterhelés csoport, a felhasználó, az alkalmazás és a parancs típusa szerint.

[![Képernyőkép az Operations nézetről, amely a leggyakoribb alkalmazási diagramokat és lekérdezéseket, valamint a parancsok és lekérdezések számát, valamint a parancs típusa szerint a leggyakoribb parancsokat jeleníti meg.](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Képernyőkép az Operations nézetről, a lekérdezések számának sorszáma alkalmazás szerint, teljes memória az alkalmazás és a teljes CPU szerint alkalmazás szerint](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

A **Tables (táblák** ) lapon a fürtben lévő táblák legújabb és korábbi tulajdonságai láthatók. Megtekintheti, hogy mely táblák használják a legtöbb helyet, nyomon követheti a növekedési előzményeket a táblázat mérete, a meleg adatok és a sorok időbeli száma alapján.

A **gyorsítótár** lapon a felhasználók elemezheti a tényleges lekérdezéseket, és összehasonlíthatja azokat a beállított gyorsítótár-szabályzattal (minden táblázat esetében). Megadhatja azokat a táblákat, amelyeket a legtöbb lekérdezés és táblázat használ, és amelyek nem kérdeznek le, és ennek megfelelően alkalmazkodnak a gyorsítótárazási házirendhez. A gyorsítótár-szabályzatokra vonatkozó ajánlásokat a Azure Advisor adott tábláira vonatkozóan kaphatja meg (jelenleg a gyorsítótárra vonatkozó javaslatok csak a [fő Azure Advisor irányítópultról](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)érhetők el), a tényleges lekérdezések alapján, az elmúlt 30 napban és egy nem optimalizált gyorsítótárazási szabályzattal a lekérdezések legalább 95%-ában. A Azure Advisor gyorsítótár-csökkentési javaslatai elérhetők az "adathoz kötött" fürtök esetében (ami azt jelenti, hogy a fürt alacsony CPU-és alacsony betöltési kihasználtsággal rendelkezik, de magas adatkapacitás miatt a fürt nem méretezhető vagy nem méretezhető).

[![A gyorsítótár részleteinek képernyőképe](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Rögzítés az Azure-irányítópulton

A szakasz jobb felső sarkában található gombostű ikonra kattintva rögzítheti az egyik metrikus szakasz ("on-Scale" perspektíva) valamelyikét egy Azure-irányítópultra.

![Képernyőkép a PIN-kód ikon kiválasztásáról](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Azure Monitor testreszabása az Azure Adatkezelő-fürthöz

Ez a szakasz azokat a gyakori forgatókönyveket ismerteti, amelyekkel a munkafüzet szerkeszthető az adatelemzési igények támogatásához:
* A munkafüzet hatóköre, hogy mindig egy adott előfizetést vagy Azure Adatkezelő-fürtöt válasszon ki
* A rács metrikáinak módosítása
* Küszöbértékek vagy színvisszaadási/kódolási beállítások módosítása

A testreszabásokat a szerkesztési mód engedélyezésével kezdheti meg, ehhez kattintson a **Testreszabás** gombra a felső eszköztáron.

![Képernyőkép a Testreszabás gombról](./media/data-explorer/customize.png)

A testreszabásokat egy egyéni munkafüzetbe menti a rendszer, hogy megakadályozza a közzétett munkafüzet alapértelmezett konfigurációjának felülírását. A munkafüzetek egy erőforráscsoporthoz lesznek mentve, vagy az Ön számára magánjellegű Saját jelentések szakaszban, vagy a megosztott jelentések szakaszban, amely mindenki számára elérhető az erőforráscsoporthoz való hozzáféréssel. Az Egyéni munkafüzet mentése után nyissa meg a munkafüzet-katalógust, és indítsa el.

![Képernyőfelvétel a munkafüzet-gyűjteményről](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Az általános hibaelhárítási útmutatóért tekintse meg a dedikált munkafüzet-alapú információkkal [kapcsolatos hibaelhárítási cikket](troubleshoot-workbooks.md).

Ez a szakasz a Azure Monitor Azure Adatkezelő-fürthöz (előzetes verzió) való használata során felmerülő gyakori problémák diagnosztizálását és hibaelhárítását ismerteti. Az alábbi lista segítségével megkeresheti az adott hibához kapcsolódó információkat.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Miért nem látom az összes előfizetést az előfizetési választóban?

Csak az Azure Adatkezelő-fürtöket tartalmazó előfizetéseket jelenítjük meg, amelyek a kiválasztott előfizetési szűrőből vannak kiválasztva, amelyek a Azure Portal fejléc "címtár + előfizetés" elemében vannak kijelölve.

![Az előfizetés-szűrő képernyőképe](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Miért nem látok adatokat az Azure Adatkezelő-fürthöz a használat, a táblák vagy a gyorsítótár szakaszban?

A naplókon alapuló adatai megtekintéséhez engedélyeznie kell a [diagnosztikai naplókat](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) a figyelni kívánt összes Azure adatkezelő-fürthöz. Ez az egyes fürtökhöz tartozó diagnosztikai beállítások alatt végezhető el. Az adatait egy Log Analytics munkaterületre kell küldenie. Az engedélyezni kívánt diagnosztikai naplók a következők: parancs, lekérdezés, TableDetails és TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Már Engedélyeztem az Azure Adatkezelő-fürthöz tartozó naplókat, miért nem látom az adataim a parancsok és lekérdezések területen?

A diagnosztikai naplók jelenleg nem működnek visszamenőlegesen, így az adatai csak akkor jelennek meg, ha az Azure-Adatkezelőra tettek lépéseket. Ezért az Azure Adatkezelő-fürt aktív működésének módjától függően eltarthat egy ideig, akár órákig is.


## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a forgatókönyvekkel, amelyek támogatják az új és a meglévő jelentések testreszabását, valamint az [interaktív jelentések Azure monitor-munkafüzetekkel való létrehozását](../visualize/workbooks-overview.md)ismertető áttekintést.
