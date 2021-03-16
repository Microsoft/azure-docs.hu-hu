---
title: Machine Learning Studio (klasszikus) függvények méretezése Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan méretezhetők a Machine Learning Studio (klasszikus) függvényeket használó Stream Analytics feladatok a particionálás és a stream egységek konfigurálásával.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 1ee1411aba7724d76ed8626de9b8b038d02339dc
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574254"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>A Stream Analytics-feladatok skálázása Azure Machine Learning Studio (klasszikus) függvényekkel

> [!TIP]
> A jobb teljesítmény és megbízhatóság érdekében javasoljuk, hogy Azure Machine Learning Studio (klasszikus) UDF helyett [Azure Machine learning UDF](machine-learning-udf.md) használjon.

Ez a cikk a Azure Machine Learning Studio (klasszikus) függvényeket használó Azure Stream Analytics feladatok hatékony méretezését ismerteti. A Stream Analytics feladatok általános méretezésével kapcsolatos információkért tekintse meg a [feladatok skálázását](stream-analytics-scale-jobs.md)ismertető cikket.

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Mi az a Studio (klasszikus) függvény a Stream Analyticsban?

A Stream Analytics Machine Learning Studio (klasszikus) függvénye az Stream Analytics lekérdezési nyelvben, például egy normál függvény hívásához is használható. A színfalak mögött azonban ezek a függvények valójában a Studio (klasszikus) webszolgáltatás-kérések.

A Studio (klasszikus) webszolgáltatási kérelmek átviteli sebességét a "batching" több sorból is javíthatja ugyanabban a webszolgáltatás API-hívásban. Ezt a csoportosítást mini-batch-nek nevezzük. További információ: [Azure Machine learning Studio (klasszikus) webszolgáltatások](../machine-learning/classic/consume-web-services.md). A Studio (klasszikus) támogatása a Stream Analyticsban.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Stream Analytics-feladatok konfigurálása a Studio (klasszikus) függvények segítségével

A Stream Analytics feladat által használt Studio (klasszikus) függvényt a következő két paraméterrel konfigurálhatja:

* A Studio (klasszikus) függvény meghívásának batch-mérete.
* A Stream Analytics feladatokhoz kiépített folyamatos átviteli egységek (SUs) száma.

Az SUs megfelelő értékeinek meghatározásához döntse el, hogy szeretné-e optimalizálni a Stream Analytics feladatok késését vagy az egyes SU átviteli sebességét. Az SUs mindig hozzáadható egy feladatokhoz, így növelheti a jól particionált Stream Analytics lekérdezések átviteli sebességét. A további SUs megnövelheti a feladatok futtatásának költségeit.

Határozza meg a Stream Analyticsi feladatokhoz tartozó késési *toleranciát* . A köteg méretének növelése növeli a Studio (klasszikus) kérések késését és a Stream Analytics feladatokhoz tartozó késést.

A Batch méretének növelése lehetővé teszi, hogy a Stream Analytics feladatok **több eseményt** is feldolgozzanak **ugyanazzal** a Studio-(klasszikus) webszolgáltatás-kérésekkel. A Studio (klasszikus) webszolgáltatás késésének növekedése általában a Batch méretének növekedésével lineáris. 

