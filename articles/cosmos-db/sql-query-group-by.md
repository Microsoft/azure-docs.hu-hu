---
title: GROUP BY záradék Azure Cosmos DB
description: A Azure Cosmos DB GROUP BY záradékának megismerése.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: d9cafc100ddd4b553577c447e82334e6ee7d1b6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545451"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY záradék Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A GROUP BY záradék a lekérdezés eredményét egy vagy több megadott tulajdonság értékei alapján osztja el.

## <a name="syntax"></a>Szintaxis

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumentumok

- `<scalar_expression_list>`

   Meghatározza azokat a kifejezéseket, amelyeket a rendszer a lekérdezési eredmények felosztásához használ majd.

- `<scalar_expression>`
  
   Minden skaláris kifejezés engedélyezett a skaláris allekérdezések és a skaláris összesítések kivételével. Minden skaláris kifejezésnek tartalmaznia kell legalább egy tulajdonság-referenciát. Nem korlátozható az egyes kifejezések száma, illetve a kifejezések kardinálisa.

## <a name="remarks"></a>Megjegyzések
  
  Ha egy lekérdezés GROUP BY záradékot használ, a SELECT záradék csak a GROUP BY záradékban szereplő tulajdonságok és rendszerfüggvények részhalmazát tartalmazza. Az egyik kivétel az [összesítő függvények](sql-query-aggregate-functions.md), amelyek a Select záradékban megjelenhetnek a Group By záradék használata nélkül. A SELECT záradékban mindig szerepelhetnek literál értékeket is.

  A GROUP BY záradéknak a SELECT, a FROM, a WHERE záradék és az ELTOLÁSi korlát záradéka után kell szerepelnie. A GROUP BY utasítás nem használható ORDER BY záradékkal, de ez tervezett.

  A GROUP BY záradék nem engedélyezi a következők egyikét sem:
  
- Alias-tulajdonságok vagy aliasrendszer-függvények (a SELECT záradékban továbbra is engedélyezett az alias)
- Segédlekérdezések
- Összesítő rendszerfunkciók (ezek csak a SELECT záradékban engedélyezettek)

Az összesített rendszerfunkcióval és allekérdezéssel rendelkező lekérdezések `GROUP BY` nem támogatottak. Például a következő lekérdezés nem támogatott:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Példák

Ezek a példák a [Azure Cosmos DB lekérdezési demókörnyezet](https://www.documentdb.com/sql/demo)által elérhető táplálkozási adatkészletet használják.

Például itt egy olyan lekérdezés, amely az egyes foodGroup lévő elemek teljes számát adja vissza:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Néhány eredmény (a leggyakoribb kulcsszó az eredmények korlátozására szolgál):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Ez a lekérdezés két kifejezést használ az eredmények felosztásához:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Néhány eredmény:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

A lekérdezés a GROUP BY záradékban található rendszerfüggvényt tartalmaz:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Néhány eredmény:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Ez a lekérdezés a kulcsszavakat és a rendszerfüggvényeket is használja az Item tulajdonság kifejezésében:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Az eredmény a következő:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [Aggregátumfüggvények](sql-query-aggregate-functions.md)
