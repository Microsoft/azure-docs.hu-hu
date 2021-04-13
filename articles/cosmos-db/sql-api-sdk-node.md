---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & erőforrások'
description: Ismerje meg az SQL Node.js API-t és az SDK-t, beleértve a kiadási dátumokat, a kieső dátumokat és a Azure Cosmos DB Node.js SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: a3e21abe2f4ed24726256689af16b48ed6721ce8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366147"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK for SQL API: Kibocsátási megjegyzések és erőforrások
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

|Erőforrás  |Hivatkozás  |
|---------|---------|
|SDK letöltése  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-dokumentáció  |  [A JavaScript SDK referenciadokumentációja](/javascript/api/%40azure/cosmos/)
|SDK telepítési útmutatója  |  [Telepítési utasítások](https://github.com/Azure/azure-sdk-for-js)
|Közreműködés az SDK-ban | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Példák | [Node.js kódminták](sql-api-nodejs-samples.md)
| Első lépések oktatóanyag | [Első lépések a JavaScript SDK-val](sql-api-nodejs-get-started.md)
| Webalkalmazás oktatóanyag | [Webalkalmazás Node.js a Azure Cosmos DB](sql-api-nodejs-application.md)
| Az aktuálisan támogatott platform | [Node.js v12.x –](https://nodejs.org/en/blog/release/v12.7.0/) SDK 3.x.x verzió<br/>[Node.js 10.x verzió –](https://nodejs.org/en/blog/release/v10.6.0/) SDK 3.x.x verzió<br/>[Node.js.x verzió –](https://nodejs.org/en/blog/release/v8.16.0/) SDK 3.x.x verziója<br/>[Node.js 6.x verzió –](https://nodejs.org/en/blog/release/v6.10.3/) SDK 2.x.x verzió<br/>[Node.js 4.2.0-s](https://nodejs.org/en/blog/release/v4.2.0/)verzió – SDK 1.x.x verziója<br/> [Node.js.12-es](https://nodejs.org/en/blog/release/v0.12.0/)verzió – SDK 1.x.x verziója<br/> [Node.js.10-es](https://nodejs.org/en/blog/release/v0.10.0/)verzió – SDK 1.x.x verziója

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Állítsa az alapértelmezett ResponseContinuationTokenLimitInKB értéket 1 kb-re. Alapértelmezés szerint ezt 1 kb-ig korlátozjuk a hosszú fejlécek elkerülése érdekében (Node.js fejlécek globális fejlécméretkorlátja van). A felhasználó beállíthatja, hogy ez a mező hosszabb fejléceket engedélyezzen, ami segíthet a háttérnek optimalizálni a lekérdezések végrehajtását.
* A disableSSLVerification eltávolítása. Ez a lehetőség új alternatívákat ismertet a [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Partíciókulcsfejléc explicit beállításának engedélyezése initialHeaders számára
* Az package.json#-fájlok használatával megakadályozhatja a felesleges fájlok közzétételét
* Az útválasztási térkép rendezési hibája kijavítva a node+v8 régebbi verzióján
* Kijavított hiba, amikor a felhasználó részleges újrapróbálkozási beállításokat adott meg

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Megakadályozza, hogy a Webpack feloldja a szükséges modulokat.

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Kijavít egy hosszú, megoldatlan hibát, amely miatt a rendszer mindig 0-ként jelentette a ru-okat az összesítő lekérdezések esetén

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 v3 kiadás! 🎉 számos új funkciót, hibajavítást és néhány jelentős változást tartalmaz. A kiadás elsődleges céljai:

* Főbb új funkciók implementálja
  * DISTINCT-lekérdezések
  * LIMIT/OFFSET lekérdezések
  * A felhasználó megszakítható kérései
* Frissítsen a Cosmos REST API legújabb verziójára, ahol minden tároló korlátlan skálázható
* Megkönnyíti a Cosmos használatát a böngészőből
* Jobb igazodás az új Azure JS SDK-irányelvekhez

#### <a name="migration-guide-for-breaking-changes"></a>Migrálási útmutató a legfrissebb változásokhoz
##### <a name="improved-client-constructor-options"></a>Továbbfejlesztett ügyfél konstruktorbeállítások

Egyszerűsítettük a konstruktorbeállításokat:

* A masterKey új kulcs lett, és át lett helyezték a legfelső szintűre
* A korábban az options.auth alatt található tulajdonságok átkerültek a legfelső szintre

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Egyszerűsített QueryIterator API
A v2-ben számos különböző módon lehet egy lekérdezés eredményeit iterálni vagy lekérni. Megpróbáltuk leegyszerűsíteni a v3 API-t, és eltávolítani a hasonló vagy duplikált API-kat:

* Távolítsa el az iterator.next() és az iterator.current() et. Az eredmények oldalait a fetchNext() használatával lehet lekérni.
* Távolítsa el az iterator.forEach() et. Használjon inkább aszinkron iterátorokat.
* iterator.exeaz új neve iterator.fetchNext()
* iterator.toArray() átnevezve: iterator.fetchAll()
* Az oldalak mostantól megfelelő válaszobjektumok egyszerű JS-objektumok helyett
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>A rögzített tárolók particionálva vannak
A Cosmos szolgáltatás mostantól támogatja a partíciókulcsokat az összes tárolón, beleértve azokat is, amelyek korábban rögzített tárolókként voltak létrehozva. A v3 SDK a legújabb API-verzióra frissül, amely megvalósítja ezt a változást, de ez nem fog megszakadni. Ha nem ad meg partíciókulcsot a műveletekhez, alapértelmezés szerint egy olyan rendszerkulcsot ad meg, amely az összes meglévő tárolóval és dokumentummal működik.

##### <a name="upsert-removed-for-stored-procedures"></a>Az Upsert el lett távolítva a tárolt eljárásokhoz
Korábban az upsert engedélyezve volt a nem particionált gyűjtemények esetén, de az API-verziófrissítéssel az összes gyűjtemény particionálva lett, ezért teljesen eltávolítottuk.

##### <a name="item-reads-will-not-throw-on-404"></a>Az elem beolvassa a 404-es elemet
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Alapértelmezett többrépontos írás
Az SDK mostantól alapértelmezés szerint több régióba ír, ha a Cosmos-konfiguráció támogatja azt. Ez korábban a feliratkozás viselkedése volt.

##### <a name="proper-error-objects"></a>Megfelelő hibaobjektumok
A sikertelen kérések mostantól megfelelő Hibát vagy Hiba alosztályokat jeleznek. Korábban egyszerű JS-objektumokat tartalmaztak.

#### <a name="new-features"></a>Új funkciók
##### <a name="user-cancelable-requests"></a>Felhasználó által megszakítható kérések
A belső beolvasási művelet lehetővé teszi, hogy a böngésző AbortController API-ját használjuk a felhasználó által megszakítható műveletek támogatásához. Olyan műveletek esetén, amelyekben több kérelem is potenciálisan folyamatban van (például több partícióra vonatkozó lekérdezések), a műveletre vonatkozó összes kérés megszakad. A modern böngészőfelhasználók már most is az AbortController vezérlővel fognak rendelkezik. Node.js felhasználóknak polyfill kódtárat kell használnia

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Átviteli sebesség beállítása a db/container create művelet részeként
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A fejléctoken generálása egy új kódtárra lett felosztva. @azure/cosmos-sign Bárki, aki a Cosmos REST API közvetlenül használhatja ezt a fejlécek kóddal való aláíráshoz, ugyanazokkal a kódokkal, mint a @azure/cosmos -ban.

##### <a name="uuid-for-generated-ids"></a>UUID generált adatokat
A v2 egyéni kóddal generálta az elem-címeket. Áttértünk a jól ismert és karbantartott közösségi könyvtárra.

##### <a name="connection-strings"></a>Kapcsolati sztringek
Most már átadható a kapcsolati sztring a Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Továbbfejlesztett böngészőélmény
Bár a v2 SDK böngészőben való használata nem volt ideális megoldás. Több beépített kódtárat is ki kellett node.js, és egy olyan kötegolót kellett használnia, mint a webpack vagy aRten. A v3 SDK a böngésző felhasználói számára sokkal jobb élményt nyújt.

* A kérés belső helyére a fetch (#245)
* Puffer használatának eltávolítása (#330)
* Távolítsa el a beépített csomóponthasználatot az univerzális csomagok/API-k (#328) érdekében
* Váltás node-abort-controller (#294)

#### <a name="bug-fixes"></a>Hibajavítások
* Ajánlat olvasási és visszahozott ajánlattesztek (#224)
* Az EnableEndpointDiscovery (#207) kijavítva
* A lapszámozott eredmények hiányzó ru-inak kijavítása (#360)
* Bontsa ki az SQL lekérdezési paramétertípust (#346)
* TTL hozzáadása az ItemDefinition elemhez (#341)
* CP-lekérdezési metrikák (#311) kijavítva
* Tevékenységazonosító hozzáadása a FeedResponse (#293)
* Váltás _ts típusról sztringről számra (#252)(#295)
* A kérelemdíj-összesítés (#289) kijavítása
* Üres sztring partíciókulcsok engedélyezése (#277)
* Sztring hozzáadása ütközési lekérdezéstípushoz (#237)
* UniqueKeyPolicy hozzáadása a tárolóhoz (#234)

#### <a name="engineering-systems"></a>Mérnöki rendszerek
Nem mindig a legláthatóbb változások, de segítenek a csapatnak jobb kódot kihozni, gyorsabban.

* Összesítés használata éles buildek (#104)
* Frissítés Typescript 3.5-re (#327)
* Konvertálás TS-projekthivatkozásokká. Tesztmappa kinyerése (#270)
* A noUnusedLocals és a noUnusedParameters (#275) engedélyezése
* Azure Pipelines YAML CI-buildek számára (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* A kód nem változik. Kijavítottunk egy problémát, amely miatt néhány további fájlt tartalmazott a 2.1.4-es csomag.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* A regionális feladatátvétel kijavítva az újrapróbálkozási szabályzatban
* Ki lett javítva a ChangeFeed hasMoreResults tulajdonsága
* Fejlesztői függőség frissítései
* Új PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Váltás _ts típusról számra
* Alapértelmezett indexelési tesztek kijavítása
* A uniqueKeyPolicy visszaportolása v2-re
* Bemutató és bemutató hibakeresési javítások

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* A v3 ágból származó backportajánlat-javítások
* Javítva a hiba az executeNext() típusú aláírásban
* Elírási javítások

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Felépítés átrendezve. Lehetővé teszi az SDK-verzió lehúzását a build időben.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Új funkciók
* ChangeFeed-támogatás hozzáadva (#196)
* Többpolygonos adattípus hozzáadva az indexeléshez (#191)
* Adja hozzá a "key" tulajdonságot a konstruktorhoz a masterKey (#202) aliasaként

#### <a name="fixes"></a>Javítások
* Ki lett javítva a hiba, amely miatt a next() helytelen értéket adott vissza az iterátoron

#### <a name="engineering-improvements"></a>Mérnöki fejlesztések
* Integrációs teszt hozzáadása TypeScript-használathoz (#199)
* Telepítés engedélyezése közvetlenül a GitHubról (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Csatoló hozzáadása a csomópont ügynöktípushoz. A TypeScript-felhasználóknak már nem kell @types/node függőségként telepíteniük
* Az előnyben részesített helyek mostantól megfelelően be vannak állítva
* A közreműködői dokumentáció fejlesztései
* Különböző elírási javítások

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Javítja a 2.0.3-as kiadásban bevezetett típusdefiníciós problémát

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Függőség `big-integer` eltávolítása
* Váltson referencia-irányelvekre az AsyncIterable típushoz. A TypeScript-felhasználóknak többé nem kell testre szabni a "lib" beállítást.
* Elírási javítások

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* A readme hivatkozások kijavítása

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Az újrapróbálkozási felület implementációja kijavítva

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* A JavaScript SDK 2.0.0-s verziójának GA verziója
* Többrépontos írások támogatása.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* A nyilvános előzetes verzióhoz készült JavaScript SDK 2.0.0-s verziójának RC1-es verziója.
* Új objektummodell, amely legfelső szintű CosmosClient objektumokkal és metódusokkal van felosztva a releváns adatbázis-, tároló- és elemosztályok között. 
* Ígéretek [támogatása.](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises) 
* TypeScriptre konvertált SDK.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* Az npm dokumentációja rögzített.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* A csatlakozási problémák alapértelmezett újrabeállítási újrabeállítási támogatása hozzáadva.
* A gyűjtemény változáscsatornája olvasásának támogatása.
* Kijavítottuk a munkamenetkonzisztencia-hibát, amely időnként "az olvasási munkamenet nem érhető el" hibát okozta.
* A lekérdezési metrikák támogatása hozzáadva.
* Módosította a HTTP-ügynök kapcsolatok maximális számát.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Frissítve lett a dokumentáció, hogy Azure Cosmos DB azure documentdb helyett hivatkozni.
* A ProxyUrl beállítás támogatása hozzáadva a ConnectionPolicyben.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Kisebb javítás a kis- és nagybetűket megkülönböztető fájlrendszerek esetében.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Támogatja a munkamenet-konzisztenciát.
* Ehhez az SDK-verzióhoz az [Azure Cosmos DB legújabb verziója szükséges.](https://aka.ms/cosmosdb-emulator)

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Többpartíciós lekérdezések felosztása.
* Támogatja az erőforrás-hivatkozásokat kezdő és záró perjelekkel (és a megfelelő tesztekkel).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    az npm dokumentációja rögzített.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Kijavítottunk egy hibát az executeStoredProcedure-ban, amely miatt az érintett dokumentumok speciális Unicode-karaktereket (LS, PS) tartalmaztak.
* Kijavítottunk egy hibát a partíciókulcsban Unicode-karakterekkel szereplő dokumentumok kezelése során.
* Ki van javítva a gyűjtemények médianévvel való létrehozásának támogatása. GitHub-probléma #114.
* Ki van javítva az engedélyengedélyezési jogkivonat támogatása. GitHub-probléma #178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Új konzisztenciaszint [támogatása](consistency-levels.md) ConsistentPrefix néven.
* Az UriFactory támogatása hozzáadva.
* Kijavítottunk egy Unicode-támogatási hibát. GitHub-probléma #171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Az összesítési lekérdezések (COUNT, MIN, MAX, SUM és AVG) támogatása hozzáadva.
* A több partícióra vonatkozó lekérdezések párhuzamosság-fokának szabályozására vonatkozó beállítás hozzáadva.
* Hozzá van adva a TLS-ellenőrzés letiltásának lehetősége, amikor a Azure Cosmos DB emulátoron fut.
* A particionált gyűjtemények minimális átviteli sebességének 10 100 RU/s-ről 2500 RU/s-re csökkent.
* Kijavítottuk az egypartíciós gyűjtemény folytatási tokenhibáját. GitHub-probléma #107.
* Kijavítottuk az executeStoredProcedure hibát a 0 paraméter egyetlen paraméterként való kezelésekor. GitHub-probléma #155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Kijavítva a felhasználói ügynök fejléce, hogy tartalmazza az SDK verzióját.
* Kisebb kód tisztítása.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* A TLS-ellenőrzés letiltása, ha az SDK-val célozza meg az emulátort (hostname=localhost).
* A szkriptnaplózás engedélyezése a tárolt eljárás végrehajtása során már támogatott.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* A többpartíciós párhuzamos lekérdezések támogatása.
* A TOP/ORDER BY lekérdezések támogatása a particionált gyűjteményekhez.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Újrapróbálkozási szabályzat támogatása hozzáadva a kérelmek szabályozásához. (A szabályozásos kérelmek túl nagy kérelemarányú kivételt, 429-es hibakódot kapnak.) Alapértelmezés szerint a Azure Cosmos DB alkalommal próbálkozik újra minden kéréshez a 429-es hibakód esetén, a válaszfejlécben található újrapróbálkozási idő tiszteletben állítva. Most már beállítható egy rögzített újrapróbálkozási időköz a ConnectionPolicy objektum RetryOptions tulajdonságának részeként, ha figyelmen kívül szeretné hagyni a kiszolgáló által az újrapróbálkozások között visszaadott retryAfter időt. Azure Cosmos DB a rendszer (az újrapróbálkozások számától függetlenül) legfeljebb 30 másodpercet vár az egyes kérelmekre, és a 429-es hibakóddal adja vissza a választ. Ez az idő a ConnectionPolicy objektum RetryOptions tulajdonságában is felülbírálható.
* Cosmos DB mostantól visszaadja az x-ms-throttle-retry-count és az x-ms-throttle-retry-wait-time-ms válaszfejléceket minden kérésben, amely a throttle retry count (újrapróbálkozások száma) és a kérés az újrapróbálkozások közötti kumulatív várakozási idejét jelöli.
* A Rendszer hozzáadta a RetryOptions osztályt, és felfedte a RetryOptions tulajdonságot a ConnectionPolicy osztályon, amely felülbírál néhány alapértelmezett újrapróbálkozási beállítást.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* A többrépontos adatbázisfiókok támogatása hozzáadva.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* A dokumentumokhoz a Time To Live (TTL) funkció támogatása hozzáadva.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Particionált gyűjteményeket és](partitioning-overview.md) felhasználói [teljesítményszinteket valósított meg.](performance-levels.md)

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Ki lett javítva a RangePartitionResolver.resolveForRead hiba, amely miatt nem adott vissza hivatkozásokat az eredmények hibás összefűzése miatt.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Javítva lett a hashPartitionResolver resolveForRead(): Ha nincs megadott partíciókulcs kivételt okozott, ahelyett, hogy az összes regisztrált hivatkozás listáját adná vissza.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Javítva a [#100](https://github.com/Azure/azure-documentdb-node/issues/100) – Dedikált HTTPS-ügynök: Kerülje a globális ügynök módosítását Azure Cosmos DB érdekében. Dedikált ügynököt használjon a lib összes kérése számára.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Kijavítottunk egy [#81](https://github.com/Azure/azure-documentdb-node/issues/81) – Megfelelően kezeli a kötőjeleket az adathordozó-kötőjelekben.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Kijavítottunk egy [#95](https://github.com/Azure/azure-documentdb-node/issues/95) – EventEmitter listener leak warning (EventEmitter listener szivárgási figyelmeztetése).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Kijavítottuk a [#92](https://github.com/Azure/azure-documentdb-node/issues/90) – mappa kivonatának átnevezése kivonatra a kis- és nagybetűket megkülönböztető rendszerek esetében.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* A horizontális skálázás támogatása kivonatolási és & partíciófel oldók hozzáadásával valósítható meg.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Az Upsert implementációja. Új upsertXXX metódusok a documentClient-en.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Kihagyva, hogy a verziószámokat a többi SZOFTVERDEK-hez igazodva hozza.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* A Q felosztása burkolót tesz az új adattárba.
* Frissítsen csomagfájlra az npm regisztrációs adatbázishoz.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Azonosítóalapú útválasztást valósít meg.
* Javítva a [#49](https://github.com/Azure/azure-documentdb-node/issues/49) – aktuális tulajdonságütközések a current() metódussal.

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* A térinformatikai index támogatása hozzáadva.
* Ellenőrzi az azonosító tulajdonságot az összes erőforráshoz. Az erőforrásokhoz használható adatokat nem lehet ?, /, #,  &#47;&#47;, karakter vagy szóköz karakterrel végződni.
* Új "indexátalakítási folyamat" fejléc hozzáadása a ResourceResponse erőforráshoz.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Megvalósítja a V2 indexelési szabályzatot.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Probléma [#40](https://github.com/Azure/azure-documentdb-node/issues/40) – Eslint- és rendszerkonfigurációk vannak megvalósítva a core and promise SDK-ban.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Probléma [#45](https://github.com/Azure/azure-documentdb-node/issues/45) – Az ígéretek burkolója nem tartalmaz hibafejlécet.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Megvalósítottuk az ütközések lekérdezésének képességét a readConflicts, a readConflictAsync és a queryConflicts hozzáadásával.
* Frissített API-dokumentáció.
* Probléma [#41](https://github.com/Azure/azure-documentdb-node/issues/41) – client.createDocumentAsync hiba.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Kiadási & dátumok

A Microsoft legalább **12** hónappal az SDK-kból való kivívás előtt értesítést küld, hogy zökkenőmentes legyen az átállás egy újabb/támogatott verzióra. Új szolgáltatásokkal, funkciókkal és optimalizálásokkal csak az aktuális SDK bővül, ezért azt javasoljuk, hogy a mindig lehető leghamarabb frissítsen a legújabb SDK-verzióra.

| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019. július 26. |--- |
| [3.0.4](#3.0.4) |2019. július 22. |--- |
| [3.0.3](#3.0.3) |2019. július 17. |--- |
| [3.0.2](#3.0.2) |2019. július 9. |--- |
| [3.0.0](#3.0.0) |2019. június 28. |--- |
| [2.1.5](#2.1.5) |2019. március 20. |--- |
| [2.1.4](#2.1.4) |2019. március 15. |--- |
| [2.1.3](#2.1.3) |2019. március 8. |--- |
| [2.1.2](#2.1.2) |2019. január 28. |--- |
| [2.1.1](#2.1.1) |2018. december 5. |--- |
| [2.1.0](#2.1.0) |2018. december 4. |--- |
| [2.0.5](#2.0.5) |2018. november 7. |--- |
| [2.0.4](#2.0.4) |2018. október 30. |--- |
| [2.0.3](#2.0.3) |2018. október 30. |--- |
| [2.0.2](#2.0.2) |2018. október 10. |--- |
| [2.0.1](#2.0.1) |2018. szeptember 25. |--- |
| [2.0.0](#2.0.0) |24, 2018. szeptember |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018. augusztus 2. |--- |
| [1.14.4](#1.14.4) |2018. május 3. |2020. augusztus 30. |
| [1.14.3](#1.14.3) |2018. május 3. |2020. augusztus 30. |
| [1.14.2](#1.14.2) |2017. december 21. |2020. augusztus 30. |
| [1.14.1](#1.14.1) |2017. november 10. |2020. augusztus 30. |
| [1.14.0](#1.14.0) |2017. november 9. |2020. augusztus 30. |
| [1.13.0](#1.13.0) |2017. október 11. |2020. augusztus 30. |
| [1.12.2](#1.12.2) |2017. augusztus 10. |2020. augusztus 30. |
| [1.12.1](#1.12.1) |2017. augusztus 10. |2020. augusztus 30. |
| [1.12.0](#1.12.0) |2017. május 10. |2020. augusztus 30. |
| [1.11.0](#1.11.0) |2017. március 16. |2020. augusztus 30. |
| [1.10.2](#1.10.2) |2017. január 27. |2020. augusztus 30. |
| [1.10.1](#1.10.1) |2016. december 22. |2020. augusztus 30. |
| [1.10.0](#1.10.0) |2016. október 3. |2020. augusztus 30. |
| [1.9.0](#1.9.0) |2016. július 7. |2020. augusztus 30. |
| [1.8.0](#1.8.0) |2016. június 14. |2020. augusztus 30. |
| [1.7.0](#1.7.0) |2016. április 26. |2020. augusztus 30. |
| [1.6.0](#1.6.0) |2016. március 29. |2020. augusztus 30. |
| [1.5.6](#1.5.6) |2016. március 8. |2020. augusztus 30. |
| [1.5.5](#1.5.5) |2016. február 2. |2020. augusztus 30. |
| [1.5.4](#1.5.4) |2016. február 1. |2020. augusztus 30. |
| [1.5.2](#1.5.2) |2016. január 26. |2020. augusztus 30. |
| [1.5.2](#1.5.2) |2016. január 22. |2020. augusztus 30. |
| [1.5.1](#1.5.1) |2016. január 4. |2020. augusztus 30. |
| [1.5.0](#1.5.0) |2015. december 31. |2020. augusztus 30. |
| [1.4.0](#1.4.0) |2015. október 6. |2020. augusztus 30. |
| [1.3.0](#1.3.0) |2015. október 6. |2020. augusztus 30. |
| [1.2.2](#1.2.2) |2015. szeptember 10. |2020. augusztus 30. |
| [1.2.1](#1.2.1) |2015. augusztus 15. |2020. augusztus 30. |
| [1.2.0](#1.2.0) |2015. augusztus 05. |2020. augusztus 30. |
| [1.1.0](#1.1.0) |2015. július 9. |2020. augusztus 30. |
| [1.0.3](#1.0.3) |2015. június 4. |2020. augusztus 30. |
| [1.0.2](#1.0.2) |2015. május 23. |2020. augusztus 30. |
| [1.0.1](#1.0.1) |2015. május 15. |2020. augusztus 30. |
| [1.0.0](#1.0.0) |2015. április 8. |2020. augusztus 30. |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.