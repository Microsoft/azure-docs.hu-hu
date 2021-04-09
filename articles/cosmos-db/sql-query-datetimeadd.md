---
title: DateTimeAdd Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DateTimeAdd Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c04c63a5ec72f08807b1702f74db39e00662656f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597657"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Visszaadja a DateTime sztring értékét, amely egy megadott számérték (aláírt egész szám) egy megadott DateTime sztringbe való hozzáadásával adódik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Argumentumok
  
*DateTimePart*  
   Az a dátum, amelybe a DateTimeAdd egy egész számot vesz fel. Ez a táblázat felsorolja az összes érvényes DateTimePart argumentumot:

| DateTimePart | rövidítéseket        |
| ------------ | -------------------- |
| Year         | "Year", "yyyy", "éé" |
| Month (hónap)        | "hónap", "PP", "m"   |
| Nap          | "Day", "dd", "d"     |
| Óra         | "Hour", "hh"         |
| Minute       | "minute", "mi", "n"  |
| Second       | "Second", "ss", "s"  |
| Ezredmásodperces  | "ezredmásodperc", "MS"  |
| Mikromásodperces  | "második", "MCS" |
| NS   | "NS", "NS"   |

*numeric_expr*  
   Egy aláírt egész érték, amely hozzá lesz adva a megadott DateTime DateTimePart.

*Dátum/idő*  
   Az UTC dátum és idő ISO 8601 karakterláncának értéke a (z) formátumban `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
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

## <a name="return-types"></a>Visszatérési típusok

Egy UTC dátum és idő ISO 8601 sztring értékét adja vissza abban a formátumban, `YYYY-MM-DDThh:mm:ss.fffffffZ` ahol:
  
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

## <a name="remarks"></a>Megjegyzések

`undefined`A DateTimeAdd a következő okok miatt fog visszatérni:

- A megadott DateTimePart érték érvénytelen.
- A megadott numeric_expr nem érvényes egész szám.
- Az argumentumban vagy az eredményben szereplő DateTime érték nem érvényes ISO 8601 DateTime.

## <a name="examples"></a>Példák
  
Az alábbi példa 1 hónapot rendel hozzá a DateTime értékhez: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

A következő példa kivonja 2 órával a DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
