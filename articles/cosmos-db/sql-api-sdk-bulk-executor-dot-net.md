---
title: 'Az Azure Cosmos DB: Tömeges végrehajtó .NET API, SDK és -erőforrások'
description: Mindent megtudhat a tömeges végrehajtó .NET API-t és az SDK kiadási dátum, kivezetési dátum és az Azure Cosmos DB tömeges végrehajtó .NET SDK minden verziója közötti végzett módosításokat.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 74eddadd7fd967daa1eebb9d7cb223fdc708025f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471423"
---
# <a name="net-bulk-executor-library-download-information"></a>Tömeges végrehajtó .NET könyvtár: Adatok letöltése 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Leírás**| A tömeges végrehajtó kódtár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveletek végrehajtása az Azure Cosmos DB-fiókokhoz. Tömeges végrehajtó kódtár BulkImport BulkUpdate és BulkDelete névterek biztosít. A modul tömegesen BulkImport dokumentumok betöltését optimalizált módon, hogy a legnagyobb mértékben használja fel a gyűjteményhez kiosztott átviteli sebesség. A modul tömegesen BulkUpdate frissíti a meglévő adatok az Azure Cosmos DB-tárolók javítások. A BulkDelete modul tömegesen delete dokumentumok optimalizált módon, hogy a legnagyobb mértékben használja fel a gyűjteményhez kiosztott átviteli sebesség.|
|**SDK letöltése**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **BulkExecutor könyvtár a Githubon**| [GitHubon](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentáció**|[.NET API dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Első lépések**|[A tömeges végrehajtó tár .NET SDK használatának első lépései](bulk-executor-dot-net.md)|
| **Aktuális támogatott keretrendszer**| A Microsoft .NET-keretrendszer 4.5.2-es, 4.6.1 és a .NET Standard 2.0 |

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Támogatás hozzáadva a graph tömeges végrehajtó fogadására a csúcsok és élek élettartam

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Kijavítva egy probléma, amely során rugalmas méretezés az Azure Cosmos DB átjáró módban való futtatáskor kivételt okozott. Javítás a funkcionális szempontból egyenértékű 1.4.1 teszi kiadás.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Hozzáadott BulkDelete támogatja az SQL API-fiókok, fogadja el a partíciós kulcs, a dokumentum azonosítója rekordok törléséhez. Ez a változás lehetővé teszi funkcionális szempontból egyenértékű 1.4.0-s kiadás.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Például MongoBulkExecutor használatával a .NET Standard 2.0 támogatása. A szolgáltatás révén funkcionális szempontból egyenértékű 1.3.0 kiadást is támogató .NET Standard 2.0 célkeretrendszerként.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Hozzáadott .NET Standard 2.0 a támogatott keretrendszerek egyik, a .NET Core-alkalmazások használata BulkExecutor könyvtárat.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* A tömeges végrehajtó használatára az Azure Cosmos DB .NET SDK (2.4.0) legújabb verziójának frissítése

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Támogatás hozzáadva a graph tömeges végrehajtó fogadására a csúcsok és élek élettartam

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Kijavítva egy probléma, amely során rugalmas méretezés az Azure Cosmos DB átjáró módban való futtatáskor kivételt okozott.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Hozzáadott BulkDelete támogatja az SQL API-fiókok, fogadja el a partíciós kulcs, a dokumentum azonosítója rekordok törléséhez.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Kijavítva egy probléma, amely egy formázási problémát az okozza, a BulkExecutor által használt felhasználói ügynök.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Kérés érkezett fokozása BulkExecutor importálás és frissítés API-k rugalmas skálázás Cosmos DB-tárolón, ha tárolási nélkül kivételeket dob meghaladja a jelenlegi kapacitás transzparens módon igazíthatja.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* A DocumentDB .NET SDK függőséget 2.1.3 verzió felfelé bumped.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Kijavítva egy probléma, amely BulkExecutor állíthatunk munkába JSRT hiba történt, miközben a gyűjtemények importálása a rögzített okozott.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Azure Cosmos DB SQL API-fiókok BulkDelete művelet támogatása.
* Támogatás hozzáadva a BulkImport művelet fiókok az Azure Cosmos DB API a mongodb-hez.
* Bumped fel a DocumentDB .NET SDK 2.0.0-s verziójával függőséget. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2-es

* Azure Cosmos DB Gremlin API-fiókok BulkImport művelet támogatása.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Az Azure Cosmos DB SQL API-fiókok BulkImport művelet kisebb hibajavítás.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Azure Cosmos DB SQL API-fiókok BulkImport és BulkUpdate műveletek támogatása.

## <a name="next-steps"></a>További lépések

A tömeges végrehajtó Java-kódtár kapcsolatos további információkért tekintse meg a következő cikket:

[Java tömeges végrehajtó könyvtár SDK és -kiadási információ](sql-api-sdk-bulk-executor-java.md)
