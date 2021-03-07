---
title: Anomáliák észlelése Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure Stream Analytics és Azure Machine Learning együtt a rendellenességek észlelésére.
ms.service: stream-analytics
author: jseb225
ms.author: jeanb
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: ec37ea6cbb1c1c6693aab1f6855948d32b85e95b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441193"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomáliák észlelése Azure Stream Analytics

A felhőben és Azure IoT Edgeban egyaránt elérhető, Azure Stream Analytics beépített gépi tanuláson alapuló anomália-észlelési képességeket kínál, amelyek a két leggyakrabban előforduló rendellenesség figyelésére használhatók: ideiglenes és állandó. A **AnomalyDetection_SpikeAndDip** és a **AnomalyDetection_ChangePoint** függvények esetében közvetlenül a stream Analytics feladatban is elvégezheti a anomáliák észlelését.

A gépi tanulási modellek egységesen mintául szolgáló idősorozatot feltételeznek. Ha az idősorozat nem egységes, a rendellenesség észlelésének meghívása előtt beillesztheti az összesítési lépést egy elválasztó ablakba.

A gépi tanulási műveletek jelenleg nem támogatják a szezonális trendeket vagy a többváltozós korrelációkat.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomáliák észlelése a Machine learning használatával Azure Stream Analytics

A következő videó bemutatja, hogyan észlelhető egy anomália valós időben a Machine learning functions használatával Azure Stream Analyticsban. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Modell viselkedése

Általában a modell pontossága nagyobb, mint a csúszó ablakban lévő adatok. A megadott csúszó ablakban lévő adatok az adott időszakra vonatkozó normál tartományának részeként vannak kezelve. A modell csak az események előzményeit tekinti meg a csúszó ablakon annak vizsgálatához, hogy az aktuális esemény rendellenes-e. A csúszó ablak mozgatásakor a rendszer kizárja a régi értékeket a modell betanítása alapján.

A függvények úgy működnek, hogy az eddig látottak alapján egy bizonyos normálisat hoznak létre. A kiugró értékeket a meghatározott normál, megbízhatósági szinten való összehasonlítással azonosítjuk. Az ablak méretének a modell normál működéséhez szükséges minimális események alapján kell megjelennie, így ha anomália van, akkor felismerhetővé válik.

A modell válaszideje az előzmények méretével nő, mivel az összehasonlításhoz nagyobb számú múltbeli eseményre van szükség. Azt javasoljuk, hogy a jobb teljesítmény érdekében csak a szükséges számú eseményt foglalja bele.

Az idősorozatbeli rések a modell bizonyos időpontokban nem fogadó eseményeinek eredményét eredményezik. Ezt a helyzetet a Stream Analytics a imputálási Logic használatával kezeli. Az előzmények méretének, valamint az időtartamnak ugyanazt a csúszó időszakot kell használnia, hogy kiszámítsa azt az átlagos sebességet, amelyen az események várhatóan megérkeznek.

