---
title: Apache Spark összekötése a Azure Cosmos DB
description: Ismerkedjen meg a Azure Cosmos DB Spark-összekötővel, amely lehetővé teszi Apache Spark összekapcsolását Azure Cosmos DBhoz.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 06498a27b95a72148497efd2d1e600d802414359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359557"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Gyorsítsa fel big data elemzést az Azure Cosmos DB Connector Apache Spark használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A [Spark](https://spark.apache.org/) -feladatokat a Cosmos db Spark-összekötő használatával Azure Cosmos db tárolt adatokkal futtathatja. A Cosmos a Batch-és stream-feldolgozáshoz, valamint a kis késleltetésű hozzáféréshez szolgáló kiszolgáló rétegként használható.

Az összekötőt használhatja [Azure Databricks](https://azure.microsoft.com/services/databricks) vagy [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), amelyek felügyelt Spark-fürtöket biztosítanak az Azure-ban. Az alábbi táblázatban a Spark-verziók láthatók.

| Összetevő | Verzió |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x és 2.1. x |
| Scala | 2,11 |
| Azure Databricks futtatókörnyezet verziója | > 3,4 |

> [!WARNING]
> Ez az összekötő támogatja a Azure Cosmos DB Core (SQL) API-ját.
> A MongoDB API-hoz Cosmos DB használja a [MongoDB Spark-összekötőt](https://docs.mongodb.com/spark-connector/master/).
> Cosmos DB Cassandra API esetén használja a [Cassandra Spark-összekötőt](https://github.com/datastax/spark-cassandra-connector).

> [!IMPORTANT]
> A Azure Cosmos DB Spark-összekötő jelenleg nem támogatott a [kiszolgáló](serverless.md) nélküli fiókoknál. Ez lesz a megoldás, mivel a kiszolgáló nélküli ajánlat általánosan elérhetővé válik.

## <a name="quickstart"></a>Gyorsútmutató

* Kövesse az Ismerkedés a [Java SDK-val](./create-sql-api-java.md) című témakör lépéseit Cosmos db fiók beállításához és adatok feltöltéséhez.
* Azure Databricks munkaterület és fürt beállításához kövesse [Azure Databricks első](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) lépések című szakasz lépéseit.
* Most már létrehozhat új jegyzetfüzeteket, és importálhatja az Cosmos DB-összekötő függvénytárát. A munkaterület beállításával kapcsolatos részletekért ugorjon [a Cosmos db-összekötővel való együttműködésre](#bk_working_with_connector) .
* A következő szakasz a-összekötő használatával történő olvasás és írás részleteit tartalmazza.

### <a name="batch-reads-from-cosmos-db"></a>A Batch beolvasása Cosmos DB

Az alábbi kódrészletből megtudhatja, hogyan hozhat létre egy Spark-DataFrame a PySpark Cosmos DBból való olvasáshoz.

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

És ugyanazt a kódrészletet a Scalaban:

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

### <a name="batch-writes-to-cosmos-db"></a>Kötegek írása a Cosmos DBba

Az alábbi kódrészletből megtudhatja, hogyan írhat egy adatkeretet, hogy Cosmos DB a PySpark.

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

És ugyanazt a kódrészletet a Scalaban:

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

### <a name="streaming-reads-from-cosmos-db"></a>Cosmos DBről érkező adatfolyam-olvasások

A következő kódrészlet azt mutatja be, hogyan lehet csatlakozni a Azure Cosmos DB változási hírcsatornához, és hogyan kell olvasni.

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
És ugyanazt a kódrészletet a Scalaban:

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

### <a name="streaming-writes-to-cosmos-db"></a>Adatfolyam-írási Cosmos DB

Az alábbi kódrészletből megtudhatja, hogyan írhat egy adatkeretet, hogy Cosmos DB a PySpark.

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

És ugyanazt a kódrészletet a Scalaban:

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
További kódrészletek és végpontok közötti minták: [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Az összekötő használata

Az összekötőt felépítheti a GitHubról a forrásból, vagy letöltheti az Über-tégelyeket a Mavenből az alábbi hivatkozásokra.

| Spark | Scala | Legújabb verzió |
|---|---|---|
| 2.4.0 | 2,11 | [Azure-cosmosdb – spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [Azure-cosmosdb-spark_2.3.0 _2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [Azure-cosmosdb-spark_2.2.0 _2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [Azure-cosmosdb-spark_2.1.0 _2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks-jegyzetfüzetek használata

Hozzon létre egy tárat a Databricks-munkaterülettel a Azure Databricks útmutatóban > a [Azure Cosmos db Spark-összekötő](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html) használatával.

> [!NOTE]
> A **Azure Cosmos db Spark-összekötő használata** oldal jelenleg nem naprakész. A hat különálló tégely 6 különböző könyvtárba való letöltése helyett letöltheti az Über jar-t a mavenből az [Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) , és telepítheti ezt az egy jar/könyvtárat.
> 

### <a name="using-spark-cli"></a>A Spark-CLI használata

Ha a Spark-CLI (azaz,,) használatával szeretné használni az összekötőt `spark-shell` `pyspark` , használhatja `spark-submit` a `--packages` paramétert az összekötő [Maven koordinátáival](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter-jegyzetfüzetek használata

Ha Jupyter Notebookt használ a HDInsight-n belül, a Spark-Magic `%%configure` cella használatával megadhatja az összekötő Maven-koordinátáit.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Vegye figyelembe, hogy a belefoglalása az `spark.jars.excludes` összekötő, a Apache Spark és a Livy közötti lehetséges ütközések eltávolítására vonatkozik.

### <a name="build-the-connector"></a>Az összekötő létrehozása

Ez az összekötő-projekt jelenleg a következőt használja `maven` a függőségek nélküli létrehozáshoz:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>A minták használata

A [Cosmos db Spark GitHub-tárházban](https://github.com/Azure/azure-cosmosdb-spark) a következő minta-jegyzetfüzeteket és parancsfájlokat lehet kipróbálni.

* **A Spark és a Cosmos db (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)HTML: a Spark és a Cosmos db összekapcsolása a  |  [](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)HDInsight Jupyter notebook szolgáltatással a Spark SQL, a GraphFrames és a repülési késések ml-folyamatok használatával történő bemutatásához.
* **Twitter-forrás Apache Spark és Azure Cosmos db változási hírcsatorna**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Cosmos db gráfok lekérdezése Apache Spark használatával**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Azure Databricks csatlakoztatása Azure Cosmos db](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** a használatával `azure-cosmosdb-spark` .  A csatolt itt az [időpontú repülési teljesítményű jegyzetfüzet](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)Azure Databricks verziója is.
* **[Lambda architektúra Azure Cosmos db-és HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: a Cosmos db és a Spark használatával csökkentheti a Big Data folyamatok fenntartásának működési terhelését.

## <a name="more-information"></a>További információ

További információ a `azure-cosmosdb-spark` [wikiben](https://github.com/Azure/azure-cosmosdb-spark/wiki) :

* [Azure Cosmos DB Spark-összekötő felhasználói útmutatója](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Összesítési példák](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfigurálás és beállítás

* [Spark-összekötő konfigurálása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark – Cosmos db összekötő telepítése](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (folyamatban)
* [Power BI közvetlen lekérdezés konfigurálása Azure Cosmos DBhoz Apache Spark (HDI) használatával](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Hibaelhárítás

* [Cosmos DB összesítések használata](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Ismert problémák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Teljesítmény

* [Teljesítménnyel kapcsolatos tippek](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Lekérdezési tesztek futtatása](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Tesztelési futtatások írása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Változáscsatorna

* [Az adatfolyam-feldolgozás változásai Azure Cosmos DB változási csatornát és Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Hírcsatorna-bemutatók módosítása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Strukturált stream-bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Figyelés

* [Spark-feladatok figyelése az Application bepillantást](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, töltse le a Sparkot Azure Cosmos DB összekötőt az [Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub adattárból. Fedezze fel a következő további forrásokat a tárházban:

* [Összesítési példák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Példa parancsfájlok és jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Érdemes áttekinteni a [Apache Spark SQL-, DataFrames-és adatkészletek útmutatóját](https://spark.apache.org/docs/latest/sql-programming-guide.html), valamint az [Azure HDInsight-on található Apache Sparkt](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) is.