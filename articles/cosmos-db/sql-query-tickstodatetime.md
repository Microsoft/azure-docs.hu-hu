---
title: TicksToDateTime Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function TicksToDateTime Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 89a8dba97725049b86fc6b38c09e0dd125bb48d1
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608771"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)

A megadott osztásjelek értékét DateTime értékre alakítja.
  
## <a name="syntax"></a>Szintaxis
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Argumentumok

*Ticks*  

Egy aláírt numerikus érték, amely a UNIX-kor óta eltelt 100 ns-osztásjelek aktuális számát jelöli. Ez azt jelenti, hogy a 100 ns-kullancsok száma, amelyek 00:00:00 csütörtök és 1970 január 1. óta elteltek.

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
  |Z|UTC (egyezményes világidő) jelölő||
  
  Az ISO 8601 formátumával kapcsolatos további információkért lásd: [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Megjegyzések

A TicksToDateTime akkor tér vissza `undefined` , ha a megadott osztásjelek értéke érvénytelen.

## <a name="examples"></a>Példák
  
Az alábbi példa a kullancsokat egy DateTime értékre konvertálja:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
