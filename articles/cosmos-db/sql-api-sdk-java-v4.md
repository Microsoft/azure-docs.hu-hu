---
title: Azure Cosmos DB JAVA SDK v4 for SQL API kibocsátási megjegyzései és erőforrásai
description: Ismerje meg a Azure Cosmos DB SQL API-hoz és SDK-hoz készült Java SDK v4 verzióját, beleértve a kiadási dátumokat, a kiesés dátumát, valamint a Azure Cosmos DB SQL Async Java SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d31f3d1c510ffe6c3f0a739a4e41313c8c6e7cf0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364821"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: kibocsátási megjegyzések és erőforrások
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

A Azure Cosmos DB Java SDK v4 for Core (SQL) egy Async API-t és egy Sync API-t egyesül egy Maven-összetevőben. A v4 SDK jobb teljesítményt, új API-funkciókat és Async-támogatást nyújt a Project Reactor és a [Netty kódtár alapján.](https://netty.io/) A felhasználók jobb teljesítményre számíthatnak a Azure Cosmos DB Java SDK v4 és az [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) és a [Azure Cosmos DB Sync Java SDK v2 esetében.](sql-api-sdk-java.md)

> [!IMPORTANT]  
> Ezek a kibocsátási megjegyzések csak Azure Cosmos DB Java SDK 4-es verziójára vonatkozóak. Ha jelenleg a v4-esnél régebbi verziót használ, a 4-es verzióra való frissítéshez tekintse meg [Azure Cosmos DB Java SDK v4-re](migrate-java-v4-sdk.md) való áttelepítésről készült útmutatót.
>
> Íme három lépés a gyors úthoz!
> 1. Telepítse [a minimálisan támogatott Java-futásidejű JDK 8-at,](/java/azure/jdk/) hogy használni tudja az SDK-t.
> 2. A Java [SDK v4-hez](./create-sql-api-java.md) készült rövid útmutató Azure Cosmos DB, amely hozzáférést biztosít a Maven-összetevőhez, és végigvezeti az alapszintű Azure Cosmos DB kérelmeken.
> 3. Olvassa el Azure Cosmos DB Java SDK [v4](performance-tips-java-sdk-v4-sql.md) [](troubleshoot-java-sdk-v4-sql.md) teljesítményével kapcsolatos tippeket és hibaelhárítási útmutatókat az SDK alkalmazáshoz való optimalizálásához.
>
> A [Azure Cosmos DB workshopok](https://aka.ms/cosmosworkshop) és tesztkörnyezetek szintén remek forrást ként használhatók a Java SDK v4 Azure Cosmos DB használatának elsajátítása érdekében.
>

## <a name="helpful-content"></a>Hasznos tartalom

| Tartalom | Hivatkozás |
|---|---|
|**SDK letöltése**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-dokumentáció** | [A Java API referenciadokumentációja](/java/api/overview/azure/cosmosdb/client) |
|**Közreműködés az SDK-ban** | [Azure SDK for Java Central-adattár a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Első lépések** | [Rövid útmutató: Java-alkalmazás összeállítása az SQL API Azure Cosmos DB adatok kezeléséhez](./create-sql-api-java.md) <br> [GitHub-adattár gyorsindítási kóddal](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Alapszintű kódminták** | [Azure Cosmos DB: Java-példák az SQL API-hoz](sql-api-java-sdk-samples.md) <br> [GitHub-adattár mintakóddal](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolalkalmazás változáscsatornával**| [Változáscsatorna – Java SDK v4 minta](create-sql-api-java-changefeed.md) <br> [GitHub-adattár mintakóddal](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Webalkalmazás-minta**| [Webalkalmazás összeállítása a Java SDK v4 használatával](sql-api-java-application.md) <br> [GitHub-adattár mintakóddal](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Teljesítménnyel kapcsolatos tippek**| [Teljesítménnyel kapcsolatos tippek Java SDK v4-hez](performance-tips-java-sdk-v4-sql.md)| 
| **Hibaelhárítás** | [Java SDK v4-hibák elhárítása](troubleshoot-java-sdk-v4-sql.md) |
| **Áttelepítés a 4-es verzióra egy régebbi SDK-ból** | [Migrálás Java V4 SDK-ba](migrate-java-v4-sdk.md) |
| **Minimális támogatott futási idő**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB workshopok és laborok** |[Cosmos DB workshopok kezdőlapja](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Következő lépések
A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.