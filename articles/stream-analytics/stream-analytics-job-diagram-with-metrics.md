---
title: Adatvezérelt hibakeresés a Azure Stream Analyticsban
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure Stream Analytics feladatot a Azure Portal a feladatütemezés és a metrikák használatával.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020400"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Adatvezérelt hibakeresés a feladatábra használatával

A Azure Portal **figyelés** paneljén a feladatok diagramja segíthet a feladatok adatcsatornájának megjelenítésében. Az ábra a bemeneteket, kimeneteket és a lekérdezések lépéseit mutatja. A feladatábrával ellenőrizheti az egyes lépések metrikáit, és a problémák elhárítása során gyorsabban felmérheti az egyes hibák eredetét.

## <a name="using-the-job-diagram"></a>A feladatütemezés használata

A Azure Portal Stream Analytics feladatban a **támogatás + hibaelhárítás** területen válassza a **feladatütemezés** elemet:

![Feladathoz tartozó diagram metrikákkal – hely](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Válassza ki az egyes lekérdezési lépéseket a lekérdezés szerkesztési paneljén a megfelelő szakasz megjelenítéséhez. A lépéshez tartozó mérőszám-diagram az oldal alsó ablaktábláján jelenik meg.

![Feladathoz tartozó diagram metrikákkal – alapszintű feladat](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Az Azure Event Hubs-bemenet partícióinak megtekintéséhez válassza a **... lehetőséget.** Megjelenik egy helyi menü. A bemeneti egyesítést is láthatja.

![Feladathoz tartozó diagram metrikákkal – a partíció kibontása](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Ha csak egy partícióhoz tartozó metrikai diagramot szeretné látni, válassza a partíció csomópontot. A metrikák az oldal alján jelennek meg.

![Feladathoz tartozó diagram metrikákkal – további mérőszámok](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Az egyesítés metrikái diagramjának megtekintéséhez válassza ki az egyesítési csomópontot. A következő diagramon látható, hogy egyetlen esemény sem lett elvetve vagy módosítva.

![Feladathoz tartozó diagram metrikákkal – rács](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

A metrika értékének és időpontjának megtekintéséhez mutasson a diagramra.

![Feladathoz tartozó diagram metrikákkal – hover](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Hibák a metrikák használatával

A **QueryLastProcessedTime** metrika azt jelzi, hogy egy adott lépés mikor kapott adatokat. A topológia megkeresésével visszafelé dolgozhat a kimeneti processzoron, hogy megtudja, melyik lépés nem fogad adatokat. Ha egy lépés nem kap meg adatgyűjtést, ugorjon a lekérdezési lépésre. Ellenőrizze, hogy az előző lekérdezési lépésnek van-e időablaka, és ha a művelethez elegendő idő telt el, adja meg a kimeneti adatokat. (Vegye figyelembe, hogy az idő a Windows az órára van csattanva.)
 
Ha a fenti lekérdezési lépés egy bemeneti processzor, a bemeneti metrikák segítségével válaszolhat a következő megcélzó kérdésekre. Segíthetnek eldönteni, hogy egy adott adat beolvassa-e az adatokat a bemeneti forrásaiból. Ha a lekérdezés particionálva van, vizsgálja meg az összes partíciót.
 
### <a name="how-much-data-is-being-read"></a>Mennyibe kerül az adatolvasás?

*   A **InputEventsSourcesTotal** az olvasott adategységek száma. Például a Blobok száma.
*   A **InputEventsTotal** a beolvasott események száma. Ez a metrika partíciónként érhető el.
*   A **InputEventsInBytesTotal** az olvasott bájtok száma.
*   A **inputeventslastarrivaltime metrika** minden fogadott esemény várólistán lévő-idejével frissül.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Az idő halad előre? Ha a rendszer tényleges eseményeket olvas be, előfordulhat, hogy lemaradnak az eseményzáró jelek.

*   Az **InputEventsLastPunctuationTime** megadja, hogy mikor adtak ki írásjelek, az idő előrehaladása érdekében. Ha a rendszer nem bocsát ki központozást, az adatfolyam letiltható.
 
### <a name="are-there-any-errors-in-the-input"></a>Vannak hibák a bemenetben?

*   A **InputEventsEventDataNullTotal** olyan események száma, amelyek null adattal rendelkeznek.
*   A **InputEventsSerializerErrorsTotal** olyan események száma, amelyek helytelenül lettek deszerializálva.
*   A **InputEventsDegradedTotal** olyan események száma, amelyek nem a deszerializálás miatt voltak probléma.
 
### <a name="are-events-being-dropped-or-adjusted"></a>A rendszer eldobja vagy módosítja az eseményeket?

*   A **InputEventsEarlyTotal** az alkalmazás időbélyegét tartalmazó események száma a felső vízjel előtt.
*   A **InputEventsLateTotal** azon események száma, amelyekben az alkalmazás időbélyegzője a felső vízjel után van.
*   A **InputEventsDroppedBeforeApplicationStartTimeTotal** a feladatok kezdési ideje előtt eldobott események száma.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Az adatolvasás mögött vannak?

*   A **bemeneti események várakozó (összesen)** jelzi, hogy hány üzenetet kell olvasni a Event Hubs és az Azure IoT hub bemenetek esetében. Ha ez a szám nagyobb, mint 0, az azt jelenti, hogy a feladatainak nem sikerült gyorsan feldolgoznia az adatfeldolgozást. Ebben az esetben szükség lehet a folyamatos átviteli egységek számának növelésére és/vagy annak biztosítására, hogy a feladatok párhuzamosak legyenek. Erről a  [lekérdezési párhuzamos oldalon](./stream-analytics-parallelization.md)talál további információt. 


## <a name="get-help"></a>Segítség kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics vonatkozó kérdés oldalát](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Stream Analyticsba](stream-analytics-introduction.md)
* [Ismerkedés a Stream Analytics szolgáltatással](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok skálázása](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezés nyelvi referenciája](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)