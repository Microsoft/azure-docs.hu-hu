---
title: Spring Data Azure Cosmos DB v3 for SQL API kibocsátási megjegyzései és erőforrásai
description: Ismerje meg az SQL API-hoz készült Spring Data Azure Cosmos DB v3-as verzióját, beleértve a kiadási dátumokat, a kiesés dátumát és a Azure Cosmos DB SQL Async Java SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 594d38425be0304a9f7737bdfba60b29187a2e2d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363512"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v3 for Core (SQL) API: Kibocsátási megjegyzések és erőforrások
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

A Spring Data Azure Cosmos DB Core (SQL) 3-as verziója lehetővé teszi a fejlesztők számára a Azure Cosmos DB Spring-alkalmazásokban való használatát. A Spring Data Azure Cosmos DB elérhetővé teszi a Spring Data felületet az adatbázisok és gyűjtemények manipulálására, a dokumentumokon való munkavégzésre és a lekérdezések kiadására. A Sync és az Async (Reaktív) API-k is támogatottak ugyanabban a Maven-összetevőben. 

> [!IMPORTANT]
> A Spring Data Azure Cosmos DB a Spring Data keretrendszertől függ.
> 
> Az azure-spring-data-cosmos 3.0.0 és 3.4.0 között elérhető verziói támogatják a Spring Data 2.2-es és 2.3-as verzióit.
> 
> Az azure-spring-data-cosmos 3.5.0-s és újabb verziói támogatják a Spring Data 2.4.3-as és újabb verzióit.
>

A [Spring Framework](https://spring.io/projects/spring-framework) egy programozási és konfigurációs modell, amely leegyszerűsíti a Java-alkalmazások fejlesztését. A Spring függőséginjektálás használatával egyszerűsíti az alkalmazások "beágyazását". Sok fejlesztő kedveli a Springet, mert így egyszerűbben lehet alkalmazásokat építeni és tesztelni. [Spring Boot](https://spring.io/projects/spring-boot) a beágyazás kezelésének kiterjesztése a webalkalmazások és a mikroszolgáltatások fejlesztésének szem előtt ható szemével. [A Spring Data](https://spring.io/projects/spring-data) egy programozási modell és keretrendszer az adattárak Azure Cosmos DB egy Spring- vagy Spring Boot-alkalmazás kontextusából. 

A Spring Data-Azure Cosmos DB használhatja a [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) alkalmazásokban.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring Data Azure Cosmos DB. A 2. verzió kibocsátási [megjegyzéseit itt találja.](sql-api-sdk-java-spring-v2.md) 
>
> A Spring Data Azure Cosmos DB csak az SQL API-t támogatja.
>
> A Spring Data-ről az alábbi cikkekben talál további információt Azure Cosmos DB API-król:
> * [Spring Data for Apache Cassandra with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Gyors első lépések

  A Spring Data-Azure Cosmos DB az Spring Boot Starter [útmutatóját követi.](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) A Spring Boot Starter módszer az ajánlott módszer a Spring Data Azure Cosmos DB használatának első lépéséhez.

  Másik lehetőségként hozzáadhatja a Spring Data-Azure Cosmos DB a fájlhoz az `pom.xml` alábbi módon:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Hasznos tartalom

| Tartalom | Hivatkozás |
|---|---|
|**SDK letöltése**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API-dokumentáció** | [A Java API referenciadokumentációja](/java/api/com.azure.spring.data.cosmos) |
|**Közreműködés az SDK-ban** | [Azure SDK for Java Central-adattár a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Első lépések** | [Rövid útmutató: Spring Data-alkalmazás Azure Cosmos DB SQL API Azure Cosmos DB adatok kezeléséhez](./create-sql-api-spring-data.md) <br> [GitHub-adattár gyorsindítási kóddal](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Alapszintű kódminták** | [Azure Cosmos DB: Spring Data Azure Cosmos DB SQL API-hoz](sql-api-spring-data-sdk-samples.md) <br> [GitHub-adattár mintakóddal](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Teljesítménnyel kapcsolatos tippek**| [Teljesítménnyel kapcsolatos tippek Java SDK v4-hez (a Spring Data esetében alkalmazható)](performance-tips-java-sdk-v4-sql.md)| 
| **Hibaelhárítás** | [A Java SDK v4 hibaelhárítása (a Spring Data esetében alkalmazható)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB workshopok és laborok** |[Cosmos DB workshopok kezdőlapja](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>További megjegyzések

* A Spring Data Azure Cosmos DB a Java JDK 8-at és a Java JDK 11-et támogatja.
* A Spring Data 2.3 jelenleg támogatott, a Spring Data 2.4 jelenleg nem támogatott.

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

További információ a [Spring Frameworkről.](https://spring.io/projects/spring-framework)

További információ a [Spring Boot.](https://spring.io/projects/spring-boot)

További információ a [Spring Data-ről.](https://spring.io/projects/spring-data)