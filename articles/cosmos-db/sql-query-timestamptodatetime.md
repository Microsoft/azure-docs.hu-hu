---
title: TimestampToDateTime Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function TimestampToDateTime Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: e9a77ab0ac32d627d59e2cb0fa4a680f174a6833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587236"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A megadott időbélyeg-értéket dátum és idő értékre alakítja.
  
## <a name="syntax"></a>Szintaxis
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>Argumentumok

*Időbélyeg*  

Egy aláírt numerikus érték, a UNIX-kor óta eltelt ezredmásodpercek aktuális száma. Más szóval a 00:00:00 csütörtök, 1970. január 1. között eltelt ezredmásodpercek száma.

## <a name="return-types"></a>Visszatérési típusok

Az UTC dátum és idő ISO 8601 sztring értékét adja vissza abban a formátumban, `YYYY-MM-DDThh:mm:ss.fffffffZ` ahol:
  
|Formátum|Leírás|
|-|-|
|ÉÉÉÉ|négyjegyű év|
|MM|kétjegyű hónap (01 = Január stb.)|
|DD|hónap kétjegyű napja (01 – 31)|
|T|az időelemek kezdetének jelölője|
|óó|kétjegyű óra (00 – 23)|
|hh|kétjegyű perc (00 – 59)|
|mm|kétszámjegyű másodperc (00 – 59)|
|.fffffff|hét számjegyű tört másodperc|
|Z|UTC (egyezményes világidő) jelölő|
  
  Az ISO 8601 formátumával kapcsolatos további információkért lásd: [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Megjegyzések

A TimestampToDateTime akkor tér vissza `undefined` , ha a megadott időbélyeg-érték érvénytelen.

## <a name="examples"></a>Példák
  
Az alábbi példa egy DateTime értékre konvertálja az időbélyeget:

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
