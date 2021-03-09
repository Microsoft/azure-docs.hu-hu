---
title: SQL-lekérdezés metrikái Azure Cosmos DB SQL API-hoz
description: További információ a Azure Cosmos DB kérelmek SQL-lekérdezési teljesítményének kialakításáról és hibakereséséről.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 019ca26143a4879efafa973299703f0abcb21162
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488086"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>A lekérdezési teljesítmény finomhangolása az Azure Cosmos DB-vel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB SQL API-t biztosít [az adatlekérdezéshez](./sql-query-getting-started.md), séma vagy másodlagos indexek megkövetelése nélkül. Ez a cikk a következő információkat tartalmazza a fejlesztőknek:

* A Azure Cosmos DB SQL-lekérdezés végrehajtásának működésével kapcsolatos magas szintű információk
* A lekérdezési kérelem és a válasz fejlécének részletei, valamint az ügyfél-SDK beállításai
* Tippek és ajánlott eljárások a lekérdezési teljesítményhez
* Példák az SQL-végrehajtási statisztikák használatára a lekérdezési teljesítmény hibakereséséhez

## <a name="about-sql-query-execution"></a>Tudnivalók az SQL-lekérdezések végrehajtásáról

A Azure Cosmos DB tárolókban tárolja az adatok tárolását, ami bármilyen [tárterület méretére vagy a kérelmek átviteli sebességére](partitioning-overview.md)is nőhet. A Azure Cosmos DB zökkenőmentesen méretezi az adatok körét a fedezetek alatt lévő fizikai partíciók között az adatok növekedésének és a kiosztott átviteli sebesség növelésének kezeléséhez. Az SQL-lekérdezéseket bármely tárolóra kiállíthatja a REST API vagy egy támogatott [SQL SDK](sql-api-sdk-dotnet.md)-k használatával.

A particionálás rövid áttekintése: egy olyan partíciós kulcsot határoz meg, mint a "város", amely meghatározza, hogy az Adatelosztás hogyan oszlik meg a fizikai partíciók között. Az egyetlen partíciós kulcsba (például "City" = = "Seattle") tartozó adattárolók egy fizikai partíción belül tárolódnak, de általában egyetlen fizikai partíció több partíciós kulccsal rendelkezik. Ha egy partíció eléri a tárterület méretét, a szolgáltatás zökkenőmentesen két új partícióra osztja fel a partíciót, és egyenletesen osztja el a partíciós kulcsot a partíciók között. Mivel a partíciók átmenetiek, az API-k egy "partíciós tartomány" absztrakcióját használják, amely a partíciós kulcs kivonatának tartományait jelöli. 

Ha Azure Cosmos DB lekérdezést ad ki, az SDK ezeket a logikai lépéseket hajtja végre:

* Elemezze az SQL-lekérdezést a lekérdezés végrehajtási tervének meghatározásához. 
* Ha a lekérdezés tartalmaz egy szűrőt a partíciós kulcsra, például egy `SELECT * FROM c WHERE c.city = "Seattle"` partícióra irányítja át. Ha a lekérdezés nem rendelkezik szűrővel a partíciós kulcson, akkor a rendszer az összes partíción végrehajtja, és az eredmény egyesítve lesz az ügyfél oldalán.
* A lekérdezés az ügyfél-konfiguráció alapján történik az adatsorozatok és a párhuzamosak mindegyik partícióján belül. A lekérdezés minden partíción belül a lekérdezés bonyolultsága, a konfigurált oldalméret és a gyűjtemény kiépített átviteli sebessége alapján egy vagy több kör alakú körutazást is végrehajthat. Minden végrehajtás visszaadja a lekérdezés-végrehajtás által felhasznált [kérelmek](request-units.md) mennyiségét, és igény szerint lekérdezi a lekérdezés végrehajtási statisztikáit. 
* Az SDK összefoglalja a lekérdezési eredményeket a partíciók között. Ha például a lekérdezés a partíciók között RENDELÉSt is magában foglal, akkor az egyes partíciók eredményei egyesítés szerint vannak rendezve, hogy a globálisan rendezett sorrendben adja vissza az eredményeket. Ha a lekérdezés egy összesítés `COUNT` , például az egyes partíciók számát összegzi a rendszer a teljes szám létrehozásához.

Az SDK-k különböző lehetőségeket biztosítanak a lekérdezés végrehajtásához. Például a .NET-ben ezek a beállítások a osztályban érhetők el `FeedOptions` . A következő táblázat ismerteti ezeket a beállításokat, és azt, hogy azok hogyan befolyásolják a lekérdezés végrehajtási idejét. 

