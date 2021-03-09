---
title: 4,0 a kiszolgáló támogatott szolgáltatásai és szintaxisa Azure Cosmos DB API-MongoDB
description: Ismerje meg a Azure Cosmos DB API-ját a MongoDB 4,0 Server Version támogatott funkcióit és szintaxisát. Ismerje meg az adatbázis-parancsokat, a lekérdezés nyelvének támogatását, az adattípusokat, az összesítési folyamat parancsait és a támogatott kezelőket.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 9eebc77c5b3d9402c766320fddfdaf05d50b574f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485401"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>Azure Cosmos DB API a MongoDB-hez (4,0 Server Version): támogatott funkciók és szintaxis
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A MongoDB Azure Cosmos DB API-jával a nyílt forráskódú MongoDB [-ügyfelek](https://docs.mongodb.org/ecosystem/drivers)bármelyikének használatával kommunikálhat. A MongoDB-hez készült Azure Cosmos DB API a MongoDB [vonalprotokolljának](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) követésével lehetővé teszi a meglévő ügyfélillesztők használatát.

A Azure Cosmos DB API-MongoDB való használatával élvezheti a használt MongoDB előnyeit, és az összes olyan vállalati funkciót, amelyet a Cosmos DB biztosít: [globális elosztás](distribute-data-globally.md), [automatikus](partitioning-overview.md)horizontális felskálázás, rendelkezésre állási és késési garanciák, titkosítás nyugalmi állapotban, biztonsági mentések és sok más.

## <a name="protocol-support"></a>Protokollok támogatása

A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia a MongoDB-hez készült Azure Cosmos DB API-hoz. Ha Azure Cosmos DB API-ját használja a MongoDB-fiókokhoz, a fiókok 3,6 + verziójának a végpontja a következő formátumú, `*.mongo.cosmos.azure.com` míg a fiókok 3,2-es verziójának formátuma a végpont `*.documents.azure.com` .

> [!NOTE]
> Ez a cikk csak a támogatott kiszolgálói parancsokat sorolja fel, és kizárja az ügyféloldali burkoló funkciókat. Az ügyféloldali burkoló függvények, például a és `deleteMany()` `updateMany()` a kiszolgáló-parancsok belső kihasználtsága `delete()` `update()` . A támogatott kiszolgálói parancsokat használó függvények kompatibilisek Azure Cosmos DB API-MongoDB.

## <a name="query-language-support"></a>Nyelvi támogatás lekérdezése

A Azure Cosmos DB API-MongoDB átfogó támogatást nyújt a MongoDB lekérdezési nyelvi szerkezetekhez. Az alábbiakban megtalálhatja a jelenleg támogatott műveletek, operátorok, szakaszok, parancsok és beállítások részletes listáját.

## <a name="database-commands"></a>Adatbázisparancsok

Azure Cosmos DB API-MongoDB a következő adatbázis-parancsokat támogatja:

### <a name="query-and-write-operation-commands"></a>Lekérdezési és írási műveletek parancsai

| Parancs | Támogatott |
|---------|---------|
| [adatfolyamok módosítása](mongodb-change-streams.md) | Yes |
| delete | Yes |
| eval | No |
| find | Yes |
| findAndModify | Yes |
| getLastError | Yes |
| getMore | Yes |
| getPrevError | No |
| insert | Yes |
| parallelCollectionScan | No |
| resetError | No |
| update | Yes |

### <a name="transaction-commands"></a>Tranzakciós parancsok

| Parancs | Támogatott |
|---------|---------|
| abortTransaction | Yes |
| commitTransaction | Yes |

### <a name="authentication-commands"></a>Hitelesítési parancsok

| Parancs | Támogatott |
|---------|---------|
| authenticate | Yes |
| getnonce | Yes |
| logout | Yes |

### <a name="administration-commands"></a>Adminisztrációs parancsok

| Parancs | Támogatott |
|---------|---------|
| cloneCollectionAsCapped | No |
| collMod | No |
| connectionStatus | No |
| convertToCapped | No |
| copydb | No |
| létrehozás | Yes |
| createIndexes | Yes |
| currentOp | Yes |
| drop | Yes |
| dropDatabase | Yes |
| dropIndexes | Yes |
| filemd5 | Yes |
| killCursors | Yes |
| Mintálné | No |
| listCollections | Yes |
| listDatabases | Yes |
| listIndexes | Yes |
| reIndex | Yes |
| renameCollection | No |

### <a name="diagnostics-commands"></a>Diagnosztikai parancsok

| Parancs | Támogatott |
|---------|---------|
| buildInfo | Yes |
| collStats | Yes |
| connPoolStats | No |
| connectionStatus | No |
| dataSize | No |
| dbHash | No |
| dbStats | Yes |
| megmagyarázni | Yes |
| funkciók | No |
| hostInfo | Yes |
| listDatabases | Yes |
| Parancslista elemre | No |
| Profiler | No |
| serverStatus | No |
| top | No |
| whatsmyuri | Yes |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Összesítési folyamat

### <a name="aggregation-commands"></a>Összesítési parancsok

| Parancs | Támogatott |
|---------|---------|
| aggregate | Yes |
| count | Yes |
| distinct | Yes |
| mapReduce | No |

### <a name="aggregation-stages"></a>Összesítési fázisok

| Parancs | Támogatott |
|---------|---------|
| $addFields | Yes |
| $bucket | No |
| $bucketAuto | No |
| $changeStream | Yes |
| $collStats | No |
| $count | Yes |
| $currentOp | No |
| $facet | Yes |
| $geoNear | Yes |
| $graphLookup | Yes |
| $group | Yes |
| $indexStats | No |
| $limit | Yes |
| $listLocalSessions | No |
| $listSessions | No |
| $lookup | Yes |
| $match | Yes |
| $out | Yes |
| $project | Yes |
| $redact | Yes |
| $replaceRoot | Yes |
| $replaceWith | No |
| $sample | Yes |
| $skip | Yes |
| $sort | Yes |
| $sortByCount | Yes |
| $unwind | Yes |

### <a name="boolean-expressions"></a>Logikai kifejezések

| Parancs | Támogatott |
|---------|---------|
| $and | Yes |
| $not | Yes |
| $or | Yes |

### <a name="conversion-expressions"></a>Átalakítási kifejezések

| Parancs | Támogatott |
|---------|---------|
| $convert | Yes |
| $toBool | Yes |
| $toDate | Yes |
| $toDecimal | Yes |
| $toDouble | Yes |
| $toInt | Yes |
| $toLong | Yes |
| $toObjectId | Yes |
| $toString | Yes |

### <a name="set-expressions"></a>Halmazkifejezések

| Parancs | Támogatott |
|---------|---------|
| $setEquals | Yes |
| $setIntersection | Yes |
| $setUnion | Yes |
| $setDifference | Yes |
| $setIsSubset | Yes |
| $anyElementTrue | Yes |
| $allElementsTrue | Yes |

### <a name="comparison-expressions"></a>Összehasonlító kifejezések

| Parancs | Támogatott |
|---------|---------|
| $cmp | Yes |
| $eq | Yes | 
| $gt | Yes | 
| $gte | Yes | 
| $lt | Yes |
| $lte | Yes | 
| $ne | Yes | 
| $in | Yes | 
| $nin | Yes | 

### <a name="arithmetic-expressions"></a>Aritmetikai kifejezések

| Parancs | Támogatott |
|---------|---------|
| $abs | Yes |
| $add | Yes |
| $ceil | Yes |
| $divide | Yes |
| $exp | Yes |
| $floor | Yes |
| $ln | Yes |
| $log | Yes |
| $log10 | Yes |
| $mod | Yes |
| $multiply | Yes |
| $pow | Yes |
| $sqrt | Yes |
| $subtract | Yes |
| $trunc | Yes |

### <a name="string-expressions"></a>Sztringkifejezések

| Parancs | Támogatott |
|---------|---------|
| $concat | Yes |
| $indexOfBytes | Yes |
| $indexOfCP | Yes |
| $ltrim | Yes |
| $rtrim | Yes |
| $trim | Yes |
| $split | Yes |
| $strLenBytes | Yes |
| $strLenCP | Yes |
| $strcasecmp | Yes |
| $substr | Yes |
| $substrBytes | Yes |
| $substrCP | Yes |
| $toLower | Yes |
| $toUpper | Yes |

### <a name="text-search-operator"></a>Szöveges keresési operátor

| Parancs | Támogatott |
|---------|---------|
| $meta | No |

### <a name="array-expressions"></a>Tömbkifejezések

| Parancs | Támogatott |
|---------|---------|
| $arrayElemAt | Yes |
| $arrayToObject | Yes |
| $concatArrays | Yes |
| $filter | Yes |
| $indexOfArray | Yes |
| $isArray | Yes |
| $objectToArray | Yes |
| $range | Yes |
| $reverseArray | Yes |
| $reduce | Yes |
| $size | Yes |
| $slice | Yes |
| $zip | Yes |
| $in | Yes |

### <a name="variable-operators"></a>Változó operátorok

| Parancs | Támogatott |
|---------|---------|
| $map | Yes |
| $let | Yes |

### <a name="system-variables"></a>Rendszerváltozók

| Parancs | Támogatott |
|---------|---------|
| $ $CURRENT | Yes |
| $ $DESCEND | Yes |
| $ $KEEP | Yes |
| $ $PRUNE | Yes |
| $ $REMOVE | Yes |
| $ $ROOT | Yes |

### <a name="literal-operator"></a>Literális operátor

| Parancs | Támogatott |
|---------|---------|
| $literal | Yes |

### <a name="date-expressions"></a>Dátumkifejezések

| Parancs | Támogatott |
|---------|---------|
| $dayOfYear | Yes |
| $dayOfMonth | Yes |
| $dayOfWeek | Yes |
| $year | Yes |
| $month | Yes | 
| $week | Yes |
| $hour | Yes |
| $minute | Yes | 
| $second | Yes |
| $millisecond | Yes | 
| $dateToString | Yes |
| $isoDayOfWeek | Yes |
| $isoWeek | Yes |
| $dateFromParts | No | 
| $dateToParts | No |
| $dateFromString | No |
| $isoWeekYear | Yes |

### <a name="conditional-expressions"></a>Feltételes kifejezések

| Parancs | Támogatott |
|---------|---------|
| $cond | Yes |
| $ifNull | Yes |
| $switch | Yes |

### <a name="data-type-operator"></a>Adattípus-operátor

| Parancs | Támogatott |
|---------|---------|
| $type | Yes |

### <a name="accumulator-expressions"></a>Gyűjtői kifejezések

| Parancs | Támogatott |
|---------|---------|
| $sum | Yes |
| $avg | Yes |
| $first | Yes |
| $last | Yes |
| $max | Yes |
| $min | Yes |
| $push | Yes |
| $addToSet | Yes |
| $stdDevPop | Yes |
| $stdDevSamp | Yes |

### <a name="merge-operator"></a>Egyesítési operátor

| Parancs | Támogatott |
|---------|---------|
| $mergeObjects | Yes |

## <a name="data-types"></a>Adattípusok

A Azure Cosmos DB API-MongoDB támogatja a MongoDB BSON formátumban kódolt dokumentumokat. A 4,0 API-verzió fokozza a formátum belső használatát a teljesítmény javítása és a költségek csökkentése érdekében. A 4,0-et futtató végponttal írt vagy frissített dokumentumok.
 
[Frissítési forgatókönyv](mongodb-version-upgrade.md)esetén a 4,0-es verzióra való frissítés előtt írt dokumentumok nem részesülnek a kibővített teljesítményben, amíg az 4,0-végponton keresztül írási művelettel nem frissülnek.

| Parancs | Támogatott |
|---------|---------|
| Dupla | Igen |
| Sztring | Yes |
| Objektum | Yes |
| Tömb | Yes |
| Bináris adatértékek | Yes | 
| ObjectId | Yes |
| Logikai | Yes |
| Date | Yes |
| Null | Yes |
| 32 bites egész szám (int) | Yes |
| Időbélyeg | Yes |
| 64 bites egész szám (hosszú) | Yes |
| MinKey | Yes |
| MaxKey | Yes |
| Decimal128 | Yes | 
| Reguláris kifejezés | Yes |
| JavaScript | Yes |
| JavaScript (hatókörrel)| Yes |
| Nem definiált | Yes |

## <a name="indexes-and-index-properties"></a>Indexek és index tulajdonságai

### <a name="indexes"></a>Indexek

| Parancs | Támogatott |
|---------|---------|
| Egyetlen mező indexe | Yes |
| Összetett index | Yes |
| Multikey index | Yes |
| Szöveges index | No |
| 2dsphere | Yes |
| 2D-index | No |
| Kivonatos index | Yes |

### <a name="index-properties"></a>Index tulajdonságai

| Parancs | Támogatott |
|---------|---------|
| TTL | Yes |
| Egyedi | Yes |
| Részleges | No |
| Kis-és nagybetűk megkülönböztetése | No |
| Ritka | No |
| Háttér | Yes |

## <a name="operators"></a>Operátorok

### <a name="logical-operators"></a>Logikai operátorok

| Parancs | Támogatott |
|---------|---------|
| $or | Yes |
| $and | Yes |
| $not | Yes |
| $nor | Yes | 

### <a name="element-operators"></a>Elem operátorai

| Parancs | Támogatott |
|---------|---------|
| $exists | Yes |
| $type | Yes |

### <a name="evaluation-query-operators"></a>Kiértékelési lekérdezési operátorok

| Parancs | Támogatott |
|---------|---------|
| $expr | No |
| $jsonSchema | No |
| $mod | Yes |
| $regex | Yes |
| $text | Nem (nem támogatott. Ehelyett használja a $regex.)| 
| $where | No | 

A $regex lekérdezésekben a bal oldali rögzített kifejezések lehetővé teszik az indexek keresését. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.

Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni. Ha például a következő eredeti lekérdezést adta meg: `find({x:{$regex: /^abc$/})` , azt a következőképpen kell módosítani:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket. A „|” sávoperátor „vagy” függvényként működik – a(z) `find({x:{$regex: /^abc |^def/})` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Tömb operátorai

| Parancs | Támogatott | 
|---------|---------|
| $all | Yes | 
| $elemMatch | Yes | 
| $size | Yes | 

### <a name="comment-operator"></a>Megjegyzés operátora

| Parancs | Támogatott | 
|---------|---------|
| $comment | Yes | 

### <a name="projection-operators"></a>Kivetítési operátorok

| Parancs | Támogatott |
|---------|---------|
| $elemMatch | Yes |
| $meta | No |
| $slice | Yes |

### <a name="update-operators"></a>Frissítési operátorok

#### <a name="field-update-operators"></a>Mezőfrissítő operátorok

| Parancs | Támogatott |
|---------|---------|
| $inc | Yes |
| $mul | Yes |
| $rename | Yes |
| $setOnInsert | Yes |
| $set | Yes |
| $unset | Yes |
| $min | Yes |
| $max | Yes |
| $currentDate | Yes |

#### <a name="array-update-operators"></a>Tömbfrissítő operátorok

| Parancs | Támogatott |
|---------|---------|
| $ | Yes |
| $[]| Yes |
| $[<identifier>]| Yes |
| $addToSet | Yes |
| $pop | Yes |
| $pullAll | Yes |
| $pull | Yes |
| $push | Yes |
| $pushAll | Yes |

#### <a name="update-modifiers"></a>Módosítók frissítése

| Parancs | Támogatott |
|---------|---------|
| $each | Yes |
| $slice | Yes |
| $sort | Yes |
| $position | Yes |

#### <a name="bitwise-update-operator"></a>Bitenként frissítő operátor

| Parancs | Támogatott |
|---------|---------|
| $bit | Yes | 
| $bitsAllSet | No |
| $bitsAnySet | No |
| $bitsAllClear | No |
| $bitsAnyClear | No |

### <a name="geospatial-operators"></a>Térinformatikai operátorok

Operátor | Támogatott | 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near | Yes |
$nearSphere | Yes |
$geometry | Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | No |
$centerSphere | No |
$box | No |
$polygon | No |

## <a name="sort-operations"></a>Rendezési műveletek

A művelet használatakor a `findOneAndUpdate` rendezési műveletek egyetlen mező esetén támogatottak, de a rendezési műveletek több mezőn nem támogatottak.

## <a name="unique-indexes"></a>Egyedi indexek

Az [egyedi indexek](mongodb-indexing.md#unique-indexes) biztosítják, hogy egy adott mező ne legyen duplikált érték a gyűjtemény összes dokumentumában, hasonlóan ahhoz, ahogyan az egyediség megmarad az alapértelmezett "_id" kulcson. A Azure Cosmos DBban egyedi indexeket hozhat létre a `createIndex` parancs és a `unique` megkötés paraméter használatával:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Összetett indexek

Az [összetett indexek](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) lehetővé teszik, hogy legfeljebb nyolc mezőhöz hozzon létre egy indexet a csoportok csoportjai számára. Ez a típusú index eltér a natív MongoDB összetett indextől. Azure Cosmos DB az összetett indexek a több mezőre alkalmazott rendezési műveletekhez használatosak. Összetett index létrehozásához több tulajdonságot kell megadnia paraméterként:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
    "createdCollectionAutomatically" : false, 
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
}
```

## <a name="gridfs"></a>GridFS

Azure Cosmos DB támogatja a GridFS bármely GridFS-kompatibilis Mongo-illesztőprogramon keresztül.

## <a name="replication"></a>Replikáció

Az Azure Cosmos DB támogatja az automatikus natív replikációt a legalsó rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. A Cosmos DB nem támogatja a manuális replikálási parancsokat.

## <a name="retryable-writes"></a>Újrapróbálkozást lehetővé tevő írások

A Cosmos DB még nem támogatja az újrapróbálkozást lehetővé tevő írásokat. Az ügyfél-illesztőprogramoknak hozzá kell adni a "retryWrites = false" URL paramétert a kapcsolódási karakterlánchoz. További URL-paramétereket adhat hozzá egy "&" előtaggal. 

## <a name="sharding"></a>Sharding

Az Azure Cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. A szegmensek létrehozását, elhelyezését és kiegyensúlyozását automatikusan kezeli. Azure Cosmos DB nem támogatja a manuális horizontális skálázási parancsokat, ami azt jelenti, hogy nem kell olyan parancsokat meghívnia, mint például a addShard, a balancerStart, a moveChunk stb. A tárolók létrehozásakor vagy az adatlekérdezés során csak a szegmens kulcsát kell megadnia.

## <a name="sessions"></a>Munkamenetek

A Azure Cosmos DB még nem támogatja a kiszolgálóoldali munkamenet-parancsokat.

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

A Azure Cosmos DB a dokumentum időbélyegzője alapján támogatja a élettartamot (TTL). A TTL engedélyezhető a gyűjtemények számára, ha a [Azure Portalra](https://portal.azure.com)kattint.

## <a name="transactions"></a>Tranzakciók

A többdokumentumos tranzakciók a nem horizontális gyűjteményekben támogatottak. A többdokumentumos tranzakciók nem támogatottak a gyűjtemények vagy a szilánkokra bontott gyűjtemények esetében. A tranzakciók időtúllépése rögzített 5 másodperc.

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

Az Azure Cosmos DB még nem támogatja a felhasználókat és a szerepköröket. A Cosmos DB azonban támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), valamint az írási és olvasási és csak olvasható jelszavakat/kulcsokat, amelyeket a [Azure Portal](https://portal.azure.com) (kapcsolati karakterlánc oldalon) lehet beszerezni.

## <a name="write-concern"></a>Írási szempont

Egyes alkalmazások [írási aggodalomra](https://docs.mongodb.com/manual/reference/write-concern/)számíthatnak, amely meghatározza az írási művelet során szükséges válaszok számát. Mivel a Cosmos DB a replikációt a háttérben kezeli, alapértelmezés szerint minden írás automatikusan kvórum. Az ügyfél kódja által meghatározott írási aggályok figyelmen kívül lesznek hagyva. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
