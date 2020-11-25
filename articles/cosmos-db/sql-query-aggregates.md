---
title: Összesítő függvények a Azure Cosmos DBban
description: Ismerje meg az SQL összesítő függvények szintaxisát, a Azure Cosmos DB által támogatott összesítő függvények típusait.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: tisande
ms.openlocfilehash: f04590e78b5f1ea9d5e00c9f3d42c2fc32bebc5f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96001779"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Összesítő függvények a Azure Cosmos DBban
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az összesítő függvények számítást végeznek a záradékban található értékek halmazán `SELECT` , és egyetlen értéket adnak vissza. A következő lekérdezés például a tárolóban lévő elemek számát adja vissza `Families` :

## <a name="examples"></a>Példák

A használatakor `COUNT()` bármilyen érvényes skaláris kifejezést használhat, például `1` bemenetként.

```sql
    SELECT COUNT(1)
    FROM Families f
```

Az eredmény a következő:

```json
    [{
        "$1": 2
    }]
```

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

## <a name="types-of-aggregate-functions"></a>Az összesítő függvények típusai

Az SQL API a következő összesítő függvényeket támogatja. `SUM``AVG`a és a függvény numerikus értékekkel, és, és `COUNT` `MIN` `MAX` számok, karakterláncok, logikai értékek és nullák alapján működik.

| Függvény | Leírás |
|-------|-------------|
| COUNT | A kifejezésben szereplő elemek számát adja vissza. |
| SUM   | A kifejezésben szereplő értékek összegét adja vissza. |
| MIN   | A kifejezésben szereplő minimális értéket adja vissza. |
| MAX   | A kifejezésben szereplő maximális értéket adja vissza. |
| AVG   | A kifejezésben szereplő értékek átlagát adja vissza. |

A tömb iterációjának eredményét is összesítheti.

> [!NOTE]
> A Azure Portal Adatkezelő az összesítési lekérdezések részleges eredményeket összesítenek csak egy lekérdezési oldalon. Az SDK egyetlen összesített értéket hoz létre az összes oldalon. Az összesítési lekérdezések kóddal való végrehajtásához .NET SDK 1.12.0, .NET Core SDK 1.1.0 vagy Java SDK 1.9.5 vagy újabb verzió szükséges.

## <a name="remarks"></a>Megjegyzések

Ezek az összesített rendszerfunkciók kihasználják a [tartomány indexét](index-policy.md#includeexclude-strategy). Ha a (z),,, `COUNT` `SUM` `MIN` `MAX` vagy tulajdonságot szeretné elvégezni, a `AVG` [megfelelő elérési utat is meg kell adni az indexelési házirendben](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Rendszerfüggvények](sql-query-system-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)