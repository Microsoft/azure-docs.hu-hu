---
title: Indexelés kezelése a MongoDB-hez készült Azure Cosmos DB API-ban
description: Ez a cikk áttekintést nyújt a Azure Cosmos DB indexelési képességeiről a Azure Cosmos DB API-MongoDB való használatával
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/02/2021
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 8d19a5dadffdfa26ccb2d84e6dab278ad272c7b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658046"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Indexelés kezelése a MongoDB-hez készült Azure Cosmos DB API-ban
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A MongoDB Azure Cosmos DB API-ját kihasználhatja Azure Cosmos DB alapvető index-felügyeleti képességeinek kihasználásához. Ez a cikk azt ismerteti, hogyan adhat hozzá indexeket a Azure Cosmos DB API-MongoDB való használatával. Áttekintheti a [Azure Cosmos db indexelésének áttekintését](index-overview.md) is, amely az összes API-ra vonatkozik.

## <a name="indexing-for-mongodb-server-version-36-and-higher"></a>A MongoDB Server 3,6-es és újabb verziójának indexelése

A MongoDB Server 3.6-os verziójának Azure Cosmos DB API-je automatikusan indexeli a `_id` mezőt, amely nem hagyható el. Automatikusan kikényszeríti a mező egyediségét `_id` . A Azure Cosmos DB API-MongoDB a horizontális felskálázás és az indexelés külön fogalmakat mutat be. Nem kell indexelni a Szilánk-kulcsot. Azonban a dokumentumban lévő többi tulajdonsághoz hasonlóan, ha ez a tulajdonság a lekérdezésekben gyakran használt szűrő, javasoljuk, hogy indexelje a Szilánk kulcsát.

További mezők indexeléséhez a MongoDB indexkezelési parancsait használja. Ahogy a MongoDB-ben, Azure Cosmos DB API-ját a MongoDB automatikusan indexeli a `_id` mezőt. Ez az automatikus indexelési szabályzat különbözik az Azure Cosmos DB SQL API-tól, amely alapértelmezés szerint az összes mezőt indexeli.

A rendezés lekérdezésre való alkalmazásához létre kell hoznia egy indexet a rendezési műveletben használt mezőkön.

### <a name="editing-indexing-policy"></a>Indexelési házirend szerkesztése

Azt javasoljuk, hogy az indexelési szabályzatot a Azure Portalon belüli Adatkezelő szerkessze.
. Az indexelési házirend szerkesztőjéből egyetlen mezőt és helyettesítő karaktert adhat hozzá a Adatkezelő:

:::image type="content" source="./media/mongodb-indexing/indexing-policy-editor.png" alt-text="Indexelő házirend-szerkesztő":::

> [!NOTE]
> Összetett indexek nem hozhatók létre a Adatkezelő indexelési házirend szerkesztőjével.

## <a name="index-types"></a>Indextípusok

### <a name="single-field"></a>Egyetlen mező

Létrehozhat indexeket bármely egyetlen mezőben. Az egymezős index rendezési sorrendje nem számít. A következő parancs létrehoz egy indexet a mezőhöz `name` :

`db.coll.createIndex({name:1})`

A Azure Portalban ugyanaz az egymezős index is létrehozható `name` :

:::image type="content" source="./media/mongodb-indexing/add-index.png" alt-text="Név index hozzáadása az indexelési házirend szerkesztőjében":::

Az egyik lekérdezés több egymezős indexet használ, ahol elérhető. Egy tárolón legfeljebb 500 egymezős index hozható létre.

### <a name="compound-indexes-mongodb-server-version-36"></a>Összetett indexek (MongoDB Server 3.6 +)

A MongoDB Azure Cosmos DB API-ját a 3,6-es és 4,0-as verziójú átviteli protokollt használó fiókok összetett indexeit támogatja. Akár nyolc mezőt is hozzáadhat egy összetett indexben. A MongoDB eltérően csak akkor érdemes összetett indexet létrehozni, ha a lekérdezésnek egyszerre több mezőn kell rendeznie. Több olyan szűrővel rendelkező lekérdezések esetén, amelyek nem szükségesek a rendezéshez, egyetlen összetett index helyett hozzon létre több egymezős indexet. 

