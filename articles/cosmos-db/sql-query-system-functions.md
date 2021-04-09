---
title: Rendszerfüggvények Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg a beépített és a felhasználó által definiált SQL-rendszerfunkciókkal Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 36ebe80671c77cc83ddba770e3259f6542472e58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526839"
---
# <a name="system-functions-azure-cosmos-db"></a>Rendszerfunkciók (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A Cosmos DB számos beépített SQL-függvényt biztosít. A beépített függvények kategóriái alább láthatók.  
  
|Function csoport|Leírás|Üzemeltetés|  
|--------------|-----------------|-----------------| 
|[Tömb függvények](sql-query-array-functions.md)|A tömb függvények a tömb bemeneti értékén hajtanak végre műveletet, és numerikus, logikai vagy tömb értéket adnak vissza. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Dátum és idő függvények](sql-query-date-time-functions.md)|A dátum-és időfüggvények lehetővé teszik az aktuális UTC dátum és idő megszerzését két formában; egy numerikus időbélyeg, amelynek értéke a UNIX-kor ezredmásodpercben, vagy egy olyan karakterlánc, amely megfelel az ISO 8601 formátumnak. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md), [GetCurrentTicks](sql-query-getcurrentticks.md) |
|[Matematikai függvények](sql-query-mathematical-functions.md)|A matematikai függvények mindegyike számítást végez, általában az argumentumként megadott bemeneti értékeken alapul, és numerikus értéket ad vissza. | [ABS](sql-query-abs.md), [ACO](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [mennyezet](sql-query-ceiling.md), [Cos](sql-query-cos.md), [gyermekágy](sql-query-cot.md), [fok](sql-query-degrees.md), [exp](sql-query-exp.md), [Floor](sql-query-floor.md), [log](sql-query-log.md), [log10](sql-query-log10.md), [PI](sql-query-pi.md), [Power](sql-query-power.md), [radián](sql-query-radians.md), [Rand](sql-query-rand.md), [Round](sql-query-round.md), [Sign](sql-query-sign.md), [Sin](sql-query-sin.md), [Sqrt](sql-query-sqrt.md), [Square](sql-query-square.md), [Tan](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Térbeli függvények](sql-query-spatial-functions.md)|A térbeli függvények egy műveletet hajtanak végre egy térbeli objektum bemeneti értékén, és numerikus vagy logikai értéket adnak vissza. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Sztringfüggvények](sql-query-string-functions.md)|A karakterlánc-függvény egy karakterlánc-bemeneti értéken hajt végre műveletet, és karakterláncot, numerikus vagy logikai értéket ad vissza. | [Összefűzés](sql-query-concat.md), [tartalmaz](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [bal](sql-query-left.md), [hosszúság](sql-query-length.md), [alsó](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[Csere](sql-query-replace.md), [REPLIKÁLÁS](sql-query-replicate.md), [fordított](sql-query-reverse.md), [jobb](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [alstring](sql-query-substring.md), [ToString](sql-query-tostring.md), [Trim](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Típusellenőrzési függvények](sql-query-type-checking-functions.md)|A típus-ellenőrzési függvények lehetővé teszik egy kifejezés típusának ellenőrzését az SQL-lekérdezéseken belül. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Beépített és felhasználó által definiált függvények (UDF)

Ha jelenleg olyan felhasználó által definiált függvényt (UDF) használ, amelyhez már elérhető egy beépített függvény, a megfelelő beépített függvény gyorsabban fog futni és hatékonyabbá válik.

## <a name="built-in-versus-ansi-sql-functions"></a>Beépített és ANSI SQL-függvények

Cosmos DB függvények és az ANSI SQL functions közötti fő különbség az, hogy a Cosmos DB Functions úgy lett kialakítva, hogy megfelelően működjön a séma nélküli és a vegyes séma adataival. Ha például egy tulajdonság hiányzik vagy nem numerikus értékkel rendelkezik (például: `undefined` ), az elem kimarad a hiba helyett.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Tömb függvények](sql-query-array-functions.md)
- [Dátum- és időfüggvények](sql-query-date-time-functions.md)
- [Matematikai függvények](sql-query-mathematical-functions.md)
- [Térbeli függvények](sql-query-spatial-functions.md)
- [Sztringfüggvények](sql-query-string-functions.md)
- [Típusellenőrzési függvények](sql-query-type-checking-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregate-functions.md)
