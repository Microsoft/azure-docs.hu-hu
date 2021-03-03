---
title: Az MongoDB Azure Cosmos DB API-MongoDB való kezeléséhez szükséges bővítmény-parancsok
description: Ez a cikk azt ismerteti, hogyan használhatók a MongoDB-bővítmény parancsai a Azure Cosmos DB API-MongoDB tárolt adatkezelési szolgáltatásokhoz.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: deba6696eb71287902fa3970ed2d83d0b09ac08d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658486"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-bővítményi parancsok használata a Azure Cosmos DB API-MongoDB tárolt adatkezeléshez 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A következő dokumentum a Azure Cosmos DB API-MongoDB tartozó egyéni műveleti parancsokat tartalmazza. Ezekkel a parancsokkal létrehozhatók és beszerezhetők a [Azure Cosmos db kapacitási modellre](account-databases-containers-items.md)jellemző adatbázis-erőforrások.

A MongoDB-hez készült Azure Cosmos DB API-val élvezheti a globális elosztást, az automatikus horizontális skálázást, a magas rendelkezésre állást, Cosmos DB a késési garanciákat, az automatikus, a REST-alapú titkosítást, a biztonsági mentéseket és sok mást, miközben megőrizheti a befektetéseit a MongoDB-alkalmazásban. A MongoDB Azure Cosmos DB API-jával a nyílt forráskódú [MongoDB-ügyfelek](https://docs.mongodb.org/ecosystem/drivers)bármelyikének használatával kommunikálhat. A MongoDB Azure Cosmos DB API-je lehetővé teszi a meglévő ügyféloldali illesztőprogramok használatát az [MongoDB Wire protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)betartásával.

## <a name="mongodb-protocol-support"></a>MongoDB protokoll támogatása

A Azure Cosmos DB API-MongoDB kompatibilis a MongoDB Server 4,0, 3,6 és 3,2 verziójával. További részletekért tekintse meg a [4,0](mongodb-feature-support-40.md)-es, [3,6](mongodb-feature-support-36.md)-as és [3,2](mongodb-feature-support.md) -es támogatott funkciókat és szintaxist. 

A következő bővítmény-parancsok lehetővé teszik Azure Cosmos DB-specifikus erőforrások adatbázis-kérelmeken keresztüli létrehozását és módosítását:

* [Adatbázis létrehozása](#create-database)
* [Adatbázis frissítése](#update-database)
* [Adatbázis beolvasása](#get-database)
* [Gyűjtemény létrehozása](#create-collection)
* [Gyűjtemény frissítése](#update-collection)
* [Gyűjtemény beolvasása](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Adatbázis létrehozása

Az adatbázis-bővítmény létrehozása parancs létrehoz egy új MongoDB-adatbázist. Az adatbázis neve a parancs által beállított adatbázis-környezetből is használható `use database` . A következő táblázat a parancson belüli paramétereket ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Az egyéni parancs neve csak "CreateDatabase" lehet.      |
| `offerThroughput` | `int`  | Az adatbázison beállított kiépített átviteli sebesség. Ezt a paramétert nem kötelező megadni. |
| `autoScaleSettings` | `Object` | Az [autoskálázási mód](provision-throughput-autoscale.md)használatához szükséges. Ez az objektum az autoskálázási kapacitás üzemmódhoz tartozó beállításokat tartalmazza. Beállíthatja az `maxThroughput` értéket, amely a gyűjtemény által dinamikusan növekedő kérelmek maximális mennyiségét mutatja. |

### <a name="output"></a>Kimenet

Ha a parancs sikeres, a következő választ fogja visszaadni:

```javascript
{ "ok" : 1 }
```

Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

#### <a name="create-a-database"></a>Adatbázis létrehozása

A következő paranccsal hozhat létre egy nevű adatbázist `"test"` , amely az összes alapértelmezett értéket használja:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Ezzel a paranccsal adatbázis-szintű átviteli sebesség nélkül hozhat létre adatbázist. Ez azt jelenti, hogy az adatbázison belüli gyűjteményeknek meg kell adniuk a használni kívánt átviteli sebesség mennyiségét.

#### <a name="create-a-database-with-throughput"></a>Adatátviteli sebességű adatbázis létrehozása

Egy nevű adatbázis létrehozásához `"test"` , valamint a 1000 RUs [adatbázis-szintű](set-throughput.md#set-throughput-on-a-database) kiépített átviteli sebességének megadásához használja a következő parancsot:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Ezzel létrehoz egy adatbázist, és beállítja az átviteli sebességet. Az adatbázison belüli gyűjtemények megosztják a készlet átviteli sebességét, kivéve, ha a gyűjtemények [egy adott átviteli szinttel](set-throughput.md#set-throughput-on-a-database-and-a-container)jönnek létre.

#### <a name="create-a-database-with-autoscale-throughput"></a>Adatbázis létrehozása az autoscale átviteli sebességgel

Hozzon létre egy nevű adatbázist `"test"` , és állítsa be az 20 000 ru/s teljesítményű maximális átviteli sebességet az [adatbázis szintjén](set-throughput.md#set-throughput-on-a-database), használja a következő parancsot:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Adatbázis frissítése

Az adatbázis-kiterjesztés frissítése parancs frissíti a megadott adatbázishoz társított tulajdonságokat. Az adatbázis kiépített átviteli sebességről az autoskálázásra való módosítása csak az Azure Portalon támogatott. A következő táblázat a parancson belüli paramétereket ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Az egyéni parancs neve. A következőnek kell lennie: "UpdateDatabase".      |
|  `offerThroughput`   |  `int`       |     Az adatbázison beállítani kívánt új, kiépített átviteli sebesség, ha az adatbázis [adatbázis-szintű átviteli sebességet](set-throughput.md#set-throughput-on-a-database) használ  |
| `autoScaleSettings` | `Object` | Az [autoskálázási mód](provision-throughput-autoscale.md)használatához szükséges. Ez az objektum az autoskálázási kapacitás üzemmódhoz tartozó beállításokat tartalmazza. Beállíthatja az `maxThroughput` értéket, amely leírja a maximális mennyiségű kérési egységet, amelyet az adatbázis dinamikusan fog növelni. |

Ez a parancs a munkamenet kontextusában megadott adatbázist használja. Ez a `use <database>` parancsban használt adatbázis. Az adatbázis neve jelenleg nem módosítható ezzel a paranccsal.

### <a name="output"></a>Kimenet

Ha a parancs sikeres, a következő választ fogja visszaadni:

```javascript
{ "ok" : 1 }
```

Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Adatbázishoz társított kiépített átviteli sebesség frissítése

A következő parancs használatával frissítheti egy, a 1200 RUs nevű adatbázis kiépített átviteli sebességét `"test"` :

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Adatbázishoz társított autoskálázási teljesítmény frissítése

Az alábbi paranccsal frissítheti egy, a 20 000 RUs nevű adatbázis kiépített átviteli sebességét `"test"` , vagy átalakíthatja azt egy [autoskálázási átviteli szintre](provision-throughput-autoscale.md):

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Adatbázis beolvasása

Az adatbázis-kiterjesztés beolvasása parancs visszaadja az adatbázis-objektumot. Az adatbázis neve a parancs végrehajtásához használt adatbázis-környezetből származik.

```javascript
{
  customAction: "GetDatabase"
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Az egyéni parancs neve. "GetDatabase" értéknek kell lennie|
        
### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz a következő mezőket tartalmazó dokumentumot tartalmazza:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |   `int`     |   A válasz állapota. 1 = = sikeres. 0 = = hiba.      |
| `database`    |    `string`        |   Az adatbázis neve.      |
|   `provisionedThroughput`  |    `int`      |    Az adatbázison beállított kiépített átviteli sebesség, ha az adatbázis manuális, [adatbázis-szintű átviteli sebességet](set-throughput.md#set-throughput-on-a-database) használ     |
| `autoScaleSettings` | `Object` | Ez az objektum tartalmazza az adatbázishoz társított kapacitási paramétereket, ha az az [autoskálázási módot](provision-throughput-autoscale.md)használja. Az `maxThroughput` érték a kérelmek maximális mennyiségét írja le, amelyet az adatbázis dinamikusan fog növelni. |

Ha a parancs végrehajtása sikertelen, a rendszer egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

#### <a name="get-the-database"></a>Adatbázis beolvasása

A nevű adatbázis adatbázis-objektumának beszerzéséhez `"test"` használja a következő parancsot:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Ha az adatbázishoz nem tartozik átviteli sebesség, a kimenet a következő lesz:

```javascript
{ "database" : "test", "ok" : 1 }
```

Ha az adatbázishoz [adatbázis-szintű manuális átviteli sebesség](set-throughput.md#set-throughput-on-a-database) van társítva, a kimenet a következő értékeket jeleníti meg `provisionedThroughput` :

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Ha az adatbázishoz van hozzárendelve egy [adatbázis-szintű autoskálázási átviteli sebesség](provision-throughput-autoscale.md) , a kimenet a következőt jeleníti meg `provisionedThroughput` :, amely leírja az adatbázishoz tartozó minimális ru/s értéket, valamint a `autoScaleSettings` `maxThroughput` (z) objektumot, amely az adatbázis maximális ru/s-ját mutatja.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Gyűjtemény létrehozása

A gyűjtemény létrehozása bővítmény parancs egy új MongoDB-gyűjteményt hoz létre. Az adatbázis nevét a parancs által beállított adatbázisok környezete használja `use database` . A CreateCollection parancs formátuma a következő:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" to use Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:

| **Mező** | **Típus** | **Kötelező** | **Leírás** |
|---------|---------|---------|---------|
| `customAction` | `string` | Kötelező | Az egyéni parancs neve. A következőnek kell lennie: "CreateCollection".|
| `collection` | `string` | Kötelező | A gyűjtemény neve. Nem engedélyezettek speciális karakterek vagy szóközök.|
| `offerThroughput` | `int` | Választható | Az adatbázison beállított kiépített átviteli sebesség. Ha ez a paraméter nincs megadva, a rendszer alapértelmezés szerint a minimális, 400 RU/s értéket fogja tartalmazni. * A (z) 10 000 RU/s-nál nagyobb átviteli sebesség megadásához a `shardKey` paraméter megadása kötelező.|
| `shardKey` | `string` | Nagy adatátviteli kapacitással rendelkező gyűjtemények esetén szükséges | A szegmensben lévő gyűjteményhez tartozó szilánk-kulcs elérési útja. Ezt a paramétert akkor kell megadni, ha a-ben több mint 10 000 RU/s van beállítva `offerThroughput` .  Ha meg van adva, az összes beszúrt dokumentumhoz meg kell adni a kulcsot és az értéket. |
| `autoScaleSettings` | `Object` | Az [autoskálázási mód](provision-throughput-autoscale.md) esetében szükséges | Ez az objektum az autoskálázási kapacitás üzemmódhoz tartozó beállításokat tartalmazza. Beállíthatja az `maxThroughput` értéket, amely a gyűjtemény által dinamikusan növekedő kérelmek maximális mennyiségét mutatja. |

### <a name="output"></a>Kimenet

Egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Hozzon létre egy gyűjteményt a minimális konfigurációval

Ha új gyűjteményt szeretne létrehozni `"testCollection"` a névvel és az alapértelmezett értékekkel, használja a következő parancsot: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Ez egy új, rögzített, nem horizontális, 400RU/s típusú gyűjteményt eredményez, és automatikusan létrejön egy index a `_id` mezőben. Az ilyen típusú konfigurációk akkor is érvényesek, amikor új gyűjteményeket hoz létre a `insert()` függvény használatával. Például: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Nem szilánkos gyűjtemény létrehozása

A következő paranccsal hozhat létre egy `"testCollection"` , a 1000 RUs nevű és kiépített átviteli sebességű, szétosztott gyűjteményt: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Létrehozhat egy olyan gyűjteményt, amely akár 10 000 RU/s-t is létrehozhatja `offerThroughput` anélkül, hogy meg kellene adnia egy szilánk-kulcsot. Nagyobb átviteli sebességű gyűjtemények esetén tekintse meg a következő szakaszt.

#### <a name="create-a-sharded-collection"></a>Szilánkokra osztott gyűjtemény létrehozása

A következő paranccsal hozhat létre egy többrészes gyűjteményt, amelynek neve `"testCollection"` és kiépített átviteli sebessége 11 000 RUs, az `shardkey` "a. b" tulajdonság pedig az alábbi parancsot használja:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Ehhez a parancshoz most a `shardKey` paramétert kell megadni, mivel a több mint 10 000 ru/s érték van megadva a (z)-ben `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Nem horizontálisan létrehozott autoscale-gyűjtemény létrehozása

A következő parancs használatával hozzon létre egy nevű, az `'testCollection'` [adatátviteli kapacitást](provision-throughput-autoscale.md) használó, a (z) 4 000 ru/s értékre beállított, felhasználatlan gyűjteményt:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Az `autoScaleSettings.maxThroughput` értéknél megadhat egy 4 000 ru/s és 10 000 ru/s közötti tartományt, amelynek a szegmens kulcsa nem található. Az átviteli sebesség növelése érdekében meg kell adnia a `shardKey` paramétert.

#### <a name="create-a-sharded-autoscale-collection"></a>Többszegmensű autoskálázási gyűjtemény létrehozása

A következő paranccsal hozhat létre egy nevű szilánkos gyűjteményt egy nevű szegmensi `'testCollection'` kulccsal `'a.b'` , és amely a 20 000 ru/s értékre állítja az [autoskálázási átviteli kapacitást](provision-throughput-autoscale.md) :

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Gyűjtemény frissítése

Az Update Collection Extension parancs frissíti a megadott gyűjteményhez társított tulajdonságokat. Ha a gyűjteményt a kiépített átviteli sebességről az autoskálázásra módosítja, és fordítva, csak az Azure Portalon támogatott.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" if using Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting. Changing between Autoscale and Provisioned throughput is only supported in the Azure Portal.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Az egyéni parancs neve. A következőnek kell lennie: "UpdateCollection".      |
|  `collection`   |   `string`      |   A gyűjtemény neve.       |
| `offerThroughput` | `int` |   Kiosztott átviteli sebesség a gyűjteményre való beállításhoz.|
| `autoScaleSettings` | `Object` | Az [autoskálázási mód](provision-throughput-autoscale.md)használatához szükséges. Ez az objektum az autoskálázási kapacitás üzemmódhoz tartozó beállításokat tartalmazza. Az `maxThroughput` érték írja le a kérelmek maximális mennyiségét, amelyet a gyűjtemény dinamikusan növelni fog. |

## <a name="output"></a>Kimenet

Egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Egy gyűjteményhez társított kiosztott teljesítmény frissítése

A következő parancs használatával frissítheti egy olyan gyűjtemény kiépített átviteli sebességét, amelynek neve `"testCollection"` 1200 RUs:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Gyűjtemény beolvasása

A gyűjtemény egyéni beolvasása parancs a gyűjtemény objektumot adja vissza.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

A következő táblázat a parancson belüli paramétereket ismerteti:


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Az egyéni parancs neve. A következőnek kell lennie: "GetCollection".      |
| `collection`    |    `string`     |    A gyűjtemény neve.     |

### <a name="output"></a>Kimenet

Ha a parancs sikeres, a válasz a következő mezőket tartalmazó dokumentumot tartalmazza


|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   A válasz állapota. 1 = = sikeres. 0 = = hiba.      |
| `database`    |    `string`     |   Az adatbázis neve.      |
| `collection`    |    `string`     |    A gyűjtemény neve.     |
|  `shardKeyDefinition`   |   `document`      |  A kiosztott kulcsként használt index-specifikációs dokumentum. Ez egy opcionális válasz paraméter.       |
|  `provisionedThroughput`   |   `int`      |    Kiosztott átviteli sebesség a gyűjteményre való beállításhoz. Ez egy opcionális válasz paraméter.     |
| `autoScaleSettings` | `Object` | Ez az objektum tartalmazza az adatbázishoz társított kapacitási paramétereket, ha az az [autoskálázási módot](provision-throughput-autoscale.md)használja. Az `maxThroughput` érték írja le a kérelmek maximális mennyiségét, amelyet a gyűjtemény dinamikusan növelni fog. |

Ha a parancs végrehajtása sikertelen, a rendszer egy alapértelmezett egyéni parancs-választ ad vissza. Tekintse meg az egyéni parancs [alapértelmezett kimenetét](#default-output) a kimenetben található paraméterekhez.

### <a name="examples"></a>Példák

#### <a name="get-the-collection"></a>A gyűjtemény beolvasása

Egy nevű gyűjteményhez tartozó gyűjtemény objektum beszerzéséhez `"testCollection"` használja a következő parancsot:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Ha a gyűjteményhez hozzá van rendelve egy adatátviteli kapacitás, a rendszer tartalmazza az `provisionedThroughput` értéket, és a kimenet a következő lesz:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Ha a gyűjteményhez van hozzárendelve egy automatikusan méretezhető átviteli sebesség, akkor a rendszer a paraméterrel együtt fogja tartalmazni az `autoScaleSettings` objektumot `maxThroughput` , amely meghatározza a gyűjtemény által dinamikusan növekedő maximális átviteli sebességet. Emellett az is tartalmazni fogja az `provisionedThroughput` értéket, amely meghatározza, hogy a gyűjtemény milyen minimális átviteli sebességet fog csökkenteni, ha a gyűjtemény nem tartalmaz kérelmeket: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Ha a gyűjtemény [adatbázis-szintű átviteli sebességet](set-throughput.md#set-throughput-on-a-database)használ, az automatikus skálázási mód vagy a manuális beállítás esetében a kimenet a következő lesz:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Egyéni parancs alapértelmezett kimenete

Ha nincs megadva, az egyéni válasz a következő mezőket tartalmazó dokumentumot tartalmazza:

|**Mező**|**Típus** |**Leírás** |
|---------|---------|---------|
|  `ok`   |    `int`     |   A válasz állapota. 1 = = sikeres. 0 = = hiba.      |
| `code`    |   `int`      |   Csak akkor tér vissza, ha a parancs végrehajtása sikertelen volt (például ok = = 0). A MongoDB hibakódot tartalmazza. Ez egy opcionális válasz paraméter.      |
|  `errMsg`   |  `string`      |    Csak akkor tér vissza, ha a parancs végrehajtása sikertelen volt (például ok = = 0). Felhasználóbarát hibaüzenetet tartalmaz. Ez egy opcionális válasz paraméter.      |

Például:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Következő lépések

Ezután folytassa a következő Azure Cosmos DB fogalmak megismerésével: 

* [Indexelés az Azure Cosmos DB-ben](../cosmos-db/index-policy.md)
* [Az Azure Cosmos DB automatikusan lejár az idő az élettartammal](../cosmos-db/time-to-live.md)
