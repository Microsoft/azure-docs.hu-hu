---
title: 'Azure Cosmos DB: Tömeges végrehajtó Java API, SDK & erőforrások'
description: Ismerje meg a tömeges végrehajtó Java API-ját és SDK-ját, beleértve a kiadási dátumokat, a kieső dátumokat és a tömeges végrehajtó Java SDK-Azure Cosmos DB végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2d3c7026fd221b1a17b8efe56b03b2a26358c7ab
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364386"
---
# <a name="java-bulk-executor-library-download-information"></a>Java tömeges végrehajtói kódtár: Információk letöltése
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

| | Hivatkozás/megjegyzések |
|---|---|
|**Leírás**|A tömeges végrehajtói kódtár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket hajtsanak végre Azure Cosmos DB fiókokban. A tömeges végrehajtói kódtár a BulkImport és a BulkUpdate névtereket biztosítja. A BulkImport modul képes a dokumentumok tömeges, optimalizált módon történő feltöltésére, hogy a gyűjteményhez kibocsátott átviteli sebességet a lehető legnagyobb mértékben felhasználja a rendszer. A BulkUpdate modul tömegesen frissítheti az Azure Cosmos-tárolókban lévő meglévő adatokat javításként.|
|**SDK letöltése**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Tömeges végrehajtói kódtár a GitHubon**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-dokumentáció**| [A Java API referenciadokumentációja](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Első lépések**|[Első lépések a tömeges végrehajtói kódtár Java SDK-ját használva](bulk-executor-java.md)|
|**Minimális támogatott futási idő**|[Java fejlesztői készlet (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* A DocumentAnalyzer.java kijavítása a beágyazott partíciókulcs-értékek jsonból való helyes kinyeréséhez.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Funkciók hozzáadása a BulkDelete műveletekhez adott hibák esetén való újrapróbálkozáshoz, valamint az újrapróbálható hibák listájának visszaadása a felhasználónak.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* A Cosmos SDK 2.4.7-es verziójának frissítése.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Ki lett javítva, hogy a mergeAll az "id" és a partíciókulcs értékével folytatódjon, így az "id" után elhelyezett frissített dokumentumtulajdonságok és a partíciókulcs-értékek bekerülnek a frissített elemlistába.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Frissítse az egyidejűség kezdő fokát 1-re, és hibakeresési naplókat ad hozzá a minibatch számára.