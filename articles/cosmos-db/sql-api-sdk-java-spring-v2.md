---
title: A Spring Data Azure Cosmos DB v2 for SQL API kibocsátási megjegyzései és erőforrásai
description: Ismerje meg a Spring Data Azure Cosmos DB v2 for SQL API-t, beleértve a kiadási dátumokat, a kieső dátumokat és a Azure Cosmos DB SQL Async Java SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4cd92efb7b6596288e4b6ef8e81f82f775a24e01
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363444"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v2 for Core (SQL) API: Kibocsátási megjegyzések és erőforrások
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

 A Spring Data Azure Cosmos DB 2. verziójának használata a Core (SQL) esetében lehetővé teszi a fejlesztők számára, hogy Azure Cosmos DB Spring-alkalmazásokban használják. A Spring Data Azure Cosmos DB elérhetővé teszi a Spring Data felületet az adatbázisok és gyűjtemények manipulálására, a dokumentumokkal való munkavégzésre és a lekérdezések kiadására. A Sync és az Async (Reaktív) API-k is támogatottak ugyanabban a Maven-összetevőben. 

A [Spring Framework](https://spring.io/projects/spring-framework) egy programozási és konfigurációs modell, amely leegyszerűsíti a Java-alkalmazások fejlesztését. A Spring függőséginjektálás használatával leegyszerűsíti az alkalmazások "beágyazását". Sok fejlesztő kedveli a Springet, mert így egyszerűbben lehet alkalmazásokat építeni és tesztelni. [Spring Boot](https://spring.io/projects/spring-boot) a beágyazás kezelését a webalkalmazások és a mikroszolgáltatások fejlesztésének szemével bővíti. [A Spring Data](https://spring.io/projects/spring-data) egy programozási modell, amely olyan adattárakat Azure Cosmos DB egy Spring- vagy Spring Boot-alkalmazás kontextusából. 

A Spring Data Azure Cosmos DB használhatja a [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) alkalmazásokban.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring Data-alkalmazás 2. Azure Cosmos DB. A [3-as](sql-api-sdk-java-spring-v3.md)verzió kibocsátási megjegyzéseit itt találja. 
>
> A Spring Data Azure Cosmos DB csak az SQL API-t támogatja.
>
> A Spring Data más api-okkal kapcsolatos további Azure Cosmos DB talál:
> * [Spring Data for Apache Cassandra with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Szeretne gyorsan haladni?
> 1. Telepítse [a minimálisan támogatott Java-futásidőt (JDK 8),](/java/azure/jdk/)hogy használni tudja az SDK-t.
> 2. Hozzon létre egy Spring Data Azure Cosmos DB-alkalmazást a [starter használatával.](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) Egyszerű!
> 3. A [Spring Data Azure Cosmos DB fejlesztői útmutatóját,](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)amely az alapszintű Azure Cosmos DB végigvezeti.
>
> A Spring Initializr használatával Spring Boot gyorsan használatba is lehet Spring Boot [Starter-alkalmazásokat.](https://start.spring.io/)
>

## <a name="resources"></a>Források

| Erőforrás | Hivatkozás |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-dokumentáció** | [A Spring Data Azure Cosmos DB referenciadokumentációja]() |
|**Közreműködés az SDK-ban** | [Spring Data Azure Cosmos DB a GitHubon](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Azure Cosmos DB Spring Boot Starter ügyféloldali kódtár Javához](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Spring TODO-alkalmazásminta Azure Cosmos DB**| [Végpontok között használható Java-élmény App Service Linuxban (2. rész)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Fejlesztői útmutató** | [Spring Data Azure Cosmos DB – fejlesztői útmutató](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**A Starter használata** | [A Spring Boot Starter használata az Azure Cosmos DB SQL API-val](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub-adattár a Azure Cosmos DB Spring Boot Starterhez](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Minta Azure App Service** | [A Spring és a Cosmos DB használata a Linuxos App Service-szel](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO alkalmazásminta](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Kiadási előzmények

### <a name="230-may-21-2020"></a>2.3.0 (2020. május 21.)
#### <a name="new-features"></a>Új funkciók
* A Spring Boot 2.3.0-s verzióra frissül.


### <a name="225-may-19-2020"></a>2.2.5 (2020. május 19.)
#### <a name="new-features"></a>Új funkciók
* A Azure Cosmos DB 3.7.3-as verzióra frissül.
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Memóriavesztési javításokat és Netty-verziófrissítéseket tartalmaz Azure Cosmos DB SDK 3.7.3-as verziójáról.

### <a name="224-april-6-2020"></a>2.2.4 (2020. április 6.)
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Javítva van `allowTelemetry` a jelző, hogy figyelembe vegye a et. `CosmosDbConfig`
* Javítja `TTL` a tároló tulajdonságát.

### <a name="223-february-25-2020"></a>2.2.3 (2020. február 25.)
#### <a name="new-features"></a>Új funkciók
* Új hozzáadása `findAll` a partíciókulcs API-ja alapján.
* A Azure Cosmos DB 3.7.0-s verzióra frissül.
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Javításokat ad `collectionName`  ->  `containerName` vissza.
* Javítások és `entityClass` `domainClass`  ->  `domainType` .
* Javítás: "A bemeneti entitás helyett adattár által mentett entitásgyűjtemény visszaadva."

### <a name="2110-february-25-2020"></a>2.1.10 (2020. február 25.)
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Backports-javítás a "Return entity collection saved by repository instead of input entity" (A bemeneti entitás helyett az adattár által mentett entitásgyűjtemény visszaadott gyűjteménye) hibajavítása.

### <a name="222-january-15-2020"></a>2.2.2 (2020. január 15.)
#### <a name="new-features"></a>Új funkciók
* A Azure Cosmos DB 3.6.0-s verzióra frissül.
#### <a name="key-bug-fixes"></a>Fő hibajavítások

### <a name="221-december-31-2019"></a>2.2.1 (2019. december 31.)
#### <a name="new-features"></a>Új funkciók
* Az Azure Cosmos DB SDK 3.5.0-s verziójára frissül.
* Jegyzetet ad hozzá az automatikus gyűjtemény-létrehozás engedélyezéséhez vagy letiltásához.
* Javítja a kivételek kezelését. A segítségével `CosmosClientException` teszi elérhetővé `CosmosDBAccessException` a-t.
* A és `requestCharge` a segítségével `activityId` elérhetővé teszi a és a `ResponseDiagnostics` et.
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Az SDK 3.5.0-s frissítés javítja a "Kivételt, ha Cosmos DB HTTP-válaszfejléc nagyobb 8192 bájtnál," "A ConsistencyPolicy.defaultConsistencyLevel() meghiúsul a kötött elévültség és a konzisztens előtag esetén."
* Kijavítja `findById` a metódus viselkedését. Korábban ez a metódus üresen adott vissza, ha az entitás nem található, és nem okozott kivételt.
* Kijavít egy hibát, amely miatt a rendszer nem alkalmazta a rendezést a következő oldalon a `CosmosPageRequest` használatakor.

### <a name="219-december-26-2019"></a>2.1.9 (2019. december 26.)
#### <a name="new-features"></a>Új funkciók
* Jegyzetet ad hozzá az automatikus gyűjtemény-létrehozás engedélyezéséhez vagy letiltásához.
#### <a name="key-bug-fixes"></a>Fő hibajavítások
*  Kijavítja `findById` a metódus viselkedését. Korábban ez a metódus üresen adott vissza, ha az entitás nem található, és nem okozott kivételt.

### <a name="220-october-21-2019"></a>2.2.0 (2019. október 21.)
#### <a name="new-features"></a>Új funkciók
* Teljes körű reaktív Cosmos-adattártámogatás.
* Azure Cosmos DB request diagnostics string and Query Metrics support (Diagnosztikai sztring- és lekérdezésmetrikák kérése) támogatása.
* Azure Cosmos DB SDK 3.3.1-es verzióra frissül.
* A Spring Framework verziófrissítése az 5.2.0.RELEASE verzióra.
* A Spring Data Commons verziófrissítése a 2.2.0.RELEASE verzióra.
* És `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` API-kat ad hozzá.
* Eltávolítja a függőséget az azure-documentdb-ból.
* A DocumentDB-t újra Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Javítás: "A rendezés kivételt ad, ha a pageSize kisebb, mint az adattár összes eleme."

### <a name="218-october-18-2019"></a>2.1.8 (2019. október 18.)
#### <a name="new-features"></a>Új funkciók
* A DocumentDB API-k elavultak.
* És `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` API-kat ad hozzá.
* Optimista zárolást ad hozzá a `_etag` alapján.
* Engedélyezi az SpEL-kifejezést a dokumentumgyűjtemény nevéhez.
* Fejlesztéseket `ObjectMapper` ad hozzá.

### <a name="217-october-18-2019"></a>2.1.7 (2019. október 18.)
#### <a name="new-features"></a>Új funkciók
* Hozzáadja Azure Cosmos DB SDK 3-as verziójának függőségét.
* Reaktív Cosmos-adattárat ad hozzá.
* Frissíti a `DocumentDbTemplate` implementációját, hogy Azure Cosmos DB SDK 3-as verzióját használja.
* További konfigurációs módosításokat ad hozzá a Reaktív Cosmos-adattár támogatásához.

### <a name="212-march-19-2019"></a>2.1.2 (2019. március 19.)
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Eltávolítja `applicationInsights` a függőséget a következőhöz:
    * A függőségek sérülésének lehetséges kockázata.
    * A Java 11 inkompatibilitása.
    * A processzorra és/vagy a memóriára gyakorolt lehetséges teljesítménybeli hatások elkerülése.

### <a name="207-march-20-2019"></a>2.0.7 (2019. március 20.)
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* A backport eltávolítja `applicationInsights` a függőséget a következő szolgáltatásokból:
    * A függőségek sérülésének lehetséges kockázata.
    * A Java 11 inkompatibilitása.
    * A processzorra és/vagy a memóriára gyakorolt lehetséges teljesítménybeli hatások elkerülése.

### <a name="211-march-7-2019"></a>2.1.1 (2019. március 7.)
#### <a name="new-features"></a>Új funkciók
* Frissíti a főverziót a 2.1.1-es verzióra.

### <a name="206-march-7-2019"></a>2.0.6 (2019. március 7.)
#### <a name="new-features"></a>Új funkciók
* Hagyja figyelmen kívül a telemetria összes kivételét.

### <a name="210-december-17-2018"></a>2.1.0 (2018. december 17.)
#### <a name="new-features"></a>Új funkciók
* A probléma megoldásához a 2.1.0-s verzióra frissül.

### <a name="205-september-13-2018"></a>2.0.5 (2018. szeptember 13.)
#### <a name="new-features"></a>Új funkciók
* Kulcsszavakat ad hozzá a `exists` és `startsWith` a hez.
* Frissül a readme fájl.
#### <a name="key-bug-fixes"></a>Fő hibajavítások
* A "Nem lehet közvetlenül az Entitáshoz hívni az önkiszolgáló href-et" hiba javítva.
* A "findAll sikertelen lesz, ha a gyűjtemény nem jön létre." hiba javítva.

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (Előzetes) (2018. augusztus 23.)
#### <a name="new-features"></a>Új funkciók
* Átnevezi a csomagot documentdb-ről cosmosdb-re.
* Hozzáadja a lekérdezési metódus kulcsszó új funkcióját. Az SQL API 16 kulcsszója mostantól támogatott.
* Új lekérdezési funkciót ad hozzá lapozással és rendezéssel.
* Leegyszerűsíti a spring-data-cosmosdb konfigurációját.
* És `deleteCollection` `deleteAll` API-kat ad hozzá.

#### <a name="key-bug-fixes"></a>Fő hibajavítások
* Hibajavítás és hibacsökkentés.

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések
További információ a [Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

További információ a [Spring Frameworkről.](https://spring.io/projects/spring-framework)

További információ a [Spring Boot.](https://spring.io/projects/spring-boot)

További információ a [Spring Data-ről.](https://spring.io/projects/spring-data)