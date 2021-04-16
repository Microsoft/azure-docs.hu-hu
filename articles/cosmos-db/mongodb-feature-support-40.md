---
title: A 4.0-s kiszolgálóverzió által támogatott szolgáltatások és szintaxis Azure Cosmos DB a MongoDB API-jának
description: Ismerje meg Azure Cosmos DB MongoDB 4.0-s verziójú kiszolgálói API támogatott funkcióit és szintaxisát. Megismerheti az adatbázisparancsokat, a lekérdezési nyelv támogatását, az adattípusokat, az összesítési folyamat parancsokat és a támogatott operátorokat.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 74e08cce381d173e0c5e1458ae99167a0c8c697f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504510"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>Azure Cosmos DB MongoDB API (4.0-s kiszolgálóverzió): támogatott funkciók és szintaxis
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A MongoDB Azure Cosmos DB API-jának használatával bármely nyílt forráskódú MongoDB-ügyfélillesztővel [kommunikálhat.](https://docs.mongodb.org/ecosystem/drivers) A MongoDB-hez készült Azure Cosmos DB API a MongoDB [vonalprotokolljának](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) követésével lehetővé teszi a meglévő ügyfélillesztők használatát.

A MongoDB-hez használható Azure Cosmos DB API-val kihasználhatja a MongoDB előnyeit, valamint a Cosmos DB által biztosított összes vállalati [képességet:](distribute-data-globally.md)a globális terjesztést, az automatikus horizontális skálázást, a rendelkezésre állási és késési garanciákat, az adattitkosítást és a biztonsági mentéseket. [](partitioning-overview.md)

## <a name="protocol-support"></a>Protokolltámogatás

A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia a MongoDB-hez készült Azure Cosmos DB API-hoz. A Azure Cosmos DB API MongoDB-fiókokhoz való használata esetén a fiókok 3.6+-os verzióinak végpontja formátumban van, míg a 3.2-es verziójú fiókok végpontja formátumban `*.mongo.cosmos.azure.com` `*.documents.azure.com` van.

> [!NOTE]
> Ez a cikk csak a támogatott kiszolgálóparancsokat sorolja fel, és kizárja az ügyféloldali burkoló függvényeket. Az olyan ügyféloldali burkolófunkciók, mint a `deleteMany()` és `updateMany()` a belsőleg használják a és `delete()` a `update()` kiszolgálóparancsot. A támogatott kiszolgálóparancsokat használó függvények kompatibilisek Azure Cosmos DB MongoDB API-jának használatával.

## <a name="query-language-support"></a>Lekérdezési nyelv támogatása

Azure Cosmos DB MongoDB API átfogó támogatást nyújt a MongoDB lekérdezési nyelvi szerkezetéhez. Az alábbiakban megtalálja a jelenleg támogatott műveletek, operátorok, fázisok, parancsok és beállítások részletes listáját.

## <a name="database-commands"></a>Adatbázisparancsok

Azure Cosmos DB MongoDB API a következő adatbázisparancsokat támogatja:

### <a name="query-and-write-operation-commands"></a>Lekérdezési és írási műveletek parancsai

| Parancs | Támogatott |
|---------|---------|
| [streamek módosítása](mongodb-change-streams.md) | Igen |
| delete | Igen |
| Eval | Nem |
| find | Igen |
| findAndModify | Igen |
| getLastError | Igen |
| getMore | Igen |
| getPrevError | Nem |
| insert | Igen |
| parallelCollectionScan | Nem |
| resetError | Nem |
| update | Igen |

### <a name="transaction-commands"></a>Tranzakciós parancsok

| Parancs | Támogatott |
|---------|---------|
| abortTransaction | Igen |
| commitTransaction (véglegesítés) | Igen |

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
| convertToCapped (convertToCapped) | Nem |
| copydb | Nem |
| létrehozás | Igen |
| createIndexes | Igen |
| currentOp (aktuálisop) | Igen |
| drop | Igen |
| dropDatabase | Igen |
| dropIndexes | Igen |
| filemd5 | Igen |
| killCursors | Igen |
| killOp | Nem |
| listCollections | Igen |
| listDatabases | Igen |
| listIndexes | Igen |
| reIndex | Igen |
| renameCollection (átnevezés) | Nem |

### <a name="diagnostics-commands"></a>Diagnosztikai parancsok

| Parancs | Támogatott |
|---------|---------|
| buildInfo | Igen |
| collStats | Igen |
| connPoolStats | Nem |
| connectionStatus | Nem |
| dataSize (adatsize) | Nem |
| dbHash | Nem |
| dbStats | Igen |
| Magyarázni | Igen |
| funkciók | Nem |
| hostInfo | Igen |
| listDatabases | Igen |
| listCommands | Nem |
| Profiler | Nem |
| serverStatus (kiszolgálóállapot) | Nem |
| top | Nem |
| whatsmyuri | Igen |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Összesítési folyamat

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
| $lookup | Részleges |
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

> [!NOTE]
> `$lookup` A még nem támogatja a kiszolgáló 3.6-os verziójában bevezetett [korrelálatlan](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries) segédlekérdezéseket. Ha a és a mezővel próbálja használni az operátort, hibaüzenet `let is not supported` jelenik meg a következő `$lookup` `let` `pipeline` üzenettel: .

### <a name="boolean-expressions"></a>Logikai kifejezések

| Parancs | Támogatott |
|---------|---------|
| $and | Igen |
| $not | Igen |
| $or | Igen |

### <a name="conversion-expressions"></a>Konverziós kifejezések

| Parancs | Támogatott |
|---------|---------|
| $convert | Igen |
| $toBool | Igen |
| $toDate | Igen |
| $toDecimal | Igen |
| $toDouble | Igen |
| $toInt | Igen |
| $toLong | Igen |
| $toObjectId | Igen |
| $toString | Igen |

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
| $ltrim | Igen |
| $rtrim | Igen |
| $trim | Igen |
| $split | Igen |
| $strLenBytes | Igen |
| $strLenCP | Igen |
| $strcasecmp | Igen |
| $substr | Igen |
| $substrBytes | Igen |
| $substrCP | Igen |
| $toLower | Igen |
| $toUpper | Igen |

### <a name="text-search-operator"></a>Szövegkeresési operátor

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
| $$CURRENT | Igen |
| $$DESCEND | Igen |
| $$KEEP | Igen |
| $$PRUNE | Igen |
| $$REMOVE | Igen |
| $$ROOT | Igen |

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

### <a name="data-type-operator"></a>Adattípus operátor

| Parancs | Támogatott |
|---------|---------|
| $type | Igen |

### <a name="accumulator-expressions"></a>Gyűjtőkifejezések

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

### <a name="merge-operator"></a>Egyesítés operátor

| Parancs | Támogatott |
|---------|---------|
| $mergeObjects | Igen |

## <a name="data-types"></a>Adattípusok

Azure Cosmos DB MongoDB API támogatja a MongoDB BSON formátumban kódolt dokumentumokat. A 4.0-s API-verzió javítja ennek a formátumnak a belső használatát a teljesítmény javítása és a költségek csökkentése érdekében. A 4.0-s végponton keresztül írt vagy frissített dokumentumok esetében ez előnyös.
 
Frissítési [forgatókönyv](mongodb-version-upgrade.md)esetén a 4.0-s verzióra való frissítés előtt írt dokumentumok nem élveznek előnyt a továbbfejlesztett teljesítményben, amíg nem frissülnek egy írási művelettel a 4.0-s végponton keresztül.

| Parancs | Támogatott |
|---------|---------|
| Dupla | Igen |
| Sztring | Igen |
| Objektum | Igen |
| Tömb | Igen |
| Bináris adatok | Igen | 
| ObjectId | Igen |
| Logikai | Igen |
| Date | Igen |
| Null | Igen |
| 32 bites egész szám (int) | Igen |
| Időbélyeg | Igen |
| 64 bites egész szám (hosszú) | Igen |
| MinKey (Minimális kulcs) | Igen |
| MaxKey (Maximális kulcs) | Igen |
| Decimális128 | Igen | 
| Reguláris kifejezés | Igen |
| JavaScript | Igen |
| JavaScript (hatókörrel)| Igen |
| Meghatározatlan | Igen |

## <a name="indexes-and-index-properties"></a>Indexek és indextulajdonságok

### <a name="indexes"></a>Indexek

| Parancs | Támogatott |
|---------|---------|
| Egymezős index | Igen |
| Összetett index | Igen |
| Többkulcsos index | Igen |
| Szövegindex | Nem |
| 2dsphere | Igen |
| 2d Index | Nem |
| Kivonatolt index | Igen |

### <a name="index-properties"></a>Index tulajdonságai

| Parancs | Támogatott |
|---------|---------|
| TTL | Igen |
| Egyedi | Igen |
| Részleges | Nem |
| A kis- és a kis- és a | Nem |
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
| $text | Nem (nem támogatott. Használja helyette $regex-t.)| 
| $where | Nem | 

Az $regex lekérdezésekben a bal oldali horgonyú kifejezések lehetővé teszik az indexkeresést. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.

Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni. A következő eredeti lekérdezés esetén például a következőképpen kell `find({x:{$regex: /^abc$/})` módosítani:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket. A „|” sávoperátor „vagy” függvényként működik – a(z) `find({x:{$regex: /^abc |^def/})` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Tömb operátorok

| Parancs | Támogatott | 
|---------|---------|
| $all | Igen | 
| $elemMatch | Igen | 
| $size | Igen | 

### <a name="comment-operator"></a>Megjegyzés operátor

| Parancs | Támogatott | 
|---------|---------|
| $comment | Igen | 

### <a name="projection-operators"></a>Leképezés operátorok

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

A művelet használata esetén az egy mezőhöz használható rendezési műveletek támogatottak, de a rendezési műveletek több mezőn `findOneAndUpdate` nem támogatottak.

## <a name="unique-indexes"></a>Egyedi indexek

[Az egyedi indexek](mongodb-indexing.md#unique-indexes) biztosítják, hogy egy adott mezőben ne legyen ismétlődő érték a gyűjteményben lévő összes dokumentumban, hasonlóan ahhoz, ahogyan az alapértelmezett "_id" kulcs megőrzi az egyediséget. Egyedi indexeket hozhat létre a Azure Cosmos DB a paranccsal és a `createIndex` `unique` megkötési paraméterrel:

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

[Az összetett indexek](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) segítségével legfeljebb nyolc mezőhöz hozhat létre indexet mezőcsoportokhoz. Ez az indextípus eltér a natív MongoDB összetett indextől. A Azure Cosmos DB összetett indexeket használnak a több mezőre alkalmazott műveletek rendezési műveleteihez. Összetett index létrehozásához több tulajdonságot kell megadnia paraméterként:

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

Azure Cosmos DB GridFS bármely GridFS-kompatibilis Mongo-illesztőn keresztül támogatott.

## <a name="replication"></a>Replikáció

Az Azure Cosmos DB támogatja az automatikus natív replikációt a legalsó rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. Cosmos DB nem támogatja a manuális replikációs parancsokat.

## <a name="retryable-writes"></a>Újrapróbálható írások

Cosmos DB még nem támogatja az újrapróbálható írásokat. Az ügyfélillesztőknek hozzá kell adniuk a "retryWrites=false" URL-paramétert a kapcsolati sztringjükhez. További URL-paramétereket is hozzá lehet adni, ha előtagként egy "&" előtagot. 

## <a name="sharding"></a>Sharding

Az Azure Cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. Automatikusan kezeli a szegmensek létrehozását, elhelyezését és terheléselosztását. Azure Cosmos DB nem támogatja a manuális horizontális skálázás parancsokat, ami azt jelenti, hogy nem kell olyan parancsokat meghívni, mint az addShard, balancerStart, moveChunk stb. A szegmenskulcsot csak a tárolók létrehozása vagy az adatok lekérdezése során kell megadnia.

## <a name="sessions"></a>Munkamenetek

Azure Cosmos DB még nem támogatja a kiszolgálóoldali munkamenetparancsokat.

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

Azure Cosmos DB a dokumentum időbélyege alapján támogatja az időalapú (TTL) időt. Az TTL engedélyezhető gyűjtemények számára a következő [Azure Portal.](https://portal.azure.com)

## <a name="transactions"></a>Tranzakciók

A többdokumentumos tranzakciók nem horizontálisan skálázatlan gyűjteményen belül támogatottak. A többdokumentumos tranzakciók nem támogatottak a gyűjtemények és a horizontálisan skálázott gyűjtemények esetében. A tranzakciók időkorlátja rögzített 5 másodperc.

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

Az Azure Cosmos DB még nem támogatja a felhasználókat és a szerepköröket. A Cosmos DB azonban támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és az írási/írási és írási jogosultsággal kapcsolatos jelszavakat/kulcsokat, amelyek a [Azure Portal (Kapcsolati](https://portal.azure.com) sztring lap) keresztül szerezhetők be.

## <a name="write-concern"></a>Írási szempont

Egyes alkalmazások írási [szempontra támaszkodnak,](https://docs.mongodb.com/manual/reference/write-concern/)amely meghatározza az írási művelethez szükséges válaszok számát. Mivel a Cosmos DB a replikációt a háttérben kezeli, alapértelmezés szerint minden írás automatikusan kvórum. A rendszer figyelmen kívül hagyja az ügyfélkód által meghatározott írási szempontokat. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) Azure Cosmos DB MongoDB API-jának használatával.
- Megtudhatja, hogyan használhatja a [Robo 3T-t](mongodb-robomongo.md) Azure Cosmos DB MongoDB API-jának használatával.
- Fedezze fel a [](mongodb-samples.md) MongoDB-mintákat Azure Cosmos DB MongoDB API-jának használatával.
