---
title: Összesítő függvények a Azure Cosmos DBban
description: Ismerje meg az SQL összesítő függvények szintaxisát, a Azure Cosmos DB által támogatott összesítő függvények típusait.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553383"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Összesítő függvények a Azure Cosmos DBban
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az összesítő függvények számítást végeznek a záradékban található értékek halmazán `SELECT` , és egyetlen értéket adnak vissza. A következő lekérdezés például egy tárolóban lévő elemek számát adja vissza:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Az összesítő függvények típusai

Az SQL API a következő összesítő függvényeket támogatja. `SUM``AVG`a és a függvény numerikus értékekkel, és, és `COUNT` `MIN` `MAX` számok, karakterláncok, logikai értékek és nullák alapján működik.

| Függvény | Leírás |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | A kifejezésben szereplő értékek átlagát adja vissza. |
| [COUNT](sql-query-aggregate-count.md) | A kifejezésben szereplő elemek számát adja vissza. |
| [MAX](sql-query-aggregate-max.md) | A kifejezésben szereplő maximális értéket adja vissza. |
| [MIN](sql-query-aggregate-min.md) | A kifejezésben szereplő minimális értéket adja vissza. |
| [SUM](sql-query-aggregate-sum.md) | A kifejezésben szereplő értékek összegét adja vissza. |


A VALUE kulcsszó használatával csak az Összesítés skaláris értékét adhatja vissza. Például a következő lekérdezés az értékek számát adja vissza egyetlen számként:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Az eredmény a következő:

```json
    [ 2 ]
```

Az összesítéseket szűrők használatával is egyesítheti. Például a következő lekérdezés visszaadja a címe állapotú elemek számát `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Az eredmény a következő:

```json
    [ 1 ]
```

## <a name="remarks"></a>Megjegyzések

Ezek az összesített rendszerfunkciók kihasználják a [tartomány indexét](index-policy.md#includeexclude-strategy). Ha a (z),,, `AVG` `COUNT` `MAX` `MIN` vagy tulajdonságot szeretné elvégezni, az `SUM` [indexelési házirendben meg kell adni a megfelelő elérési utat is](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Rendszerfüggvények](sql-query-system-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)