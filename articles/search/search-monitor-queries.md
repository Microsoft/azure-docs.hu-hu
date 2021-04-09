---
title: Lekérdezések figyelése
titleSuffix: Azure Cognitive Search
description: A teljesítmény és az átviteli sebesség lekérdezési metrikáinak figyelése. Lekérdezési karakterlánc-bemenetek összegyűjtése és elemzése az erőforrás-naplókban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592389"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Lekérdezési kérelmek figyelése az Azure Cognitive Search

Ez a cikk azt ismerteti, hogyan mérhető a lekérdezés teljesítménye és mennyisége a mérőszámok és az erőforrás-naplózás használatával. Emellett azt is ismerteti, hogyan kell összegyűjteni a lekérdezésekben használt beviteli kifejezéseket, ha az eszköz értékelése és a keresési Corpus hatékonysága szükséges.

A metrikákat tartalmazó korábbi adatok 30 napig őrződnek meg. A hosszú megőrzéshez, illetve az operatív adatok és a lekérdezési karakterláncok jelentéséhez mindenképpen engedélyezzen egy olyan [diagnosztikai beállítást](search-monitor-logs.md) , amely a naplózott események és metrikák megőrzésére szolgáló tárolási lehetőséget határozza meg.

Az adatmérés integritását maximalizáló feltételek a következők:

+ Használjon számlázható szolgáltatást (az alapszintű vagy a standard szinten létrehozott szolgáltatás). Az ingyenes szolgáltatást több előfizető is megosztja, ami egy bizonyos mértékű volatilitást vezet be, amely terhelési elmozdulást mutat be.

+ Egy replika és egy partíció használata, ha lehetséges, egy foglalt és elkülönített környezet létrehozásához. Ha több replikát használ, a lekérdezési mérőszámok átlaga több csomópont között történik, ami csökkentheti az eredmények pontosságát. Hasonlóképpen, a több partíció azt is jelenti, hogy az adatmegosztás történik, és a lehetséges, hogy egyes partíciók eltérő adattal rendelkezhetnek, ha az indexelés is folyamatban van. A lekérdezési teljesítmény finomhangolása esetén egyetlen csomópont és partíció a teszteléshez stabilabb környezetet biztosít.

