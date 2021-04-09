---
title: A Sparkból származó Azure Cosmos DB Cassandra API-táblák összesítési műveletei
description: Ez a cikk a Spark Azure Cosmos DB Cassandra API-tábláival kapcsolatos alapszintű összesítési műveleteket ismerteti.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087621"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>A Sparkból származó Azure Cosmos DB Cassandra API-táblák összesítési műveletei 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ez a cikk a Sparkból származó Azure Cosmos DB Cassandra API-táblákon végrehajtható alapszintű összesítési műveleteket írja le. 

> [!NOTE]
> A Kiszolgálóoldali szűrés és a kiszolgálóoldali összesítés jelenleg nem támogatott Azure Cosmos DB Cassandra API.

## <a name="cassandra-api-configuration"></a>Cassandra API konfiguráció

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="sample-data-generator"></a>Mintaadatok-generátor

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Művelet számlálása


### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Kimeneti**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Dataframe API

A dataframes száma jelenleg nem támogatott.  Az alábbi minta azt mutatja be, hogyan hajtható végre a dataframe száma, miután megmaradt a dataframe a memóriához megkerülő megoldás.

Válassza ki a [tárolási lehetőséget]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) a következő elérhető lehetőségek közül, hogy elkerülje a "kevés a memória" problémáit:

* MEMORY_ONLY: ez az alapértelmezett tárolási beállítás. A RDD deszerializált Java-objektumként tárolja a JVM. Ha a RDD nem fér el a memóriában, egyes partíciók nem lesznek gyorsítótárazva, és a rendszer minden alkalommal újra kiszámítja őket.

* MEMORY_AND_DISK: a RDD a JVM-ben deszerializált Java-objektumként tárolja. Ha a RDD nem fér el a memóriában, tárolja azokat a partíciókat, amelyek nem férnek hozzá a lemezhez, és amikor szükséges, olvassa el azokat a tárolt helyről.

* MEMORY_ONLY_SER (Java/Scala): a RDD-t szerializált Java-objektumként tárolja – egybájtos tömb/partíció. Ez a beállítás a deszerializált objektumokhoz képest hatékony, különösen a gyors szerializáló használata esetén, de nagyobb CPU-igényű olvasásra.

* MEMORY_AND_DISK_SER (Java/Scala): Ez a tárolási lehetőség olyan, mint MEMORY_ONLY_SER, az egyetlen különbség, hogy a lemez memóriába nem illeszkedő partíciókat kivezeti, és nem kell azokat újratervezni, amikor szükség van rájuk.

* DISK_ONLY: csak a lemezen tárolja a RDD-partíciókat.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: ugyanaz, mint a fenti szintek, de az egyes partíciókat két fürtcsomóponton replikálja.

* OFF_HEAP (kísérleti): hasonló a MEMORY_ONLY_SERhoz, de a memóriában tárolja az adatvesztést, és a memórián kívüli memóriát igényel az idő előtt. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Átlagos művelet

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Kimeneti**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Kimeneti**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Kimeneti**
```
16.016000175476073
```

## <a name="min-operation"></a>Minimális művelet

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Kimeneti**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Kimeneti**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Kimeneti**
```
11.33
```

## <a name="max-operation"></a>Maximális művelet

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Dataframe API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Kimeneti**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Kimeneti**
```
22.45
```

## <a name="sum-operation"></a>Sum művelet

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Kimeneti**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Kimeneti**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Kimeneti**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Leggyakoribb vagy hasonló művelet

### <a name="rdd-api"></a>RDD API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Kimeneti**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Dataframe API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Kimeneti**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Következő lépések

A táblázatos másolási műveletek végrehajtásához tekintse meg a következőt:

* [Táblázatos másolási műveletek](cassandra-spark-table-copy-ops.md)
