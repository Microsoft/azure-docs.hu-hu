---
title: Azure Cosmos DB Apache Spark SQL API 2. OLTP-összekötő kibocsátási megjegyzései és erőforrásai
description: Ismerje meg az AZURE COSMOS DB APACHE SPARK 2 OLTP Connector for SQL API-t, beleértve a kiadási dátumokat, a kieső dátumokat és a Azure Cosmos DB SQL Async Java SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: bd948814b4b647bcc3fbfe58b090b1e794504232
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363631"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 2. OLTP-összekötő a Core (SQL) API-hoz: Kibocsátási megjegyzések és erőforrások
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
> * [Spark 3 OLTP-összekötő](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

A Core -big data 2 OLTP-összekötő (SQL Azure Cosmos DB Apache Spark használatával felgyorsíthatja az adatelemzést. A Spark-összekötő lehetővé teszi [Spark-feladatok](https://spark.apache.org/) futtatását a Azure Cosmos DB. A kötegelt és a streamfeldolgozás támogatott.

Az összekötőt használhatja a Azure Databricks [vagy](https://azure.microsoft.com/services/databricks) [a Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)amelyek felügyelt Spark-fürtöt biztosítanak az Azure-ban. Az alábbi táblázat a támogatott verziókat mutatja be:

| Összetevő | Verzió |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3. *x*, 2.2. *x*, és 2.1. *x* |
| Scala | 2,11 |
| Azure Databricks (futásidejű verzió) | 3.4-esnél újabb |

> [!WARNING]
> Ez az összekötő támogatja a központi (SQL) API-Azure Cosmos DB.
> A MongoDB Cosmos DB API-jához használja a [Spark MongoDB-összekötőt.](https://docs.mongodb.com/spark-connector/master/)
> A Cosmos DB Cassandra API használja a [Cassandra Spark-összekötőt.](https://github.com/datastax/spark-cassandra-connector)
>

## <a name="resources"></a>Források

| Erőforrás | Hivatkozás |
|---|---|
| **SDK letöltése** | [A legújabb .jar,](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) [Maven letöltése](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API-dokumentáció** | [Spark-összekötő referenciája]() |
|**Közreműködés az SDK-ban** | [Azure Cosmos DB Összekötő a GitHubon Apache Spark összekötő](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Első lépések** | [Az big data felgyorsítása az Apache Spark összekötő Azure Cosmos DB használatával](./spark-connector.md#bk_working_with_connector) <br> [Strukturált Apache Spark használata Apache Kafka és Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Kiadási előzmények

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Új funkciók
- Új konfigurációs beállítást ad hozzá, amellyel megadható a változáscsatorna feldolgozásának kezdési `changefeedstartfromdatetime` ideje. További információ: [Konfigurációs beállítások.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Fő hibajavítások
- Kijavít egy regressziót, amely túlzott memória-használatot okozott a végrehajtóknál nagy eredményhalmazok esetén (például több millió sorból), és végül a hibát `java.lang.OutOfMemoryError: GC overhead limit exceeded` eredményezte.

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Kijavít egy streamelési ellenőrzőpont élének esetét, amelyben a tartalmazza a pipe karaktert `ID` (|) az `ChangeFeedMaxPagesPerBatch` alkalmazott konfigurációval.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Új funkciók
* Támogatja a tömeges frissítéseket beágyazott partíciókulcsok használata során.
* A decimális és lebegőpontos adattípusok támogatása az írási Azure Cosmos DB.
* Támogatja az időbélyeg-típusokat, ha hosszú (Unix-alapidőt) használ értékként.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Kijavítja a konfiguráció használata esetén bekövetkező typecast `WriteThroughputBudget` kivételt.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Új funkciók
* Hibainformációkat ad hozzá a kivételek és a naplók tömeges meghibásodása esetén.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Kijavítja a streamelési ellenőrzőpontokkal kapcsolatos problémákat.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* A zaj csökkentése érdekében kijavítja az üzenetek naplószintje akaratlan hibaszinttel.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Kijavít egy hibát a strukturált streamelésben a partíciók felosztása során. A hiba hiányzó változáscsatorna-rekordokat vagy null kivételeket eredményezhet az ellenőrzőpont-írások számára.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Kijavít egy hibát, amely miatt a readStreamhez megadott egyéni séma figyelmen kívül lesz hagyva.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Kijavít egy regressziót (a nem árnyékolt JAR minden árnyékolt függőséget tartalmaz), amely 50%-kal növeli a buildidőt.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Kijavít egy függőségi problémát, amely miatt a TCP-kapcsolaton keresztüli közvetlen szállítás requestTimeoutException hibát okoz.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Új funkciók
* Javítja a kapcsolatkezelést és a kapcsolatkészletezést a metaadat-hívások számának csökkentése érdekében.

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

További tudnivalók az [Apache Sparkról](https://spark.apache.org/).
