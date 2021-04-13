---
title: A 2. Apache Spark csatlakoztatása a Azure Cosmos DB
description: Ismerje meg a Azure Cosmos DB Spark 2 OLTP-összekötőt, amely lehetővé teszi a Apache Spark csatlakoztatását Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 9d4a1a7d359bac83159d0e5ca4bddfc5935394a5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364260"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-v2-to-azure-cosmos-db-oltp-connector"></a>Az big data felgyorsítása az OLTP Apache Spark 2-es Azure Cosmos DB használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Spark [v2-feladatokat](https://spark.apache.org/) futtathat a Azure Cosmos DB Spark 2 OLTP-összekötővel Cosmos DB adatokkal. A Cosmos használható kötegelt és streamfeldolgozáshoz, valamint kiszolgáló rétegként a kis késésű hozzáféréshez.

Az összekötőt használhatja a Azure Databricks [vagy](https://azure.microsoft.com/services/databricks) [a Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)amelyek felügyelt Spark-fürtöt biztosítanak az Azure-ban. Az alábbi táblázat a támogatott Spark-verziókat mutatja be.

| Összetevő | Verzió |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x és 2.1.x |
| Scala | 2,11 |
| Azure Databricks-verzió | > 3.4 |

> [!WARNING]
> Ez az összekötő támogatja a központi (SQL) API-Azure Cosmos DB.
> A Cosmos DB MongoDB API-hoz használja a [MongoDB Spark-összekötőt.](https://docs.mongodb.com/spark-connector/master/)
> A Cosmos DB Cassandra API a [Cassandra Spark-összekötőt használja.](https://github.com/datastax/spark-cassandra-connector)

> [!IMPORTANT]
> A Azure Cosmos DB Spark OLTP-összekötő jelenleg nem támogatott a [kiszolgáló nélküli fiókokon.](serverless.md) Ez a kiszolgáló nélküli ajánlat általánosan elérhetővé válnak.

## <a name="quickstart"></a>Gyorsútmutató

* A Java [SDK](./create-sql-api-java.md) első lépéseit követve állítson be egy Cosmos DB fiókot, és töltse fel az adatokat.
* Kövesse a Azure Databricks [első lépéseit](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) egy új Azure Databricks és fürt beállítását.
* Most már létrehozhat új jegyzetfüzeteket, és importálhatja a Cosmos DB összekötő könyvtárát. A [munkaterület beállításával kapcsolatos Cosmos DB az](#bk_working_with_connector) Összekötő használatával.
* A következő szakasz az összekötő használatával való olvasásra és írásra használható kódrészleteket tartalmazza.

### <a name="batch-reads-from-cosmos-db"></a>Kötegelt beolvassa a Cosmos DB

Az alábbi kódrészlet bemutatja, hogyan hozhat létre Spark DataFrame-et a PySpark-Cosmos DB való olvasáshoz.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

És ugyanez a kódrészlet a Scala-ban:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Kötegelt írások a Cosmos DB

Az alábbi kódrészlet bemutatja, hogyan írhat adatkeretet a PySparkban Cosmos DB adatkeretek írására.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

És ugyanez a kódrészlet a Scala-ban:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streamelési olvasások Cosmos DB

Az alábbi kódrészlet bemutatja, hogyan csatlakozhat a változáscsatornához, és hogyan olvashat Azure Cosmos DB a változáscsatornából.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
És ugyanez a kódrészlet a Scala-ban:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Írások streamelése Cosmos DB

Az alábbi kódrészlet bemutatja, hogyan írhat adatkeretet a PySparkban Cosmos DB adatkeretek írására.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

És ugyanez a kódrészlet a Scala-ban:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
További kódrészletek és teljes minták: [Jupyter.](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Az összekötővel való munka

Az összekötőt a GitHubról is felépítheti a forrásból, vagy letöltheti az uber jar-fájlokat a Mavenből az alábbi hivatkozásokon.

| Spark | Scala | Legújabb verzió |
|---|---|---|
| 2.4.0 | 2,11 | [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks-jegyzetfüzetek használata

Hozzon létre egy kódtárat a Databricks-munkaterület használatával a Azure Databricks Útmutató > [Az Azure Cosmos DB Spark-összekötő használata](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> A **Use the Azure Cosmos DB Spark Connector (A Spark-összekötő** használata) lap jelenleg nem naprakész. Ahelyett, hogy letölti a hat különálló JAR-t hat különböző kódtárba, letöltheti az uber jar-t a Mavenből az [azure-cosmosdb-spark_lkg_version,](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) és telepítheti ezt az egy jar-/kódtárat.
> 

### <a name="using-spark-cli"></a>A spark-cli használata

Ha a spark-cli (azaz , , ) használatával használja az összekötőt, használhatja a paramétert az összekötő `spark-shell` `pyspark` `spark-submit` `--packages` [Maven-koordinátáival.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter-notebookok használata

Ha HDInsighton Jupyter Notebook használ, a spark-magic cellával megadhatja az összekötő `%%configure` Maven-koordinátáit.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Vegye figyelembe, hogy a belefoglalása csak az összekötő, a Apache Spark és a Livy közötti lehetséges `spark.jars.excludes` ütközések eltávolítására vonatkozik.

### <a name="build-the-connector"></a>Az összekötő összeállítása

Ez az összekötő-projekt jelenleg a következőt használja a függőségek nélküli `maven` buildhez:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>A mintáinkkal való munka

A [Cosmos DB Spark GitHub-adattárban](https://github.com/Azure/azure-cosmosdb-spark) az alábbi mintajegyzetfüzetek és szkriptek érhetők el.

* **On-Time Flight Performance with Spark and Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)html: Connect Spark to Cosmos DB using HDInsight Jupyter Notebook service to showcase Spark SQL, GraphFrames, and predicting flight  |  [](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)delays using ML pipelines.
* **Twitter-forrás Apache Spark és Azure Cosmos DB változáscsatornával:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Diagramok Apache Spark lekérdezése Cosmos DB** használatával: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Csatlakozás Azure Databricks a Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** `azure-cosmosdb-spark` használatával.  Az itt hivatkozott Azure Databricks [On-Time Flight Performance](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)jegyzetfüzet egy új verziója.
* **[Lambda architektúra Azure Cosmos DB és a HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** használatával: A big data-folyamatok fenntartásával kapcsolatos üzemeltetési többletterhelés csökkenthető a Cosmos DB És a Spark használatával.

## <a name="more-information"></a>További információ

A wiki további információkat tartalmaz, `azure-cosmosdb-spark` [többek](https://github.com/Azure/azure-cosmosdb-spark/wiki) között a következőket:

* [Azure Cosmos DB Spark-összekötő felhasználói útmutatója](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Aggregációk – példák](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfigurálás és beállítás

* [Spark-összekötő konfigurálása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark-Cosmos DB összekötő beállítása](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (folyamatban)
* [Direct Power BI konfigurálása Azure Cosmos DB hdi Apache Spark n keresztüli lekérdezéshez](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Hibaelhárítás

* [Az Cosmos DB összesítések használata](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Ismert problémák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Teljesítmény

* [Teljesítménnyel kapcsolatos tippek](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Lekérdezési tesztek futtatásai](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Tesztelési futtatás írása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Változáscsatorna

* [Streamfeldolgozási módosítások Azure Cosmos DB változáscsatornával és Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Változáscsatorna bemutatói](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Strukturált streambemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Figyelés

* [Spark-feladatok monitorozása az Application Insights segítségével](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, töltse le a Sparkot az Azure Cosmos DB-összekötőre az [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-adattárból. Tekintse át az alábbi további erőforrásokat az tárban:

* [Aggregációk – példák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Példaszk szkriptek és jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Érdemes lehet áttekintenünk a [Apache Spark SQL, DataFrames és Datasets Guide (SQL- és Datasets-útmutató)](https://spark.apache.org/docs/latest/sql-programming-guide.html)című cikket, valamint a Apache Spark [a Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikkét.