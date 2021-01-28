---
title: Telepítse át az Azure HDInsight 3,6 Apache Storm HDInsight 4,0 Apache Spark
description: A Apache Storm számítási feladatok áttelepítésének különbségei és áttelepítése a Spark streaming vagy a Spark strukturált streaming számára.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: aa57c01558cfdcf069b17fad9e86f7640553dcfd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944791"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Telepítse át az Azure HDInsight 3,6 Apache Storm HDInsight 4,0 Apache Spark

Ez a dokumentum ismerteti, hogyan telepítheti át Apache Storm munkaterheléseket a 3,6-es HDInsight a HDInsight 4,0-es verzióra. A HDInsight 4,0 nem támogatja a Apache Storm-fürt típusát, és át kell térnie egy másik adatfolyam-adatplatformra. Két megfelelő lehetőség Apache Spark streaming és a Spark strukturált streaming. Ez a dokumentum ismerteti a platformok közötti különbségeket, és a munkafolyamatokat is javasolja Apache Storm számítási feladatok áttelepítéséhez.

## <a name="storm-migration-paths-in-hdinsight"></a>Storm áttelepítési útvonalak a HDInsight-ben

Ha a 3,6-es HDInsight-ről szeretne áttérni Apache Storm, több lehetőség közül választhat:

* Spark streaming on HDInsight 4,0
* Spark strukturált streaming on HDInsight 4,0
* Azure Stream Analytics

Ez a dokumentum útmutatást nyújt a Apache Storm a Spark streaming és a Spark strukturált streaming rendszerbe való áttelepítéséhez.

