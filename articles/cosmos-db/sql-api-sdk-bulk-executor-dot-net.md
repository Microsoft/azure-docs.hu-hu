---
title: 'Azure Cosmos DB: tömeges végrehajtó .NET API, SDK &-erőforrások'
description: Ismerkedjen meg a tömeges végrehajtó .NET API-val és az SDK-val, beleértve a kiadási dátumokat, a kivonási dátumokat és a Azure Cosmos DB tömeges végrehajtó .NET SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 23c0787b45a6e3f52dae58c24a33e00437f1e09c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577189"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET tömeges végrehajtó könyvtár: letöltési információ 
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

| | Hivatkozás/megjegyzések |
|---|---|
| **Leírás**| A .NET tömeges végrehajtó függvénytár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket végezzenek Azure Cosmos DB fiókokon. Ez a függvénytár BulkImport, BulkUpdate és BulkDelete névtereket biztosít. A BulkImport modul képes a dokumentumok egy optimalizált módon történő betöltésére, így a gyűjtemény számára kiépített átviteli sebesség a maximális mértékben felhasználható. A BulkUpdate modul a meglévő, az Azure Cosmos-tárolókban tárolt adatmennyiséget javításként is frissítheti. A BulkDelete modul képes a dokumentumok tömeges törlésére olyan optimalizált módon, hogy a gyűjteményhez kiépített átviteli sebesség a maximális mértékig legyen felhasználva.|
|**SDK letöltése**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Tömeges végrehajtó könyvtár a GitHubban**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentáció**|[.NET API-referenciák dokumentációja](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Első lépések**|[Ismerkedés a tömeges végrehajtó függvénytár .NET SDK-val](bulk-executor-dot-net.md)|
| **Jelenleg támogatott keretrendszer**| Microsoft .NET Framework 4.5.2, 4.6.1 és .NET Standard 2,0 |

> [!NOTE]
> Ha tömeges végrehajtót használ, tekintse meg a [.net SDK](tutorial-sql-api-dotnet-bulk-import.md)legújabb 3. x verzióját, amely az SDK-ba épített tömeges végrehajtót tartalmaz. 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1 – előzetes verzió

* A BulkDelete válaszában rögzített TotalElapsedTime, hogy megfelelően mérje a teljes időt, beleértve az újrapróbálkozásokat is.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0 – előzetes verzió

* Módosult az SDK-függőség a következőre: >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0 – Preview2

* A Graph tömeges végrehajtó támogatása a csúcspontok és élek élettartamának elfogadásához

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0 – Preview2

* Kijavított egy problémát, amely kivételeket okozott a Azure Cosmos DB rugalmas skálázása során, ha átjáró módban fut. Ez a javítás az 1.4.1-es kiadással egyenértékű működést tesz lehetővé.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0 – Preview2

* BulkDelete-támogatás hozzáadva az SQL API-fiókokhoz a partíciós kulcs elfogadásához, a rekordok törléséhez. Ez a módosítás a 1.4.0-kiadásnak megfelelő működést tesz lehetővé.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0 – Preview2

* A .NET Standard 2,0-et támogató MongoBulkExecutor is beleértve. Ez a funkció lehetővé teszi, hogy az a 1.3.0-kiadásnak megfelelően működjön, és a .NET Standard 2,0-et a célként megadott keretrendszerként támogatja.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0 – előzetes verzió

* A .NET Standard 2,0 a támogatott cél keretrendszerek egyike, hogy a tömeges végrehajtó függvénytár működjön a .NET Core-alkalmazásokkal.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Javítva lett a BulkDeleteAsync hibája, ha az Escape-idézőjelekkel rendelkező értékek bemeneti paraméterekként lettek átadva.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Javítva lett egy olyan probléma a MongoBulkExecutor, amely a dokumentum méretét váratlanul megnövelte a kitöltés hozzáadásával és bizonyos esetekben a dokumentum maximális méretére vonatkozó korláttal.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* A BulkDeleteAsync hibája javítva, ha a gyűjtemény beágyazott partíciós kulcs elérési utakat tartalmaz.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* A MongoBulkExecutor most megvalósítja a IDisposable-t, és a használat után várhatóan el lesz távolítva.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Az SDK-verzió zárolása törölve. A csomag mostantól függ az SDK >= 2.5.1-től.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Az azonosítók rögzített kezelése a BulkImport hívásakor numerikus értékkel rendelkező POCO objektumok listájával.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* A BulkDelete válaszában rögzített TotalElapsedTime, hogy megfelelően mérje a teljes időt, beleértve az újrapróbálkozásokat is.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Rögzített magas CPU-felhasználás bizonyos helyzetekben.
* A nyomkövetés mostantól a TraceSource-t használja. A felhasználók megadhatnak figyelőket a `BulkExecutorTrace` forráshoz.
* Rögzített egy ritka forgatókönyv, amely zárolást okozhat a dokumentumok 2 MB-os méretének közelében történő küldésekor.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Frissítette a tömeges végrehajtót, hogy most a Azure Cosmos DB .NET SDK (2.4.0) legújabb verzióját használja.

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* A Graph tömeges végrehajtó támogatása a csúcspontok és élek élettartamának elfogadásához

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Kijavított egy problémát, amely kivételeket okozott a Azure Cosmos DB rugalmas skálázása során, ha átjáró módban fut.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* BulkDelete-támogatás hozzáadva az SQL API-fiókokhoz a partíciós kulcs elfogadásához, a rekordok törléséhez.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Kijavított egy problémát, amely a tömeges végrehajtó által használt felhasználói ügynök formázási hibáját okozta.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Továbbfejlesztettük a tömeges végrehajtó importálási és frissítési API-kat, hogy transzparens módon igazodjanak a Cosmos-tároló rugalmas skálázásához, ha a tárterület meghaladja a jelenlegi kapacitást a kivételek nélkül.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* A DocumentDB .NET SDK függőségének összeütközött a 2.1.3-es verzióra.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Kijavított egy problémát, amely a tömeges végrehajtót okozta, hogy JSRT hibát okozott a rögzített gyűjteményekbe való importálás során.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* A BulkDelete művelet támogatása a Azure Cosmos DB SQL API-fiókokhoz.
* A BulkImport művelet támogatása a Azure Cosmos DB API-MongoDB rendelkező fiókokhoz.
* A DocumentDB .NET SDK függőségének a 2.0.0 verziójára ütközött. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* A BulkImport művelet támogatása a Azure Cosmos DB Gremlin API-fiókokhoz.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Kisebb hibajavítás a BulkImport művelethez Azure Cosmos DB SQL API-fiókokhoz.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* A BulkImport-és BulkUpdate-műveletek támogatása a Azure Cosmos DB SQL API-fiókokhoz.

## <a name="next-steps"></a>Következő lépések

A tömeges végrehajtó Java-függvénytárat a következő cikkben tekintheti meg:

[Java tömeges végrehajtó könyvtár SDK és kiadási információk](sql-api-sdk-bulk-executor-java.md)