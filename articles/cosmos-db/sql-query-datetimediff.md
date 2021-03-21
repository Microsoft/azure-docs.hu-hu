---
title: DateTimeDiff Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DateTimeDiff Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595637"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
A megadott DateTimePart határait (aláírt egész értékként) adja vissza a megadott *StartDate* és *EndDate* között.
  
## <a name="syntax"></a>Szintaxis
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*StartDate*  
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

*EndDate*  
   Az UTC dátum és idő ISO 8601 karakterláncának értéke formátumban `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Visszatérési típusok

Egy aláírt egész értéket ad vissza.

## <a name="remarks"></a>Megjegyzések

`undefined`A DateTimeDiff a következő okok miatt fog visszatérni:

- A megadott DateTimePart érték érvénytelen.
- A StartDate vagy a EndDate nem érvényes ISO 8601 DateTime

A DateTimeDiff mindig egy aláírt egész értéket ad vissza, és a DateTimePart-határok számának mérését, az időintervallum mérése nélkül.

## <a name="examples"></a>Példák
  
Az alábbi példa a és a közötti napi határvonalak számát számítja ki `2020-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

A következő példa a és a közötti év közötti határok számát számítja `2028-01-01T01:02:03.1234527Z` ki `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

A következő példa a és a közötti óra határok számát számítja ki `2020-01-01T01:00:00.1234527Z` `2020-01-01T01:59:59.1234567Z` . Annak ellenére, hogy ezek a DateTime értékek több mint 0,99 órát vesznek igénybe, a `DateTimeDiff` 0 értéket adja vissza, mert nem lépte túl az óra határait.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