> [!div class="mx-imgBorder"]
> ![HDInsight Storm áttelepítési útvonala](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Összehasonlítás a Apache Storm és a Spark streaming, a Spark strukturált streaming között

Az Apache Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Egy alapszintű Storm-alkalmazás például garantálhatja a legalább egyszeri feldolgozást, a [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) pedig pontosan egyszeri feldolgozást tud biztosítani. A Spark streaming és a Spark strukturált streaming garantálja, hogy minden bemeneti esemény feldolgozása pontosan egyszer történik, még akkor is, ha a csomópont meghibásodik. A Storm olyan modellt használ, amely minden egyes eseményt feldolgoz, és a Micro batch-modellt a Trident használatával is használhatja. A Spark streaming és a Spark strukturált streaming Micro-Batch feldolgozási modellt biztosít.

|  |Storm |Spark-streamelés | Spark strukturált streaming|
|---|---|---|---|
|**Esemény-feldolgozási garancia**|Legalább egyszer <br> Pontosan egyszer (Trident) |[Pontosan egyszer](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Pontosan egyszer](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Feldolgozási modell**|Valós idejű <br> Micro batch (Trident) |Micro batch |Micro batch |
|**Esemény időpontjának támogatása**|[Igen](https://storm.apache.org/releases/2.0.0/Windowing.html)|No|[Igen](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Nyelvek**|Java stb.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark streaming vs Spark strukturált streaming

A Spark strukturált streaming a Spark streaminget (DStreams) váltja fel. A strukturált streaming továbbra is a fejlesztéseket és a karbantartást fogja kapni, a DStreams pedig csak karbantartási módban lesznek elérhetők. **Megjegyzés: a pont kiemeléséhez hivatkozásokra van szükség**. A strukturált adatfolyamok nem rendelkeznek annyi funkcióval, mint a DStreams az általa támogatott források és nyelők számára, ezért a követelmények kiértékelésével kiválaszthatja a megfelelő Spark stream-feldolgozási lehetőséget.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Streaming (Single Event) Processing vs Micro-Batch Processing

A Storm olyan modellt biztosít, amely minden egyes eseményt feldolgoz. Ez azt jelenti, hogy a rendszer minden bejövő rekordot feldolgoz, amint megérkeznek. A Spark streaming-alkalmazásoknak meg kell várniuk egy második töredékét az egyes mikro-köteg események összegyűjtéséhez, mielőtt elküldené a köteget a feldolgozásra. Ezzel szemben az eseményvezérelt alkalmazások azonnal feldolgozzák az egyes eseményeket. A Spark streaming késése általában néhány másodperc alatt van. A mikro-batch megközelítés előnyei hatékonyabb adatfeldolgozást és egyszerűbb összesített számításokat tesznek elérhetővé.

> [!div class="mx-imgBorder"]
> ![folyamatos átvitel és mikro-kötegelt feldolgozás](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Storm-architektúra és-összetevők

A Storm-topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az adatáramlás a gráf összetevői között zajlik. Minden összetevőbe egy vagy több stream érkezik be, valamint egy vagy több streamet sugároz.

|Összetevő |Leírás |
|---|---|
|Spout|Az adathalmazt egy topológiába hozza. Egy vagy több streamet sugároznak a topológiába.|
|Bolt|A kiöntő vagy más boltokból kibocsátott adatfolyamokat használja fel. A boltok opcionálisan streameket is sugározhatnak a topológiába. A boltok felelősek az adatok külső szolgáltatásokba vagy tárolókba, például HDFS-, Kafka- vagy HBase-tárolókba történő kiírásáért.|

> [!div class="mx-imgBorder"]
> ![Storm-összetevők interakciója](./media/migrate-storm-to-spark/apache-storm-components.png)

A Storm a következő három démonból áll, amelyek megőrzik a Storm-fürt működését.

|Démon |Description |
|---|---|
|Nimbus|A Hadoop JobTracker hasonlóan a kód a fürt körének terjesztése, valamint a feladatok gépekhez és a hibák figyeléséhez való hozzárendelésének feladata.|
|Zookeeper|A fürt koordinálásához használatos.|
|Felügyelő|Figyeli a géphez rendelt munkát, és elindítja és leállítja a munkavégző folyamatokat a Nimbus irányelvek alapján. Minden munkavégző folyamat végrehajtja a topológia egy részhalmazát. A felhasználó alkalmazás-logikája (kiöntő és bolt) itt fut.|

> [!div class="mx-imgBorder"]
> ![Nimbus, Zookeeper és felügyelő démonok](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark streaming-architektúra és-összetevők

A következő lépések összefoglalják, hogyan működnek együtt az összetevők a Spark Streamingben (DStreams) és a Spark strukturált Streamingben:

* A Spark streaming indításakor az illesztőprogram elindítja a feladatot a végrehajtóban.
* A végrehajtó adatfolyamot kap egy adatfolyam-adatforrásból.
* Amikor a végrehajtó megkapja az adatfolyamokat, a leosztja a streamet a blokkokba, és megtartja őket a memóriában.
* Az adatblokkokat más végrehajtóknak kell replikálni.
* A rendszer ezután a feldolgozott adattárolóban tárolja a feldolgozott adattárakat.

> [!div class="mx-imgBorder"]
> ![a Spark streaming elérési útja a kimenethez](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Spark streaming-(DStream-) munkafolyamat

Mivel minden egyes batch-intervallum eltelik, egy új RDD állít elő, amely az adott intervallum összes adatait tartalmazza. A RDD folyamatos készleteit egy DStream gyűjti. Ha például a Batch-intervallum egy másodperces, a DStream egy olyan köteget bocsát ki másodpercenként, amely egy RDD tartalmaz, amely tartalmazza az adott másodpercben betöltött összes adatot. A DStream feldolgozásakor a hőmérsékleti esemény a következő kötegek egyikében jelenik meg. A Spark streaming-alkalmazás dolgozza fel az eseményeket tartalmazó kötegeket, és végül az egyes RDD tárolt adategységeket is végrehajtja.

> [!div class="mx-imgBorder"]
> ![Spark streaming-feldolgozási kötegek](./media/migrate-storm-to-spark/spark-streaming-batches.png)

A Spark Streamingtel elérhető különböző átalakításokkal kapcsolatos részletekért lásd: [átalakítások a DStreams-on](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Spark strukturált streaming

A Spark strukturált streaming az adatstreamet olyan táblaként jelöli, amely nincs mélységben kötve. A tábla folyamatosan növekszik, ahogy az új adatmennyiség érkezik. Ezt a bemeneti táblázatot egy hosszan futó lekérdezés folyamatosan dolgozza fel, és az eredményeket egy kimeneti táblába küldi a rendszer.

A strukturált adatfolyamban az adatok beérkeznek a rendszerbe, és azonnal bekerülnek egy bemeneti táblába. Olyan lekérdezéseket írhat (a DataFrame és az adatkészlet API-k használatával), amelyek a bemeneti táblán műveleteket hajtanak végre.

A lekérdezés kimenete egy *Result (eredmény) táblázatot* eredményez, amely a lekérdezés eredményét tartalmazza. A külső adattár, például a kapcsolódó adatbázis eredményeinek táblázatában is megrajzolhat egy adatforrást.

Az adatok bemeneti táblából való feldolgozásának időzítését az aktiválási intervallum vezérli. Alapértelmezés szerint az trigger intervalluma nulla, így a strukturált adatfolyam a megérkezése után azonnal feldolgozza az adatfeldolgozást. A gyakorlatban ez azt jelenti, hogy amint a strukturált adatfolyam feldolgozása befejeződött az előző lekérdezés futtatásával, egy másik feldolgozást indít el az újonnan fogadott összes adattal. Az indítást beállíthatja úgy, hogy intervallumban fusson, hogy a folyamatos átviteli adatkötegek feldolgozása időalapú kötegekben történjen.

> [!div class="mx-imgBorder"]
> ![az adatfeldolgozás strukturált adatfolyamban](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![programozási modell strukturált folyamatos átvitelhez](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Általános áttelepítési folyamat

A Storm-ről Spark-ra történő ajánlott áttelepítési folyamat a következő kezdeti architektúrát feltételezi:

* A Kafka továbbítási adatforrásként szolgál
* A Kafka és a Storm ugyanazon a virtuális hálózaton van üzembe helyezve
* A Storm által feldolgozott adattárolók (például az Azure Storage vagy a Azure Data Lake Storage Gen2) egy adatfogadóba íródnak.

    > [!div class="mx-imgBorder"]
    > ![feltételezett aktuális környezet diagramja](./media/migrate-storm-to-spark/presumed-current-environment.png)

Ha az alkalmazást a Storm-ből az egyik Spark streaming API-ra szeretné áttelepíteni, tegye a következőket:

1. **Helyezzen üzembe egy új fürtöt.** Helyezzen üzembe egy új HDInsight 4,0 Spark-fürtöt ugyanabban a virtuális hálózatban, és végezze el a Spark streaming vagy a Spark strukturált streaming-alkalmazás üzembe helyezését, és tesztelje alaposan.

    > [!div class="mx-imgBorder"]
    > ![új Spark-telepítés a HDInsight-ben](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **A régi Storm-fürtön való fogyasztás leállítása.** A meglévő Storm-ben állítsa le az adatok átvitelét a streaming adatforrásból, és várjon, amíg az adatok befejeződik a cél fogadóba való írás.

    > [!div class="mx-imgBorder"]
    > ![az aktuális fürtön való felhasználás leállítása](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Az új Spark-fürtön való használat megkezdése.** Egy újonnan telepített HDInsight 4,0 Spark-fürtről származó adatfolyam-adatok indítása. Ebben az időben a folyamat a legújabb Kafka-eltoláson keresztül történik.

    > [!div class="mx-imgBorder"]
    > ![használat megkezdése az új fürtön](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Szükség szerint távolítsa el a régi fürtöt.** Ha a kapcsoló elkészült és megfelelően működik, távolítsa el a régi HDInsight 3,6 Storm-fürtöt szükség szerint.

    > [!div class="mx-imgBorder"]
    > ![szükség szerint távolítsa el a régi HDInsight-fürtöket](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Következő lépések

A Storm, a Spark streaming és a Spark strukturált streaming szolgáltatással kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [A Apache Spark streaming áttekintése](../spark/apache-spark-streaming-overview.md)
* [Strukturált streaming Apache Spark áttekintése](../spark/apache-spark-structured-streaming-overview.md)
