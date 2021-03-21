---
title: 'Azure Cosmos DB SQL API: Spring adatforrások v3 példák'
description: A Azure Cosmos DB SQL API-t használó gyakori feladatokhoz, például a SZIFILISZi műveletekhez használható Spring adat v3-példák a GitHubon.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 378bb891c8539a6cf3d61f6511a0f58377d2bfd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93091140"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API: Spring-alapú adatAzure Cosmos DB v3 példa
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK-példák](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-példák](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-példák](sql-api-java-sdk-samples.md)
> * [Spring-adatforrások v3 SDK-példák](sql-api-spring-data-sdk-samples.md)
> * [Node.js példák](sql-api-nodejs-samples.md)
> * [Python-példák](sql-api-python-samples.md)
> * [Azure Kódminta-katalógus](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

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

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- [Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Az [Azure-Spring-recosmos-Java-SQL-API-Samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) GitHub-adattár tartalmazza a legújabb, a szifilisz-műveleteket és a Azure Cosmos db erőforrásokkal kapcsolatos gyakori műveleteket végrehajtó alkalmazásokat. Ez a cikk a következő információkat tartalmazza:

* A példában szereplő Spring-adatok Azure Cosmos DB projektfájlok feladataira mutató hivatkozások. 
* A kapcsolódó API-referenciatartalmak hivatkozásai.

**Előfeltételek**

A mintaalkalmazás futtatásához a következőkre lesz szüksége:

* Java fejlesztői készlet 8-as verziója
* Spring-adatAzure Cosmos DB v3

Igény szerint a Maven használatával lekérheti a legújabb Spring-adatAzure Cosmos DB v3 bináris fájlokat a projektben való használatra. A Maven automatikusan hozzáadja az összes szükséges függőséget. Ellenkező esetben közvetlenül letöltheti a **pom.xml** fájlban felsorolt függőségeket, és hozzáadhatja őket a létrehozási útvonalhoz.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**A mintaalkalmazások futtatása**

A mintaadattár klónozása:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

A mintákat IDE (Eclipse, IntelliJ vagy VSCODE) vagy a parancssorból is futtathatja a Maven használatával.

Az **Application. properties tulajdonságban** ezeket a környezeti változókat be kell állítani

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

a minták olvasási/írási hozzáférésének biztosítása a fiókhoz, az adatbázisokhoz és a tárolóhoz.

Az IDE tartozhat a Spring adatminta kód végrehajtásának lehetősége. Ellenkező esetben a következő terminál-parancs használatával hajtsa végre a mintát:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>Dokumentum-SZIFILISZi példák
A [minták](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) fájl a következő feladatok elvégzését mutatja be. Ha az alábbi minták futtatása előtt szeretne többet megtudni az Azure Cosmos-dokumentumokról, tekintse meg a következő témakört: [adatbázisok, tárolók és elemek](account-databases-containers-items.md) fogalmi cikkének használata.

| Feladat | API-referencia |
| --- | --- |
| [Dokumentum létrehozása](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository. Save |
| [Dokumentum olvasása ID (azonosító) alapján](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Az összes dokumentum törlése](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository. deleteAll |

## <a name="derived-query-method-examples"></a>Példák származtatott lekérdezési módszerekre
A [minták](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) fájl a következő feladatok elvégzését mutatja be. Ha Azure Cosmos DB lekérdezéseket szeretne megtudni a következő minták futtatása előtt, hasznos lehet a [Baeldung származtatott lekérdezési módszereinek](https://www.baeldung.com/spring-data-derived-queries) beolvasása a Spring cikkben.

| [Dokumentumok lekérdezése](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository. derivedQueryMethod |

## <a name="custom-query-examples"></a>Egyéni lekérdezési példák
A [Samples (minta](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) ) fájl bemutatja, hogyan végezheti el az alábbi feladatokat az SQL-lekérdezési nyelvtan használatával. Ha többet szeretne megtudni a Azure Cosmos DB SQL-lekérdezési referenciáról az alábbi minták futtatása előtt, tekintse meg a [Azure Cosmos db SQL-lekérdezési példákat](./sql-query-getting-started.md). 


| Feladat | API-referencia |
| --- | --- |
| [Az összes dokumentum lekérdezése](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query Jegyzet |
| [Egyenlőség lekérdezése == használatával](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query Jegyzet |
| [Egyenlőtlenség lekérdezése != és NOT használatával](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query Jegyzet |
| [Lekérdezés tartományi operátorokkal, például a >, <, >=, <= használatával](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query Jegyzet |
| [Lekérdezés tartományi operátorokkal sztringekben](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query Jegyzet |
| [Lekérdezés RENDELÉSsel](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query Jegyzet |
| [Lekérdezés eltérő](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query Jegyzet |
| [Lekérdezés összesítő függvényekkel](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query Jegyzet |
| [Aldokumentumok használata](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query Jegyzet |
| [Lekérdezés dokumentumon belüli illesztésekkel](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query Jegyzet |
| [Lekérdezés string, Math és Array operátorral](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query Jegyzet |