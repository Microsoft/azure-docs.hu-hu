---
title: Tömbök és objektumok használata Azure Cosmos DB
description: Megtudhatja, hogyan hozhat létre tömböket és objektumokat a Azure Cosmos DBban az SQL-szintaxisban. Ez a cikk néhány példát is tartalmaz a tömb objektumain végrehajtott műveletek végrehajtására.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493787"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Tömbök és objektumok használata Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB SQL API egyik fő funkciója a tömb és az objektum létrehozása. Ez a dokumentum olyan példákat használ, amelyek a [család adatkészletének](sql-query-getting-started.md#upload-sample-data)használatával hozhatók létre újra.

Az alábbi példa egy példát mutat be ebben az adatkészletben:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

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

Az eredmény a következő:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Iteráció

Az SQL API támogatja a JSON-tömbök megismétlését, a FROM forrásban található [kulcsszóval](sql-query-keywords.md#in) . A következő példában:

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
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Az eredmény a következő:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Ha a IN kulcsszót használja az iterációhoz, a tömbön kívül semmilyen tulajdonság nem szűrhető vagy nem végezhető el. Ehelyett használjon [illesztéseket](sql-query-join.md).

További példákért olvassa el a [blogbejegyzések használata a Azure Cosmos db tömbökben című részt](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Összekapcsolások](sql-query-join.md)
