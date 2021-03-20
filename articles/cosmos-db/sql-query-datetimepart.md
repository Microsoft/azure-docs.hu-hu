---
title: DateTimePart Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DateTimePart Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 97eaff405086190b60279ac7d5cf8bf441c8f840
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336367"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A megadott DateTimePart értékét adja vissza a megadott dátum és idő között.
  
## <a name="syntax"></a>Szintaxis
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argumentumok
  
*DateTimePart*  
   Annak a dátumnak a része, amelyre a DateTimePart vissza fogja adni az értéket. Ez a táblázat felsorolja az összes érvényes DateTimePart argumentumot:

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

*Dátum/idő*  
   Az UTC dátum és idő ISO 8601 karakterláncának értéke formátumban `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Visszatérési típusok

Pozitív egész értéket ad vissza.

## <a name="remarks"></a>Megjegyzések

`undefined`A DateTimePart a következő okok miatt fog visszatérni:

- A megadott DateTimePart érték érvénytelen.
- A DateTime nem érvényes ISO 8601 DateTime

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák

Íme egy példa, amely a hónap egész értékét adja vissza:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Íme egy példa, amely a másodpercenkénti számok számát adja vissza:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