Az [itt](https://aka.ms/asaanomalygenerator) elérhető anomáliák generátora egy IOT hub különböző anomália-mintázatú adattal való megkötésére használható. Ezekkel a rendellenesség-észlelési funkciókkal egy ASA-feladattal lehet beolvasni az IOT-hubhoz, és észleli a rendellenességeket.

## <a name="spike-and-dip"></a>Spike és dip

Az idősorozat-adatfolyamban az ideiglenes anomáliák a tüskék és a dips néven ismertek. A tüskék és a dips a Machine Learning-alapú operátor, [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
)segítségével figyelhető meg.

![Példa a Spike és a DIP anomáliára](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Ugyanabban a tolóajtóban, ha egy második tüske kisebb, mint az első, a kisebb tüske számított pontszáma valószínűleg nem elég jelentős ahhoz képest, mint a megadott megbízhatósági szinten lévő első tüske pontszáma. Megpróbálhatja csökkenteni a modell megbízhatósági szintjét az ilyen anomáliák észlelése érdekében. Ha azonban túl sok riasztást szeretne kapni, nagyobb megbízhatósági intervallumot is használhat.

Az alábbi lekérdezés feltételezi, hogy egy esemény másodpercenként egy egységes bemeneti arányt mutat be egy 2 perces csúszó ablakban, amelynek előzménye 120 esemény. Az utolsó SELECT utasítás kinyeri és kiírja a pontszám és a rendellenesség állapotát 95%-os megbízhatósági szinttel.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Változási pont

Az idősorozat-adatfolyamban az állandó anomáliák módosulnak az esemény-adatfolyamban lévő értékek eloszlásában, például a szintek változásaiban és trendjeiben. Stream Analytics az ilyen anomáliák észlelése a Machine Learning-alapú [AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operátor használatával történik.

Az állandó változások a tüskéknél és a dipsnál jóval hosszabb ideig tartanak, és katasztrofális esemény (ek) et jelezhetnek. Az állandó módosítások általában nem láthatók a szabad szemmel, de a **AnomalyDetection_ChangePoint** operátorral észlelhetők.

Az alábbi ábrán egy példa látható a szint változására:

![Példa a szint változási rendellenességére](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Az alábbi ábrán egy példa látható a trend változására:

![Példa a trend változási rendellenességére](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Az alábbi lekérdezés feltételezi, hogy egy esemény másodpercenként egy egységes bemeneti sebességű egy 20 perces csúszó ablakban, amelynek előzmény mérete 1200 esemény. Az utolsó SELECT utasítás kinyeri és kiírja a pontszám és a rendellenesség állapotát 80%-os megbízhatósági szinttel.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Teljesítmény jellemzői

Ezeknek a modelleknek a teljesítménye az előzmények méretétől, az időtartamtól, az események betöltéstől, valamint attól függ, hogy a rendszer használja-e a függvény szintű particionálást. Ez a szakasz ezeket a konfigurációkat ismerteti, és mintákat biztosít a másodpercenkénti 1K-, 5K-és 10K-események betöltési arányának fenntartásához.

* **Előzmények mérete** – ezek a modellek lineárisan, az **Előzmények méretével** végezhetők el. Minél hosszabb az előzmények mérete, annál hosszabb lesz a modell egy új esemény kiértékeléséhez. Ennek az az oka, hogy a modellek összehasonlítják az új eseményt az előzmények pufferben lévő összes korábbi eseménysel.
* **Ablak időtartama** – az **ablak időtartamának** tükröznie kell, hogy mennyi időt vesz igénybe az előzmények mérete által meghatározott számú esemény fogadása. Az ablakban számos esemény nélkül Azure Stream Analytics a hiányzó értékeket. Ezért a CPU-felhasználás az előzmények méretének függvénye.
* **Esemény betöltése** – minél nagyobb az **esemény terhelése**, annál több munka van, amelyet a modellek végeznek, ami hatással van a CPU-felhasználásra. A feladatot zavaróan párhuzamosan lehet kibővíteni, feltételezve, hogy az üzleti logika több bemeneti partíció használatát teszi lehetővé.
* **Függvény szintű particionálás**  -  A **függvények szintjének particionálását** ```PARTITION BY``` az anomáliák észlelése függvény hívásával végezheti el. Ez a particionálási típus felveszi a terhelést, mivel az állapotot egyszerre több modell esetében is fenn kell tartani. A függvények szintjének particionálását olyan forgatókönyvek használják, mint az eszközök szintjének particionálás.

### <a name="relationship"></a>Kapcsolat
Az előzmények mérete, az ablak időtartama és az összes esemény terhelése a következő módon kapcsolódik:

windowDuration (MS) = 1000 * historySize/(összes bemeneti esemény másodpercenként/bemeneti partíciók száma)

A függvény deviceId szerinti particionálásakor adja hozzá a "PARTITION BY deviceId" kifejezést a rendellenesség-észlelési függvény hívásához.

### <a name="observations"></a>Észrevételeket
A következő táblázat a nem particionált esetekhez tartozó egyetlen csomópont (6 SU) átviteli sebességét tartalmazza:

| Előzmények mérete (események)    | Ablak időtartama (MS) | Összes bemeneti esemény másodpercenként |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1 650 |
| 6.000 | 10 910 | 1100 |

A következő táblázat a particionált eset egyetlen csomópontjának (6 SU) átviteli sebességét tartalmazza:

| Előzmények mérete (események) | Ablak időtartama (MS) | Összes bemeneti esemény másodpercenként | Eszközök száma |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1 091 | 1100 | 10 |
| 600 | 10 910 | 1100 | 10 |
| 6.000 | 218 182 | <550 | 10 |
| 60 | 21 819 | 550 | 100 |
| 600 | 218 182 | 550 | 100 |
| 6.000 | 2 181 819 | <550 | 100 |

A fentiekben nem particionált konfigurációk futtatásához használandó mintakód az Azure-minták [streaming skáláján](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) található. A kód olyan stream Analytics-feladatot hoz létre, amely nem rendelkezik működési szintű particionálással, ami az Event hub bemenetként és kimenetként való használatát is felhasználja. A bemeneti terhelés a tesztelési ügyfelek használatával jön létre. Minden bemeneti esemény egy 1KB JSON-dokumentum. Az események a JSON-adatokat küldő IoT-eszközt szimulálják (legfeljebb 1K-eszközök esetén). Az előzmények mérete, az ablak időtartama és az események teljes terhelése két bemeneti partíción keresztül változhat.

> [!Note]
> A pontosabb becslés érdekében szabja testre a mintákat úgy, hogy illeszkedjenek a forgatókönyvhöz.

### <a name="identifying-bottlenecks"></a>Szűk keresztmetszetek azonosítása
A Azure Stream Analytics feladatok mérőszámok paneljén azonosíthatja a folyamat szűk keresztmetszeteit. Tekintse át a **bemeneti/kimeneti eseményeket** az átviteli sebesség és a "küszöbértékek [késleltetése"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) vagy a **várakozó események** között, és ellenőrizze, hogy a feladatban szerepel-e a bemeneti sebesség. Az Event hub mérőszámai esetében keresse meg a **szabályozott kérelmeket** , és ennek megfelelően módosítsa a küszöbértékeket. Cosmos DB metrikák esetében tekintse át a **maximálisan felhasznált ru/s** értékeit az átviteli sebesség alatt, hogy a partíciós kulcsok tartománya egységesen legyen felhasználva. Az Azure SQL DB esetében figyelje a **log IO** és a **CPU**-t.

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)