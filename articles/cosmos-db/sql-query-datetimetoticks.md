---
title: DateTimeToTicks Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DateTimeToTicks Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336333"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A megadott DateTime értékre alakítja a jelölőket. Egy pipa 100 nanoszekundumban vagy 1 10-milliomodik jelöl. 

## <a name="syntax"></a>Szintaxis
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumentumok
  
*Dátum/idő*  
   Az UTC dátum és idő ISO 8601 karakterláncának értéke formátumban `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Visszatérési típusok

Egy aláírt numerikus értéket ad vissza, amely a UNIX-kor óta eltelt 100 ns-osztásjelek aktuális számát adja eredményül. Ez azt jelenti, hogy a DateTimeToTicks a 100-ns kullancsok számát adja vissza, amelyek 00:00:00 csütörtök és 1970 január 1. között elteltek.

## <a name="remarks"></a>Megjegyzések

A DateTimeDateTimeToTicks akkor tér vissza `undefined` , ha a DateTime nem érvényes ISO 8601 datetime

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák

Íme egy példa, amely a kullancsok számát adja vissza:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Íme egy példa, amely visszaadja a kullancsok számát a tört másodpercek számának meghatározása nélkül:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
