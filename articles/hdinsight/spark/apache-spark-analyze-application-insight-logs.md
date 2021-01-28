---
title: Alkalmazás-betekintési naplók elemzése a Spark-Azure HDInsight
description: Ismerje meg, hogyan exportálhatja az Application Insight-naplókat a blob Storage-ba, majd elemezheti a naplókat a Spark on HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: fbca4b23b6fc414727ea1b5a4012d2847d6b8d78
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940479"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Application Insights telemetria-naplók elemzése a HDInsight Apache Spark

Megtudhatja, hogyan használhatja a [Apache Spark](https://spark.apache.org/) on HDInsight az alkalmazás Insight telemetria-adatainak elemzéséhez.

A [Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) egy elemzési szolgáltatás, amely figyeli a webalkalmazásait. A Application Insights által generált telemetria-adatkészletek exportálhatók az Azure Storage szolgáltatásba. Ha az Azure Storage-ban található az adattároló, a HDInsight használatával elemezheti azt.

## <a name="prerequisites"></a>Előfeltételek

* Application Insights használatára konfigurált alkalmazás.

* Linux-alapú HDInsight-fürt létrehozásának ismerete. További információ: [Create Apache Spark on HDInsight](apache-spark-jupyter-spark-sql.md).

* Egy webböngésző.

A következő erőforrásokat használták a dokumentum fejlesztése és tesztelése során:

* Application Insights telemetria az [ Application Insights használatára konfiguráltNode.js webalkalmazás](../../azure-monitor/app/nodejs.md)használatával generálták.

* A Linux-alapú Spark on HDInsight-fürt 3,5-es verziója az adatelemzésre szolgál.

## <a name="architecture-and-planning"></a>Architektúra és tervezés

A következő ábra a példa szolgáltatási architektúráját szemlélteti:

![Application Insightsról a blob Storage-ba áramló adatok, majd a Spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Azure Storage

A Application Insights konfigurálható úgy, hogy folyamatosan exportálja a telemetria-adatokat a blobokra. A HDInsight ezután olvasni tudják a blobokban tárolt adatfájlokat. Vannak azonban néhány követelmény, amelyeket követnie kell:

* **Hely**: Ha a Storage-fiók és a HDInsight különböző helyen vannak, akkor ez megnövelheti a késést. Emellett növeli a költségeket is, mivel a kimenő költségek a régiók közötti adatforgalomra vonatkoznak.

    > [!WARNING]  
    > A HDInsight eltérő helyen lévő Storage-fiók használata nem támogatott.

* **Blob típusa**: a HDInsight csak a blokk blobokat támogatja. Application Insights alapértelmezett értéke a blokk Blobok használata, ezért a HDInsight alapértelmezés szerint működnie kell.

A tárolók meglévő fürthöz való hozzáadásával kapcsolatos információkért lásd a [további Storage-fiókok hozzáadása](../hdinsight-hadoop-add-storage.md) dokumentumot.

### <a name="data-schema"></a>Adatséma

A Application Insights a blobokra exportált telemetria adatformátumra vonatkozó adatokat biztosít az [adatmodell exportálásához](../../azure-monitor/app/export-data-model.md) . A jelen dokumentumban leírt lépések a Spark SQL használatával működnek együtt az adatkezeléssel. A Spark SQL automatikusan létrehozhat sémát a Application Insights által naplózott JSON-adatstruktúra számára.

## <a name="export-telemetry-data"></a>Telemetria-adatexportálás

Kövesse a [folyamatos exportálás konfigurálásának](../../azure-monitor/app/export-telemetry.md) lépéseit a Application Insights konfigurálásához a telemetria-adatok Azure Storage-blobba való exportálásához.

## <a name="configure-hdinsight-to-access-the-data"></a>HDInsight konfigurálása az adateléréshez

Ha HDInsight-fürtöt hoz létre, adja hozzá a Storage-fiókot a fürt létrehozása során.

Az Azure Storage-fiók meglévő fürthöz való hozzáadásához használja a [további Storage-fiókok hozzáadása](../hdinsight-hadoop-add-storage.md) dokumentum információit.

## <a name="analyze-the-data-pyspark"></a>Az adatgyűjtés elemzése: PySpark

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/jupyter` oda, ahol a CLUSTERNAME a fürt neve.

2. A Jupyter lap jobb felső sarkában válassza az **új**, majd a **PySpark** lehetőséget. Megnyílik egy új, Python-alapú Jupyter Notebookt tartalmazó böngésző lap.

3. Az oldal első mezőjében ( **cella** neve) írja be a következő szöveget:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ez a kód úgy konfigurálja a Sparkot, hogy a bemeneti adatokhoz való rekurzív módon hozzáférhessen a címtár struktúrájához. Application Insights telemetria a következőhöz hasonló címtár-struktúrába van naplózva: `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. A kód futtatásához használja a **SHIFT + ENTER** billentyűkombinációt. A cella bal oldalán egy " \* " szöveg jelenik meg a zárójelek között, hogy jelezze, hogy a cellában lévő kód végrehajtása folyamatban van. Ha a művelet befejeződött, a " \* " szám módosul, és az alábbi szöveghez hasonló kimenet jelenik meg a cella alatt:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Az első egy új cella jön létre. Adja meg a következő szöveget az új cellában. Cserélje le az és az attribútumot `CONTAINER` `STORAGEACCOUNT` Az Azure Storage-fiók nevére és a blob-tároló nevére, amely Application Insights-adathalmazt tartalmaz.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    A cella végrehajtásához használja a **SHIFT + ENTER** billentyűkombinációt. A következő szöveghez hasonló eredmény jelenik meg:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    A visszaadott wasbs útvonal a Application Insights telemetria-adatmennyiség helye. Módosítsa a `hdfs dfs -ls` cella vonalát a visszaadott wasbs elérési út használatára, majd a **SHIFT + ENTER** billentyűkombinációval futtassa újra a cellát. Ezúttal az eredményeknek meg kell jelennie a telemetria tartalmazó címtárakban.

   > [!NOTE]  
   > Az ebben a szakaszban szereplő lépések hátralévő részében a `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` könyvtárat használták. A címtár szerkezete eltérő lehet.

6. A következő cellában adja meg a következő kódot: cserélje le az `WASB_PATH` előző lépésben megadott elérési útra.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód egy dataframe hoz létre a folyamatos exportálási folyamat által exportált JSON-fájlokból. A cella futtatásához használja a **SHIFT + ENTER** billentyűkombinációt.
7. A következő cellában írja be és futtassa a következőt a JSON-fájlokhoz létrehozott séma megtekintéséhez:

   ```python
   jsonData.printSchema()
   ```

    Az egyes telemetria sémája eltér. A következő példa a webes kérelmekhez létrehozott séma (az alkönyvtárban tárolt adattartalom `Requests` ):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. A következő paranccsal regisztrálja a dataframe ideiglenes táblaként, és futtasson lekérdezést az adatkezeléshez:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Ez a lekérdezés az első 20 rekord városának adatait adja vissza, ahol a Context. location. City nem null értékű.

   > [!NOTE]  
   > A környezeti struktúra a Application Insights által naplózott összes telemetria megtalálható. A város elem nem tölthető fel a naplókban. A sémával azonosíthatja azokat a más elemeket, amelyeket a naplók adatait tartalmazó lekérdezéshez használhat.

    Ez a lekérdezés az alábbi szöveghez hasonló adatokat ad vissza:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>Az adatelemzés: Scala

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/jupyter` oda, ahol a CLUSTERNAME a fürt neve.

2. A Jupyter lap jobb felső sarkában válassza az **új**, majd a **Scala** lehetőséget. Megjelenik egy új, a Scala-alapú Jupyter Notebook tartalmazó böngésző lap.

3. Az oldal első mezőjében ( **cella** neve) írja be a következő szöveget:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ez a kód úgy konfigurálja a Sparkot, hogy a bemeneti adatokhoz való rekurzív módon hozzáférhessen a címtár struktúrájához. Application Insights telemetria a következőhöz hasonló címtár-struktúrába van naplózva: `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. A kód futtatásához használja a **SHIFT + ENTER** billentyűkombinációt. A cella bal oldalán egy " \* " szöveg jelenik meg a zárójelek között, hogy jelezze, hogy a cellában lévő kód végrehajtása folyamatban van. Ha a művelet befejeződött, a " \* " szám módosul, és az alábbi szöveghez hasonló kimenet jelenik meg a cella alatt:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Az első egy új cella jön létre. Adja meg a következő szöveget az új cellában. A és az helyére írja be `CONTAINER` `STORAGEACCOUNT` Az Azure Storage-fiók nevét és a blob-tároló nevét, amely Application Insights naplókat tartalmaz.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    A cella végrehajtásához használja a **SHIFT + ENTER** billentyűkombinációt. A következő szöveghez hasonló eredmény jelenik meg:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    A visszaadott wasbs útvonal a Application Insights telemetria-adatmennyiség helye. Módosítsa a `hdfs dfs -ls` cella vonalát a visszaadott wasbs elérési út használatára, majd a **SHIFT + ENTER** billentyűkombinációval futtassa újra a cellát. Ezúttal az eredményeknek meg kell jelennie a telemetria tartalmazó címtárakban.

   > [!NOTE]  
   > Az ebben a szakaszban szereplő lépések hátralévő részében a `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` könyvtárat használták. Előfordulhat, hogy ez a könyvtár nem létezik, kivéve, ha a telemetria-adatai egy webalkalmazáshoz készültek.

6. A következő cellában adja meg a következő kódot: cserélje le az `WASB\_PATH` előző lépésben megadott elérési útra.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód egy dataframe hoz létre a folyamatos exportálási folyamat által exportált JSON-fájlokból. A cella futtatásához használja a **SHIFT + ENTER** billentyűkombinációt.

7. A következő cellában írja be és futtassa a következőt a JSON-fájlokhoz létrehozott séma megtekintéséhez:

   ```scala
   jsonData.printSchema
   ```

    Az egyes telemetria sémája eltér. A következő példa a webes kérelmekhez létrehozott séma (az alkönyvtárban tárolt adattartalom `Requests` ):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. A következő paranccsal regisztrálja a dataframe ideiglenes táblaként, és futtasson lekérdezést az adatkezeléshez:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Ez a lekérdezés az első 20 rekord városának adatait adja vissza, ahol a Context. location. City nem null értékű.

   > [!NOTE]  
   > A környezeti struktúra a Application Insights által naplózott összes telemetria megtalálható. A város elem nem tölthető fel a naplókban. A sémával azonosíthatja azokat a más elemeket, amelyeket a naplók adatait tartalmazó lekérdezéshez használhat.

    Ez a lekérdezés az alábbi szöveghez hasonló adatokat ad vissza:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>Következő lépések

Ha további példákat szeretne használni a Apache Spark használatára az Azure-beli adatkezelési és-szolgáltatásokkal kapcsolatban, tekintse meg a következő dokumentumokat:

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

A Spark-alkalmazások létrehozásával és futtatásával kapcsolatos információkért tekintse meg a következő dokumentumokat:

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)