| Beállítás | Leírás |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Minden olyan lekérdezés esetében igaz értékre kell állítani, amely több partíción való végrehajtást igényel. Ez egy explicit jelző, amely lehetővé teszi, hogy a fejlesztési idő során tudatos teljesítménybeli kompromisszumokat hozzon. |
| `EnableScanInQuery` | Igaz értékre kell állítani, ha kikapcsolta az indexelést, de mindenképpen egy vizsgálaton keresztül szeretné futtatni a lekérdezést. Csak akkor alkalmazható, ha a kért szűrő elérési útjának indexelése le van tiltva. | 
| `MaxItemCount` | A kiszolgálónak oda-vissza nem küldött elemek maximális száma. Az-1 értékre állítva engedélyezheti, hogy a kiszolgáló kezelje az elemek számát. Vagy csökkentheti ezt az értéket, ha csak kis számú elemet szeretne beolvasni egy menettérti útvonalon. 
| `MaxBufferedItemCount` | Ez egy ügyféloldali lehetőség, amely a memória-felhasználás korlátozására szolgál a több partíciós megrendelés általi végrehajtásakor. A magasabb érték segít csökkenteni a több partíciós rendezés késését. |
| `MaxDegreeOfParallelism` | Lekérdezi vagy beállítja a párhuzamos műveletek számát az ügyféloldali lekérdezés-végrehajtás során az Azure Cosmos Database szolgáltatásban. A pozitív tulajdonságérték korlátozza az egyidejű műveletek számát a set értékre. Ha a beállítás értéke kisebb nullánál, a rendszer automatikusan meghatározza, hogy hány párhuzamos művelet fusson. |
| `PopulateQueryMetrics` | Lehetővé teszi a lekérdezés-végrehajtás különböző fázisaiban töltött idő részletes naplózását, például a fordítási időt, az indexelési ciklus idejét és a dokumentum betöltési idejét. Az Azure-támogatással megoszthatja a lekérdezési statisztikák kimenetét a lekérdezések teljesítményével kapcsolatos problémák diagnosztizálásához. |
| `RequestContinuation` | A lekérdezés végrehajtásának folytatásához adja meg a lekérdezés által visszaadott átlátszatlan folytatási tokent. A folytatási jogkivonat a lekérdezés végrehajtásához szükséges összes állapotot beágyazza. |
| `ResponseContinuationTokenLimitInKb` | Korlátozhatja a kiszolgáló által visszaadott folytatási jogkivonat maximális méretét. Előfordulhat, hogy ezt akkor kell megadnia, ha az alkalmazás gazdagépe korlátozza a válasz fejlécének méretét. Ez a beállítás növelheti a lekérdezéshez felhasznált teljes időtartamot és az RUs-t.  |

Tegyük fel például, hogy egy gyűjteményen a partíciós kulccsal megkövetelt partíciós kulcsot kell megadnia, `/city` és az átviteli sebesség 100 000 ru/s-vel van kiépítve. Ezt a lekérdezést a `CreateDocumentQuery<T>` .net-ben a következőhöz hasonló módon kérheti le:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

A fent látható SDK-kódrészlet a következő REST API kérelemnek felel meg:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Az egyes lekérdezés-végrehajtási lapok egy REST APInak felelnek meg a `POST` `Accept: application/query+json` fejlécben és a törzsben lévő SQL-lekérdezésben. Minden lekérdezés egy vagy több oda-és visszautazást tesz lehetővé a kiszolgálónak az `x-ms-continuation` ügyfél és a kiszolgáló közötti, a végrehajtás folytatására szolgáló jogkivonat használatával. A FeedOptions konfigurációs beállításait a rendszer a kérelem fejlécének formájában továbbítja a kiszolgálónak. Például a következőnek `MaxItemCount` felel meg: `x-ms-max-item-count` . 

A kérelem a következőt adja vissza (az olvashatóság érdekében csonkítva):

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

A lekérdezés által visszaadott kulcs-válasz fejlécek a következők:

| Beállítás | Leírás |
| ------ | ----------- |
| `x-ms-item-count` | A válaszban visszaadott elemek száma. Ez a megadott értéktől függ `x-ms-max-item-count` , a maximális válasz-adattartalom méretétől, a kiosztott átviteli sebességtől és a lekérdezés végrehajtási idejétől függően elférő elemek száma. |  
| `x-ms-continuation:` | A folytatási token a lekérdezés végrehajtásának folytatásához, ha további eredmények állnak rendelkezésre. | 
| `x-ms-documentdb-query-metrics` | A végrehajtás lekérdezési statisztikája. Ez egy tagolt karakterlánc, amely a lekérdezés-végrehajtás különböző fázisaiban eltöltött idő statisztikáit tartalmazza. Ha `x-ms-documentdb-populatequerymetrics` a érték van beállítva, a rendszer visszaadja `True` . | 
| `x-ms-request-charge` | A lekérdezés által felhasznált [kérelmek egységeinek](request-units.md) száma. | 

