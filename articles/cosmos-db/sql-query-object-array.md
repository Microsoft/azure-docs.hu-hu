---
title: Tömbök és objektumok használata Azure Cosmos DB
description: Megtudhatja, hogyan hozhat létre tömböket és objektumokat a Azure Cosmos DBban az SQL-szintaxisban. Ez a cikk néhány példát is tartalmaz a tömb objektumain végrehajtott műveletek végrehajtására.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 2b882e1e39f035d27fc6d09d1a9d0c04691b499c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426248"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Tömbök és objektumok használata Azure Cosmos DB

A Azure Cosmos DB SQL API egyik fő funkciója a tömb és az objektum létrehozása.

## <a name="arrays"></a>Tömbök

Tömböket a következő példában látható módon hozhat létre:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Az eredmény a következő:

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

A [Array kifejezéssel](sql-query-subquery.md#array-expression) a [segédlekérdezés](sql-query-subquery.md) eredményeiből is készíthet tömböt. Ez a lekérdezés lekérdezi a tömbben lévő gyermekek egyedi nevét.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Ismétlés

Az SQL API támogatást nyújt a JSON-tömbök megismétléséhez, és egy új, a FROM forrásban található [kulcsszóval](sql-query-keywords.md#in) hozzáadott összeállítást biztosít. A következő példában:

```sql
SELECT *
FROM Families.children
```

Az eredmény a következő:

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

A következő lekérdezés ismétlést hajt végre a `children` `Families` tárolóban. A kimeneti tömb eltér az előző lekérdezéstől. Ez a példa feldarabolja `children` , és az eredményeket egyetlen tömbbe simítsa:  

```sql
SELECT *
FROM c IN Families.children
```

Az eredmény a következő:

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

A tömb egyes bejegyzésein tovább szűrhető, ahogy az alábbi példában is látható:

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

Az eredmény a következő:

```json
[{
  "givenName": "Lisa"
}]
```

Egy tömb iterációs eredménye is összesíthető. Az alábbi lekérdezés például megszámolja a gyermekek számát az összes család között:

```sql
SELECT COUNT(child)
FROM child IN Families.children
```

Az eredmény a következő:

```json
[
  {
    "$1": 3
  }
]
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Illesztések](sql-query-join.md)
