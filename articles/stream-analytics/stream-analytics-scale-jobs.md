---
title: Horizontális felskálázás Azure Stream Analytics feladatokban
description: Ez a cikk azt ismerteti, hogyan méretezhető a Stream Analytics feladatok a bemeneti adatok particionálásával, a lekérdezés finomhangolásával és a feladatok folyamatos átviteli egységének beállításával.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018683"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Azure Stream Analytics feladatok méretezése az átviteli sebesség növelése érdekében
Ez a cikk bemutatja, hogyan lehet Stream Analytics-lekérdezést hangolni a streaming Analytics-feladatok átviteli sebességének növeléséhez. A következő útmutató segítségével méretezheti a feladatokat nagyobb terhelés kezelésére, és kihasználhatja a több rendszererőforrást (például több sávszélességet, több processzor-erőforrást, több memóriát).
Előfeltételként előfordulhat, hogy a következő cikkeket kell elolvasnia:
-   [A streamelési egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md)
-   [Párhuzamosítható-feladatok létrehozása](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>1. eset – a lekérdezés eredendően teljesen párhuzamosítható a bemeneti partíciók között
Ha a lekérdezés eredendően teljesen párhuzamosítható a bemeneti partíciók között, kövesse az alábbi lépéseket:
1.  Készítse el a lekérdezést zavaróan párhuzamosan a **Partition by** kulcsszó használatával. További részletekért tekintse meg a zavaró párhuzamos feladatok című szakaszt [ezen az oldalon](stream-analytics-parallelization.md).
2.  A lekérdezésben használt kimeneti típusoktól függően előfordulhat, hogy egyes kimenetek nem párhuzamosítható, vagy további konfigurációra van szükségük, hogy zavaróan párhuzamos legyen. A PowerBI kimenete például nem párhuzamosítható. A kimenetek mindig össze lesznek fésülve a kimeneti fogadóba való küldés előtt. A Blobok, táblák, ADLS, Service Bus és Azure függvények automatikusan párhuzamosak. Az SQL és az Azure szinapszis Analytics kimenetei párhuzamos is rendelkeznek. Az Event hub-nak meg kell egyeznie a PartitionKey-konfigurációval, hogy egyezzen a **Partition by** mezővel (általában PartitionID). Az Event hub esetében külön figyelmet kell fordítani a partíciók számának megadására az összes bemenetnél és az összes kimenetnél, hogy elkerülje a partíciók közti átkelést. 
3.  Futtassa a lekérdezést a **6 Su** -vel (amely egy számítástechnikai csomópont teljes kapacitása) a maximálisan elérhető átviteli sebesség méréséhez, és ha **Group By**-t használ, mérje fel, hogy hány csoport (kardinális) kezelésére van lehetőség. A rendszererőforrás-korlátokat ütő feladat általános tünetei a következők.
    - A SU% kihasználtsági metrika meghaladja a 80%-ot. Ez azt jelzi, hogy a memóriahasználat magas. A mérőszám növeléséhez hozzájáruló tényezők leírása [itt](stream-analytics-streaming-unit-consumption.md)található. 
    -   A kimeneti időbélyeg a falióra időpontjával kapcsolatos. A lekérdezési logikától függően előfordulhat, hogy a kimeneti időbélyeg a fal órajelének időpontjához képest logikai eltolással rendelkezik. Azonban nagyjából azonos sebességgel kell haladni. Ha a kimeneti időbélyeg további és további mögött van, akkor azt jelzi, hogy a rendszer túlműködik-e. Ez lehet az alsóbb rétegbeli kimeneti sávszélesség-szabályozás vagy a nagy CPU-kihasználtság eredménye. Jelenleg nem biztosítunk CPU-kihasználtsági mérőszámot, ezért nehéz lehet különbséget tenni a kettőnél.
        - Ha a probléma a fogadó általi szabályozás miatt lehetséges, akkor szükség lehet a kimeneti partíciók számának növelésére (és a bemeneti partíciók megtartására is, hogy a feladatok teljes mértékben párhuzamosítható), vagy növelje a fogadó erőforrásainak mennyiségét (például a CosmosDB vonatkozó kérelmek száma).
    - A Job ábrán az egyes adatbevitelek esetében a partíciók száma minden esetben az esemény metrikája. Ha a várakozó események mérőszáma továbbra is növekszik, azt is jelzi, hogy a rendszererőforrás korlátozott (a kimeneti fogadó szabályozása vagy a nagy CPU miatt).
4.  Miután meghatározta a 6 SU-feladatok elérésének korlátait, lineárisan kikövetkeztetheti a feladatokhoz tartozó feldolgozási kapacitást, ha további SUs-t ad hozzá, feltéve, hogy nincs olyan adattorzítása, amely bizonyos partíciókat "gyors" állapotba helyez.

> [!NOTE]
> Válassza ki a folyamatos átviteli egységek számát: mivel Stream Analytics létrehoz egy feldolgozási csomópontot minden egyes hozzáadott 6 SU számára, a legjobb, ha a csomópontok számát a bemeneti partíciók számának osztója, így a partíciók egyenletesen eloszthatók a csomópontok között.
> Például megmérte 6 SU-feladatát, így 4 MB/s feldolgozási sebességet érhet el, és a bemeneti partíciók száma 4. Kiválaszthatja, hogy a feladatainak futtatásához 12 SU, körülbelül 8 MB/s feldolgozási sebességet, vagy 24 SU-t érhet el 16 MB/s eléréséhez. Ezután eldöntheti, hogy mikor növelje a feladathoz tartozó SU számát a bemeneti arány függvényében.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>2. eset – Ha a lekérdezés nem zavaróan párhuzamos.
Ha a lekérdezés nem zavaróan párhuzamos, kövesse az alábbi lépéseket.
1.  Először egy olyan lekérdezéssel kezdjen el, **amely nem particionálja az** összetettséget, és az [1. esetben](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)a maximális terhelés méréséhez 6 Su értékű lekérdezést futtat.
2.  Ha elérheti a várható terhelést az átviteli sebesség időtartama alatt, készen áll. Azt is megteheti, hogy ugyanazt a feladatot használja, amely a 3 SU és 1 SU értéknél fut, hogy megtudja, milyen minimális számú SU működik a forgatókönyvben.
3.  Ha nem tudja elérni a kívánt átviteli sebességet, próbálja meg megszakítani a lekérdezést több lépésre, ha lehetséges, ha nem rendelkezik több lépéssel, és legfeljebb 6 SU-t foglal le a lekérdezés egyes lépéseihez. Ha például 3 lépésből áll, foglalja le 18 SU-t a "Scale" beállításban.
4.  Ha egy ilyen feladatot futtat, Stream Analytics a saját csomópontján helyezi el az egyes lépéseket, dedikált 6 SU-erőforrásokkal. 
5.  Ha még nem érte el a terhelési célt, megpróbálhatja a **partíció** használatát a bemenethez közelebbi lépésekkel. Ha **az operátor nem** lehet természetesen particionálható, a helyi/globális összesítési minta használatával particionált **csoportokat** hajthat végre, amelyet a nem particionált **csoportok** követnek. Ha például azt szeretné megszámolni, hogy hány autó halad végig 3 percenként az egyes autópályadíj-standokon, és az adatmennyiség meghaladja a 6 SU által kezelhető mennyiséget.

Lekérdezés:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
A fenti lekérdezésben partícióként számítja fel a személygépkocsik számát, majd a darabszámot az összes partícióból együtt adja hozzá.

A particionálás után a lépés minden partíciója esetében akár 6 SU-t is lefoglalhat, és mindegyik partíció 6 SU értékkel rendelkezik, így minden partíció a saját feldolgozó csomópontjára helyezhető el.

> [!Note]
> Ha a lekérdezés nem particionálható, a több lépésből álló lekérdezésben további SU hozzáadásával előfordulhat, hogy nem mindig javítja az átviteli sebességet. A teljesítmény megszerzésének egyik módja a kezdeti lépések kötetének csökkentése a helyi/globális aggregált minta használatával, az 5. lépésben leírtak szerint.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>3. eset: sok független lekérdezést futtat egy feladatokban.
Bizonyos ISV-használati esetek esetében, ahol a több bérlőről származó adatok feldolgozását egyetlen feladatban, külön bemenetek és kimenetek használatával végezheti el az egyes bérlők számára, így az egyes feladatokhoz tartozó önálló lekérdezéseket (például 20) is futtathatja. A feltételezés az egyes allekérdezések terhelése viszonylag kicsi. Ebben az esetben kövesse az alábbi lépéseket.
1.  Ebben az esetben ne használja a **particionálást** a lekérdezésben.
2.  Ha az Event hub-t használja, csökkentse a bemeneti partíciók számát a 2 legalacsonyabb értékre.
3.  Futtassa a lekérdezést 6 SU-val. Az egyes allekérdezések várható terhelésének megfelelően adja hozzá a lehető legtöbb allekérdezést, amíg a feladat el nem éri a rendszererőforrás-korlátokat. Ha ez történik, tekintse meg az [1. esetet](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) .
4.  Miután elindította a lekérdezett allekérdezési korlátot, kezdje el hozzáadni az allekérdezést egy új feladatokhoz. A független lekérdezések számának függvényében futtatandó feladatok számának viszonylag lineárisnak kell lennie, feltéve, hogy nem rendelkezik terhelési döntéssel. Ezután előre megtudhatja, hány 6 SU feladatot kell futtatnia a kiszolgálni kívánt bérlők számának függvényében.
5.  Ha az ilyen lekérdezésekkel való hivatkozással csatlakozik, egyesítse a bemeneti adatokat, mielőtt ugyanahhoz a hivatkozási adatokhoz csatlakozna. Ezt követően bontsa ki az eseményeket, ha szükséges. Ellenkező esetben az egyes hivatkozási adatok összekapcsolása a memóriában tárolja a hivatkozási adatokat, valószínűleg szükségtelenül felrobban a memóriahasználat.

> [!Note] 
> Hány bérlőt helyez el az egyes feladatokban?
> Ez a lekérdezési minta gyakran nagy számú allekérdezéssel rendelkezik, és nagyon nagy és összetett topológiát eredményez. Előfordulhat, hogy a feladatokhoz tartozó vezérlő nem tud kezelni egy ilyen nagy topológiát. Az általános szabály, hogy 1 SU feladathoz 40-bérlőt tart fenn, és 60-es bérlőt biztosít 3 SU és 6 SU feladathoz. Ha túllépi a vezérlő kapacitását, a feladatot a rendszer nem indítja el sikeresen.



## <a name="get-help"></a>Segítség kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/