> [!Tip]
> A további ügyféloldali kóddal és Application Insightsekkel a mélyebb betekintéshez is rögzítheti az átkattintási adatait, hogy mi vonzza az alkalmazás felhasználóinak érdeklődését. További információ: [Search Traffic Analytics](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Lekérdezési kötet (QPS)

A kötet a **másodpercenkénti keresési lekérdezések** (QPS) alapján mérhető, amely egy egyperces időszakon belül végrehajtható lekérdezések átlagos, darabszámbeli, minimális vagy maximális értékének jelentésére szolgál. A metrikák esetében az egyperces időközök (TimeGrain = "PT1M") a rendszeren belül vannak kijavítva.

A lekérdezések végrehajtása általában ezredmásodpercben történik, ezért a mérőszámokban csak a másodpercben lekérdezett lekérdezések jelennek meg.

| Aggregáció típusa | Leírás |
|------------------|-------------|
| Átlag | A lekérdezés végrehajtásának időpontjában egy percen belül eltelt másodpercek átlagos száma.|
| Darabszám | Az egyperces intervallumon belül a naplóba kibocsátott metrikák száma. |
| Maximum | Egy percen belül másodpercenként regisztrált keresési lekérdezések másodpercenkénti száma. |
| Minimális | Egy perc alatt másodpercenként regisztrált keresési lekérdezések másodpercenkénti száma.  |
| Sum | A percen belül végrehajtott lekérdezések összege.  |

Előfordulhat például, hogy egy percen belül egy ilyen mintázattal rendelkezik: egy másodperces magas terhelés, amely a SearchQueriesPerSecond maximális értéke, majd az átlagos terhelés 58 másodperce, végül egy másodperc csak egy lekérdezéssel, amely a minimum.

Egy másik példa: Ha egy csomópont 100 mérőszámot bocsát ki, ahol az egyes mérőszámok értéke 40, akkor a "Count" a 100, az "összeg" értéke 4000, az "átlag" pedig a 40, a "Max" pedig az 40.

## <a name="query-performance"></a>Lekérdezési teljesítmény

Az egész szolgáltatásra kiterjedő lekérdezési teljesítmény a keresési késés (a lekérdezés befejezésének időtartama) és az erőforrás-tartalom miatt eldobott szabályozott lekérdezések esetében mérhető.

### <a name="search-latency"></a>Keresési késés

| Aggregáció típusa | Késés | 
|------------------|---------|
| Átlag | Lekérdezés átlagos időtartama ezredmásodpercben. | 
| Darabszám | Az egyperces intervallumon belül a naplóba kibocsátott metrikák száma. |
| Maximum | Leghosszabb ideig futó lekérdezés a mintában. | 
| Minimális | A legrövidebb futó lekérdezés a mintában.  | 
| Összesen | A mintában lévő összes lekérdezés teljes végrehajtási ideje (egy perc) az intervallumon belül.  |

Vegye figyelembe a következő példát a **keresési késési** mérőszámokra: 86 lekérdezés lett mintavétel alatt, átlagosan 23,26 ezredmásodperc. Legalább 0 érték azt jelzi, hogy néhány lekérdezés el lett dobva. A leghosszabb ideig futó lekérdezés 1000 ezredmásodpercet vett igénybe. A végrehajtási idő összesen 2 másodperc volt.

![Késések összesítései](./media/search-monitor-usage/metrics-latency.png "Késések összesítései")

### <a name="throttled-queries"></a>Szabályozott lekérdezések

A szabályozott lekérdezések a folyamat helyett eldobott lekérdezésekre vonatkoznak. A legtöbb esetben a szabályozás a szolgáltatás futtatásának normális része.  Nem feltétlenül jelzi, hogy valami nem megfelelő.

A szabályozás akkor fordul elő, ha a jelenleg feldolgozott kérelmek száma meghaladja a rendelkezésre álló erőforrásokat. Előfordulhat, hogy megnövekszik a szabályozott kérelmek száma, ha a replika kikerül a rotációs vagy az indexelés során. A lekérdezési és indexelési kérelmeket ugyanazon erőforrások kezelik.

A szolgáltatás meghatározza, hogy az erőforrások felhasználása alapján kell-e eldobnia a kérelmeket. A memóriában, a PROCESSZORban és a lemez IO-ban felhasznált erőforrások százalékos aránya átlagosan egy adott időszakra terjed ki. Ha ez a százalék meghaladja a küszöbértéket, a rendszer az indexre irányuló összes kérelmet szabályozza, amíg a kérelmek mennyisége nem csökken. 

Az ügyféltől függően a rendszer a következő módokon jelezheti a szabályozott kérelmeket:

+ A szolgáltatás egy hibaüzenetet ad vissza, ha túl sok kérelmet küld. Próbálkozzon újra később.” 
+ A szolgáltatás egy 503-es hibakódot ad vissza, amely azt jelzi, hogy a szolgáltatás jelenleg nem érhető el. 
+ Ha a portált (például a keresési Explorert) használja, a lekérdezés csendesen törlődik, és a Keresés elemre kell kattintania.

A szabályozott lekérdezések megerősítéséhez használja a **szabályozott keresési lekérdezések** mérőszámát. A mérőszámokat megtekintheti a portálon, vagy létrehozhat egy riasztási metrikát a jelen cikkben leírtak szerint. A mintavételi intervallumon belül eldobott lekérdezések esetében a *Total (teljes* ) értéket használja a nem végrehajtott lekérdezések százalékos arányának lekéréséhez.

| Aggregáció típusa | Throttling |
|------------------|-----------|
| Átlag | Az intervallumon belül eldobott lekérdezések százalékos aránya. |
| Darabszám | Az egyperces intervallumon belül a naplóba kibocsátott metrikák száma. |
| Maximum | Az intervallumon belül eldobott lekérdezések százalékos aránya.|
| Minimális | Az intervallumon belül eldobott lekérdezések százalékos aránya. |
| Összesen | Az intervallumon belül eldobott lekérdezések százalékos aránya. |

A **szabályozott keresési lekérdezések aránya**, a minimum, a maximum, az átlag és az összes érték azonos értékű: a megadott keresési lekérdezések százalékos aránya, a keresési lekérdezések teljes száma egy percen belül.

Az alábbi képernyőképen az első szám a darabszám (vagy a naplóba eljuttatott metrikák száma). További összesítések, amelyek felül jelennek meg, vagy amikor a metrika fölé mutatnak, a következők: átlag, maximum és összeg. Ebben a példában egyetlen kérelem sem lett elvetve.

![Szabályozott összesítések](./media/search-monitor-usage/metrics-throttle.png "Szabályozott összesítések")

## <a name="explore-metrics-in-the-portal"></a>A metrikák megismerése a portálon

A jelenlegi számok gyors áttekintéséhez a szolgáltatás áttekintő lapjának **figyelés** lapja három mérőszámot jelenít meg (**keresési késés**, **keresési lekérdezések másodpercenként (keresési egység alapján)**, a **szabályozott keresési lekérdezések százalékos aránya**) a rögzített intervallumok között óra, nap és hét szerint, az Összesítés típusának módosításához.

A mélyebb feltáráshoz nyissa meg a metrikák Explorert a **figyelés** menüből, hogy a trendek és rendellenességek feltárásához a rétegek, a nagyítás és az adatok megjelenítése is megtörténjen. További információ a metrikák Explorerrel: az oktatóanyag elvégzése [a metrikák diagram létrehozásához](../azure-monitor/essentials/tutorial-metrics-explorer.md).

1. A figyelés szakaszban válassza a **metrikák** lehetőséget, hogy megnyissa a metrikák Explorert a keresési szolgáltatás hatókörével.

1. A metrika területen válasszon egyet a legördülő listából, és tekintse át az elérhető összesítések listáját egy előnyben részesített típushoz. Az Összesítés azt határozza meg, hogy az összegyűjtött értékek hogyan legyenek mintavételezés alatt az egyes időintervallumokban.

   ![Metrikák Intéző QPS metrikához](./media/search-monitor-usage/metrics-explorer-qps.png "Metrikák Intéző QPS metrikához")

1. A jobb felső sarokban állítsa be az időintervallumot.

1. Válasszon egy vizualizációt. Az alapértelmezett érték egy vonalas diagram.

1. A további összesítések réteg **hozzáadásához válassza a metrika hozzáadása** elemet, és válassza a különböző összesítések lehetőséget.

1. Nagyítás egy fontos területre a vonalas diagramon. Vigye az egérmutatót a terület elejére, kattintson és tartsa nyomva a bal egérgombot, húzza a terület másik oldalára, és szabadítsa fel a gombot. A diagram az adott időtartományon nagyítja fel.

## <a name="identify-strings-used-in-queries"></a>A lekérdezésekben használt karakterláncok azonosítása

Az erőforrás-naplózás engedélyezésekor a rendszeren a **AzureDiagnostics** táblában rögzíti a lekérdezési kérelmeket. Előfeltételként már engedélyezve kell lennie az [erőforrás-naplózásnak](search-monitor-logs.md), meg kell adnia egy log Analytics-munkaterületet vagy egy másik tárolási lehetőséget.

1. A figyelés szakaszban válassza a **naplók** lehetőséget a log Analytics egy üres lekérdezési ablak megnyitásához.

1. Futtassa a következő kifejezést a lekérdezés kereséséhez. keresési műveletek, táblázatos eredményhalmaz visszaadása, amely a művelet nevét, a lekérdezési karakterláncot, a lekérdezett indexet és a talált dokumentumok számát tartalmazza. Az utolsó két utasítás kizárja a lekérdezési karakterláncokat, amelyek egy üres vagy nem meghatározott keresésből állnak, mint egy minta index, amely csökkenti az eredményekben lévő zajt.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Szükség esetén beállíthat egy oszlop szűrőt *Query_s* egy adott szintaxis vagy karakterlánc kereséséhez. Például megadhatja, hogy a feletti szűrés *egyenlő legyen* `?api-version=2020-06-30&search=*&%24filter=HotelName` .)

   ![Naplózott lekérdezési karakterláncok](./media/search-monitor-usage/log-query-strings.png "Naplózott lekérdezési karakterláncok")

