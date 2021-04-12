---
title: 'Oktatóanyag: Apache Spark streaming & Apache Kafka – Azure HDInsight'
description: Megtudhatja, hogyan használhatja az Apache Spark streamelést az adatok az Apache Kafkába való betöltéséhez, illetve az onnan való exportálásához. Ebben az oktatóanyagban az adatok továbbítása a Spark on HDInsight Jupyter Notebook használatával végezhető el.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 72c82e8f425b05dde37352225dd7167b089ba48a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868460"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Oktatóanyag: Az Apache Spark strukturált stream használata az Apache Kafkával a HDInsighton

Ez az oktatóanyag azt mutatja be, hogyan használhatók az [Apache Spark strukturált streamek](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) az Azure HDInsight lévő [Apache Kafkaekkel](./kafka/apache-kafka-introduction.md) való adatolvasásra és-írásra.

A Spark strukturált streaming egy Spark SQL-re épülő stream-feldolgozó motor. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Fürtök létrehozása Azure Resource Manager sablon használatával
> * Spark strukturált streaming használata a Kafka használatával

Ha elkészült a jelen dokumentumban leírt lépésekkel, ne felejtse el törölni a fürtöket a felesleges költségek elkerülése érdekében.

## <a name="prerequisites"></a>Előfeltételek

