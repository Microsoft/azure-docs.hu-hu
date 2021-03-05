---
title: Spring-adatAzure Cosmos DB v2 az SQL API kibocsátási megjegyzésekhez és erőforrásokhoz
description: Ismerkedjen meg az SQL API-hoz készült Spring adatok Azure Cosmos DB v2 szolgáltatással, beleértve a kiadási dátumokat, a kivonási dátumokat és a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 1f78d7185415b66445c78f3b4cdb54ed0d63cb0a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216615"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring-adatAzure Cosmos DB v2 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
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

 A Spring-adatAzure Cosmos DB a Core (SQL) rendszerhez készült 2. verzió lehetővé teszi a fejlesztők számára a Azure Cosmos DB használatát a Spring-alkalmazásokban. A Spring-adatAzure Cosmos DB teszi elérhetővé a Spring-adatfelületet az adatbázisok és gyűjtemények, a dokumentumok kezelése és a lekérdezések kiállítása során. A szinkronizálási és aszinkron (reaktív) API-k ugyanabban a Maven-összetevőben támogatottak. 

A [Spring Framework](https://spring.io/projects/spring-framework) egy programozási és konfigurációs modell, amely megkönnyíti a Java-alkalmazások fejlesztését. A Spring leegyszerűsíti az alkalmazások "vízmennyiségét" a függőségi befecskendezés használatával. Számos fejlesztő, mint például a Spring, egyszerűbbvé teszi az alkalmazások létrehozását és tesztelését. A [Spring boot](https://spring.io/projects/spring-boot) kiterjeszti a vízvezetékek kezelését a webalkalmazások és a szolgáltatások fejlesztésének szem előtt tartásával. A [Spring-adatok](https://spring.io/projects/spring-data) egy olyan programozási modell, amellyel olyan adattárolók férnek hozzá, mint a Azure Cosmos db egy Spring vagy Spring rendszerindítási alkalmazás kontextusában. 

Az [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) -alkalmazásaiban a Spring-alapú adatAzure Cosmos DBek is használhatók.

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések a Spring-adatAzure Cosmos DB 2. verziójára vonatkoznak. [A 3. verzió kibocsátási megjegyzéseit itt](sql-api-sdk-java-spring-v3.md)találja. 
>
> A Spring-alapú adatAzure Cosmos DB csak az SQL API-t támogatja.
>
> Az egyéb Azure Cosmos DB API-kkal kapcsolatos Spring-adatokról a következő cikkekben talál további információt:
> * [Spring-adatforrások az Apache Cassandra és a Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring-adatMongoDB Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring-adatGremlin Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Szeretne gyorsan eljutni?
> 1. Telepítse a [minimális támogatott Java-futtatókörnyezetet (JDK 8](/java/azure/jdk/)), így használhatja az SDK-t is.
> 2. Hozzon létre egy Spring-adatAzure Cosmos DB alkalmazást a [Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)használatával. Egyszerű!
> 3. A Spring- [adatokon keresztül Azure Cosmos db Fejlesztői útmutatóban](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)dolgozhat, amely az alapszintű Azure Cosmos db kérelmeken keresztül halad.
>
> A Spring [inicializáló](https://start.spring.io/)használatával gyorsan üzembe helyezheti a Spring boot Starter-alkalmazásokat.
>

## <a name="resources"></a>Források

| Erőforrás | Hivatkozás |
|---|---|
| **SDK letöltése** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-dokumentáció** | [A Spring-adatAzure Cosmos DB dokumentációja]() |
|**Közreműködés az SDK-ban** | [Spring-adatAzure Cosmos DB-tárház a GitHubon](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring boot Starter**| [A Azure Cosmos DB Spring boot Starter ügyféloldali kódtára a Javához](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Spring TODO app-minta Azure Cosmos DB**| [Teljes körű Java-élmény App Service Linux rendszerben (2. rész)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Fejlesztői útmutató** | [Spring Data Azure Cosmos DB – fejlesztői útmutató](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Starter használata** | [A Spring boot Starter használata a Azure Cosmos DB SQL API-val](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub-tárház Azure Cosmos DB Spring boot Starter-hez](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Minta Azure App Service** | [A Spring és a Cosmos DB használata a Linuxos App Service-szel](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO-alkalmazás mintája](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Kiadási előzmények

### <a name="230-may-21-2020"></a>2.3.0 (2020. május 21.)
#### <a name="new-features"></a>Új funkciók
* Frissíti a Spring boot-verziót a 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (május 19., 2020)
#### <a name="new-features"></a>Új funkciók
* Frissíti Azure Cosmos DB verziót a 3.7.3-re.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Memóriavesztés-javításokat és a Azure Cosmos DB SDK-3.7.3 származó nettó verziófrissítést tartalmaz.

### <a name="224-april-6-2020"></a>2.2.4 (2020. április 6.)
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javítások `allowTelemetry` jelzője, amelyből a rendszer figyelembe veszi a következőt: `CosmosDbConfig` .
* Kijavítja `TTL` a tároló tulajdonságát.

### <a name="223-february-25-2020"></a>2.2.3 (2020. február 25.)
#### <a name="new-features"></a>Új funkciók
* Új `findAll` partíciós kulcs API-val egészül ki.
* Frissíti Azure Cosmos DB verziót a 3.7.0-re.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javítások `collectionName`  ->  `containerName` .
* Javítások `entityClass` és `domainClass`  ->  `domainType` .
* Javítások: az adattárban tárolt visszaküldési entitás gyűjtése a bemeneti entitás helyett.

### <a name="2110-february-25-2020"></a>2.1.10 (2020. február 25.)
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* A backports-javítás a (z) adattár által mentett visszaküldési entitás-gyűjteményhez bemeneti entitás helyett.

### <a name="222-january-15-2020"></a>2.2.2 (január 15., 2020)
#### <a name="new-features"></a>Új funkciók
* Frissíti Azure Cosmos DB verziót a 3.6.0-re.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="221-december-31-2019"></a>2.2.1 (december 31., 2019)
#### <a name="new-features"></a>Új funkciók
* Frissíti Azure Cosmos DB SDK-verziót a 3.5.0-re.
* Megjegyzések mező hozzáadásával engedélyezheti vagy letilthatja az automatikus gyűjtés létrehozását.
* Javítja a kivételek kezelését. Elérhetővé teszi a-t `CosmosClientException` `CosmosDBAccessException` .
* A `requestCharge` és `activityId` a segítségével elérhető `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Az SDK-3.5.0 frissítésének javítása "kivétel történt, ha Cosmos DB HTTP-válasz fejléce 8192 bájtnál nagyobb," "ConsistencyPolicy. defaultConsistencyLevel () sikertelen a kötött elavulás és az konzisztens előtag esetén."
* Megjavítja `findById` a metódus viselkedését. Korábban ez a metódus üresen tért vissza, ha az entitás nem található kivétel ledobása helyett.
* Kijavít egy olyan hibát, amelyben a rendszer a következő oldalon nem alkalmazta a rendezést, amikor a `CosmosPageRequest` használatban volt.

### <a name="219-december-26-2019"></a>2.1.9 (2019. december 26.)
#### <a name="new-features"></a>Új funkciók
* Megjegyzések mező hozzáadásával engedélyezheti vagy letilthatja az automatikus gyűjtés létrehozását.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
*  Megjavítja `findById` a metódus viselkedését. Korábban ez a metódus üresen tért vissza, ha az entitás nem található kivétel ledobása helyett.

### <a name="220-october-21-2019"></a>2.2.0 (2019. október 21.)
#### <a name="new-features"></a>Új funkciók
* Fejezze be a reaktív Cosmos-tárház támogatását.
* Azure Cosmos DB a diagnosztikai karakterlánc és a lekérdezési mérőszámok támogatását.
* Azure Cosmos DB SDK-verzió frissítése a 3.3.1-ra.
* A Spring Framework verziófrissítése a 5.2.0. RELEASE verzióra.
* A Spring adatcommons verziójának frissítése a 2.2.0. RELEASE csomagra.
* Hozzáadások `findByIdAndPartitionKey` és API-k `deleteByIdAndPartitionKey` .
* Eltávolítja a függőséget az Azure-documentdb.
* A rebrands DocumentDB Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* A kijavítja a "rendezési dob kivételt, ha a pageSize kisebb, mint a tárházban lévő összes elem."

### <a name="218-october-18-2019"></a>2.1.8 (2019. október 18.)
#### <a name="new-features"></a>Új funkciók
* Elavult DocumentDB API-k.
* Hozzáadások `findByIdAndPartitionKey` és API-k `deleteByIdAndPartitionKey` .
* Optimista zárolást hoz létre a alapján `_etag` .
* Az SpEL kifejezés engedélyezése a dokumentum-gyűjtemény neveként.
* Bővíti a `ObjectMapper` funkciókat.

### <a name="217-october-18-2019"></a>2.1.7 (2019. október 18.)
#### <a name="new-features"></a>Új funkciók
* Hozzáadja Azure Cosmos DB SDK 3-as verziójának függőségét.
* Hozzáadja a reaktív Cosmos-tárházat.
* A `DocumentDbTemplate` az Azure Cosmos db SDK 3-as verziójának használatára frissíti.
* A reaktív Cosmos-tárház támogatásának további konfigurációs módosításait adja meg.

### <a name="212-march-19-2019"></a>2.1.2 (2019. március 19.)
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* `applicationInsights`A következő függőség eltávolítása:
    * A függőségek szennyezésének lehetséges kockázata.
    * Java 11 inkompatibilitás.
    * A CPU és/vagy a memória lehetséges teljesítményének elkerülése.

### <a name="207-march-20-2019"></a>2.0.7 (2019. március 20.)
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* A vezetnie eltávolítja `applicationInsights` a következő függőséget:
    * A függőségek szennyezésének lehetséges kockázata.
    * Java 11 inkompatibilitás.
    * A CPU és/vagy a memória lehetséges teljesítményének elkerülése.

### <a name="211-march-7-2019"></a>2.1.1 (2019. március 7.)
#### <a name="new-features"></a>Új funkciók
* Frissíti a fő verziót a 2.1.1-re.

### <a name="206-march-7-2019"></a>2.0.6 (2019. március 7.)
#### <a name="new-features"></a>Új funkciók
* A telemetria összes kivételének figyelmen kívül hagyása.

### <a name="210-december-17-2018"></a>2.1.0 (2018. december 17.)
#### <a name="new-features"></a>Új funkciók
* A probléma megoldása érdekében frissíti a 2.1.0 verzióját.

### <a name="205-september-13-2018"></a>2.0.5 (2018. szeptember 13.)
#### <a name="new-features"></a>Új funkciók
* Kulcsszavakat `exists` és `startsWith` .
* Frissítések – readme.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javítások: "nem hívható meg a saját href közvetlenül az entitáshoz."
* Javítások: a findAll sikertelen lesz, ha a gyűjtemény nincs létrehozva.

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (előzetes verzió) (2018. augusztus 23.)
#### <a name="new-features"></a>Új funkciók
* Átnevezi a csomagot a documentdb-ből a cosmosdb-be.
* Hozzáadja a lekérdezési módszer kulcsszava új funkcióját. Mostantól 16 kulcsszó érhető el az SQL API-ból.
* A lekérdezés új funkciójának bevonása a lapozással és a rendezéssel.
* Leegyszerűsíti a Spring-cosmosdb konfigurációját.
* Hozzáadások `deleteCollection` és API-k `deleteAll` .

#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Hibajavítás és hibák elhárítása.

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések
További információ a [Azure Cosmos DBról](https://azure.microsoft.com/services/cosmos-db/).

További információ a [Spring Framework](https://spring.io/projects/spring-framework)-ről.

További információ a [Spring boot](https://spring.io/projects/spring-boot)-ről.

További információ a [Spring-adatforrásokról](https://spring.io/projects/spring-data).