> [!NOTE]
> Beágyazott tulajdonságokon vagy tömbökön nem hozhatók létre összetett indexek.

A következő parancs létrehoz egy összetett indexet a mezőkön `name` , és `age` :

`db.coll.createIndex({name:1,age:1})`

Az összetett indexek segítségével egyszerre több mezőn rendezheti a műveleteket, ahogy az az alábbi példában is látható:

`db.coll.find().sort({name:1,age:1})`

Az előző összetett index használatával a lekérdezéseket az összes mező ellentétes rendezési sorrendjével is hatékonyan rendezheti. Bemutatunk egy példát:

`db.coll.find().sort({name:-1,age:-1})`

Az összetett indexben lévő elérési utak sorrendjének azonban pontosan egyeznie kell a lekérdezéssel. Az alábbi példa egy olyan lekérdezést mutat be, amely további összetett indexet igényel:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> Az összetett indexeket csak az eredmények rendezésére szolgáló lekérdezésekben használják. Olyan lekérdezéseknél, amelyeknek nem kell rendezniük a több szűrőt, hozzon létre Soklábú egyetlen mező indexeket.

### <a name="multikey-indexes"></a>Multikey indexek

A Azure Cosmos DB multikey indexeket hoz létre a tömbökben tárolt tartalom indexeléséhez. Ha Array értéket tartalmazó mezőt indexel, Azure Cosmos DB automatikusan indexeli a tömb minden elemét.

### <a name="geospatial-indexes"></a>Térinformatikai indexek

Számos térinformatikai operátor kihasználja a térinformatikai indexeket. Jelenleg Azure Cosmos DB API-MongoDB támogatja az `2dsphere` indexeket. Az API még nem támogatja az `2d` indexeket.

Az alábbi példa egy térinformatikai index létrehozását szemlélteti a `location` mezőn:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Szöveges indexek

Azure Cosmos DB API-MongoDB jelenleg nem támogatja a szöveges indexeket. A karakterláncokra vonatkozó szöveges keresési lekérdezésekhez az [Azure Cognitive Search](../search/search-howto-index-cosmosdb.md) integrációját kell használnia Azure Cosmos DBokkal. 

## <a name="wildcard-indexes"></a>Helyettesítő karakterek indexei

Használhat helyettesítő indexeket az ismeretlen mezőkkel kapcsolatos lekérdezések támogatásához. Tegyük fel, hogy van egy gyűjteménye, amely a családokkal kapcsolatos információkat tárol.

Íme egy példa a gyűjteményben szereplő dokumentum részeként:

```json
"children": [
   {
     "firstName": "Henriette Thaulow",
     "grade": "5"
   }
]
```

Íme egy másik példa, ezúttal egy kis mértékben eltérő tulajdonságokkal `children` :

```json
"children": [
    {
     "familyName": "Merriam",
     "givenName": "Jesse",
     "pets": [
         { "givenName": "Goofy" },
         { "givenName": "Shadow" }
         ]
   },
   {
     "familyName": "Merriam",
     "givenName": "John",
   }
]
```

Ebben a gyűjteményben a dokumentumok számos különböző tulajdonsággal rendelkezhetnek. Ha a tömbben lévő összes értéket szeretné indexelni `children` , két lehetőség közül választhat: hozzon létre külön indexeket minden egyes tulajdonsághoz, vagy hozzon létre egy helyettesítő karaktert a teljes `children` tömbhöz.

### <a name="create-a-wildcard-index"></a>Helyettesítő karakteres index létrehozása

A következő parancs egy helyettesítő karaktert hoz létre bármely tulajdonságon belül `children` :

`db.coll.createIndex({"children.$**" : 1})`

**A MongoDB eltérően a helyettesítő karakterek több mezőt is támogatnak a lekérdezési predikátumokban**. Ha egyetlen helyettesítő karaktert használ, nem lehet különbséget adni a lekérdezési teljesítményben, és nem kell külön indexet létrehoznia az egyes tulajdonságokhoz.