* jQ, parancssori JSON-processzor.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* A [Jupyter notebookok](https://jupyter.org/) és a Spark on HDInsight használatával való ismerete. További információ: [adatok betöltése és lekérdezések futtatása Apache Spark HDInsight-](spark/apache-spark-load-data-run-query.md) dokumentumon.

* A Scala programozási nyelv ismerete. Az oktatóanyagban használt kód Scala nyelven van megírva.

* A Kafka-témakörök létrehozásának ismerete. További információ: [Apache Kafka on HDInsight](kafka/apache-kafka-get-started.md) rövid útmutató dokumentum.

> [!IMPORTANT]  
> A dokumentum lépéseihez egy olyan Azure-erőforráscsoport szükséges, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> A kényelmes használat érdekében ez a dokumentum tartalmaz egy hivatkozást egy olyan sablonra, amellyel az összes szükséges Azure-erőforrás létrehozható.
>
> A virtuális hálózat HDInsight használatával kapcsolatos további információkért lásd a [virtuális hálózat megtervezése HDInsight](hdinsight-plan-virtual-network-deployment.md) dokumentumhoz című témakört.

## <a name="structured-streaming-with-apache-kafka"></a>Strukturált streaming Apache Kafka

A Spark strukturált stream egy, a Spark SQL-motorra épülő streamfeldolgozó rendszer. A strukturált adatfolyam használatakor az adatfolyam-lekérdezések ugyanúgy írhatók, mint a Batch-lekérdezések.

Az alábbi kódrészletek az adatok Kafkából való beolvasását és fájlban való tárolását mutatják be. Az első egy köteg-, míg a második egy streamelési művelet:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Mindkét kódrészlet a Kafkából olvassa be az adatokat, majd fájlba írja azokat. A két példa közötti különbségek:

| Batch | Streamelés |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Az adatfolyam-továbbítási művelet a szolgáltatást is használja `awaitTermination(30000)` , amely leállítja az adatfolyamot a 30 000 MS után.

A strukturált streamelés a Kafkával való használatához a projektnek függőségi viszonyban kell lennie az `org.apache.spark : spark-sql-kafka-0-10_2.11` csomaggal. A csomag verziójának egyeznie kell a Spark on HDInsight verziójával. A Spark 2.2.0 esetében (amely a HDInsight 3,6-ben érhető el) a különböző projekttípus függőségi adatait megtalálja a következő helyen: [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar) .

Az oktatóanyaghoz használt Jupyter Notebook a következő cella tölti be a csomag függőségét:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>A fürtök létrehozása

A HDInsight Apache Kafka nem biztosít hozzáférést a Kafka-közvetítők számára a nyilvános interneten keresztül. A Kafkát használó minden eszköznek ugyanabban az Azure virtuális hálózatban kell lennie. Ebben az oktatóanyagban a Kafka- és a Spark-fürtök is ugyanabban az Azure virtuális hálózatban szerepelnek.

Az alábbi ábra a Spark és a Kafka közötti kommunikáció áramlását mutatja be.

:::image type="content" source="./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png" alt-text="Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája" border="false":::

> [!NOTE]  
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Azure-beli virtuális hálózat, majd az abban lévő Kafka- és Spark-fürtök létrehozásához hajtsa végre a következő lépéseket:

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    A Azure Resource Manager sablon a következő helyen található: **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Ez a sablon a következő erőforrásokat hozza létre:

   * Egy Kafka on HDInsight 3.6-fürt.
   * Egy Spark 2.2.0 on HDInsight 3.6-fürt.
   * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket.

     > [!IMPORTANT]  
     > Az oktatóanyagban alkalmazott strukturált stream használatához a Spark 2.2.0 on HDInsight 3.6 szükséges. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

2. A következő információkkal töltheti ki a **Testreszabott sablon** szakaszban lévő bejegyzéseket:

    | Beállítás | Érték |
    | --- | --- |
    | Előfizetés | Az Azure-előfizetése |
    | Erőforráscsoport | Az erőforrásokat tartalmazó erőforráscsoport. |
    | Hely | Az az Azure-régió, amelyben az erőforrások létrejönnek. |
    | Spark-fürt neve | A Spark-fürt neve. Az első hat karakternek a Kafka-fürt nevétől eltérőnek kell lennie. |
    | Kafka-fürt neve | A Kafka-fürt neve. Az első hat karakternek a Spark-fürt nevétől eltérőnek kell lennie. |
    | Fürt bejelentkezési felhasználóneve | A fürtök rendszergazdai felhasználóneve. |
    | Fürt bejelentkezési jelszava | A fürtök rendszergazdai felhasználójának jelszava. |
    | SSH-felhasználónév | A fürtökhöz létrehozandó SSH-felhasználó. |
    | SSH-jelszó | Az SSH-felhasználó jelszava. |

    :::image type="content" source="./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png" alt-text="A testreszabott sablon képernyőképe":::

3. Olvassa el a **használati** feltételeket, majd válassza **az Elfogadom a fenti feltételeket és** kikötéseket lehetőséget.

4. Válassza a **Beszerzés** lehetőséget.

> [!NOTE]  
> A fürtök létrehozása 20 percig is eltarthat.

## <a name="use-spark-structured-streaming"></a>Spark strukturált streaming használata

Ez a példa bemutatja, hogyan használható a Spark strukturált Stream és a Kafka on HDInsight. A szolgáltatás a New York City által biztosított taxis utakon tárolt információk alapján működik.  A jegyzetfüzet által használt adatkészletből [2016 zöld taxis adat](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb)található.

1. A gazdagép adatainak összegyűjtése. A következő curl-és [jQ](https://stedolan.github.io/jq/) -parancsok használatával szerezheti be a Kafka-ZooKeeper és a közvetítő gazdagépeit. A parancsok Windows-parancssorhoz készültek, és más környezetekben kisebb eltérésekre lesz szükség. Cserélje le a `KafkaCluster` nevet a Kafka-fürt nevére, és `KafkaPassword` a fürt bejelentkezési jelszavára. Továbbá cserélje le a- `C:\HDI\jq-win64.exe` t a jQ-telepítés tényleges elérési útjára. Adja meg a parancsokat egy Windows-parancssorban, és mentse a kimenetet a későbbi lépésekben való használatra.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/jupyter` , ahol a a `CLUSTERNAME` fürt neve. Amikor a rendszer kéri, írja be a fürt létrehozásakor használt bejelentkezési (rendszergazdai) nevet és jelszót.

1. Jegyzetfüzet létrehozásához válassza az **új > Spark** elemet.

1. A Spark streaming rendelkezik a batchs szolgáltatással, ami azt jelenti, hogy az adatkötegek és a végrehajtók az adatkötegeken futnak. Ha a végrehajtó üresjárati időkorlátja kisebb, mint a köteg feldolgozásához szükséges idő, akkor a végrehajtók folyamatosan lesznek hozzáadva és eltávolítva. Ha a végrehajtók üresjárati időkorlátja nagyobb, mint a köteg időtartama, a végrehajtó soha nem lesz eltávolítva. Ezért **azt javasoljuk, hogy a Spark. dynamicAllocation. enabled beállítással tiltsa le a dinamikus foglalást a streaming-alkalmazások futtatásakor** .

    A notebook által használt csomagok betöltéséhez írja be az alábbi adatokat egy jegyzetfüzet-cellába. Futtassa a parancsot a **CTRL + ENTER billentyűkombinációval**.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Hozzon létre egy Kafka-témakört. Szerkessze az alábbi parancsot úgy, hogy az `YOUR_ZOOKEEPER_HOSTS` első lépésben kibontott Zookeeper-gazdagépi adatokat cseréli le. A témakör létrehozásához adja meg az Jupyter Notebook szerkesztett parancsát `tripdata` .

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. A taxis utakon tárolt adatkérések. Adja meg a parancsot a következő cellában a New York-i taxis utakon tárolt adattöltéshez. A rendszer betölti az adatokat egy dataframe, majd a dataframe megjeleníti a cella kimenetét.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString

    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)

    // Display the dataframe containing trip data
    taxiDF.show()
    ```

1. Adja meg a Kafka-közvetítő gazdagépek adatait. Cserélje le `YOUR_KAFKA_BROKER_HOSTS` a és a közvetítő gazdagépek adatait az 1. lépésben kinyert adatokra.  Adja meg a szerkesztett parancsot a következő Jupyter Notebook cellában.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Küldje el az adatgyűjtést a Kafka-nek. A következő parancsban a `vendorid` program a Kafka-üzenet kulcs értékeként használja a mezőt. A kulcsot a Kafka az adatparticionáláskor használja. Az összes mezőt a Kafka-üzenetben egy JSON-karakterlánc értékként tárolja a rendszer. Adja meg a következő parancsot a Jupyter-ben, hogy egy batch-lekérdezéssel mentse az adataikat a Kafka-ba.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Séma deklarálása. A következő parancs bemutatja, hogyan használhat sémát JSON-adatok a Kafka-ből való olvasásakor. Adja meg a parancsot a következő Jupyter-cellában.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)

    println("Schema declared")
    ```

1. Válassza az adatforrást, és indítsa el az adatfolyamot. A következő parancs azt mutatja be, hogyan lehet lekérdezni a Kafka adatait batch-lekérdezés használatával. Ezután írja meg az eredményeket a Spark-fürtön lévő HDFS. Ebben a példában a `select` lekéri az üzenet (érték) mezőt a Kafka-ből, és a sémát alkalmazza rá. Ezután a rendszer a HDFS (WASB vagy ADL) írja a parketta formátumát. Adja meg a parancsot a következő Jupyter-cellában.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. A következő Jupyter-cellában található parancs megadásával ellenőrizheti, hogy a fájlok létrejöttek-e. Felsorolja a könyvtárban található fájlokat `/example/batchtripdata` .

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. Az előző példában egy batch-lekérdezést használtunk, az alábbi parancs azt mutatja be, hogyan végezheti el ugyanezt egy adatfolyam-lekérdezés használatával. Adja meg a parancsot a következő Jupyter-cellában.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Futtassa a következő cellát annak ellenőrzéséhez, hogy a fájlokat az adatfolyam-lekérdezés írta-e be.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlése a társított HDInsight-fürtöt is törli. És az erőforráscsoporthoz társított egyéb erőforrások.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. A [Azure Portal](https://portal.azure.com/)bontsa ki a bal oldalon található menüt a szolgáltatások menüjének megnyitásához, majd válassza az __erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
>
> A Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja Apache Spark strukturált adatfolyamot. Adatok írása és olvasása Apache Kafkaról a HDInsight-on. A következő hivatkozásra kattintva megtudhatja, hogyan használhatja a Apache Stormt a Kafka használatával.

> [!div class="nextstepaction"]
> [Apache Storm használata a Apache Kafka](hdinsight-apache-storm-with-kafka.md)
