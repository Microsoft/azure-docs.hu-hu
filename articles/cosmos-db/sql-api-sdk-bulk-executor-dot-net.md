---
title: 'Azure Cosmos DB: Tömeges végrehajtó .NET API, SDK & erőforrások'
description: Ismerje meg a tömeges végrehajtó .NET API-ját és SDK-ját, beleértve a kiadási dátumokat, a kiesés dátumát, valamint a tömeges végrehajtó .NET SDK Azure Cosmos DB módosításait.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 99b6e06b817f98e64968615b5e652b707268fe78
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364498"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET tömeges végrehajtói kódtár: Információk letöltése 
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
| **Leírás**| A .NET tömeges végrehajtói kódtára lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket hajtsanak végre Azure Cosmos DB fiókokon. Ez a kódtár BulkImport, BulkUpdate és BulkDelete névtereket biztosít. A BulkImport modul optimalizált módon tudja tömegesen behozni a dokumentumokat, hogy a gyűjteményhez kihozott átviteli sebességet a lehető legnagyobb mértékben felhasználja a rendszer. A BulkUpdate modul tömegesen frissítheti a meglévő adatokat javításként az Azure Cosmos-tárolókban. A BulkDelete modul optimalizált módon képes tömegesen törölni a dokumentumokat, hogy a gyűjteményhez kihozott átviteli sebességet a lehető legnagyobb mértékben felhasználja a rendszer.|
|**SDK letöltése**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Tömeges végrehajtói kódtár a GitHubon**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentáció**|[A .NET API referenciadokumentációja](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Első lépések**|[Első lépések a tömeges végrehajtói kódtár .NET SDK-val](bulk-executor-dot-net.md)|
| **Jelenleg támogatott keretrendszer**| Microsoft .NET Framework 4.5.2, 4.6.1 és .NET Standard 2.0 |

> [!NOTE]
> Ha tömeges végrehajtót használ, tekintse meg a [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md)legújabb 3.x verzióját, amelynek tömeges végrehajtója be van építve az SDK-ba. 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* Ki lett javítva a TotalElapsedTime a BulkDelete válaszában, hogy helyesen mérje meg a teljes időt, beleértve az újratitkozásokat is.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-s előzetes verzió

* Az SDK függősége a >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* A gráf tömeges végrehajtója már támogatja a csúcsok és élek ttl-ének elfogadását

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Kijavítottunk egy problémát, amely kivételeket okozott a rugalmas méretezés során, Azure Cosmos DB átjáró módban való futtatáskor. Ez a javítás funkcionálisan egyenértékű az 1.4.1-es kiadással.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* BulkDelete támogatás hozzáadva az SQL API-fiókok számára a partíciókulcsok és a törlesztni kívánt dokumentumazonosító-rekordok elfogadásához. Ez a módosítás funkcionálisan egyenértékű az 1.4.0-s kiadással.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* A MongoBulkExecutor a .NET Standard 2.0 támogatásához. Ezzel a funkcióval funkcionálisan egyenértékű az 1.3.0-s kiadással, és a .NET Standard 2.0-t is támogatja célkeretrendszerként.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-s előzetes verzió

* A .NET Standard 2.0 hozzá van adva az egyik támogatott célkeretrendszerként, hogy a tömeges végrehajtói kódtár működjön a .NET Core-alkalmazásokkal.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Ki lett javítva a BulkDeleteAsync problémája, amikor az escape-idézőjeles értékek bemeneti paraméterként voltak átvéve.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Kijavítottunk egy problémát a MongoBulkExecutoron, amely a dokumentumméretet kitöltés hozzáadásával váratlanul megnövelte, és bizonyos esetekben túllépte a dokumentum maximális méretkorlátját.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Ki lett javítva a BulkDeleteAsync problémája, amikor a gyűjtemény beágyazott partíciókulcs-elérési utakkal rendelkezik.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* A MongoBulkExecutor mostantól implementálja az IDisposable-t, és a használata után el kell majd dobni.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Az SDK-verzió zárolása el lett távolítva. A csomag mostantól az SDK->= 2.5.1-es verziójától függ.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Ki van javítva az azonosítók kezelése a BulkImport numerikus értékeket tartalmazó POCO-objektumok listájával történő hívor.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* Ki lett javítva a TotalElapsedTime a BulkDelete válaszában, hogy helyesen mérje meg a teljes időt, beleértve az újratitkozásokat is.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Kijavítottuk a magas PROCESSZORhasználatot bizonyos forgatókönyvek esetében.
* A nyomkövetés most a TraceSource-t használja. A felhasználók meghatározhatnak figyelőket a `BulkExecutorTrace` forráshoz.
* Kijavítottunk egy ritka forgatókönyvet, amely zárolást okozhat a dokumentumok 2 MB-os mérethez közeli küldésekor.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Frissítve lett a tömeges végrehajtó, hogy a Azure Cosmos DB .NET SDK (2.4.0) legújabb verzióját használja

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* A gráf tömeges végrehajtója már támogatja a csúcsok és élek ttl-ének elfogadását

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Kijavítottunk egy problémát, amely kivételeket okozott a rugalmas méretezés során, Azure Cosmos DB átjáró módban való futtatáskor.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* BulkDelete támogatás hozzáadva az SQL API-fiókok számára a partíciókulcsok és a törlesztni kívánt dokumentumazonosító-rekordok elfogadásához.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Kijavítottunk egy hibát, amely formázási problémát okozott a tömeges végrehajtó által használt felhasználói ügynökben.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* A tömeges végrehajtói importálás és frissítés API-k fejlesztése, hogy átlátható módon alkalmazkodjanak a Cosmos-tároló rugalmas méretezéséhez, ha a tárterület túllépi az aktuális kapacitást, és nem jelent kivételeket.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* A DocumentDB .NET SDK-függősége a 2.1.3-as verzióra nőtt.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Kijavítottunk egy hibát, amely miatt a tömeges végrehajtó JSRT-hibát jelzett a rögzített gyűjteményekbe való importálás során.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Hozzá lett adva a BulkDelete művelet támogatása Azure Cosmos DB SQL API-fiókok esetében.
* A BulkImport művelet támogatása a MongoDB-hez Azure Cosmos DB API-val rendelkező fiókok esetében.
* A DocumentDB .NET SDK függősége a 2.0.0-s verzióra nőtt. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* A BulkImport művelet támogatása hozzáadva Azure Cosmos DB Gremlin API-fiókokhoz.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Kisebb hibajavítás az SQL API-fiókokhoz Azure Cosmos DB BulkImport művelethez.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* A BulkImport és a BulkUpdate műveletek támogatása hozzáadva Azure Cosmos DB SQL API-fiókokhoz.

## <a name="next-steps"></a>Következő lépések

A tömeges végrehajtó Java-kódtárával kapcsolatos további információkért tekintse meg a következő cikket:

[Java tömeges végrehajtói kódtár SDK és kiadási információk](sql-api-sdk-bulk-executor-java.md)