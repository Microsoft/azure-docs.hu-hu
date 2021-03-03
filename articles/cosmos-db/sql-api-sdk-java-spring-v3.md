---
title: Spring-adatAzure Cosmos DB v3 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerje meg az SQL API-hoz készült Spring-adatok Azure Cosmos DB v3-as verzióját, beleértve a kiadási dátumokat, a kivonulási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/28/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9c3209895902a11ad0b9f29ff28e9ac7f845b101
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692725"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring-adatAzure Cosmos DB v3 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
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

A Spring-adatAzure Cosmos DB a Core (SQL) 3-as verziójának köszönhetően a fejlesztők a Azure Cosmos DBt a Spring Applications szolgáltatásban használhatják. A Spring-adatAzure Cosmos DB teszi elérhetővé a Spring-adatfelületet az adatbázisok és gyűjtemények, a dokumentumok kezelése és a lekérdezések kiállítása során. A szinkronizálási és aszinkron (reaktív) API-k ugyanabban a Maven-összetevőben támogatottak. 

A Spring-adatAzure Cosmos DB a Spring adatkeretrendszertől függ. A Azure Cosmos DB SDK csapata a 2,2-es és a 2,3-os tavaszi adatverziók Maven-összetevőit bocsátja ki.

A [Spring Framework](https://spring.io/projects/spring-framework) egy programozási és konfigurációs modell, amely megkönnyíti a Java-alkalmazások fejlesztését. A Spring leegyszerűsíti az alkalmazások "vízmennyiségét" a függőségi befecskendezés használatával. Számos fejlesztő, mint például a Spring, egyszerűbbvé teszi az alkalmazások létrehozását és tesztelését. A [Spring boot](https://spring.io/projects/spring-boot) kiterjeszti a vízvezetékek kezelését a webalkalmazások és a szolgáltatások fejlesztésének szem előtt tartásával. A [Spring-adatok](https://spring.io/projects/spring-data) egy programozási modell és keretrendszer az adattárolók eléréséhez, például a Azure Cosmos db egy Spring vagy Spring rendszerindítási alkalmazás kontextusában. 

Az [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) -alkalmazásaiban a Spring-alapú adatAzure Cosmos DBek is használhatók.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring-adatmennyiségek Azure Cosmos DB 3. verziójára vonatkoznak. [A 2. verzió kibocsátási megjegyzéseit itt](sql-api-sdk-java-spring-v2.md)találja. 
>
> A Spring-alapú adatAzure Cosmos DB csak az SQL API-t támogatja.
>
> Tekintse meg ezeket a cikkeket a más Azure Cosmos DB API-k tavaszi adataival kapcsolatban:
> * [Spring-adatforrások az Apache Cassandra és a Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring-adatMongoDB Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring-adatGremlin Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Gyors első lépések

  A Spring [boot Starter útmutatót](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)követve megkezdheti a tavaszi adatAzure Cosmos DBek futtatását. A Spring boot Starter megközelítése az ajánlott módszer a Spring-adatAzure Cosmos DB-összekötő használatának megkezdésére.

  Azt is megteheti, hogy felveszi a Spring adat Azure Cosmos DB függőséget a `pom.xml` fájlhoz az alábbiak szerint:

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
|**API-dokumentáció** | [Java API-referenciák dokumentációja](/java/api/com.azure.spring.data.cosmos) |
|**Közreműködés az SDK-val** | [Azure SDK a Java központi tárházhoz a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Első lépések** | [Gyors útmutató: Spring-alapú adatAzure Cosmos DB-alkalmazás létrehozása Azure Cosmos DB SQL API-alapú adatkezeléshez](./create-sql-api-spring-data.md) <br> [GitHub-tárház a gyors üzembe helyezési kóddal](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Alapszintű kódok** | [Azure Cosmos DB: Spring-adatAzure Cosmos DB példák az SQL API-ra](sql-api-spring-data-sdk-samples.md) <br> [GitHub-tárház a mintakód](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Teljesítménnyel kapcsolatos tippek**| [Teljesítményre vonatkozó tippek Java SDK v4-hez (a Spring-adatokra érvényes)](performance-tips-java-sdk-v4-sql.md)| 
| **Hibaelhárítás** | [A Java SDK v4 hibáinak megoldása (a rugós adatra alkalmazható)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB workshopok és Labs** |[Cosmos DB workshopok kezdőlapja](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>További megjegyzések

* A Spring-adatAzure Cosmos DB támogatja a Java JDK 8 és a Java JDK 11 használatát.
* Az 2,3-as tavaszi adatfeldolgozás jelenleg támogatott, a Spring-adat2,4 jelenleg nem támogatott.

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Cosmos DBról](https://azure.microsoft.com/services/cosmos-db/).

További információ a [Spring Framework](https://spring.io/projects/spring-framework)-ről.

További információ a [Spring boot](https://spring.io/projects/spring-boot)-ről.

További információ a [Spring-adatforrásokról](https://spring.io/projects/spring-data).