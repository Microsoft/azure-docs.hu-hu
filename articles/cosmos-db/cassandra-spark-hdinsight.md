---
title: Hozzáférés Azure Cosmos DB Cassandra API a Sparkból a HDInsight-mel
description: Ez a cikk azt ismerteti, hogyan használható a Spark on Azure Cosmos DB Cassandra API a HDInsight-mel
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e09ac39037024b5c360af4bb6ada26590b142924
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359591"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Hozzáférés Azure Cosmos DB Cassandra API a Sparkból a HDInsight-mel
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ez a cikk azt ismerteti, hogyan érheti el Azure Cosmos DB Cassandra API a Sparkból a FONALból a Spark-Shell HDInsight-Spark használatával. A HDInsight a Microsoft Hortonworks Hadoop, amely az Azure-on található, és a HDFS számos különböző, többek között a [Spark](../hdinsight/spark/apache-spark-overview.md)használatát is lehetővé teszi.  Habár a jelen dokumentumban szereplő tartalom a HDInsight-Spark-ra hivatkozik, minden Hadoop-eloszlásra érvényes.  

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB kiépítése Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Tekintse át Azure Cosmos DB Cassandra APIhoz való csatlakozás alapjait](cassandra-spark-generic.md)

* [HDInsight-Spark-fürt kiépítése](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Tekintse át a Cassandra API használatáról szóló kód mintáit](cassandra-spark-generic.md#next-steps)

* [Cqlsh használata az érvényesítéshez, ha szeretné](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API konfiguráció a Spark2-ben** – a Cassandra Connector for Cassandra szükséges, hogy a Cassandra kapcsolat részletei a Spark-környezet részeként legyenek inicializálva. A Jupyter-jegyzetfüzet indításakor a Spark-munkamenet és a környezet már inicializálva van, és nem tanácsos leállítani és újrainicializálni a Spark-környezetet, kivéve, ha az HDInsight alapértelmezett Jupyter-jegyzetfüzet-telepítés részeként beállított minden konfigurációs beállítással rendelkezik. Az egyik megkerülő megoldás, ha a Cassandra-példány részleteit közvetlenül a Ambari, a Spark2 szolgáltatás konfigurációjában adja hozzá. Ez egy egyszeri tevékenység, amely egy Spark2 szolgáltatás újraindítását igényli.
 
  1. Nyissa meg a Ambari, a Spark2 szolgáltatást, és válassza a konfigurációk lehetőséget.

  2. Ezután nyissa meg az egyéni spark2-alapértékeket, és adjon hozzá egy új tulajdonságot a következővel, és indítsa újra a Spark2 szolgáltatást:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Hozzáférés Azure Cosmos DB Cassandra API a Spark shellből

A Spark Shell tesztelési/feltárási célokra szolgál.

* Indítsa el a Spark-shellt a fürt Spark-verziójával kompatibilis szükséges Maven-függőségekkel.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Néhány DDL-és DML-művelet végrehajtása

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* SZIFILISZ-műveletek futtatása

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Hozzáférés Azure Cosmos DB Cassandra API Jupyter-jegyzetfüzetből

A HDInsight-Spark a Zeppelin és a Jupyter notebook Services szolgáltatással érhető el. Ezek mind a web-alapú notebook-környezetek, amelyek támogatják a Scalat és a Pythont. A notebookok hasznosak az interaktív felderítő elemzésekhez és az együttműködéshez, de nem jelentenek operatív/éles folyamatokat.

A következő Jupyter-jegyzetfüzeteket feltöltheti a HDInsight Spark-fürtbe, és kész mintákat készíthet a Azure Cosmos DB Cassandra API való munkához. Tekintse át az első jegyzetfüzetet `1.0-ReadMe.ipynb` , és tekintse át a Spark szolgáltatás konfigurációját a Azure Cosmos DB Cassandra APIhoz való csatlakozáshoz.

Töltse le ezeket a jegyzetfüzeteket az [Azure-Cosmos-db-Cassandra-API-Spark-Notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) a gépre.
  
### <a name="how-to-upload"></a>Feltöltés:
A Jupyter indításakor navigáljon a Scala-hoz. Először hozzon létre egy könyvtárat, majd töltse fel a jegyzetfüzeteket a könyvtárba. A feltöltés gomb a jobb felső sarokban található.  

### <a name="how-to-run"></a>Futtatás:
Futtasson végig a jegyzetfüzeteken, és mindegyik jegyzetfüzet-cellát egymás után.  Kattintson az egyes jegyzetfüzetek tetején található Futtatás gombra az összes cella végrehajtásához, vagy a SHIFT + ENTER billentyűkombinációt az egyes cellákhoz.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Hozzáférés Azure Cosmos DB Cassandra API a Spark Scala programból

Az éles környezetben futó automatizált folyamatok esetében a Spark-programokat a [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html)használatával küldi el a rendszer a fürtnek.

## <a name="next-steps"></a>Következő lépések

* [Spark Scala-program létrehozása egy IDE-ben, és elküldése a HDInsight Spark-fürtnek a Livy-en keresztül a végrehajtáshoz](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Kapcsolódás Azure Cosmos DB Cassandra API Spark Scala programból](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [A Cassandra API használatához használható mintakód-minták teljes listája](cassandra-spark-generic.md)