Fontos figyelembe venni a Studio (klasszikus) webszolgáltatás leghatékonyabb batch-méretét bármilyen adott helyzetben. A webszolgáltatási kérelmek alapértelmezett mérete 1000. Ezt az alapértelmezett méretet a Stream Analytics [Stream Analytics REST API](/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics – REST API") vagy a [PowerShell-ügyfél](stream-analytics-monitor-and-manage-jobs-use-powershell.md)használatával módosíthatja.

Ha úgy döntött, hogy a Batch-méretet választotta, beállíthatja a folyamatos átviteli egységek számát azon események száma alapján, amelyeket a függvénynek másodpercenként kell feldolgoznia. További információ a folyamatos átviteli egységekről: [stream Analytics skálázási feladatok](stream-analytics-scale-jobs.md).

Minden 6 SUs 20 egyidejű kapcsolatot kap a Studio (klasszikus) webszolgáltatással. Azonban 1 SU feladat és 3 SU feladat 20 egyidejű kapcsolatot kap.  

Ha az alkalmazás másodpercenként 200 000 eseményt hoz létre, és a köteg mérete 1000, akkor az eredményül kapott webszolgáltatás késése 200 MS. Ez az arány azt jelenti, hogy minden kapcsolat öt kérelmet tesz elérhetővé a Studio (klasszikus) webszolgáltatás számára másodpercenként. A 20 kapcsolattal a Stream Analytics feladatokkal 20 000 eseményt dolgozhat fel 200 MS-és 100 000-eseményekbe egy másodperc alatt.

Az 200 000-események másodpercenkénti feldolgozásához a Stream Analytics feladatoknak 40 egyidejű kapcsolatokra van szükségük, amelyek a 12 SUs-hez tartoznak. Az alábbi ábra a Stream Analytics feladatokból a Studio (klasszikus) webszolgáltatás-végpontra irányuló kérelmeket mutatja be – minden 6 SUs esetében 20 egyidejű kapcsolat van a Studio (klasszikus) webszolgáltatással, max.

![Stream Analytics méretezése a Studio (klasszikus) függvényekben két feladatra példa](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Stream Analytics méretezése a Studio (klasszikus) függvényekben két feladatra példa")

Általánosságban: ***b** _ a Batch mérethez, _*_L_*_ a webszolgáltatás késéséhez a B köteg mérete ezredmásodpercben, a stream Analytics-feladatok átviteli sebessége (_ *_N_** SUs):

![Stream Analytics méretezése a Studio (klasszikus) függvények képlettel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics méretezése a Studio (klasszikus) függvények képlettel")

Az "egyidejű hívások maximális száma" a Studio (klasszikus) webszolgáltatás esetében is konfigurálható. Ajánlott ezt a paramétert a maximális értékre beállítani (jelenleg 200).

A beállítással kapcsolatos további információkért tekintse át a [Machine learning Studio (klasszikus) webszolgáltatások skálázási cikkét](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Példa – Hangulatelemzés
Az alábbi példa egy Stream Analytics feladatot tartalmaz az "a klasszikus", az [Stream Analytics Machine learning Studio (klasszikus) integrációs oktatóanyagban](stream-analytics-machine-learning-integration-tutorial.md)leírtaknak megfelelően.

A lekérdezés egy egyszerű, teljes particionált lekérdezés, amelyet az **érzelem** függvény követ, ahogy az az alábbi példában is látható:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vizsgáljuk meg a Stream Analytics feladatok létrehozásához szükséges konfigurációt, amely a tweetek elemzését a másodpercenként 10 000 tweetek alapján végzi.

Az 1 SU használatával a Stream Analytics-feladatok kezelni tudják a forgalmat? A művelet az alapértelmezett batch 1000-as mérettel lépést tarthat a bemenettel. A hangulatos Analysis Studio (klasszikus) webszolgáltatás alapértelmezett késése (a 1000-es alapértelmezett mérettel) nem hoz létre a késések másodpercenkénti számát.

A Stream Analytics-feladathoz tartozó **teljes** vagy végpontok közötti késés általában néhány másodperc. Tekintse meg részletesebben ezt a Stream Analytics feladatot, *különösen* a Studio (klasszikus) függvény hívásait. A Batch mérete 1000, a 10 000-es események átviteli sebessége körülbelül 10 kérelmet tesz a webszolgáltatásnak. Még egy SU esetében is elég egyidejű kapcsolat áll rendelkezésre a bemeneti forgalom befogadásához.

Ha a bemeneti esemény sebessége a 100x értékkel növekszik, akkor a Stream Analytics feladatnak másodpercenként 1 000 000 tweetet kell feldolgoznia. A megnövekedett méretezés két lehetőséggel valósítható meg:

1. Növelje a köteg méretét.
2. Particionálja a bemeneti adatfolyamot, hogy párhuzamosan dolgozza fel az eseményeket.

Az első lehetőséggel a feladatok **késése** növekszik.

A második lehetőséggel további SUs-t kell kiépítenie, hogy a rendszer több egyidejű Studio-(klasszikus) webszolgáltatás-kérelmet hozzon létre. Ez a nagyobb számú SUs, növeli a feladatok **költségeit**.

Nézzük meg a skálázást az egyes batch-méretek következő késési mértékének használatával:

| Késés | Köteg mérete |
| --- | --- |
| 200 MS | 1000 – eseményvezérelt kötegek vagy alul |
| 250 MS | 5 000 – eseményvezérelt kötegek |
| 300 MS | 10 000 – eseményvezérelt kötegek |
| 500 ms | 25 000 – eseményvezérelt kötegek |

1. Az első lehetőség használata (további SUs kiépítés **nélkül** ). A köteg mérete **25 000**-ra növelhető. A Batch méretének növelése lehetővé teszi, hogy a feladatok feldolgozzák a 1 000 000-es eseményeket 20 egyidejű kapcsolattal a Studio (klasszikus) webszolgáltatással (a hagyományosnál 500 ms-os késéssel). Így a Stream Analytics feladat további késése miatt a Studio (klasszikus) webszolgáltatásra irányuló kérések iránti kérések az **200 MS** -ról **500 MS**-ra növekednek. A Batch mérete azonban **nem** növelhető végtelenül, mert a Studio (klasszikus) webszolgáltatások esetében a kérések hasznos mérete 4 MB vagy kisebb, a webszolgáltatások pedig a 100-as művelet után időtúllépést igényelnek.
1. A második lehetőség használatával a köteg mérete 1000-kor marad, az 200-MS webszolgáltatás késése esetén pedig a webszolgáltatás 20 egyidejű kapcsolata képes lesz az 1000 * 20 * 5 esemény = 100 000 másodpercenkénti feldolgozására. Így a 1 000 000-események másodpercenkénti feldolgozásához a feladatoknak 60 SUs-re van szükségük. Az első lehetőséghez képest Stream Analytics a feladatok több webszolgáltatási batch-kérést is igénybe vennének, ami nagyobb költségeket eredményez.

Alább látható a különböző SUs-és batch-méretekhez tartozó Stream Analytics-feladatok átviteli sebességének táblázata (az események másodpercenkénti száma).

| köteg mérete (ML késleltetés) | 500 (200 MS) | 1 000 (200 MS) | 5 000 (250 MS) | 10 000 (300 MS) | 25 000 (500 MS) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2500 |5000 |20 000 |30 000 |50,000 |
| **3 SUs** |2500 |5000 |20 000 |30 000 |50,000 |
| **6 SUs** |2500 |5000 |20 000 |30 000 |50,000 |
| **12 SUs** |5000 |10,000 |40,000 |60.000 |100.000 |
| **18 SUs** |7500 |15 000 |60.000 |90,000 |150 000 |
| **24 SUs** |10,000 |20 000 |80,000 |120 000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300 000 |500 000 |

Most már jól megértette, hogy a Studio (klasszikus) függvények hogyan működnek Stream Analyticsban. Valószínűleg azt is tudomásul veszi, hogy Stream Analytics feladatok "lekéréses" adatai az adatforrásokból és az egyes "lekérések" az Stream Analytics feladathoz tartozó események kötegét adja vissza. Hogyan befolyásolja ez a lekérési modell a Studio (klasszikus) webszolgáltatás kérelmeit?

A Studio (klasszikus) függvényekhez beállított batch-méret általában nem osztható meg pontosan az egyes Stream Analytics feladatok "lekérése" által visszaadott események számával. Ebben az esetben a Studio (klasszikus) webszolgáltatás "részleges" kötegekkel van meghívva. A részleges kötegek használata elkerüli, hogy a lekéréses művelet során felmerülő további feladatok késése coalescing-eseményeknél.

## <a name="new-function-related-monitoring-metrics"></a>Új függvényekkel kapcsolatos figyelési metrikák
Egy Stream Analytics feladat figyelés területén három további függvényhez kapcsolódó metrika lett hozzáadva. Ezek a **függvények,** a **függvények** és a **sikertelen függvények kérései**, az alábbi ábrán látható módon.

![Stream Analytics méretezése a Studio (klasszikus) függvények Metrikái](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Stream Analytics méretezése a Studio (klasszikus) függvények Metrikái")

A a következőképpen van meghatározva:

**Function kérelmek**: a függvények kéréseinek száma.

**Function Events**: a függvény kéréseinek száma.

**Sikertelen függvények kérései**: a sikertelen függvények kéréseinek száma.

## <a name="key-takeaways"></a>Kulcs elvihető

Ha egy Stream Analytics feladatot Studio (klasszikus) függvényekkel szeretne méretezni, vegye figyelembe a következő tényezőket:

1. A bemeneti esemény díjszabása.
2. A futó Stream Analytics feladatokhoz (és így a Studio (klasszikus) webszolgáltatás-kérelmek batch-méretéhez) tartozó tolerált késés.
3. A kiépített Stream Analytics SUs és a Studio (klasszikus) webszolgáltatás kéréseinek száma (a további függvényekkel kapcsolatos költségek).

Példaként egy teljesen particionált Stream Analytics lekérdezést használtak. Ha összetettebb lekérdezésre van szükség, a [Microsoft Q&a Azure stream Analytics egy kérdés oldalát](/answers/topics/azure-stream-analytics.html) , amellyel további segítséget kaphat a stream Analytics csapattól.

## <a name="next-steps"></a>Következő lépések
A Stream Analyticsról további információt a következő témakörben talál:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)
