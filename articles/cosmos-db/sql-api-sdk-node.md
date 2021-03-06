---
title: 'Azure Cosmos DB: SQL Node.js API, SDK &-erőforrások'
description: Ismerkedjen meg az SQL Node.js API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB Node.js SDK egyes verziói között végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 8408cdada40bd3595f9bf7cf2c6a2de90040ec0b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217261"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK for SQL API: kibocsátási megjegyzések és erőforrások
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

|Erőforrás  |Hivatkozás  |
|---------|---------|
|SDK letöltése  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-dokumentáció  |  [A JavaScript SDK dokumentációja](/javascript/api/%40azure/cosmos/)
|SDK telepítési utasítások  |  [Telepítési utasítások](https://github.com/Azure/azure-sdk-for-js)
|Közreműködés az SDK-val | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Példák | [Node.js kód mintái](sql-api-nodejs-samples.md)
| Útmutató az első lépésekhez | [Ismerkedés a JavaScript SDK-val](sql-api-nodejs-get-started.md)
| Webalkalmazás-oktatóanyag | [Node.js Webalkalmazás létrehozása Azure Cosmos DB használatával](sql-api-nodejs-application.md)
| Jelenleg támogatott platform | [Node.js v12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK Version 3. x. x<br/>[Node.js v10. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK Version 3. x. x<br/>[Node.js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK Version 3. x. x<br/>[Node.js v6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK 2. x. x verzió<br/>[Node.js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK 1-es verziójú. x. x<br/> [Node.js v 0,12](https://nodejs.org/en/blog/release/v0.12.0/)– SDK 1-es verziójú. x. x<br/> [Node.js v 0,10](https://nodejs.org/en/blog/release/v0.10.0/)– SDK 1-es verziójú. x. x

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Állítsa be az alapértelmezett ResponseContinuationTokenLimitInKB a 1kb értékre. Alapértelmezés szerint a 1kb a hosszú fejlécek elkerülésére (Node.js globális fejléc-korláttal rendelkezik). A felhasználók megadhatják ezt a mezőt a hosszú fejlécek engedélyezéséhez, ami segíthet a háttérben a lekérdezés-végrehajtás optimalizálásában.
* Távolítsa el a disableSSLVerification. Ez a beállítás új alternatívákat tartalmaz [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* A partíciós kulcs fejlécének explicit módon történő beállításának engedélyezése a initialHeaders számára
* package.jshasználata # fájlokon a külső fájlok közzétételének megakadályozása érdekében
* Az útválasztási Térkép rendezési hibájának javítása a Node + V8 régebbi verziójában
* Kijavítja a hibát, ha a felhasználó részleges újrapróbálkozási lehetőségeket biztosít

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* A webpack használatának megakadályozása a Required nevű modulok feloldásával

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Kijavítja a hosszú ideig fennálló hibát, amelyben az RUs az összesített lekérdezések esetében 0

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 v3 kiadás! 🎉 számos új funkciót, hibajavítást és néhány megszakítási változást. A kiadás elsődleges céljai:

* Jelentős új funkciók implementálása
  * KÜLÖNBÖZŐ lekérdezések
  * Lekérdezések korlátozása/ELTOLÁSa
  * Felhasználó által megszakítható kérelmek
* A legújabb Cosmos REST API verzió frissítése, ahol az összes tároló korlátlan méretű
* A Cosmos használatának egyszerűbbé tétele a böngészőben
* Az új Azure JS SDK-irányelvek jobb összehangolása

#### <a name="migration-guide-for-breaking-changes"></a>Áttelepítési útmutató a változtatások megszüntetéséhez
##### <a name="improved-client-constructor-options"></a>Továbbfejlesztett ügyféloldali konstruktor-beállítások

A konstruktor beállításait egyszerűsítettük:

* a masterKey átnevezték a kulcsot, és áthelyezték a legfelső szintre
* Korábbi tulajdonságok a beállítások alatt. az Auth a legfelső szintre került

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
A v2-ben számos különböző módon lehet megismételni vagy beolvasni az eredményeket egy lekérdezésből. Megpróbálta leegyszerűsíteni a V3 API-t, és távolítsa el a hasonló vagy ismétlődő API-kat:

* Távolítsa el a következőt: iteráció. Next () és iteráció. Current (). A fetchNext () segítségével lekérdezheti a találati lapokat.
* Távolítsa el az iterációt. forEach (). Használjon aszinkron iterációkat.
* iterator.executeNext () átnevezve a következőre: iteráció. fetchNext ()
* az iteráció. toArray () átnevezve a következőre: iteráció. fetchAll ()
* Az oldalak mostantól megfelelő válasz-objektumok, egyszerű JS-objektumok helyett
* CONST-tároló = Client. Database (dbId). Container (containerId)

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

##### <a name="fixed-containers-are-now-partitioned"></a>A rögzített tárolók már particionálva vannak
A Cosmos szolgáltatás mostantól támogatja a partíciós kulcsokat minden tárolón, beleértve azokat is, amelyek korábban rögzített tárolóként lettek létrehozva. A v3 SDK a legújabb API-verzióra frissül, amely megvalósítja ezt a változást, de nem szakad meg. Ha nem ad meg partíciós kulcsot a műveletekhez, a rendszer alapértelmezés szerint az összes meglévő tárolóval és dokumentummal együtt használható rendszerkulcsot fogja tartalmazni.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert eltávolítva a tárolt eljárásokhoz
Korábban upsert volt engedélyezve a nem particionált gyűjtemények esetében, de az API-verzió frissítése esetén az összes gyűjtemény particionálva van, így teljes egészében eltávolították.

##### <a name="item-reads-will-not-throw-on-404"></a>Az elemek beolvasása nem fog megjelenni a 404
CONST-tároló = Client. Database (dbId). Container (containerId)

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

##### <a name="default-multi-region-write"></a>Alapértelmezett többrégiós írás
Az SDK alapértelmezés szerint több régióba is ír, ha a Cosmos-konfiguráció támogatja azt. Ez korábban nem volt engedélyezve.

##### <a name="proper-error-objects"></a>Megfelelő hibák objektumai
A sikertelen kérelmek mostantól megfelelő hibát vagy alosztályt dobjanak. Korábban egyszerű JS-objektumokat dobtak.

#### <a name="new-features"></a>Új funkciók
##### <a name="user-cancelable-requests"></a>Felhasználó által visszavonható kérelmek
A belső beolvasásra való áttérés lehetővé teszi, hogy a böngésző AbortController API használatával támogassa a felhasználó által megszakítható műveleteket. Olyan műveletek esetén, ahol több kérelem is folyamatban van (például a több partíciós lekérdezéseknél), a művelethez tartozó összes kérelem meg lesz szakítva. A modern böngésző felhasználói már rendelkeznek AbortController. Node.js felhasználóknak egy kitöltési függvénytárat kell használniuk

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Átviteli sebesség beállítása db/Container Create művelet részeként
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A fejléc-jogkivonat generációját egy új könyvtárba osztották fel @azure/cosmos-sign . Bárki, aki a Cosmos-REST API hívja fel a jelet, használhatja a fejléceket ugyanazzal a kóddal @azure/cosmos .

##### <a name="uuid-for-generated-ids"></a>A generált azonosítók UUID azonosítója
a v2 egyéni kóddal rendelkezett az elemek azonosítóinak létrehozásához. A jól ismert és karbantartott közösségi könyvtár UUID-t váltottuk ki.

##### <a name="connection-strings"></a>Kapcsolati sztringek
Mostantól lehetséges a Azure Portalból másolt kapcsolódási karakterlánc átadása:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Továbbfejlesztett böngésző-élmény
Habár a v2 SDK-t a böngészőben lehetett használni, nem volt ideális megoldás. Több node.js beépített kódtárat kell kitöltenie, és olyan köteget kell használnia, mint a webpack vagy a parcella. A v3 SDK sokkal hatékonyabban teszi elérhetővé a böngésző felhasználói számára.

* A kérések belső elemeinek cseréje a lekéréssel (#245)
* Puffer használatának eltávolítása (#330)
* Csomópontok beépített használatának eltávolítása az univerzális csomagok/API-k (#328) javára
* Váltás csomópont-megszakítási vezérlőre (#294)

#### <a name="bug-fixes"></a>Hibajavítások
* Kijavítási ajánlat – olvasási és visszahívási ajánlati tesztek (#224)
* EnableEndpointDiscovery javítása (#207)
* Hiányzó RUs kijavítása a többoldalas eredményekben (#360)
* SQL-lekérdezési paraméter típusának kibontása (#346)
* TTL hozzáadása a ItemDefinition (#341)
* A CP lekérdezési metrikáinak javítása (#311)
* Tevékenységazonosító hozzáadása a FeedResponse-hez (#293)
* Váltás _ts típus karakterláncról számra (#252) (#295)
* A kérések feltöltésének összesítése (#289)
* Üres karakterlánc-partíciós kulcsok engedélyezése (#277)
* Karakterlánc hozzáadása az ütközési lekérdezési típushoz (#237)
* UniqueKeyPolicy hozzáadása a tárolóhoz (#234)

#### <a name="engineering-systems"></a>Mérnöki rendszerek
Nem mindig a leglátványosabb változások, de segítünk a csapatnak jobb kódot kiszolgálni, gyorsabban.

* Kumulatív használata éles buildekhez (#104)
* Frissítés írógéppel 3,5 (#327)
* Konvertálás a terminálszolgáltatási projekt hivatkozásaira. Teszt mappa kibontása (#270)
* NoUnusedLocals és noUnusedParameters engedélyezése (#275)
* A CI buildek Azure-YAML (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Nem változik a kód. Kijavít egy problémát, amelyben néhány további fájl szerepel a 2.1.4-csomagban.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Regionális feladatátvétel javítása az újrapróbálkozási szabályzaton belül
* A ChangeFeed hasMoreResults tulajdonságának javítása
* Fejlesztői függőségek frissítései
* PolicheckExclusions.txt hozzáadása

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Váltás _ts típus karakterláncról számra
* Alapértelmezett indexelési tesztek javítása
* Vezetnie uniqueKeyPolicy – v2
* Javítások a bemutató és a bemutató hibakereséséhez

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* A vezetnie a v3 ág javításait kínálja
* Hibajavítás a executeNext () típusú aláírásban
* Sajtóhiba-javítások

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Készítse elő a szerkezetátalakítást. Lehetővé teszi az SDK verziójának kihúzását a felépítési időpontban.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Új funkciók
* ChangeFeed-támogatás hozzáadva (#196)
* Többsokszögű adattípus hozzáadva az indexeléshez (#191)
* A "Key" tulajdonság hozzáadása a konstruktorhoz aliasként a masterKey (#202)

#### <a name="fixes"></a>Javítások
* Javítsa ki a hibát, hogy a következő () helytelen értéket adott vissza az iterációnál

#### <a name="engineering-improvements"></a>Mérnöki fejlesztések
* Integrált teszt hozzáadása az írógéppel történő használathoz (#199)
* Közvetlen telepítés engedélyezése a GitHubról (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* A csomópont-ügynök típusához tartozó felületet adja meg. A géppel rendelkező felhasználóknak többé nem kell @types/node függőségként telepíteniük
* Az előnyben részesített helyszínek mostantól megfelelőek
* Fejlesztés a fejlesztői dokumentációban
* Különböző typo-javítások

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* A 2.0.3-ben bevezetett hibajavítások definíciós problémája

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* `big-integer`Függőség eltávolítása
* Váltson a AsyncIterable típusra vonatkozó hivatkozási irányelvekre. Az írógéppel a felhasználóknak már nem kell testreszabnia a "lib" beállítást.
* Sajtóhiba-javítások

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Readme-hivatkozások javítása

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Az újrapróbálkozási felület implementációjának javítása

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* A JavaScript SDK 2.0.0 verziója
* A többrégiós írások támogatása hozzáadva.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0 – 3
* A nyilvános előzetes verzióhoz készült JavaScript SDK 2.0.0 verziója RC1.
* Új objektummodell, amely a legfelső szintű CosmosClient és metódusokat a megfelelő adatbázis, tároló és elem osztályok között osztja szét. 
* Az [ígéretek](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)támogatása. 
* Az SDK formátuma írógéppel lett konvertálva.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* a NPM dokumentációja kijavítva.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Az alapértelmezett újrapróbálkozások támogatása a kapcsolódási problémák esetén.
* A gyűjtemény módosítási csatornájának olvasási támogatása.
* Rögzített munkamenet-konzisztenciai hiba, amely időnként az "olvasási munkamenet nem érhető el" hibát okozott.
* A lekérdezési metrikák támogatása hozzáadva.
* Módosította a http-ügynök maximális számú kapcsolatát.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Az Azure DocumentDB helyett a Azure Cosmos DB referenciára szolgáló dokumentáció frissítve.
* A proxyUrl-beállítás támogatása a ConnectionPolicy-ben.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Kis javítás a kis-és nagybetűket megkülönböztető fájlrendszerekhez.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* A munkamenet-konzisztencia támogatását adja meg.
* Az SDK-verzióhoz a [Azure Cosmos db Emulator](https://aka.ms/cosmosdb-emulator)legújabb verziójára van szükség.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Megszakított több partíciós lekérdezés felosztása.
* Támogatja az erőforrás-hivatkozáshoz vezető és záró perjelet (és a hozzájuk tartozó teszteket).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    a NPM dokumentációja kijavítva.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Kijavított egy hibát a executeStoredProcedure, ahol az érintett dokumentumok speciális Unicode-karaktereket (LS, PS) tartalmaztak.
* A partíciós kulcsban Unicode karaktereket tartalmazó dokumentumok kezelésére vonatkozó hiba javítva.
* Rögzített támogatás az adathordozó nevű gyűjtemények létrehozásához. GitHub-probléma #114.
* Az engedély-engedélyezési jogkivonat rögzített támogatása. GitHub-probléma #178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Egy új, ConsistentPrefix nevű [konzisztencia-szint](consistency-levels.md) támogatása.
* A UriFactory támogatása.
* Rögzített Unicode-támogatási hiba. GitHub-probléma #171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Az összesítési lekérdezések támogatása (DARABSZÁM, MIN., MAX., SUM és AVG).
* Ezzel a beállítással megadható a párhuzamossági fok a több partíciós lekérdezéseknél.
* A TLS-ellenőrzés letiltásának lehetősége a Azure Cosmos DB emulátoron való futtatáskor.
* A 10 100 RU/s és 2500 RU/s közötti particionált gyűjtemények minimális átviteli sebességének csökkentése.
* Kijavította a folytatási token hibáját egy partíciós gyűjteményhez. GitHub-probléma #107.
* A executeStoredProcedure-hiba javítva lett a 0 értékben, egyetlen param-ként. GitHub-probléma #155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Rögzített felhasználói ügynök fejléce, amely tartalmazza az SDK verzióját.
* Másodlagos kód karbantartása.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* A TLS-ellenőrzés letiltása, ha az SDK használatával célozza meg az emulátort (hostname = localhost).
* A parancsfájl-naplózás engedélyezésének támogatása a tárolt eljárás végrehajtása során.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* További támogatás a több partíciós párhuzamos lekérdezésekhez.
* A TOP/ORDER szintű támogatás hozzáadva a particionált gyűjtemények lekérdezései alapján.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Ismételt újrapróbálkozási szabályzat támogatása a szabályozott kérelmekhez. (A szabályozott kérelmek túl nagy kivételt kapnak, hibakód: 429.) Azure Cosmos DB alapértelmezés szerint a 429-es hibakód miatt a rendszer az egyes kérések esetében kilenc alkalommal újrapróbálkozik, a válasz fejlécében a retryAfter idő tiszteletben tartásával. A ConnectionPolicy objektum RetryOptions tulajdonságának részeként megadható a rögzített újrapróbálkozási időköz, ha figyelmen kívül hagyja az újrapróbálkozások között a kiszolgáló által visszaadott retryAfter időt. Azure Cosmos DB most legfeljebb 30 másodpercig vár minden szabályozott kérelem esetében (az újrapróbálkozások számától függetlenül), és visszaadja a 429-es hibakódú választ. Ez az idő felülbírálható a ConnectionPolicy objektum RetryOptions tulajdonságában is.
* Cosmos DB most visszaadja az x-MS-szabályozás-újrapróbálkozások száma és az x-MS-szabályozás-újrapróbálkozás-WAIT-Time-MS értéket az összes kérelemben, hogy az újrapróbálkozások számának és a kérésnek az újrapróbálkozások között megvárt kumulatív időpontját jelöli.
* A rendszer hozzáadta a RetryOptions osztályt, felfedte a RetryOptions tulajdonságot a ConnectionPolicy osztályban, amely az alapértelmezett újrapróbálkozási lehetőségek némelyikének felülbírálására használható.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* A többrégiós adatbázis-fiókok támogatása hozzáadva.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Az élettartam (TTL) szolgáltatás támogatása a dokumentumok számára.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* A [particionált gyűjtemények](partitioning-overview.md) és a [felhasználó által definiált teljesítményszint](performance-levels.md)implementálva.

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Rögzített RangePartitionResolver. resolveForRead hiba, amely miatt nem tért vissza hivatkozások az eredmények rossz Összefűzés miatt.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Rögzített hashPartitionResolver resolveForRead (): Ha nincs megadva partíciós kulcs, a rendszer az összes regisztrált hivatkozás listájának visszaadása helyett kivételt váltott ki.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Javítások [#100](https://github.com/Azure/azure-documentdb-node/issues/100) – dedikált HTTPS-ügynök: ne módosítsa a globális ügynököt Azure Cosmos db célokra. A lib összes kérelméhez használjon dedikált ügynököt.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Kijavítja a problémát [#81](https://github.com/Azure/azure-documentdb-node/issues/81) – az adathordozó-azonosítókban megfelelően kezeli a kötőjeleket.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Javítások [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter figyelő szivárgásával kapcsolatos figyelmeztetés.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Javítja a problémát [#92](https://github.com/Azure/azure-documentdb-node/issues/90) – mappa kivonatának átnevezése a kis-és nagybetűket megkülönböztető rendszerek esetében.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* A felosztási támogatás megvalósításához kivonatoló &-tartomány partíció-feloldókat adhat hozzá.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert implementálása. Új upsertXXX metódusok a documentClient-on.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* A rendszer kihagyta a verziószámok más SDK-kal való igazítását.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* A Split Q megígéri a burkolót az új tárházhoz.
* Frissítsen a csomagfájl NPM-beállításjegyzékbe.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Az azonosító alapú útválasztás implementálása.
* Javítások [#49](https://github.com/Azure/azure-documentdb-node/issues/49) – az aktuális tulajdonság ütközik az aktuális metódussal ().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* A térinformatikai index támogatása.
* Érvényesíti az összes erőforrás azonosító tulajdonságát. Az erőforrások azonosítói nem tartalmazhatják a következő karaktereket:?,/, #,  &#47;&#47;, karakter, vagy egy szóközzel végződik.
* Hozzáadja az új "index átalakítási folyamat" fejlécet a ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* A v2 indexelési házirendet implementálja.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* [#40](https://github.com/Azure/azure-documentdb-node/issues/40) által megvalósított eslint-és morog-konfigurációk kiadása a Core és az Promise SDK-ban.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Probléma [#45](https://github.com/Azure/azure-documentdb-node/issues/45) – az ígéretek burkolója nem tartalmaz hibát tartalmazó fejlécet.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* A readConflicts, a readConflictAsync és a queryConflicts hozzáadásával megvalósítható az ütközések lekérdezésének lehetősége.
* Frissített API-dokumentáció.
* Probléma [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -Client. createDocumentAsync hiba.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok

A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra. Új szolgáltatásokkal, funkciókkal és optimalizálásokkal csak az aktuális SDK bővül, ezért azt javasoljuk, hogy a mindig lehető leghamarabb frissítsen a legújabb SDK-verzióra.

| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019. július 26. |--- |
| [3.0.4](#3.0.4) |2019. július 22. |--- |
| [3.0.3](#3.0.3) |Július 17., 2019 |--- |
| [3.0.2](#3.0.2) |2019. július 9. |--- |
| [3.0.0](#3.0.0) |Június 28., 2019 |--- |
| [2.1.5](#2.1.5) |Március 20., 2019 |--- |
| [2.1.4](#2.1.4) |Március 15., 2019 |--- |
| [2.1.3](#2.1.3) |Március 8., 2019 |--- |
| [2.1.2](#2.1.2) |2019. január 28. |--- |
| [2.1.1](#2.1.1) |2018. december 5. |--- |
| [2.1.0](#2.1.0) |2018. december 4. |--- |
| [2.0.5](#2.0.5) |November 7., 2018 |--- |
| [2.0.4](#2.0.4) |Október 30-ig 2018 |--- |
| [2.0.3](#2.0.3) |Október 30-ig 2018 |--- |
| [2.0.2](#2.0.2) |Október 10., 2018 |--- |
| [2.0.1](#2.0.1) |2018. szeptember 25. |--- |
| [2.0.0](#2.0.0) |24, 2018. szeptember |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018. augusztus 2. |--- |
| [1.14.4](#1.14.4) |2018. május 03. |2020. augusztus 30-ig |
| [1.14.3](#1.14.3) |2018. május 03. |2020. augusztus 30-ig |
| [1.14.2](#1.14.2) |December 21., 2017 |2020. augusztus 30-ig |
| [1.14.1](#1.14.1) |November 10., 2017 |2020. augusztus 30-ig |
| [1.14.0](#1.14.0) |November 9., 2017 |2020. augusztus 30-ig |
| [1.13.0](#1.13.0) |Október 11.2017 |2020. augusztus 30-ig |
| [1.12.2](#1.12.2) |Augusztus 10., 2017 |2020. augusztus 30-ig |
| [1.12.1](#1.12.1) |Augusztus 10., 2017 |2020. augusztus 30-ig |
| [1.12.0](#1.12.0) |2017. május 10. |2020. augusztus 30-ig |
| [1.11.0](#1.11.0) |Március 16., 2017 |2020. augusztus 30-ig |
| [1.10.2](#1.10.2) |2017. január 27. |2020. augusztus 30-ig |
| [1.10.1](#1.10.1) |December 22., 2016 |2020. augusztus 30-ig |
| [1.10.0](#1.10.0) |Október 03., 2016 |2020. augusztus 30-ig |
| [1.9.0](#1.9.0) |2016. július 7. |2020. augusztus 30-ig |
| [1.8.0](#1.8.0) |Június 14., 2016 |2020. augusztus 30-ig |
| [1.7.0](#1.7.0) |2016. április 26. |2020. augusztus 30-ig |
| [1.6.0](#1.6.0) |Március 29., 2016 |2020. augusztus 30-ig |
| [1.5.6](#1.5.6) |Március 08., 2016 |2020. augusztus 30-ig |
| [1.5.5](#1.5.5) |Február 02, 2016 |2020. augusztus 30-ig |
| [1.5.4](#1.5.4) |Február 01., 2016 |2020. augusztus 30-ig |
| [1.5.2](#1.5.2) |2016. január 26. |2020. augusztus 30-ig |
| [1.5.2](#1.5.2) |2016. január 22. |2020. augusztus 30-ig |
| [1.5.1](#1.5.1) |2016. január 4. |2020. augusztus 30-ig |
| [1.5.0](#1.5.0) |December 31., 2015 |2020. augusztus 30-ig |
| [1.4.0](#1.4.0) |Október 06.2015 |2020. augusztus 30-ig |
| [1.3.0](#1.3.0) |Október 06.2015 |2020. augusztus 30-ig |
| [1.2.2](#1.2.2) |Szeptember 10., 2015 |2020. augusztus 30-ig |
| [1.2.1](#1.2.1) |Augusztus 15., 2015 |2020. augusztus 30-ig |
| [1.2.0](#1.2.0) |Augusztus 05, 2015 |2020. augusztus 30-ig |
| [1.1.0](#1.1.0) |Július 9., 2015 |2020. augusztus 30-ig |
| [1.0.3](#1.0.3) |Június 04., 2015 |2020. augusztus 30-ig |
| [1.0.2](#1.0.2) |2015. május 23. |2020. augusztus 30-ig |
| [1.0.1](#1.0.1) |Május 15., 2015 |2020. augusztus 30-ig |
| [1.0.0](#1.0.0) |2015. április 08. |2020. augusztus 30-ig |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.