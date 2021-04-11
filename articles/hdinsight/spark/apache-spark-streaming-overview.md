---
title: Spark streaming az Azure HDInsight
description: Apache Spark streaming-alkalmazások használata a HDInsight Spark-fürtökön.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: e8a9f771827b870f493d6b0d7590feee7fc52b20
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870245"
---
# <a name="overview-of-apache-spark-streaming"></a>A Apache Spark streaming áttekintése

[Apache Spark](https://spark.apache.org/) A streaming HDInsight Spark-fürtökön biztosít adatfolyam-feldolgozást. Garantáljuk, hogy minden bemeneti esemény feldolgozása pontosan egyszer történik, még akkor is, ha a csomópont meghibásodik. A Spark stream egy hosszan futó feladat, amely számos különböző forrásból fogad bemeneti adatokat, beleértve az Azure Event Hubst is. Továbbá: Azure IoT Hub, Apache Kafka, Apache Flume, Twitter, `ZeroMQ` , nyers TCP-szoftvercsatornák, vagy a Apache HADOOP fonál-fájlrendszerek figyelése. A kizárólag eseményvezérelt folyamatokkal ellentétben a Spark-adatfolyamok a bemeneti adatokat a Windowsba. Például egy 2 másodperces szelet, majd a Térkép, a csökkentés, a csatlakozás és a kinyerési műveletek használatával átalakítja az egyes kötegeket. A Spark stream ezután az átalakított adatot a fájlrendszer, az adatbázisok, az irányítópultok és a konzolba írja.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png" alt-text="Adatfolyam-feldolgozás a HDInsight és a Spark streaming segítségével" border="false":::

A Spark streaming-alkalmazásoknak meg kell várniuk egy második töredékét az egyes események összegyűjtéséhez, `micro-batch` mielőtt elküldené a köteget a feldolgozásra. Ezzel szemben az eseményvezérelt alkalmazások azonnal feldolgozzák az egyes eseményeket. A Spark streaming késése általában néhány másodperc alatt van. A mikro-batch megközelítés előnyei hatékonyabb adatfeldolgozást és egyszerűbb összesített számításokat tesznek elérhetővé.

## <a name="introducing-the-dstream"></a>A DStream bemutatása

A Spark streaming a beérkező adatstreamek folyamatos továbbítását jelenti egy DStream nevű *diszkretizált-adatfolyam* használatával. A DStream olyan bemeneti forrásokból is létrehozhatók, mint a Event Hubs vagy a Kafka. Vagy más DStream való átalakítások alkalmazásával.

A DStream egy absztrakciós réteget biztosít a nyers eseményekre vonatkozó adatmennyiségek felett.

Kezdje egyetlen eseménnyel, hogy egy hőmérsékletet Olvasson egy csatlakoztatott termosztátból. Ha ez az esemény megérkezik a Spark streaming-alkalmazásba, az eseményt megbízható módon tárolja a rendszer, ahol több csomóponton replikálódik. Ez a hibatűrés biztosítja, hogy egyetlen csomópont meghibásodása ne okozza az esemény elvesztését. A Spark Core olyan adatstruktúrát használ, amely a fürt több csomópontján keresztül osztja el az adatmennyiséget. Ahol az egyes csomópontok általában a legjobb teljesítmény érdekében fenntartják a saját adataikat a memóriában. Ezt az adatstruktúrát *rugalmasan elosztott adatkészletnek* (RDD) nevezzük.

Mindegyik RDD a *Batch-intervallum* nevű felhasználó által meghatározott időkereten összegyűjtött eseményeket jelöli. Mivel minden egyes batch-intervallum eltelik, egy új RDD állít elő, amely az adott intervallum összes adatait tartalmazza. A RDD folyamatos készletét egy DStream gyűjti. Ha például a Batch-intervallum egy másodperces, a DStream egy olyan köteget bocsát ki másodpercenként, amely egy RDD tartalmaz, amely tartalmazza az adott másodpercben betöltött összes adatot. A DStream feldolgozásakor a hőmérsékleti esemény a következő kötegek egyikében jelenik meg. A Spark streaming-alkalmazás dolgozza fel az eseményeket tartalmazó kötegeket, és végül az egyes RDD tárolt adategységeket is végrehajtja.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png" alt-text="Példa DStream hőmérsékleti eseményekkel" border="false":::

## <a name="structure-of-a-spark-streaming-application"></a>Spark streaming-alkalmazás szerkezete

A Spark streaming-alkalmazás egy hosszú ideig futó alkalmazás, amely beolvassa az adatok betöltését a forrásokból. Átalakításokat alkalmaz az adatfeldolgozásra, majd leküldi az adatvesztést egy vagy több célhelyre. A Spark streaming-alkalmazás szerkezete statikus résszel és dinamikus résszel rendelkezik. A statikus rész meghatározza, hogy az adatok honnan származnak, milyen feldolgozást kell végezni az adatokon. És hová kell mennek az eredmények. A dinamikus rész határozatlan ideig futtatja az alkalmazást, várakozás a leállítási jelre.

Például a következő egyszerű alkalmazás egy szöveges sort kap egy TCP-szoftvercsatornán, és megszámolja, hogy az egyes szavak hányszor jelennek meg.

### <a name="define-the-application"></a>Az alkalmazás megadása

Az alkalmazás logikai definíciója négy lépésből áll:

1. Hozzon létre egy StreamingContext.
2. Hozzon létre egy DStream a StreamingContext.
3. Átalakítások alkalmazása a DStream.
4. Az eredmények kimenete.

Ez a definíció statikus, és az alkalmazás futtatása előtt nem dolgozza fel a rendszer az összes adatfeldolgozást.

#### <a name="create-a-streamingcontext"></a>StreamingContext létrehozása

Hozzon létre egy StreamingContext a SparkContext, amely a fürtre mutat. StreamingContext létrehozásakor a köteg méretét másodpercben kell megadnia, például:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>DStream létrehozása

A StreamingContext-példánnyal hozzon létre egy bemeneti DStream a bemeneti forráshoz. Ebben az esetben az alkalmazás figyeli az új fájlok megjelenését az alapértelmezett csatolt tárolóban.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Átalakítások alkalmazása

A feldolgozás a DStream átalakítások alkalmazásával valósítható meg. Ez az alkalmazás egyszerre egy sort kap a fájlból, és az egyes sorokra bontja a szavakat. Ezután egy Térkép-csökkentési mintát használ az egyes szavak számának megszámlálásához.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Kimeneti eredmények

A kimeneti műveletek alkalmazásával leküldheti az átalakítás eredményeit a célhelyekre. Ebben az esetben a számításon keresztül futtatott egyes futtatások eredményét a konzol kimenetében kell kinyomtatni.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Indítsa el a folyamatos átviteli alkalmazást, és futtassa a parancsot a megszakítási jel fogadása előtt.

```
ssc.start()
ssc.awaitTermination()
```

További információ a Spark stream API-ról: [Apache Spark streaming programozási útmutató](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

A következő minta alkalmazás önálló, így egy [Jupyter notebook](apache-spark-jupyter-notebook-kernels.md)belül futtathatja. Ez a példa egy olyan DummySource-adatforrást hoz létre a osztályban, amely egy számláló értékét és az aktuális időt ezredmásodpercben, öt másodpercenként adja vissza. Az új StreamingContext-objektumnak 30 másodperces batch-intervalluma van. Minden alkalommal, amikor létrejön egy köteg, az adatfolyam-alkalmazás megvizsgálja a létrehozott RDD. Ezután átalakítja a RDD egy Spark-DataFrame, és létrehoz egy ideiglenes táblát a DataFrame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Várjon körülbelül 30 másodpercig a fenti alkalmazás elindítása után.  Ezután lekérdezheti a DataFrame rendszeres időközönként, hogy megjelenjenek-e a kötegben található aktuális értékek, például az SQL-lekérdezés használatával:

```sql
%%sql
SELECT * FROM demo_numbers
```

Az eredményül kapott kimenet a következő kimenethez hasonlít:

| érték | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Hat érték van, mivel a DummySource 5 másodpercenként hoz létre egy értéket, és az alkalmazás 30 másodpercenként bocsát ki egy köteget.

## <a name="sliding-windows"></a>Ablakok becsúszása

Ha egy adott időszakon belül összesíteni szeretné a DStream összesített számításait, például az utolsó két másodperc átlagos hőmérsékletének beszerzéséhez, használja a `sliding window` Spark streaminghez tartozó műveleteket. A csúszó ablak rendelkezik egy időtartammal (az ablak hosszával), valamint azt az intervallumot, amely alatt a rendszer kiértékeli az ablak tartalmát (a dia intervalluma).

A csúszó ablakok átfedésben lehetnek, például megadható egy két másodperc hosszúságú ablak, amely a diák másodpercenkénti számát jelzi. Ez a művelet azt jelenti, hogy minden alkalommal, amikor összesítési számítást végez, az ablak az előző ablak utolsó egy másodpercének adatait fogja tartalmazni. És minden új, a következő egy másodpercben lévő adattal.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png" alt-text="Példa a kezdeti időszakra hőmérsékleti eseményekkel" border="false":::

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png" alt-text="Példa a hőmérsékleti eseményekre a csúszó után" border="false":::

Az alábbi példa frissíti a DummySource használó kódot, hogy a kötegeket egy egyperces időtartammal és egy perces diához Gyűjtse.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Az első perc után 12 bejegyzés van – hat bejegyzés az ablakban gyűjtött két kötegből.

| érték | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

A Spark streaming API-ban elérhető csúszó ablak függvények közé tartozik az ablak, a countByWindow, a reduceByWindow és a countByValueAndWindow. A függvények részleteiért lásd: [átalakítások a DStreams-on](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ellenőrző pontok használata

A rugalmasság és a hibatűrés biztosításához a Spark streaming az ellenőrzőpontokon alapul, így biztosítva, hogy a stream feldolgozása zavartalan maradjon, még a csomópont meghibásodása előtt is. A Spark ellenőrzőpontokat hoz létre a tartós tároláshoz (Azure Storage vagy Data Lake Storage). Ezek az ellenőrzőpontok a folyamatos átviteli alkalmazások metaadatait, például a konfigurációt és az alkalmazás által meghatározott műveleteket tárolják. Továbbá minden olyan köteg, amely még nem lett feldolgozva, várólistára került. Az ellenőrzőpontok időnként a RDD található adatok mentését is magukban foglalják, hogy gyorsabban építsék újra az adatok állapotát a Spark által felügyelt RDD.

## <a name="deploying-spark-streaming-applications"></a>Spark streaming-alkalmazások üzembe helyezése

A Spark streaming-alkalmazásokat általában egy JAR-fájlba építjük. Ezután telepítse a Spark on HDInsight-ba a JAR-fájl az alapértelmezett csatolt tárolóba másolásával. Az alkalmazást a fürtön elérhető REST API-k használatával LIVY a POST művelettel. A bejegyzés törzse tartalmaz egy JSON-dokumentumot, amely megadja a JAR elérési útját. Valamint annak az osztálynak a neve, amelynek a fő metódusa meghatározza és futtatja a folyamatos átviteli alkalmazást, valamint opcionálisan a feladathoz tartozó erőforrás-követelményeket (például a végrehajtók, a memória és a magok számát). Emellett az alkalmazás kódjához szükséges konfigurációs beállítások is megadhatók.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png" alt-text="Spark streaming-alkalmazás üzembe helyezése" border="false":::

Az összes alkalmazás állapota a GET kérelemmel is ellenőrizhető egy LIVY-végponton. Végül a LIVY-végpontra vonatkozó TÖRLÉSi kérelem kibocsátásával leállíthatja a futó alkalmazást. A LIVY API-val kapcsolatos részletekért lásd: [távoli feladatok Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Következő lépések

* [Apache Spark-fürt létrehozása a HDInsight-ben](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark streaming programozási útmutató](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Strukturált streaming Apache Spark áttekintése](apache-spark-structured-streaming-overview.md)
