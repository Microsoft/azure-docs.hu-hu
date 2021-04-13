---
title: Azure Cosmos DB Apache Spark 3 OLTP Connector for SQL API (előzetes verzió) kibocsátási megjegyzései és erőforrásai
description: Ismerje meg az Azure Cosmos DB Apache Spark 3 OLTP Connector for SQL API (előzetes verzió) verzióját, beleértve a kiadási dátumokat, a kieső dátumokat és a Azure Cosmos DB SQL Java SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368608"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 OLTP-összekötő a Core (SQL) API-hoz (előzetes verzió): Kibocsátási megjegyzések és erőforrások
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

**Azure Cosmos DB Spark 3 OLTP-összekötő (előzetes verzió)** Apache Spark v3-as Azure Cosmos DB AZ SQL API használatával.
[Azure Cosmos DB](introduction.md) egy globálisan elosztott adatbázis-szolgáltatás, amely lehetővé teszi a fejlesztők számára, hogy különböző szabványos API-k, például az SQL, a MongoDB, a Cassandra, a Graph és a Table használatával dolgoznak az adatokkal.

> [!Note]
> A Spark 3 OLTP Azure Cosmos DB összekötőnek ez a verziója egy előzetes verziójú build.
> Ez a build nem lett betöltéskor vagy teljesítményt tesztelve.
> Ez a build éles környezetben nem ajánlott.
>

## <a name="documentation"></a>Dokumentáció

- [Bevezetés](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Catalog API](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Konfigurációs paraméterek referenciája](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Verziók kompatibilitása

| Összekötő     | Spark         | Minimális Java-verzió | Támogatott Scala-verziók |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta.1  | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>Letöltés

A Jar Maven-koordinátája használatával automatikusan telepítheti a Spark-összekötőt a Mavenből a Databricks Runtime 8-as portra: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

Az SBT-projektben Cosmos DB Spark-összekötővel is integrálható:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Cosmos DB Spark-összekötő a Maven központi [adattában érhető el.](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar)

### <a name="general"></a>Általános kérdések

Ha bármilyen hibát észlelt, itt jelentsen [be egy problémát.](https://github.com/Azure/azure-sdk-for-java/issues/new)

Ha új funkciót vagy módosításokat szeretne javasolni, jelentsen be egy problémát ugyanúgy, mint egy hiba esetén.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Következő lépések

Tekintse át [a Spark 3 OLTP-összekötő Azure Cosmos DB útmutatóját.](create-sql-api-spark.md)
