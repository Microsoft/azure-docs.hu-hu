---
title: Cosmos DB Apache Spark-összekötő az SQL API kibocsátási megjegyzései és erőforrásai számára
description: Ismerkedjen meg az SQL API-hoz készült Azure Cosmos DB Apache Spark-összekötővel, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint az Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 7ff75bf9d45803dd96927bcf7c70e7c7912db979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097158"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark-összekötő a Core (SQL) API-hoz: kibocsátási megjegyzések és erőforrások
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Big data elemzést a Core (SQL) Azure Cosmos DB Apache Spark Connector használatával gyorsíthatja fel. A Spark-összekötő lehetővé teszi [Spark](https://spark.apache.org/) -feladatok futtatását Azure Cosmos DBban tárolt adatokon. A Batch-és adatfolyam-feldolgozás támogatott.

Az összekötőt használhatja [Azure Databricks](https://azure.microsoft.com/services/databricks) vagy [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), amelyek felügyelt Spark-fürtöket biztosítanak az Azure-ban. A következő táblázat a támogatott verziókat tartalmazza:

| Összetevő | Verzió |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x* és 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (futtatókörnyezet verziója) | Később, mint 3,4 |

> [!WARNING]
> Ez az összekötő támogatja a Azure Cosmos DB Core (SQL) API-ját.
> A MongoDB Cosmos DB API-hoz használja a [Spark MongoDB-összekötőjét](https://docs.mongodb.com/spark-connector/master/).
> A Cosmos DB Cassandra API használja a [Cassandra Spark-összekötőt](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Források

| Erőforrás | Hivatkozás |
|---|---|
| **SDK letöltése** | A [legújabb. jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) letöltése |
|**API-dokumentáció** | [Spark-összekötő referenciája]() |
|**Közreműködés az SDK-ban** | [Azure Cosmos DB-összekötő Apache Spark a GitHubon](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Első lépések** | [Gyorsítsa fel big data elemzést az Azure Cosmos DB Connector Apache Spark használatával](./spark-connector.md#bk_working_with_connector) <br> [Apache Spark strukturált adatfolyam használata Apache Kafka és Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Kiadási előzmények

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Új funkciók
- Új konfigurációs lehetőség hozzáadása, `changefeedstartfromdatetime` amely a changefeed feldolgozásának kezdési időpontjának megadására használható. További információ: [konfigurációs beállítások](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references).

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
- Javít egy olyan regressziót, amely nagy mennyiségű memóriát okoz a végrehajtók számára a nagyméretű eredményhalmaz esetében (például több millió sorból), ami végső soron a hibát eredményezi `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavítja a folyamatos átviteli ellenőrzőpontok Edge-esetét, amelyben az `ID` alkalmazott konfigurációt tartalmazó pipe (|) karaktert tartalmazza `ChangeFeedMaxPagesPerBatch` .

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Új funkciók
* Támogatja a tömeges frissítéseket beágyazott partíciós kulcsok használata esetén.
* A a decimális és az úszó adattípusok támogatását adja a Azure Cosmos DBba írás során.
* Az időbélyeg-típusok támogatását adja meg, ha a Long (UNIX EPOCH) értéket használják értékként.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Megjavítja a konfiguráció használatakor előforduló typecast-kivételt `WriteThroughputBudget` .

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Új funkciók
* Hibaüzeneteket ad a tömeges hibákról a kivétel és a napló számára.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javítja a streaming ellenőrzőponttal kapcsolatos problémákat.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* A zaj csökkentése érdekében az üzenetek naplózási szintjének kijavítása akaratlanul, a szint HIBÁja miatt megmarad.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavít egy hibát a strukturált adatfolyamban a partíciók felosztása során. A hiba oka lehet néhány hiányzó módosítási hírcsatorna vagy Null kivétel az ellenőrzőpontok írásakor.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javít egy olyan hibát, amely miatt a rendszer figyelmen kívül hagyja a readStream számára megadott egyéni sémát.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javít egy regressziót (nem árnyékolt JAR tartalmazza az összes árnyékolt függőséget), amely 50 százalékkal növeli a felépítési időt.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavít egy függőségi problémát, amely a TCP protokollon keresztüli közvetlen átvitelt okoz a RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Új funkciók
* Javítja a kapcsolatok felügyeletét és a kapcsolatok készletezését, hogy csökkentse a metaadat-hívások számát.

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Cosmos DBról](https://azure.microsoft.com/services/cosmos-db/).

További tudnivalók az [Apache Sparkról](https://spark.apache.org/).