További információ a REST API kérelmek fejlécéről és lehetőségeiről: [erőforrások lekérdezése a REST API használatával](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Ajánlott eljárások a lekérdezési teljesítményhez
A következő leggyakoribb tényezők befolyásolják Azure Cosmos DB lekérdezési teljesítményt. Ebben a cikkben részletesebben ismertetjük ezeket a témákat.

| Szempont | Tipp | 
| ------ | -----| 
| Kiosztott átviteli sebesség | Adja meg a kérdéses RU-t, és ellenőrizze, hogy rendelkezik-e a szükséges kiosztott átviteli sebességgel a lekérdezésekhez. | 
| Particionálás és particionálási kulcsok | Az alacsony késés érdekében a Filter záradékban lévő Partition Key értékkel rendelkező lekérdezéseket a rendszer nem támogatja. |
| Az SDK és a lekérdezés beállításai | Kövesse az SDK ajánlott eljárásait, például a közvetlen kapcsolódást és az ügyféloldali lekérdezés-végrehajtási beállítások finomhangolását. |
| Indexelési szabályzat | Győződjön meg arról, hogy rendelkezik a lekérdezéshez szükséges indexelési útvonalakkal/házirenddel. |
| Lekérdezés-végrehajtási mérőszámok | Elemezze a lekérdezés-végrehajtási mérőszámokat a lekérdezési és adatalakzatok lehetséges újraírásának azonosításához.  |

### <a name="provisioned-throughput"></a>Kiosztott átviteli sebesség
A Cosmos DBban adattárolókat hoz létre, amelyek mindegyike fenntartott adatátviteli sebességgel (kérés egységben (RU) kifejezve) másodpercenként. Egy 1 KB-os dokumentum olvasása 1 RU, és minden művelet (beleértve a lekérdezéseket is) a bonyolultság alapján rögzített számú RUs-re van normalizálva. Ha például a tárolóhoz 1000 RU/s van kiépítve, és olyan lekérdezéssel rendelkezik, mint `SELECT * FROM c WHERE c.city = 'Seattle'` amely 5 RUs-t használ, akkor a következő műveleteket hajthatja végre (1000 ru/s)/(5 ru/Query) = 200 lekérdezés/s ilyen lekérdezések másodpercenkénti száma. 

Ha több mint 200 lekérdezést/másodpercet küld be, a szolgáltatás elkezdi a 200/s-nál nagyobb bejövő kérelmek arányának korlátozását. Az SDK-k automatikusan kezelik ezt az esetet egy leállítási/újrapróbálkozás végrehajtásával, ezért ennél a lekérdezésnél nagyobb késést tapasztalhat. A kiépített átviteli sebesség és a szükséges érték növelése javítja a lekérdezés késését és átviteli sebességét. 

További információ a kérelmek egységéről: [kérelmek egysége](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Particionálás és particionálási kulcsok
A Azure Cosmos DB általában a következő sorrendben hajtja végre a lekérdezéseket, amelyek a leggyorsabb/leghatékonyabb, lassabb/kevésbé hatékonyak. 

* Egyszeri partíciós kulcs és elem kulcsának beolvasása
* Lekérdezés szűrő záradékkal egyetlen partíciós kulcson
* Lekérdezés egyenlőség vagy Range Filter záradék nélkül bármely tulajdonsághoz
* Lekérdezés szűrő nélkül

Az összes partícióval kapcsolatban szükséges lekérdezéseknek nagyobb késésre van szükségük, és magasabb szintű RUs-t is felhasználhatnak. Mivel az egyes partíciók automatikusan indexelve vannak az összes tulajdonsággal, ebben az esetben a lekérdezés hatékonyan kiszolgálható az indexből. A Parallels lehetőségekkel gyorsabban lehet a partíciókat átölelő lekérdezéseket használni.

A particionálással és a partíciós kulcsokkal kapcsolatos további tudnivalókért lásd: [particionálás Azure Cosmos DBban](partitioning-overview.md).

### <a name="sdk-and-query-options"></a>Az SDK és a lekérdezés beállításai
Tekintse meg a [teljesítménnyel kapcsolatos tippeket](performance-tips.md) és a [teljesítménytesztet](performance-testing.md) a Azure Cosmos db legjobb ügyféloldali teljesítményének megismeréséhez. Ez magában foglalja a legújabb SDK-k használatát, a platform-specifikus konfigurációk, például a kapcsolatok alapértelmezett számának, a Garbage-gyűjtemények gyakoriságának és a könnyű kapcsolódási lehetőségek (például a Direct/TCP) használatával történő konfigurálását. 


#### <a name="max-item-count"></a>Elemek maximális száma
A lekérdezések esetében a értéke `MaxItemCount` jelentős hatással lehet a végpontok közötti lekérdezési időpontra. A kiszolgálónak küldött összes útvonal nem haladja meg a `MaxItemCount` (alapértelmezett 100 elem) értéket. Ha magasabb értékre (-1 értékre) állítja be ezt a beállítást, a lekérdezési időtartamot összességében növelni fogja a kiszolgáló és az ügyfél közötti adatváltások számának korlátozásával, különösen a nagyméretű eredményhalmaz lekérdezései esetében.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximális párhuzamossági fok
A lekérdezéseknél hangolja `MaxDegreeOfParallelism` be az alkalmazáshoz legmegfelelőbb konfigurációkat, különösen akkor, ha több partíciós lekérdezést hajt végre (szűrő nélkül a Partition-Key értéknél). `MaxDegreeOfParallelism`  meghatározza a párhuzamos feladatok maximális számát, azaz a párhuzamosan meglátogatható partíciók maximális számát. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Tegyük fel, hogy
* D = a párhuzamos feladatok alapértelmezett maximális száma (= a processzor teljes száma az ügyfélszámítógépen)
* P = felhasználó által megadott maximális számú párhuzamos feladat
* N = a lekérdezés megválaszolásához felkeresni kívánt partíciók száma

A következő szempontok arra vonatkoznak, hogy a párhuzamos lekérdezések hogyan viselkedjenek a P különböző értékeinél.
* (P = = 0) => soros mód
* (P = = 1) => legfeljebb egy feladat
* (P > 1) => perces (P, N) párhuzamos feladatok 
* (P < 1) => min (N, D) párhuzamos feladatok

Az SDK kibocsátási megjegyzései és a megvalósított osztályok és metódusok részleteiért lásd: [SQL SDK](sql-api-sdk-dotnet.md) -k

### <a name="network-latency"></a>Hálózati késés
A globális terjesztés beállításával és a legközelebbi régióhoz való kapcsolódással kapcsolatban lásd: [Azure Cosmos db globális terjesztés](tutorial-global-distribution-sql-api.md) . A hálózati késés jelentős hatással van a lekérdezési teljesítményre, ha több kör-vagy időpontot kell lekérnie a lekérdezésből. 

A lekérdezés-végrehajtási mérőszámok szakasza azt ismerteti, hogyan kérhető le a lekérdezések kiszolgáló-végrehajtási ideje ( `totalExecutionTimeInMs` ), így megkülönböztethető a lekérdezés-végrehajtás során eltöltött idő és a hálózati átvitel során töltött idő között.

### <a name="indexing-policy"></a>Indexelési szabályzat
Lásd: [indexelési házirend konfigurálása](index-policy.md) az indexelési elérési utakhoz, a típusokhoz és a módokhoz, valamint a lekérdezések végrehajtásának következményei. Alapértelmezés szerint az indexelési házirend a karakterláncok kivonatoló indexelését használja, amely az esélyegyenlőségi lekérdezések esetében érvényes, de a tartományon belüli lekérdezésekhez és sorrendekhez nem. Ha a karakterláncokhoz tartomány-lekérdezésekre van szüksége, javasoljuk, hogy adja meg a tartomány indexének típusát az összes karakterlánchoz. 

Alapértelmezés szerint a Azure Cosmos DB minden adattal automatikusan alkalmazza az indexelést. A nagy teljesítményű beszúrási forgatókönyvek esetében érdemes lehet kihagyni az elérési utakat, mivel ez csökkenti az egyes INSERT műveletekhez tartozó RU-költségeket. 

## <a name="query-execution-metrics"></a>Lekérdezés-végrehajtási mérőszámok
A lekérdezés-végrehajtás részletes mérőszámait a nem kötelező `x-ms-documentdb-populatequerymetrics` fejléc (a `FeedOptions.PopulateQueryMetrics` .net SDK-ban) átadásával szerezheti be. A ben visszaadott érték `x-ms-documentdb-query-metrics` a következő kulcs-érték párokat jelenti a lekérdezés-végrehajtás speciális hibaelhárításához. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metric | Unit (Egység) | Leírás | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | ezredmásodperc | Lekérdezés végrehajtási ideje | 
| `queryCompileTimeInMs` | ezredmásodperc | Lekérdezés fordítási ideje  | 
| `queryLogicalPlanBuildTimeInMs` | ezredmásodperc | A logikai lekérdezési terv létrehozásához szükséges idő | 
| `queryPhysicalPlanBuildTimeInMs` | ezredmásodperc | Fizikai lekérdezési terv elkészítésének ideje | 
| `queryOptimizationTimeInMs` | ezredmásodperc | A lekérdezés optimalizálása során eltelt idő | 
| `VMExecutionTimeInMs` | ezredmásodperc | A lekérdezési futtatókörnyezetben töltött idő | 
| `indexLookupTimeInMs` | ezredmásodperc | A fizikai index rétegében töltött idő | 
| `documentLoadTimeInMs` | ezredmásodperc | A dokumentumok betöltésével töltött idő  | 
| `systemFunctionExecuteTimeInMs` | ezredmásodperc | A System (beépített) függvények végrehajtásának teljes ideje (ezredmásodpercben)  | 
| `userFunctionExecuteTimeInMs` | ezredmásodperc | A felhasználó által definiált függvények végrehajtásának teljes ideje (ezredmásodperc) | 
| `retrievedDocumentCount` | count | A beolvasott dokumentumok száma összesen  | 
| `retrievedDocumentSize` | bájt | A beolvasott dokumentumok teljes mérete bájtban  | 
| `outputDocumentCount` | count | Kimeneti dokumentumok száma | 
| `writeOutputTimeInMs` | ezredmásodperc | Lekérdezés végrehajtási ideje (ezredmásodperc) | 
| `indexUtilizationRatio` | arány (<= 1) | A szűrő által egyeztetett dokumentumok számának és a betöltött dokumentumok számának aránya  | 

Az ügyfél SDK-k belsőleg több lekérdezési műveletet végezhetnek el a lekérdezés kiszolgálásához az egyes partíciók között. Ha a `x-ms-max-item-count` lekérdezés meghaladja a partíció kiosztott átviteli sebességét, vagy ha a lekérdezési adattartalom eléri a maximális méretet, vagy ha a lekérdezés eléri a rendszer által lefoglalt időtúllépési korlátot, az ügyfél több hívást is végrehajt. Az összes részleges lekérdezés-végrehajtás `x-ms-documentdb-query-metrics` az adott oldal egy részét adja vissza. 

Íme néhány példa a lekérdezésekre, és hogyan kell értelmezni a lekérdezés-végrehajtásból visszaadott metrikákat: 

| Lekérdezés | Minta metrika | Leírás | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | A beolvasott dokumentumok száma 100 + 1 a TOP záradéknak megfelelően. A lekérdezési időt többnyire a és a vizsgálat során kell kitölteni `WriteOutputTime` `DocumentLoadTime` . | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | A RetrievedDocumentCount mostantól magasabb (500 + 1 a TOP záradéknak megfelelően). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | A IndexLookupTime-ről a 0,9 MS-ot a rendszer a kulcs keresésekor tölti fel, mert az index a következő: `/N/?` . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Valamivel több idő (1,7 MS) lett elvégezve a IndexLookupTime egy tartományon belüli vizsgálat során, mert ez egy index-keresés `/N/?` . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Ugyanaz az idő `DocumentLoadTime` , mint a korábbi lekérdezések, de alacsonyabb, `WriteOutputTime` mert csak egy tulajdonságot tervezünk ki. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Körülbelül 213 MS-ot az `UserDefinedFunctionExecutionTime` UDF minden egyes értékének végrehajtásával töltöttek el `c.N` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Körülbelül 0,6 MS használatban van `IndexLookupTime` `/Name/?` . A lekérdezés végrehajtási idejének (~ 7 MS) nagy része a következőben: `SystemFunctionExecutionTime` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | A lekérdezés vizsgálatként történik, mivel a rendszer a-t használja `LOWER` , és 500-ből 2491 lekért dokumentumokat ad vissza. |


## <a name="next-steps"></a>Következő lépések
* További információ a támogatott SQL-lekérdezési operátorokról és-kulcsszavakról: [SQL-lekérdezés](sql-query-getting-started.md). 
* A kérelmek egységeit a [kérelmek egységei](request-units.md)című témakörben tekintheti meg.
* Az indexelési szabályzattal kapcsolatos további tudnivalókért lásd: [indexelési házirend](index-policy.md)