---
title: Azure Cosmos DB API a MongoDB-hez (3,2-es verzió) támogatott funkciók és szintaxis
description: Ismerkedjen meg Azure Cosmos DB API-MongoDB (3,2 verzió) támogatott funkciókkal és szintaxissal.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce6b01a99c29020b0ede5a860da9d3d6f37e9f06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605749"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>MongoDB-hez készült Azure Cosmos DB API (3.2-es verzió): támogatott funkciók és szintaxis
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A MongoDB Azure Cosmos DB API-jával a nyílt forráskódú MongoDB [-ügyfelek](https://docs.mongodb.org/ecosystem/drivers)bármelyikének használatával kommunikálhat. A MongoDB-hez készült Azure Cosmos DB API a MongoDB [vonalprotokolljának](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) követésével lehetővé teszi a meglévő ügyfélillesztők használatát.

A Azure Cosmos DB API-MongoDB való használatával élvezheti a használt MongoDB előnyeit, és az összes olyan vállalati funkciót, amelyet a Cosmos DB biztosít: [globális elosztás](distribute-data-globally.md), [automatikus](partitioning-overview.md)horizontális felskálázás, rendelkezésre állási és késési garanciák, az összes mező automatikus indexelése, a titkosítás nyugalmi állapotban, biztonsági mentések és sok más további lehetőség.

> [!NOTE]
> A MongoDB-hez készült Cosmos DB API 3,2-es verziója nem rendelkezik aktuális tervekkel az életciklus végéhez (EOL). A jövőbeli EOL vonatkozó minimális figyelmeztetés három év.

## <a name="protocol-support"></a>Protokollok támogatása

A Azure Cosmos DB API-MongoDB tartozó összes új fiók kompatibilis a MongoDB Server **3,6**-es verziójával. Ez a cikk a MongoDB 3.2-es verzióját ismerteti. A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia a MongoDB-hez készült Azure Cosmos DB API-hoz. 

A MongoDB-hez készült Azure Cosmos DB API emellett zökkenőmentes frissítési élményt nyújt a fiókokhoz. További információ a MongoDB verziófrissítési [útmutatójában](mongodb-version-upgrade.md)olvasható.

## <a name="query-language-support"></a>Nyelvi támogatás lekérdezése

A Azure Cosmos DB API-MongoDB átfogó támogatást nyújt a MongoDB lekérdezési nyelvi szerkezetekhez. Az alábbiakban megtalálhatja a jelenleg támogatott műveletek, operátorok, szakaszok, parancsok és beállítások részletes listáját.

## <a name="database-commands"></a>Adatbázisparancsok

Azure Cosmos DB API-MongoDB a következő adatbázis-parancsokat támogatja:

> [!NOTE]
> Ez a cikk csak a támogatott kiszolgálói parancsokat sorolja fel, és kizárja az ügyféloldali burkoló funkciókat. Az ügyféloldali burkoló függvények, például a és `deleteMany()` `updateMany()` a kiszolgáló-parancsok belső kihasználtsága `delete()` `update()` . A támogatott kiszolgálói parancsokat használó függvények kompatibilisek Azure Cosmos DB API-MongoDB.

### <a name="query-and-write-operation-commands"></a>Lekérdezési és írási műveletek parancsai

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Hitelesítési parancsok

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Adminisztrációs parancsok

- dropDatabase
- listCollections
- drop
- létrehozás
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Diagnosztikai parancsok

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Összesítési folyamat</a>

A Cosmos DB a nyilvános előzetes verzióban támogatja a MongoDB 3,2-es összesítési folyamatát. A nyilvános előzetes verzióra való regisztrációval kapcsolatos útmutatásért tekintse meg az [Azure blogot](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/).

### <a name="aggregation-commands"></a>Összesítési parancsok

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Összesítési fázisok

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Összesítő kifejezések

#### <a name="boolean-expressions"></a>Logikai kifejezések

- $and
- $or
- $not

#### <a name="set-expressions"></a>Halmazkifejezések

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Összehasonlító kifejezések

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetikai kifejezések

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Sztringkifejezések

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Tömbkifejezések

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Dátumkifejezések

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Feltételes kifejezések

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Összesítő gyűjtők

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operátorok

Alább a támogatott operátorok láthatók a használatukat bemutató megfelelő példákkal. Tekintse meg a lenti lekérdezésekben használt mintadokumentumot:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

| Operátor | Példa |
| --- | --- |
| $eq | `{ "Volcano Name": { $eq: "Rainier" } }` |
| $gt | `{ "Elevation": { $gt: 4000 } }` | 
| $gte | `{ "Elevation": { $gte: 4392 } }` | 
| $lt | `{ "Elevation": { $lt: 5000 } }` | 
| $lte | `{ "Elevation": { $lte: 5000 } }` | 
| $ne | `{ "Elevation": { $ne: 1 } }` | 
| $in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |
| $nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` |
| $or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` | 
| $and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |
| $not | `{ "Elevation": { $not: { $gt: 5000 } } }`| 
| $nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |
| $exists | `{ "Status": { $exists: true } }`|
| $type | `{ "Status": { $type: "string" } }`| 
| $mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` | 
| $regex | `{ "Volcano Name": { $regex: "^Rain"} }`| 

### <a name="notes"></a>Jegyzetek

A $regex lekérdezésekben a balra horgonyzott kifejezések engedélyezik az indexben való keresést. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.
Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni.
Például ha az eredeti lekérdezés a következő: ```find({x:{$regex: /^abc$/})```, a következőképpen kell módosítani: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket.
A „|” sávoperátor „vagy” függvényként működik – a(z) ```find({x:{$regex: /^abc|^def/})``` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Frissítési operátorok

#### <a name="field-update-operators"></a>Mezőfrissítő operátorok

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Tömbfrissítő operátorok

- $addToSet
- $pop
- $pullAll
- $pull  (Megjegyzés: a $pull feltétellel együtt való használata nem támogatott)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Bitenként frissítő operátor

- $bit

### <a name="geospatial-operators"></a>Térinformatikai operátorok

Operátor | Példa | Támogatott |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Yes |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Yes |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Yes |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |

## <a name="sort-operations"></a>Rendezési műveletek

A művelet használatakor a `findOneAndUpdate` rendezési műveletek egyetlen mező esetén támogatottak, de a rendezési műveletek több mezőn nem támogatottak.

## <a name="other-operators"></a>Egyéb operátorok

Operátor | Példa | Megjegyzések
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Nem támogatott. A $regex használható helyette.

## <a name="unsupported-operators"></a>Nem támogatott operátorok

A Cosmos DB nem támogatja a ```$where``` és az ```$eval``` operátort.

### <a name="methods"></a>Metódusok

A következő metódusok támogatottak:

#### <a name="cursor-methods"></a>Kurzormetódusok

Metódus | Példa | Megjegyzések
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | A rendszer nem adja vissza a rendezési kulccsal nem rendelkező dokumentumokat

## <a name="unique-indexes"></a>Egyedi indexek

A Cosmos DB indexeli az összes olyan mezőt, amely alapértelmezés szerint az adatbázisba íródik. Az egyedi indexek biztosítják, hogy egy adott mező ne legyen duplikált érték a gyűjtemény összes dokumentumában, hasonlóan ahhoz, ahogyan az egyediség megmarad az alapértelmezett `_id` kulcson. A createIndex parancs használatával egyéni indexeket hozhat létre Cosmos DBban, beleértve az "egyedi" korlátozást is.

A MongoDB Azure Cosmos DB API-ját használó Cosmos-fiókok esetében egyedi indexek érhetők el.

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

A Cosmos DB a dokumentum időbélyegzője alapján támogatja a élettartamot (TTL). A TTL engedélyezhető a gyűjtemények számára, ha a [Azure Portalra](https://portal.azure.com)kattint.

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

A Cosmos DB még nem támogatja a felhasználókat és a szerepköröket. A Cosmos DB azonban támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), valamint az írási és olvasási és csak olvasható jelszavakat/kulcsokat, amelyeket a [Azure Portal](https://portal.azure.com) (kapcsolati karakterlánc oldalon) lehet beszerezni.

## <a name="replication"></a>Replikáció

Cosmos DB támogatja az automatikus és a natív replikálást a legalacsonyabb rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. A Cosmos DB nem támogatja a manuális replikálási parancsokat.

## <a name="write-concern"></a>Írási szempont

Néhány alkalmazás olyan [írási aggályra](https://docs.mongodb.com/manual/reference/write-concern/) támaszkodik, amely az írási művelet során szükséges válaszok számát határozza meg. Mivel a Cosmos DB a replikációt a háttérben kezeli, alapértelmezés szerint minden írás automatikusan kvórum. Az ügyfél kódja által meghatározott írási aggályok figyelmen kívül lesznek hagyva. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

## <a name="sharding"></a>Sharding

Az Azure Cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. A szegmensek létrehozását, elhelyezését és kiegyensúlyozását automatikusan kezeli. Azure Cosmos DB nem támogatja a manuális horizontális skálázási parancsokat, ami azt jelenti, hogy nem kell olyan parancsokat meghívnia, mint például a shardCollection, a addShard, a balancerStart, a moveChunk stb. A tárolók létrehozásakor vagy az adatlekérdezés során csak a szegmens kulcsát kell megadnia.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
