---
title: Teljesítményelemzés
titleSuffix: Azure Cognitive Search
description: TBD
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582287"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Teljesítmény elemzése az Azure Cognitive Search

Ez a cikk a lekérdezési és indexelési teljesítmény Cognitive Searchban történő elemzéséhez szükséges eszközöket, viselkedéseket és megközelítéseket ismerteti.

## <a name="develop-baseline-numbers"></a>Alapterv-számok fejlesztése

A nagy megvalósításban kritikus fontosságú, hogy a Cognitive Search szolgáltatás teljesítményének teljesítményteszt-tesztjét az éles környezetbe való bevezetésük előtt végezze el. Tesztelje mind a várt keresési terhelést, hanem a várt adatfeldolgozási munkaterheléseket is (ha lehetséges, futtassa ezeket egyszerre). A teljesítményteszt-számok segítségével ellenőrizheti a megfelelő [keresési szintet](search-sku-tier.md), a [szolgáltatás konfigurációját](search-capacity-planning.md)és a várt [lekérdezési késést](search-performance-analysis.md#average-query-latency).

A referenciaértékek fejlesztése érdekében javasoljuk az [Azure-Search-Performance-test (GitHub)](https://github.com/Azure-Samples/azure-search-performance-testing) eszközt.

Egy elosztott szolgáltatási architektúra hatásainak elkülönítéséhez próbálkozzon egy replika és egy partíció szolgáltatási konfigurációjának tesztelésével.

> [!NOTE]
> A Storage optimalizált szintjeihez (L1 és L2) a standard szintnél kisebb lekérdezési sebességet és nagyobb késést kell várni.
>

## <a name="use-diagnostic-logging"></a>Diagnosztikai naplózás használata

A legfontosabb eszköz, amelyet a rendszergazda a lehetséges teljesítménybeli problémák diagnosztizálására használ, [diagnosztikai naplózást](search-monitor-logs.md) végez, amely az üzemeltetési adatokat és mérőszámokat gyűjti a keresési szolgáltatással kapcsolatban. A diagnosztikai naplózás a [Azure monitoron](../azure-monitor/overview.md)keresztül engedélyezhető. A Azure Monitor és az adattárolással kapcsolatos költségekkel jár, de ha engedélyezi azt a szolgáltatás számára, akkor a teljesítménnyel kapcsolatos problémák kivizsgálásában is szerepet játszik.

Több lehetőség is van a késések előfordulására, akár hálózati átvitel során, akár az App Services-rétegben vagy a keresési szolgáltatásban végzett tartalom feldolgozására. A diagnosztikai naplózás egyik legfontosabb előnye, hogy a tevékenységeket a keresési szolgáltatás szemszögéből naplózzák, ami azt jelenti, hogy a napló segítségével meghatározhatja, hogy a teljesítménnyel kapcsolatos problémák a lekérdezéssel vagy indexeléssel kapcsolatos problémák vagy más meghibásodási pontok miatt jelentkeznek-e.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Naplózott események lánca" border="true":::

A diagnosztikai naplózás lehetőséget biztosít a naplózott információk tárolására. Javasoljuk, hogy a [log Analytics](../azure-monitor/logs/log-analytics-overview.md) használatával speciális Kusto-lekérdezéseket hajtson végre az adatokon a használattal és a teljesítménnyel kapcsolatos számos kérdés megválaszolására. 

A keresési szolgáltatás portáljának oldalain engedélyezheti a naplózást a **diagnosztikai beállításokon** keresztül, majd a **naplók** lehetőség kiválasztásával Kusto-lekérdezéseket adhat ki a log Analytics. A beállításával kapcsolatos további információkért lásd: a [naplófájlok adatainak összegyűjtése és elemzése](search-monitor-logs.md).

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Naplózási menü beállításai" border="true":::

## <a name="throttling-behaviors"></a>Szabályozási viselkedés

A szabályozás akkor fordul elő, ha a keresési szolgáltatás elérte azt a korlátot, amelyet az erőforrás szempontjából képes kezelni. A szabályozás a lekérdezések vagy az indexelés során fordulhat elő. Az ügyfél oldaláról egy API-hívás 503 HTTP-választ eredményez, ha a szabályozása megtörtént. Az indexelés során lehetőség van egy 207 HTTP-válasz fogadására is, amely azt jelzi, hogy egy vagy több elem nem tudott indexelni. Ez a hiba azt jelzi, hogy a keresési szolgáltatás a kapacitáshoz közeledik. 

A legalkalmasabb szabály, hogy számszerűsítse a megjelenő szabályozás mértékét. Ha például egy, a 500 000-es keresési lekérdezés le van szabályozva, lehetséges, hogy nem nagy probléma. Ha azonban a lekérdezések nagy hányadát szabályozzák egy adott időszakra, ez nagyobb aggodalomra ad okot. Egy adott időszakra vonatkozó szabályozással megtekintheti az olyan időkereteket is, amelyekben a szabályozás nagyobb valószínűséggel fordul elő, és segít eldönteni, hogy miként lehet a legjobban alkalmazkodni.

A legtöbb szabályozási probléma megoldásának egyszerű megoldása, ha több erőforrást szeretne a keresési szolgáltatásban (általában lekérdezés-alapú szabályozás replikái, vagy indexelési alapú szabályozásra szolgáló partíciók). Ugyanakkor a replikák és a partíciók egyre nagyobb költségeket okoznak, ezért fontos tudni, hogy miért történik a szabályozás. A szabályozást okozó feltételek kivizsgálását a következő néhány szakaszban ismertetjük. 

Az alábbi példa egy olyan Kusto-lekérdezést mutat be, amely képes azonosítani a betöltés alatt álló keresési szolgáltatásból származó HTTP-válaszok részletezését. Egy 7 napos időszakon keresztüli lekérdezéssel a megjelenített sávdiagram azt mutatja, hogy a keresési lekérdezések viszonylag nagy hányadát szabályozták a sikeres (200) válaszok számával összehasonlítva.

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Http-hibák számlálása sávdiagram" border="true":::

Egy adott időszakra vonatkozó szabályozás megvizsgálása segíthet azonosítani azokat az időpontokat, amikor gyakrabban fordulhat elő a szabályozás. Az alábbi példában egy idősorozat-diagram jelenik meg, amely megjeleníti a megadott időkereten belül bekövetkezett szabályozott lekérdezések számát. Ebben az esetben a teljesítmény-összehasonlítási időpontokkal összefügg a szabályozott lekérdezések.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="A szabályozott lekérdezések vonalas diagramja" border="true":::

## <a name="measure-individual-queries"></a>Egyéni lekérdezések mérése

Bizonyos esetekben hasznos lehet az egyes lekérdezések tesztelése, hogy lássák, hogyan működnek. Ehhez fontos, hogy megtudja, mennyi ideig tart a keresési szolgáltatás a munka elvégzéséhez, valamint azt, hogy mennyi ideig tart az ügyfél és az ügyfél közötti oda-vissza kérés. A diagnosztikai naplókat felhasználhatja az egyes műveletek keresésére, de egyszerűbb lehet mindezt egy ügyfél-eszközről, például a Poster-ből.

Az alábbi példában egy REST-alapú keresési lekérdezés lett végrehajtva. A Cognitive Search minden válaszban a lekérdezés befejezéséhez szükséges ezredmásodpercek számát tartalmazza, a fejlécek lapon látható "eltelt idő". A válasz felső részén található állapot elem mellett megtalálhatja az oda-és visszaút időtartamát. Ebben az esetben 418 ezredmásodperc. Az eredmények szakaszban a "headers" (fejlécek) lap lett kiválasztva. Ha ezt a két értéket az alábbi képen egy piros mezővel jelölte ki, akkor a Search szolgáltatás 21 MS-ot vett igénybe a keresési lekérdezés befejezéséhez, a teljes ügyfél-oda-és visszaút-kérelem pedig 125 MS volt. Ennek a két számnak a kivonásával megállapítható, hogy 104 MS további időt vett igénybe a keresési lekérdezés továbbítása a keresési szolgáltatáshoz, és vissza kell vinni a keresési eredményeket az ügyfélre.

Ez rendkívül hasznos lehet annak megállapítására, hogy lehet-e hálózati késés vagy más tényező, amely hatással van a lekérdezési teljesítményre.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Lekérdezés időtartamának mérőszámai" border="true":::

## <a name="query-rates"></a>Lekérdezési díjak

A keresési szolgáltatásnak a kérelmek szabályozására való egyik lehetséges oka az, hogy a rendszer a másodpercenkénti lekérdezések másodpercenkénti számát (QPS) vagy a másodpercenkénti lekérdezéseket (QPM) rögzíti. Mivel a keresési szolgáltatás több QPS kap, általában hosszabb időt vesz igénybe a lekérdezésekre, amíg a továbbiakban nem tud lépést tartani, ahogy a rendszer visszaküldi a 503-es HTTP-választ. 

A következő Kusto-lekérdezés a lekérdezési kötetet jeleníti meg a QPM-ben mérve, valamint a lekérdezések átlagos időtartamát ezredmásodpercben (AvgDurationMS), valamint a dokumentumok (AvgDocCountReturned) átlagos számát.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="A lekérdezéseket percenként bemutató diagram" border="true":::

> [!TIP]
> A diagram mögötti adatmegjelenítéshez távolítsa el a sort, `| render timechart` majd futtassa újra a lekérdezést.

## <a name="impact-of-indexing-on-queries"></a>Lekérdezések indexelésének következményei

A teljesítmény megvizsgálása során megfontolandó tényező, hogy az indexelés ugyanazokat az erőforrásokat használja, mint a keresési lekérdezések. Ha nagy mennyiségű tartalmat indexel, várhatóan megtekintheti a késés növekedését, mivel a szolgáltatás mindkét munkaterhelést megpróbálja befogadni.

Ha a lekérdezések lelassulnak, tekintse meg az indexelési tevékenység időzítését, és ellenőrizze, hogy egybeesik-e a lekérdezés romlásával. Előfordulhat például, hogy az indexelő egy napi vagy óránkénti feladatot futtat, amely a keresési lekérdezések csökkentett teljesítményével összefügg. 

Ez a szakasz olyan lekérdezéseket tartalmaz, amelyek segíthetnek a keresési és az indexelési díjak megjelenítésében. Ezekben a példákban az időtartomány van beállítva a lekérdezésben. Ügyeljen arra, hogy a **lekérdezésekben** Azure Portal-ben való futtatásakor jelezzen a beállítás.

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Időtartományok beállítása a lekérdezési eszközben" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Lekérdezés átlagos késése

Az alábbi lekérdezésben egy 1 perces intervallumot használ a keresési lekérdezések átlagos késésének megjelenítéséhez. A diagramon láthatjuk, hogy az átlagos késés 5:17:45, és 5:53pm-ig tartott.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="A lekérdezési késleltetés átlagos késését bemutató diagram" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Lekérdezések átlagos száma percenként (QPM)

A következő lekérdezés lehetővé teszi a percenkénti lekérdezések átlagos számának megkeresését, így biztosítva, hogy nem volt valami olyan tüske a keresési kérelmekben, amelyek hatással lehetnek a késésre. A diagramon láthatjuk, hogy van néhány eltérés, de semmi sem jelzi a kérések számának csúcsát.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Diagram átlagos lekérdezéseit ábrázoló diagram percenként" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Indexelési műveletek percenként (OPM)

Itt a percenkénti indexelési műveletek számát fogjuk megtekinteni. A diagramon láthatjuk, hogy a nagy mennyiségű adatok indexelve kezdődtek a 5:42 órakor, és 5:50pm-kor végződött. Ez az indexelés 3 percet vesz igénybe, mielőtt a keresési lekérdezések megkezdték a látens és 3 percet, amíg a keresési lekérdezések nem voltak többé rejtettek.

Ebből azt láthatjuk, hogy körülbelül 3 percet vett igénybe, hogy a keresési szolgáltatás elég elfoglalt legyen az indexeléstől, hogy megkezdődjön a keresési lekérdezés késésének hatása. Azt is láthatjuk, hogy az indexelés befejezése után még 3 percet vett igénybe, hogy a keresési szolgáltatás az újonnan indexelt tartalomból elvégezze az összes munkát, amíg a keresési lekérdezések nem lesznek többé rejtettek.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Az indexelési műveleteket percenként ábrázoló diagram" border="true":::

## <a name="background-service-processing"></a>Háttérben futó szolgáltatás feldolgozása

Nem szokatlan, hogy a lekérdezésben vagy az indexelési késésben ismétlődő tüskék láthatók. Az indexelési vagy a nagy lekérdezési sebességekre adott válaszokban fordulhat elő, de az egyesítési műveletek során is előfordulhatnak. A keresési indexek adattömbökben vagy szegmensekben vannak tárolva. A rendszer rendszeres időközönként összefésüli a kisebb szegmenseket a nagy szegmensekre, ami segít optimalizálni a szolgáltatás teljesítményét. Ez az egyesítési folyamat olyan dokumentumokat is töröl, amelyek korábban törlésre lettek megjelölve az indexből, ami a tárolóhely helyreállítását eredményezte. 

A szegmensek egyesítése gyors, de erőforrás-igényes, és így a szolgáltatás teljesítményének romlása is lehetséges. Ebben az esetben, ha a lekérdezés késésének rövid összetörteit látja, és az indexelt tartalomra vonatkozó legutóbbi változások egybeesnek, akkor valószínű, hogy ezt a késést az adatszegmens egyesítési műveleteire fogja számítani. 

## <a name="next-steps"></a>Következő lépések

Tekintse át a szolgáltatás teljesítményének elemzésével kapcsolatos további cikkeket.

+ [Teljesítménnyel kapcsolatos tippek](search-performance-tips.md)
+ [Szolgáltatási szintek kiválasztása](search-sku-tier.md)
+ [Kapacitás kezelése](search-capacity-planning.md)