A következő típusú indexeket hozhatja létre helyettesítő szintaxis használatával:

* Egyetlen mező
* Térinformatikai

### <a name="indexing-all-properties"></a>Az összes tulajdonság indexelése

A következő módon hozhat létre helyettesítő karaktert az összes mezőhöz:

`db.coll.createIndex( { "$**" : 1 } )`

Helyettesítő karakteres indexeket is létrehozhat a Azure Portal Adatkezelő használatával:

:::image type="content" source="./media/mongodb-indexing/add-wildcard-index.png" alt-text="Helyettesítő karakteres index hozzáadása az indexelési házirend szerkesztőjében":::

> [!NOTE]
> Ha most kezdi a fejlesztést, **javasoljuk,** hogy az összes mezőhöz egy helyettesítő karakteres indexszel kezdjen. Ez egyszerűbbé teheti a fejlesztést, és egyszerűbbé teszi a lekérdezések optimalizálását.

A sok mezővel rendelkező dokumentumok esetében az írási és a frissítési műveletek esetében magas kérési egység (RU) díjat számítunk fel. Ezért ha nagy írási szintű számítási feladattal rendelkezik, a helyettesítő karakteres indexek helyett egyéni indexeket kell választania.

### <a name="limitations"></a>Korlátozások

A helyettesítő karakteres indexek nem támogatják a következő típusú indexeket vagy tulajdonságokat:

* Összetett
* TTL
* Egyedi

A **MongoDB-től eltérően** Azure Cosmos db API-ját a MongoDB esetében **nem** használhat helyettesítő indexeket a következőhöz:

* Több megadott mezőt tartalmazó helyettesítő index létrehozása

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection " :
          {
             "children.givenName" : 1,
             "children.grade" : 1
          }
      }
  )
  ```

* Több megadott mezőt nem tartalmazó helyettesítő index létrehozása

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection" :
          {
             "children.givenName" : 0,
             "children.grade" : 0
          }
      }
  )
  ```

Másik lehetőségként létrehozhat több helyettesítő indexet.

## <a name="index-properties"></a>Index tulajdonságai

A következő műveletek közösek a 4,0-es és korábbi verziókat kiszolgáló fiókok esetében. További információ a [támogatott indexekről és az indexelt tulajdonságokról](mongodb-feature-support-40.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Egyedi indexek

Az [egyedi indexek](unique-keys.md) akkor hasznosak, ha a két vagy több dokumentum nem tartalmaz azonos értéket az indexelt mezőkhöz.

> [!IMPORTANT]
> Egyedi indexek csak akkor hozhatók létre, ha a gyűjtemény üres (nem tartalmaz dokumentumokat).

A következő parancs létrehoz egy egyedi indexet a mezőhöz `student_id` :

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 4
}
```

A többrészes gyűjtemények esetében meg kell adnia a szegmens (partíció) kulcsot egy egyedi index létrehozásához. Ez azt jelenti, hogy a megosztott gyűjteményekben esetében az összes egyedi index összetett index, és az egyik mező a partíciókulcs.

A következő parancsok a ```coll``` ```university``` mezőkben egyedi indextel (a szegmens kulcs) hoznak létre egy többszegmenses gyűjteményt (a Szilánk kulcsa) `student_id` `university` :

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "university" : 1, "student_id" : 1 }, {unique:true});
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 3,
    "numIndexesAfter" : 4
}
```

Az előző példában a záradék kihagyása ```"university":1``` hibaüzenetet ad vissza a következő üzenettel:

*nem hozható létre egyedi index a (z) {student_id: 1,0} kerettel a következő szegmens kulcs mintával: {University: 1,0}*

### <a name="ttl-indexes"></a>TTL-indexek

Ha engedélyezni szeretné a dokumentumok lejáratát egy adott gyűjteményben, létre kell hoznia egy [élettartam (TTL) indexet](../cosmos-db/time-to-live.md). A TTL index egy `_ts` értékkel rendelkező mező indexe `expireAfterSeconds` .

Példa:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Az előző parancs törli a gyűjteményben lévő összes olyan dokumentumot, ```db.coll``` amely nem lett módosítva az elmúlt 10 másodpercben.

