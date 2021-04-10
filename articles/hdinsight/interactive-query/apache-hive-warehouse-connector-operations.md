---
title: A kaptár Warehouse-összekötő által támogatott Apache Spark műveletek az Azure HDInsight
description: Ismerje meg a méhkas Warehouse-összekötő különböző képességeit az Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 1799aff8bff96d404ddcbefbf58a5f5014cdba6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871588"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>A kaptár Warehouse-összekötő által támogatott Apache Spark műveletek az Azure HDInsight

Ez a cikk a méhkas Warehouse-összekötő (ÜZEMELTETHETŐ WEBMAG) által támogatott Spark-alapú műveleteket mutatja be. Az alább látható összes példa a Apache Spark rendszerhéjon keresztül lesz végrehajtva.

## <a name="prerequisite"></a>Előfeltétel

Fejezze be a [kaptár Warehouse-összekötő telepítési](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) lépéseit.

## <a name="getting-started"></a>Első lépések

A Spark-Shell-munkamenet elindításához hajtsa végre a következő lépéseket:

1. A Apache Spark-fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből hajtsa végre a következő parancsot a `hive-warehouse-connector-assembly` verzió megjegyzéséhez:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Szerkessze az alábbi kódot a `hive-warehouse-connector-assembly` fent azonosított verzióval. Ezután hajtsa végre a parancsot a Spark Shell elindításához:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. A Spark-Shell elindítása után a rendszer elindíthatja a méhkas Warehouse Connector-példányt az alábbi parancsokkal:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Spark-DataFrames létrehozása a kaptár-lekérdezésekkel

A ÜZEMELTETHETŐ WEBMAG könyvtárat használó összes lekérdezés eredményét DataFrame adja vissza. Az alábbi példák bemutatják, hogyan hozhat létre alapszintű kaptár-lekérdezést.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

A lekérdezés eredménye Spark DataFrames, amely a Spark-függvénytárak, például a MLIB és a SparkSQL használatával használható.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark-DataFrames kiírása a struktúra tábláiba

A Spark nem támogatja natív módon az írást a kaptár felügyelt savas tábláiba. A ÜZEMELTETHETŐ WEBMAG használatával azonban bármilyen DataFrame kiírhat egy struktúra-táblába. Ezt a funkciót a következő példában tekintheti meg a munkahelyen:

1. Hozzon létre egy nevű táblát `sampletable_colorado` , és határozza meg az oszlopokat a következő parancs használatával:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Szűrje azt a táblázatot, `hivesampletable` ahol az oszlop `state` egyenlő `Colorado` . Ez a kaptár-lekérdezés a függvény használatával a kaptár táblában mentett Spark DataFrame ans SIS-t adja vissza `sampletable_colorado` `write` .

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Tekintse meg az eredményeket a következő paranccsal:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="méhkas-raktár összekötő a struktúra-táblázat megjelenítése" border="true":::


## <a name="structured-streaming-writes"></a>Strukturált adatfolyam-írások

A méhkas Warehouse-összekötő használatával a Spark streaming használatával írhat adatbevitelt a kaptár tábláiba.

> [!IMPORTANT]
> A strukturált adatfolyam-írások nem támogatottak az ESP engedélyezve Spark 4,0-fürtökben.

Az alábbi lépéseket követve betöltheti a 9999-es localhost porton lévő Spark streamből származó adatok adatait egy kaptár-táblába a használatával. Kas-tárház összekötője

1. A nyitott Spark-rendszerhéjból kezdjen el egy Spark streamet a következő paranccsal:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Hozzon létre adatforrást a létrehozott Spark streamhez a következő lépések végrehajtásával:
    1. Nyisson meg egy második SSH-munkamenetet ugyanazon a Spark-fürtön.
    1. A parancssorba írja be a következőt: `nc -lk 9999` . Ez a parancs a netcat segédprogramot használja az adatok parancssorból a megadott portra történő küldéséhez.

1. Térjen vissza az első SSH-munkamenethez, és hozzon létre egy új struktúra-táblázatot a folyamatos átviteli adattároláshoz. A Spark-shellben adja meg a következő parancsot:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Ezután írja be az adatfolyam-továbbítási adataikat az újonnan létrehozott táblába a következő parancs használatával:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > A `metastoreUri` és a `database` beállításokat a Apache Spark ismert hibája miatt manuálisan kell beállítani. További információ erről a hibáról: [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Térjen vissza a második SSH-munkamenethez, és adja meg a következő értékeket:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Térjen vissza az első SSH-munkamenethez, és jegyezze fel a rövid tevékenységet. Az alábbi parancs használatával tekintheti meg az adatfájlokat:

    ```scala
    hive.table("stream_table").show()
    ```

A **CTRL + C** billentyűkombinációval állítsa le a netcat a második SSH-munkamenetben. `:q`Az első SSH-munkamenetből való kilépéshez használja a Spark-shellt.

## <a name="next-steps"></a>Következő lépések

* [HWC-integráció az Apache Sparkkal és az Apache Hive-val](./apache-hive-warehouse-connector.md)
* [Az interaktív lekérdezés használata HDInsighttal](./apache-interactive-query-get-started.md)
* [HWC-integráció az Apache Zeppelinnel](./apache-hive-warehouse-connector-zeppelin.md)