Habár ez a technika ad hoc vizsgálathoz működik, a jelentések készítése lehetővé teszi, hogy a lekérdezési karakterláncokat egy adott elrendezésben jobban áttekintse az elemzéshez.

## <a name="identify-long-running-queries"></a>Hosszú ideig futó lekérdezések azonosítása

Adja hozzá az időtartam oszlopot az összes lekérdezés számának lekéréséhez, nem csak azokat, amelyek mérőszámként lettek kiválasztva. Az Adatrendezés során megtekintheti, hogy mely lekérdezések a leghosszabb ideig tartó lekérdezéseket hajtják végre.

1. A figyelés szakaszban válassza a **naplók** lehetőséget a naplózási adatok lekérdezéséhez.

1. Futtassa a következő lekérdezést a lekérdezések visszaadásához, időtartam szerint, ezredmásodpercben rendezve. A leghosszabb ideig futó lekérdezések felül vannak.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Lekérdezések rendezése időtartam szerint](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Lekérdezések rendezése időtartam szerint")

## <a name="create-a-metric-alert"></a>Metrikai riasztás létrehozása

A metrikai riasztások egy küszöbértéket határoznak meg, amelyen értesítést kap, vagy egy előre meghatározott javítási műveletet indít el. 

Egy keresési szolgáltatás esetében gyakori, hogy metrikai riasztást hozzon létre a keresési késés és a szabályozott lekérdezések esetében. Ha ismeri a lekérdezések eldobását, megkeresheti a terhelést csökkentő vagy a kapacitás növelését okozó jogorvoslatokat. Ha például a szabályozott lekérdezések az indexelés során növekednek, akkor elhalaszthatja a lekérdezési tevékenységig.

Egy adott replika-partíciós konfiguráció korlátainak leküldésekor a rendszer a lekérdezési mennyiségi küszöbértékekhez (QPS) tartozó riasztások beállítását is hasznosnak tekinti.

1. A figyelés szakaszban válassza a **riasztások** elemet, majd kattintson az **+ új riasztási szabály** elemre. Győződjön meg arról, hogy a keresési szolgáltatás az erőforrásként van kiválasztva.

1. A feltétel területen kattintson a **Hozzáadás** gombra.

1. Adja meg a jel logikáját. A jel típusa mezőben válassza a **metrikák** lehetőséget, majd válassza ki a jelet.

1. A jel kiválasztása után egy diagram segítségével megjelenítheti a korábbi, tájékozott döntést a feltételek beállításának folytatásáról.

1. Ezután görgessen le a riasztási logikához. A megvalósíthatóság érdekében mesterségesen alacsony értéket adhat meg tesztelési célokra.

   ![Riasztási logika](./media/search-monitor-usage/alert-logic-qps.png "Riasztási logika")

1. Ezután egy műveleti csoportot kell megadnia vagy létrehoznia. Ez a válasz a küszöbérték teljesülésekor való meghívására. Lehet, hogy leküldéses értesítés vagy automatikus válasz.

1. Utolsó, a riasztás részleteinek megadása. Nevezze el és írja le a riasztást, rendeljen hozzá egy súlyossági értéket, és adja meg, hogy engedélyezett vagy letiltott állapotban szeretné-e létrehozni a szabályt.

   ![Riasztás részletei](./media/search-monitor-usage/alert-details.png "Riasztás részletei")

Ha e-mailes értesítést adott meg, a "Microsoft Azure" üzenet jelenik meg az "Azure: aktivált súlyosság: 3" tárgy sorával `<your rule name>` .

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, tekintse át a keresési szolgáltatás figyelésének alapjait, és ismerkedjen meg a teljes körű felügyeleti funkciókkal.

> [!div class="nextstepaction"]
> [Műveletek és tevékenységek figyelése az Azure Cognitive Search](search-monitor-usage.md)