> [!NOTE]
> A **_ts** mező a Azure Cosmos DBra vonatkozik, és nem érhető el a MongoDB-ügyfelektől. Ez egy fenntartott (rendszer) tulajdonság, amely a dokumentum utolsó módosításának időbélyegzőjét tartalmazza.

## <a name="track-index-progress"></a>Index előrehaladásának nyomon követése

A MongoDB 3.0-s verziója (Azure Cosmos DB API-je `currentOp()` ) támogatja a parancsot az index előrehaladásának nyomon követéséhez egy adatbázis-példányon. Ez a parancs egy olyan dokumentumot ad vissza, amely az adatbázis-példányon végzett folyamatban lévő műveletekkel kapcsolatos információkat tartalmaz. A `currentOp` paranccsal nyomon követheti az összes folyamatban lévő műveletet a natív MongoDB. Azure Cosmos DB API-MongoDB ez a parancs csak az indexelési művelet nyomon követését támogatja.

Az alábbi példák azt mutatják be, hogyan használható a `currentOp` parancs az index előrehaladásának nyomon követéséhez:

* Az index előrehaladásának beolvasása egy gyűjteménynél:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Az index előrehaladásának beolvasása az adatbázis összes gyűjteménye esetében:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Az index előrehaladásának beolvasása az Azure Cosmos-fiókban található összes adatbázis és gyűjtemény esetében:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Példa az index folyamatjelző kimenetére

Az index előrehaladásának részletei az aktuális indexelési művelet előrehaladásának százalékos arányát mutatják. Az alábbi példa bemutatja a kimeneti dokumentum formátumát az index különböző szakaszaiban:

* A "foo" gyűjtemény és a "sáv" adatbázisának 60 százalékos készültségű index-művelete a következő kimeneti dokumentummal fog rendelkezni. A `Inprog[0].progress.total` mező a 100 értéket jeleníti meg célként megadott készültségi százalékként.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

* Ha egy indexelési művelet csak a "foo" gyűjtemény és a "sáv" adatbázison indult el, a kimeneti dokumentum 0 százalékos előrehaladást eredményezhet, amíg el nem éri a mérhető szintet.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

* Ha a folyamatban lévő index művelet befejeződik, a kimeneti dokumentum üres műveleteket jelenít meg `inprog` .

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>Háttérben futó index frissítései

A **háttérbeli** index tulajdonsághoz megadott értéktől függetlenül az index frissítése mindig a háttérben történik. Mivel az index frissítései alacsonyabb prioritással használják a kérelmek egységeit, mint a többi adatbázis-műveletnél, az index módosításai nem eredményeznek semmilyen állásidőt az írási, frissítési és törlési műveletekhez.

Új index hozzáadásakor nincs hatása a rendelkezésre állás olvasására. A lekérdezések csak akkor használják az új indexeket, ha az index átalakítása befejeződött. Az index átalakítása során a lekérdezési motor továbbra is a meglévő indexeket fogja használni, így az indexelési módosítás megkezdése előtt meg kell figyelnie a hasonló olvasási teljesítményt az indexelési átalakítás során. Új indexek hozzáadásakor nem áll fenn a hiányos vagy inkonzisztens lekérdezési eredmények kockázata is.

Az indexek eltávolításakor és a lekérdezések azonnali futtatásakor a szűrők az eldobott indexeken vannak, az eredmények inkonzisztensek lehetnek, és nem teljesek, amíg az index átalakítása be nem fejeződik. Ha eltávolítja az indexeket, a lekérdezési motor nem biztosít konzisztens vagy teljes eredményt, amikor a lekérdezések szűrik ezeket az újonnan eltávolított indexeket. A legtöbb fejlesztő nem dobja el az indexeket, és azonnal megpróbálja lekérdezni őket, így a gyakorlatban ez a helyzet nem valószínű.

