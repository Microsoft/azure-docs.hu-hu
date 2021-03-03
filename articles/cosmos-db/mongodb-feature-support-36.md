---
title: Azure Cosmos DB API a MongoDB-hez (3,6-es verzió) támogatott funkciók és szintaxis
description: Ismerkedjen meg Azure Cosmos DB API-MongoDB (3,6 verzió) támogatott funkciókkal és szintaxissal.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce79b450e5eaed04150ffafd88528a131417044a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692306"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>MongoDB-hez készült Azure Cosmos DB API (3.6-os verzió): támogatott funkciók és szintaxis
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A MongoDB Azure Cosmos DB API-jával a nyílt forráskódú MongoDB [-ügyfelek](https://docs.mongodb.org/ecosystem/drivers)bármelyikének használatával kommunikálhat. A MongoDB-hez készült Azure Cosmos DB API a MongoDB [vonalprotokolljának](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) követésével lehetővé teszi a meglévő ügyfélillesztők használatát.

A Azure Cosmos DB API-MongoDB való használatával élvezheti a használt MongoDB előnyeit, és az összes olyan vállalati funkciót, amelyet a Cosmos DB biztosít: [globális elosztás](distribute-data-globally.md), [automatikus](partitioning-overview.md)horizontális felskálázás, rendelkezésre állási és késési garanciák, titkosítás nyugalmi állapotban, biztonsági mentések és sok más.

> [!NOTE]
> A MongoDB-hez készült Cosmos DB API 3,6-es verziója nem rendelkezik aktuális tervekkel az életciklus végéhez (EOL). A jövőbeli EOL vonatkozó minimális figyelmeztetés három év.

## <a name="protocol-support"></a>Protokollok támogatása

A MongoDB-hez készült Azure Cosmos DB API új fiókok esetén alapértelmezés szerint kompatibilis a MongoDB-kiszolgáló **3.6**-os verziójával. A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia a MongoDB-hez készült Azure Cosmos DB API-hoz. Vegye figyelembe, hogy ha Azure Cosmos DB API-ját használja a MongoDB-fiókokhoz, a fiók 3,6-es verziójának a végpontja a következő formátumú, `*.mongo.cosmos.azure.com` míg a fiók 3,2-es verziója a végpontot formázza `*.documents.azure.com` .

## <a name="query-language-support"></a>Nyelvi támogatás lekérdezése

A Azure Cosmos DB API-MongoDB átfogó támogatást nyújt a MongoDB lekérdezési nyelvi szerkezetekhez. A következő szakaszok a Azure Cosmos DB által jelenleg támogatott kiszolgálói műveletek, operátorok, szakaszok, parancsok és beállítások részletes listáját mutatják be.

> [!NOTE]
> Ez a cikk csak a támogatott kiszolgálói parancsokat sorolja fel, és kizárja az ügyféloldali burkoló funkciókat. Az ügyféloldali burkoló függvények, például a és `deleteMany()` `updateMany()` a kiszolgáló-parancsok belső kihasználtsága `delete()` `update()` . A támogatott kiszolgálói parancsokat használó függvények kompatibilisek Azure Cosmos DB API-MongoDB.

## <a name="database-commands"></a>Adatbázisparancsok

Azure Cosmos DB API-MongoDB a következő adatbázis-parancsokat támogatja:

### <a name="query-and-write-operation-commands"></a>Lekérdezési és írási műveletek parancsai

| Parancs | Támogatott |
|---------|---------|
| [adatfolyamok módosítása](mongodb-change-streams.md) | Igen |
| delete | Igen |
| eval | Nem |
| find | Igen |
| findAndModify | Igen |
| getLastError | Igen |
| getMore | Igen |
| getPrevError | Nem |
| insert | Igen |
| parallelCollectionScan | Nem |
| resetError | Nem |
| update | Igen |

### <a name="authentication-commands"></a>Hitelesítési parancsok

| Parancs | Támogatott |
|---------|---------|
| authenticate | Igen |
| getnonce | Igen |
| logout | Igen |

### <a name="administration-commands"></a>Adminisztrációs parancsok

| Parancs | Támogatott |
|---------|---------|
| cloneCollectionAsCapped | Nem |
| collMod | Nem |
| connectionStatus | Nem |
| convertToCapped | Nem |
| copydb | Nem |
| létrehozás | Igen |
| createIndexes | Igen |
| currentOp | Igen |
| drop | Igen |
| dropDatabase | Igen |
| dropIndexes | Igen |
| filemd5 | Igen |
| killCursors | Igen |
| Mintálné | Nem |
| listCollections | Igen |
| listDatabases | Igen |
| listIndexes | Igen |
| reIndex | Igen |
| renameCollection | Nem |


### <a name="diagnostics-commands"></a>Diagnosztikai parancsok

| Parancs | Támogatott |
|---------|---------|
| buildInfo | Igen |
| collStats | Igen |
| connPoolStats | Nem |
| connectionStatus | Nem |
| dataSize | Nem |
| dbHash | Nem |
| dbStats | Igen |
| megmagyarázni | Igen |
| funkciók | Nem |
| hostInfo | Igen |
| listDatabases | Igen |
| Parancslista elemre | Nem |
| Profiler | Nem |
| serverStatus | Nem |
| top | Nem |
| whatsmyuri | Igen |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Összesítési folyamat</a>

### <a name="aggregation-commands"></a>Összesítési parancsok

| Parancs | Támogatott |
|---------|---------|
| aggregate | Igen |
| count | Igen |
| distinct | Igen |
| mapReduce | Nem |

### <a name="aggregation-stages"></a>Összesítési fázisok

| Parancs | Támogatott |
|---------|---------|
| $addFields | Igen |
| $bucket | Nem |
| $bucketAuto | Nem |
| $changeStream | Igen |
| $collStats | Nem |
| $count | Igen |
| $currentOp | Nem |
| $facet | Igen |
| $geoNear | Igen |
| $graphLookup | Igen |
| $group | Igen |
| $indexStats | Nem |
| $limit | Igen |
| $listLocalSessions | Nem |
| $listSessions | Nem |
| $lookup | Igen |
| $match | Igen |
| $out | Igen |
| $project | Igen |
| $redact | Igen |
| $replaceRoot | Igen |
| $replaceWith | Nem |
| $sample | Igen |
| $skip | Igen |
| $sort | Igen |
| $sortByCount | Igen |
| $unwind | Igen |

### <a name="boolean-expressions"></a>Logikai kifejezések

| Parancs | Támogatott |
|---------|---------|
| $and | Igen |
| $not | Igen |
| $or | Igen |

### <a name="set-expressions"></a>Halmazkifejezések

| Parancs | Támogatott |
|---------|---------|
| $setEquals | Igen |
| $setIntersection | Igen |
| $setUnion | Igen |
| $setDifference | Igen |
| $setIsSubset | Igen |
| $anyElementTrue | Igen |
| $allElementsTrue | Igen |

### <a name="comparison-expressions"></a>Összehasonlító kifejezések

| Parancs | Támogatott |
|---------|---------|
| $cmp | Igen |
| $eq | Igen | 
| $gt | Igen | 
| $gte | Igen | 
| $lt | Igen |
| $lte | Igen | 
| $ne | Igen | 
| $in | Igen | 
| $nin | Igen | 

### <a name="arithmetic-expressions"></a>Aritmetikai kifejezések

| Parancs | Támogatott |
|---------|---------|
| $abs | Igen |
| $add | Igen |
| $ceil | Igen |
| $divide | Igen |
| $exp | Igen |
| $floor | Igen |
| $ln | Igen |
| $log | Igen |
| $log10 | Igen |
| $mod | Igen |
| $multiply | Igen |
| $pow | Igen |
| $sqrt | Igen |
| $subtract | Igen |
| $trunc | Igen |

### <a name="string-expressions"></a>Sztringkifejezések

| Parancs | Támogatott |
|---------|---------|
| $concat | Igen |
| $indexOfBytes | Igen |
| $indexOfCP | Igen |
| $split | Igen |
| $strLenBytes | Igen |
| $strLenCP | Igen |
| $strcasecmp | Igen |
| $substr | Igen |
| $substrBytes | Igen |
| $substrCP | Igen |
| $toLower | Igen |
| $toUpper | Igen |

### <a name="text-search-operator"></a>Szöveges keresési operátor

| Parancs | Támogatott |
|---------|---------|
| $meta | Nem |

### <a name="array-expressions"></a>Tömbkifejezések

| Parancs | Támogatott |
|---------|---------|
| $arrayElemAt | Igen |
| $arrayToObject | Igen |
| $concatArrays | Igen |
| $filter | Igen |
| $indexOfArray | Igen |
| $isArray | Igen |
| $objectToArray | Igen |
| $range | Igen |
| $reverseArray | Igen |
| $reduce | Igen |
| $size | Igen |
| $slice | Igen |
| $zip | Igen |
| $in | Igen |

### <a name="variable-operators"></a>Változó operátorok

| Parancs | Támogatott |
|---------|---------|
| $map | Igen |
| $let | Igen |

### <a name="system-variables"></a>Rendszerváltozók

| Parancs | Támogatott |
|---------|---------|
| $ $CURRENT | Igen |
| $ $DESCEND | Igen |
| $ $KEEP | Igen |
| $ $PRUNE | Igen |
| $ $REMOVE | Igen |
| $ $ROOT | Igen |

### <a name="literal-operator"></a>Literális operátor

| Parancs | Támogatott |
|---------|---------|
| $literal | Igen |

### <a name="date-expressions"></a>Dátumkifejezések

| Parancs | Támogatott |
|---------|---------|
| $dayOfYear | Igen |
| $dayOfMonth | Igen |
| $dayOfWeek | Igen |
| $year | Igen |
| $month | Igen | 
| $week | Igen |
| $hour | Igen |
| $minute | Igen | 
| $second | Igen |
| $millisecond | Igen | 
| $dateToString | Igen |
| $isoDayOfWeek | Igen |
| $isoWeek | Igen |
| $dateFromParts | Nem | 
| $dateToParts | Nem |
| $dateFromString | Nem |
| $isoWeekYear | Igen |

### <a name="conditional-expressions"></a>Feltételes kifejezések

| Parancs | Támogatott |
|---------|---------|
| $cond | Igen |
| $ifNull | Igen |
| $switch | Igen |

### <a name="data-type-operator"></a>Adattípus-operátor

| Parancs | Támogatott |
|---------|---------|
| $type | Igen |

### <a name="accumulator-expressions"></a>Gyűjtői kifejezések

| Parancs | Támogatott |
|---------|---------|
| $sum | Igen |
| $avg | Igen |
| $first | Igen |
| $last | Igen |
| $max | Igen |
| $min | Igen |
| $push | Igen |
| $addToSet | Igen |
| $stdDevPop | Igen |
| $stdDevSamp | Igen |

### <a name="merge-operator"></a>Egyesítési operátor

| Parancs | Támogatott |
|---------|---------|
| $mergeObjects | Igen |

## <a name="data-types"></a>Adattípusok

| Parancs | Támogatott |
|---------|---------|
| Dupla | Igen |
| Sztring | Igen |
| Objektum | Igen |
| Tömb | Igen |
| Bináris adatértékek | Igen | 
| ObjectId | Igen |
| Logikai | Igen |
| Date | Igen |
| Null | Igen |
| 32 bites egész szám (int) | Igen |
| Időbélyeg | Igen |
| 64 bites egész szám (hosszú) | Igen |
| MinKey | Igen |
| MaxKey | Igen |
| Decimal128 | Igen | 
| Reguláris kifejezés | Igen |
| JavaScript | Igen |
| JavaScript (hatókörrel)| Igen |
| Nem definiált | Igen |

## <a name="indexes-and-index-properties"></a>Indexek és index tulajdonságai

### <a name="indexes"></a>Indexek

| Parancs | Támogatott |
|---------|---------|
| Egyetlen mező indexe | Igen |
| Összetett index | Igen |
| Multikey index | Igen |
| Szöveges index | Nem |
| 2dsphere | Igen |
| 2D-index | Nem |
| Kivonatos index | Igen |

### <a name="index-properties"></a>Index tulajdonságai

| Parancs | Támogatott |
|---------|---------|
| TTL | Igen |
| Egyedi | Igen |
| Részleges | Nem |
| Kis-és nagybetűk megkülönböztetése | Nem |
| Ritka | Nem |
| Háttér | Igen |

## <a name="operators"></a>Operátorok

### <a name="logical-operators"></a>Logikai operátorok

| Parancs | Támogatott |
|---------|---------|
| $or | Igen |
| $and | Igen |
| $not | Igen |
| $nor | Igen | 

### <a name="element-operators"></a>Elem operátorai

| Parancs | Támogatott |
|---------|---------|
| $exists | Igen |
| $type | Igen |

### <a name="evaluation-query-operators"></a>Kiértékelési lekérdezési operátorok

| Parancs | Támogatott |
|---------|---------|
| $expr | Nem |
| $jsonSchema | Nem |
| $mod | Igen |
| $regex | Igen |
| $text | Nem (nem támogatott. Ehelyett használja a $regex.)| 
| $where | Nem | 

A $regex lekérdezésekben a bal oldali rögzített kifejezések lehetővé teszik az indexek keresését. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.

Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni. Ha például a következő eredeti lekérdezést adta meg: ```find({x:{$regex: /^abc$/})``` , azt a következőképpen kell módosítani:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```

Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket. A „|” sávoperátor „vagy” függvényként működik – a(z) ```find({x:{$regex: /^abc |^def/})``` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Tömb operátorai

| Parancs | Támogatott | 
|---------|---------|
| $all | Igen | 
| $elemMatch | Igen | 
| $size | Igen | 

### <a name="comment-operator"></a>Megjegyzés operátora

| Parancs | Támogatott | 
|---------|---------|
| $comment | Igen | 

### <a name="projection-operators"></a>Kivetítési operátorok

| Parancs | Támogatott |
|---------|---------|
| $elemMatch | Igen |
| $meta | Nem |
| $slice | Igen |

### <a name="update-operators"></a>Frissítési operátorok

#### <a name="field-update-operators"></a>Mezőfrissítő operátorok

| Parancs | Támogatott |
|---------|---------|
| $inc | Igen |
| $mul | Igen |
| $rename | Igen |
| $setOnInsert | Igen |
| $set | Igen |
| $unset | Igen |
| $min | Igen |
| $max | Igen |
| $currentDate | Igen |

#### <a name="array-update-operators"></a>Tömbfrissítő operátorok

| Parancs | Támogatott |
|---------|---------|
| $ | Igen |
| $[]| Igen |
| $[<identifier>]| Igen |
| $addToSet | Igen |
| $pop | Igen |
| $pullAll | Igen |
| $pull | Igen |
| $push | Igen |
| $pushAll | Igen |


#### <a name="update-modifiers"></a>Módosítók frissítése

| Parancs | Támogatott |
|---------|---------|
| $each | Igen |
| $slice | Igen |
| $sort | Igen |
| $position | Igen |

#### <a name="bitwise-update-operator"></a>Bitenként frissítő operátor

| Parancs | Támogatott |
|---------|---------|
| $bit | Igen | 
| $bitsAllSet | Nem |
| $bitsAnySet | Nem |
| $bitsAllClear | Nem |
| $bitsAnyClear | Nem |

### <a name="geospatial-operators"></a>Térinformatikai operátorok

Operátor | Támogatott | 
--- | --- |
$geoWithin | Igen |
$geoIntersects | Igen | 
$near | Igen |
$nearSphere | Igen |
$geometry | Igen |
$minDistance | Igen |
$maxDistance | Igen |
$center | Nem |
$centerSphere | Nem |
$box | Nem |
$polygon | Nem |

## <a name="sort-operations"></a>Rendezési műveletek

A művelet használatakor a `findOneAndUpdate` rendezési műveletek egyetlen mező esetén támogatottak, de a rendezési műveletek több mezőn nem támogatottak.

## <a name="unique-indexes"></a>Egyedi indexek

Az [egyedi indexek](mongodb-indexing.md#unique-indexes) biztosítják, hogy egy adott mező ne legyen duplikált érték a gyűjtemény összes dokumentumában, hasonlóan ahhoz, ahogyan az egyediség megmarad az alapértelmezett "_id" kulcson. A Cosmos DBban egyedi indexeket hozhat létre a `createIndex` parancs és a `unique` megkötés paraméter használatával:

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

Az [összetett indexek](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) lehetővé teszik, hogy legfeljebb 8 mezőből álló csoportok számára hozzon létre indexet. Ez a típusú index eltér a natív MongoDB összetett indextől. Azure Cosmos DB az összetett indexek a több mezőre alkalmazott rendezési műveletekhez használatosak. Összetett index létrehozásához több tulajdonságot kell megadnia paraméterként:

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

Azure Cosmos DB támogatja a GridFS bármely GridFS-kompatibilis MongoDB-illesztőprogramon keresztül.

## <a name="replication"></a>Replikáció

Cosmos DB támogatja az automatikus és a natív replikálást a legalacsonyabb rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. A Cosmos DB nem támogatja a manuális replikálási parancsokat.





## <a name="retryable-writes"></a>Újrapróbálkozást lehetővé tevő írások

A Azure Cosmos DB még nem támogatja az újrapróbálkozást lehetővé tevő írásokat. Az ügyfél-illesztőprogramokat hozzá kell adni `retryWrites=false` a kapcsolódási karakterlánchoz.

## <a name="sharding"></a>Sharding

Az Azure Cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. A szegmensek létrehozását, elhelyezését és kiegyensúlyozását automatikusan kezeli. Azure Cosmos DB nem támogatja a manuális horizontális skálázási parancsokat, ami azt jelenti, hogy nem kell olyan parancsokat meghívnia, mint például a addShard, a balancerStart, a moveChunk stb. A tárolók létrehozásakor vagy az adatlekérdezés során csak a szegmens kulcsát kell megadnia.

## <a name="sessions"></a>Munkamenetek

A Azure Cosmos DB még nem támogatja a kiszolgálóoldali munkamenet-parancsokat.

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

A Azure Cosmos DB a dokumentum időbélyegzője alapján támogatja a élettartamot (TTL). A TTL engedélyezhető a [Azure Portal](https://portal.azure.com)gyűjtemények számára.

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

Az Azure Cosmos DB még nem támogatja a felhasználókat és a szerepköröket. Azonban támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és az írási és olvasási és csak olvasható jelszavakat vagy kulcsokat, amelyeket a [Azure Portal](https://portal.azure.com)kapcsolati karakterlánc paneljén lehet beszerezni.

## <a name="write-concern"></a>Írási szempont

Egyes alkalmazások [írási aggodalomra](https://docs.mongodb.com/manual/reference/write-concern/) számíthatnak, amely meghatározza az írási művelet során szükséges válaszok számát. Mivel a Azure Cosmos DB kezeli a replikációt, a rendszer alapértelmezés szerint az összes írást automatikusan többségi kvórumnak tekinti, ha erős konzisztenciát használ. Az ügyfél kódja által meghatározott írási aggályok figyelmen kívül lesznek hagyva. További információ: [konzisztencia-szintek használata a rendelkezésre állás és a teljesítmény maximalizálása érdekében](consistency-levels.md) .

## <a name="next-steps"></a>Következő lépések

- További információ: [Mongo 3,6 version features](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
