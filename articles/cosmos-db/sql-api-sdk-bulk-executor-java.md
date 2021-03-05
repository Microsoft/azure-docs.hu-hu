---
title: 'Azure Cosmos DB: tömeges végrehajtó Java API, SDK &-erőforrások'
description: Ismerkedjen meg a tömeges végrehajtó Java API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB tömeges végrehajtó Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d0d8dfe43d8d51107b226277918bb4ab9925a1da
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211821"
---
# <a name="java-bulk-executor-library-download-information"></a>Java tömeges végrehajtó könyvtár: letöltési információ
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

| |  |
|---|---|
|**Leírás**|A tömeges végrehajtó függvénytár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket végezzenek Azure Cosmos DB fiókokban. a tömeges végrehajtó függvénytár BulkImport és BulkUpdate névtereket biztosít. A BulkImport modul képes a dokumentumok egy optimalizált módon történő betöltésére, így a gyűjtemény számára kiépített átviteli sebesség a maximális mértékben felhasználható. A BulkUpdate modul a meglévő, az Azure Cosmos-tárolókban tárolt adatmennyiséget javításként is frissítheti.|
|**SDK letöltése**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Tömeges végrehajtó könyvtár a GitHubban**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-dokumentáció**| [Java API-referenciák dokumentációja](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Első lépések**|[Ismerkedés a tömeges végrehajtó függvénytár Java SDK-val](bulk-executor-java.md)|
|**Minimális támogatott futtatókörnyezet**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Javítsa ki a DocumentAnalyzer. Java fájlt, hogy megfelelően kinyerje a beágyazott partíciós kulcs értékeit a JSON-ből.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Adja hozzá a BulkDelete műveletek funkcióit, és próbálkozzon újra a hibákkal, és adja vissza azon hibák listáját, amelyeket újra lehet próbálni.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* A Cosmos SDK verziójának 2.4.7 frissítése.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Javítsa ki a "mergeAll" parancsot a "id" és a "Partition Key" érték folytatásához, hogy a rendszer az "id" és a "Partition Key" érték után elhelyezett javított dokumentum-tulajdonságokat hozzáadja a frissített elemek listájához.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Frissítse a párhuzamossági fokot 1 értékre, és adja hozzá a hibakeresési naplókat a minibatch.