> [!NOTE]
> [Nyomon követheti az index előrehaladását](#track-index-progress).

## <a name="reindex-command"></a>Parancs újraindexelése

A `reIndex` parancs a gyűjtemény összes indexét újra létrehozza. A legtöbb esetben ez szükségtelen. Bizonyos ritka esetekben azonban a lekérdezés teljesítménye a parancs futtatása után is javulhat `reIndex` .

A `reIndex` parancsot a következő szintaxissal futtathatja:

`db.runCommand({ reIndex: <collection> })`

Az alábbi szintaxissal ellenőrizze, hogy szükséges-e a `reIndex` parancs futtatása:

`db.runCommand({"customAction":"GetCollection",collection:<collection>, showIndexes:true})`

Példa a kimenetre:

```
{
        "database" : "myDB",
        "collection" : "myCollection",
        "provisionedThroughput" : 400,
        "indexes" : [
                {
                        "v" : 1,
                        "key" : {
                                "_id" : 1
                        },
                        "name" : "_id_",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                },
                {
                        "v" : 1,
                        "key" : {
                                "b.$**" : 1
                        },
                        "name" : "b.$**_1",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                }
        ],
        "ok" : 1
}
```

Ha `reIndex` szükséges, a **requiresReIndex** értéke TRUE (igaz) lesz. Ha `reIndex` nem szükséges, ez a tulajdonság ki lesz hagyva.

## <a name="migrate-collections-with-indexes"></a>Gyűjtemények migrálása indexekkel

Jelenleg csak akkor hozhat létre egyedi indexeket, ha a gyűjtemény nem tartalmaz dokumentumokat. A népszerű MongoDB áttelepítési eszközök megpróbálják létrehozni az egyedi indexeket az Adatimportálás után. A probléma megkerüléséhez manuálisan is létrehozhatja a megfelelő gyűjteményeket és egyedi indexeket ahelyett, hogy engedélyezné az áttelepítési eszköz kipróbálását. (Ezt a viselkedést a ```mongorestore``` parancssorban a jelölő használatával érheti el `--noIndexRestore` .)

## <a name="indexing-for-mongodb-version-32"></a>Az MongoDB 3,2-es verziójának indexelése

Az elérhető indexelési funkciók és alapértékek eltérnek az Azure Cosmos-fiókoknál, amelyek kompatibilisek az MongoDB-os 3,2-es verziójával. Megtekintheti [a fiókja verzióját](mongodb-feature-support-36.md#protocol-support) , és [frissítheti a 3,6-es verzióra](mongodb-version-upgrade.md).

Ha az 3,2-es verziót használja, ez a szakasz a főbb különbségeket ismerteti a 3.6 + verzióban.

### <a name="dropping-default-indexes-version-32"></a>Alapértelmezett indexek eldobása (3,2-es verzió)

A MongoDB-hez készült Azure Cosmos DB API 3.5-ös verziójától eltérően a 3,2-es verzió alapértelmezés szerint minden tulajdonságot indexel. A következő parancs használatával elhúzhatja ezeket az alapértelmezett indexeket egy gyűjteményhez ( ```coll``` ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Az alapértelmezett indexek eldobása után további indexeket adhat hozzá, mint a 3.6 + verzióban.

### <a name="compound-indexes-version-32"></a>Összetett indexek (3,2-es verzió)

Az összetett indexek egy dokumentum több mezőjére vonatkozó hivatkozásokat tartalmaznak. Ha összetett indexet szeretne létrehozni, [frissítsen az 3,6-es vagy a 4,0-es verzióra](mongodb-version-upgrade.md).

### <a name="wildcard-indexes-version-32"></a>Helyettesítő karakterek indexei (3,2-es verzió)

Ha helyettesítő karaktert szeretne létrehozni, [frissítsen a 4,0-es vagy a 3,6-es verzióra](mongodb-version-upgrade.md).

## <a name="next-steps"></a>Következő lépések

* [Indexelés az Azure Cosmos DB-ben](../cosmos-db/index-policy.md)
* [Az Azure Cosmos DB automatikusan lejár az idő az élettartammal](../cosmos-db/time-to-live.md)
* A particionálás és az indexelés közötti kapcsolat megismeréséhez tekintse meg az [Azure Cosmos Container lekérdezését](how-to-query-container.md) ismertető cikket.
