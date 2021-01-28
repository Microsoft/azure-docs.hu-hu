---
title: Egyéni Maven-csomagok használata a Jupyter a Sparkban – Azure HDInsight
description: Részletes útmutató a HDInsight Spark-fürtökkel elérhető Jupyter-jegyzetfüzetek egyéni Maven-csomagok használatához való konfigurálásához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: a3d787059345b5971d99ebd7a1e26a9135b61bed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930341"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Külső csomagok használata Jupyter notebookokkal Apache Spark-fürtökben a HDInsight-ben

Megtudhatja, hogyan konfigurálhat egy [Jupyter Notebookt](https://jupyter.org/) Apache Spark-fürtön a HDInsight olyan külső, Közösség által támogatott Apache **Maven** -csomagok használatára, amelyek nem szerepelnek a fürtben.

A [Maven-tárházban](https://search.maven.org/) megkeresheti a rendelkezésre álló csomagok teljes listáját. Lekérheti az egyéb forrásokból származó elérhető csomagok listáját is. A közösségi hozzájárulású csomagok teljes listája például a [Spark-csomagokban](https://spark-packages.org/)érhető el.

Ebből a cikkből megtudhatja, hogyan használhatja a [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot a Jupyter notebook.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információ: [adatok betöltése és lekérdezések futtatása Apache Spark a HDInsight-on](./apache-spark-load-data-run-query.md).

* A fürtök elsődleges tárolójának [URI-sémája](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ez az `wasb://` Azure Storage számára lenne `abfs://` Azure Data Lake Storage Gen2 vagy Azure Data Lake Storage Gen1 esetén `adl://` . Ha az Azure Storage vagy a Data Lake Storage Gen2 esetében engedélyezve van a biztonságos átvitel, az URI-t vagy a-t, illetve a `wasbs://` `abfss://` [biztonságos átvitelt](../../storage/common/storage-require-secure-transfer.md)is el kell látnia.

## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal

1. Navigáljon `https://CLUSTERNAME.azurehdinsight.net/jupyter` oda, ahol `CLUSTERNAME` a a Spark-fürt neve.

1. Hozzon létre új notebookot. Válassza az **új**, majd a **Spark** lehetőséget.

    ![Új Spark-Jupyter Notebook létrehozása](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Új Jupyter Notebook létrehozása")

1. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. Válassza ki a jegyzetfüzet nevét a tetején, és adjon meg egy rövid nevet.

    ![A notebook elnevezése](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "A notebook elnevezése")

1. A Magic használatával úgy `%%configure` konfigurálhatja a jegyzetfüzetet, hogy külső csomagot használjon. Külső csomagokat használó jegyzetfüzetekben ügyeljen arra, hogy az `%%configure` első kód cellájában hívja meg a magict. Ez biztosítja, hogy a kernel a csomag használatára legyen konfigurálva a munkamenet elindítása előtt.

    >[!IMPORTANT]  
    >Ha elfelejti a rendszermag konfigurálását az első cellában, használja a `%%configure` `-f` paramétert, de a rendszer újraindítja a munkamenetet, és az összes folyamat el fog veszni.

    | HDInsight verziója | Parancs |
    |-------------------|---------|
    | HDInsight 3,5 és HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |HDInsight 3,3 és HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. A fenti kódrészlet a Maven Central adattárában lévő külső csomaghoz tartozó Maven-koordinátákat várja. Ebben a kódrészletben `com.databricks:spark-csv_2.11:1.5.0` a **Spark-CSV** csomag Maven-koordinálása. A csomagok koordinátáit a következőképpen hozhatja létre.

    a. Keresse meg a csomagot a Maven-tárházban. Ebben a cikkben a [Spark-CSV-](https://mvnrepository.com/artifact/com.databricks/spark-csv)t használjuk.

    b. A tárházból Gyűjtse össze a **GroupID**, a **ArtifactId** és a **verzió** értékeit. Győződjön meg arról, hogy az összegyűjtött értékek megfelelnek a fürtnek. Ebben az esetben a Scala 2,11 és a Spark 1.5.0 csomagot használjuk, de előfordulhat, hogy a fürt megfelelő Scala vagy Spark verziójának különböző verzióit kell kiválasztania. A Scala verzióját a fürtön a `scala.util.Properties.versionString` Spark Jupyter kernelen vagy a Spark beküldésen futtatva találja meg. A Spark-verziót a fürtön a Jupyter notebookokon futtatva találja meg `sc.version` .

    ![Külső csomagok használata Jupyter Notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Külső csomagok használata Jupyter Notebook")

    c. Összefűzi a három értéket kettősponttal elválasztva (**:**).

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Futtassa a kód cellát a `%%configure` magicmal. Ezzel konfigurálja a mögöttes Livy-munkamenetet a megadott csomag használatára. A jegyzetfüzet következő celláiban már használhatja a csomagot az alább látható módon.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    A 3,4-es és az alábbi HDInsight az alábbi kódrészletet kell használnia.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Ezután az előző lépésben létrehozott dataframe származó adatok megtekintéséhez futtathatja a kódrészleteket, például alább látható.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Külső Python-csomagok használata Jupyter notebookokkal Apache Spark-fürtökön a HDInsight Linux rendszeren](apache-spark-python-package-installation.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
