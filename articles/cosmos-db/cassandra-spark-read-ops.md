---
title: Cassandra API Table-információk olvasása a Spark használatával
titleSufix: Azure Cosmos DB
description: Ez a cikk a Azure Cosmos DB Cassandra API tábláiból származó adatok beolvasását ismerteti.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93073496"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Adatok beolvasása Azure Cosmos DB Cassandra API-táblákból a Spark használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 Ez a cikk a Spark-Azure Cosmos DB Cassandra API tárolt adatok olvasását ismerteti.

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
## <a name="dataframe-api"></a>Dataframe API

### <a name="read-table-using-sessionreadformat-command"></a>Táblázat olvasása a Session. Read. format paranccsal

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Táblázat olvasása a Spark használatával. Read. cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Adott oszlopok olvasása a táblában

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Szűrők alkalmazása

Leküldheti a predikátumokat az adatbázisba, hogy jobban optimalizált Spark-lekérdezéseket lehessen. A predikátum olyan feltétel, amely igaz vagy hamis értéket ad vissza, általában a WHERE záradékban található. Egy predikátum leküldésével szűri az adatokat az adatbázis-lekérdezésben, csökkentve az adatbázisból beolvasott bejegyzések számát, és javítja a lekérdezési teljesítményt. Alapértelmezés szerint a Spark adatkészlet API automatikusan leküldi az érvényes WHERE záradékot az adatbázisba. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

A fizikai terv PushedFilters szakasza tartalmazza a GreaterThan leküldéses szűrőt. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="partíciók":::

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>Táblázat olvasása
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Adott oszlopok olvasása a táblában

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL-nézetek 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Ideiglenes nézet létrehozása dataframe

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Lekérdezések futtatása a nézettel

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkek a Spark Azure Cosmos DB Cassandra API való használatáról tartalmaznak további cikkeket:
 
 * [Upsert-műveletek](cassandra-spark-upsert-ops.md)
 * [Törlési műveletek](cassandra-spark-delete-ops.md)
 * [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
 * [Táblázatos másolási műveletek](cassandra-spark-table-copy-ops.md)

