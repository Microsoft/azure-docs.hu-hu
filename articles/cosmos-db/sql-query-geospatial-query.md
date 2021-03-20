---
title: Térinformatikai adatbázis lekérdezése Azure Cosmos DB
description: Térbeli adatainak lekérdezése Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336129"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Térinformatikai adatbázis lekérdezése Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk bemutatja, hogyan lehet lekérdezni a térinformatikai adataikat Azure Cosmos DB az SQL és a LINQ használatával. Jelenleg csak Azure Cosmos DB SQL API-fiókok támogatják a térinformatikai adatok tárolását és elérését. A Azure Cosmos DB a következő Nyílt térinformatikai konzorcium (OGC) beépített függvényeket támogatja a térinformatikai lekérdezésekhez. Az SQL nyelv beépített funkcióinak teljes készletével kapcsolatos további információkért lásd: [rendszerfunkciók lekérdezése Azure Cosmos DBban](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Térbeli SQL beépített függvények

Itt találja a Azure Cosmos DB lekérdezéséhez hasznos térinformatikai rendszerfunkciók listáját:

|**Használat**|**Leírás**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | A két GeoJSON pont, a sokszög vagy a LineString kifejezés közötti távolságot adja vissza.|
|ST_WITHIN (spatial_expr, spatial_expr) | Egy logikai kifejezést ad vissza, amely azt jelzi, hogy az első GeoJSON objektum (pont, sokszög vagy LineString) a második GeoJSON objektumon (pont, sokszög vagy LineString) belül van-e.|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Egy logikai kifejezést ad vissza, amely azt jelzi, hogy a két megadott GeoJSON-objektum (pont, sokszög vagy LineString) metszve van-e.|
|ST_ISVALID| Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes-e.|
| ST_ISVALIDDETAILED| Egy logikai értéket tartalmazó JSON-értéket ad vissza, ha a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes. Ha érvénytelen, a rendszer karakterlánc-értékként adja vissza az okot.|

A térbeli függvények használatával közelségi lekérdezéseket végezhet a térbeli adatokon. Például egy olyan lekérdezés, amely az összes olyan családi dokumentumot adja vissza, amely a megadott hely 30 km-n belül található a `ST_DISTANCE` beépített függvénnyel.

**Lekérdezés**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Results (Eredmények)**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Ha a térbeli indexelést az indexelési házirend tartalmazza, akkor a "távolsági lekérdezések" hatékonyan lesznek kézbesítve az indexen keresztül. További információ a térbeli indexelésről: [térinformatikai indexelés](sql-query-geospatial-index.md). Ha nem rendelkezik térbeli indexszel a megadott elérési utakhoz, a lekérdezés ellenőrzi a tárolót.

`ST_WITHIN` a használatával ellenőrizhető, hogy egy pont egy Sokszögen belül található-e. A gyakran használt sokszögek olyan határokat jelölnek, mint például a zip-kódok, az állami határok vagy a természetes képződmények. Ha a térbeli indexelést is tartalmazza az indexelési házirendben, a "belül" lekérdezéseket a rendszer hatékonyan kézbesíti az indexen keresztül.

A-ben a sokszög argumentumai `ST_WITHIN` csak egyetlen gyűrűt tartalmazhatnak, azaz a sokszög nem tartalmazhat lyukakat bennük.

**Lekérdezés**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Results (Eredmények)**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Hasonlóan ahhoz, ahogyan a nem egyező típusok működnek Azure Cosmos DB lekérdezésekben, ha az argumentumban megadott Helykód helytelen formátumú vagy érvénytelen, akkor a rendszer nem **definiált** értékre értékeli, és a kiértékelt dokumentumot kihagyja a lekérdezés eredményeiből. Ha a lekérdezés nem ad vissza találatot, futtassa a parancsot a (z) parancs futtatásával, `ST_ISVALIDDETAILED` hogy miért érvénytelen a térbeli típus.
>
>

A Azure Cosmos DB támogatja az inverz lekérdezések végrehajtását is, azaz a sokszögeket vagy vonalakat indexelheti Azure Cosmos DB, majd lekérdezheti a megadott pontot tartalmazó területeket. Ezt a mintát gyakran használják a logisztikában annak azonosítására, hogy például egy teherautó belép vagy kilép egy kijelölt régióból.

**Lekérdezés**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Results (Eredmények)**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` és használható `ST_ISVALIDDETAILED` annak ellenőrzéséhez, hogy a térbeli objektum érvényes-e. Például a következő lekérdezés ellenőrzi egy pont érvényességét egy tartományon kívüli szélességi értékkel (-132,8). `ST_ISVALID` csak egy logikai értéket ad vissza, és `ST_ISVALIDDETAILED` visszaadja a logikai értéket, és egy olyan karakterláncot, amely az OK okának okát tartalmazza.

**Lekérdezés**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results (Eredmények)**

```json
    [{
      "$1": false
    }]
```

Ezek a függvények a sokszögek ellenőrzéséhez is használhatók. Itt például `ST_ISVALIDDETAILED` egy nem lezárt sokszög ellenőrzésére használjuk.

**Lekérdezés**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Results (Eredmények)**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ-lekérdezés a .NET SDK-ban

Az SQL .NET SDK emellett a `Distance()` `Within()` betekintő módszereit és a LINQ-kifejezéseken belüli használatot is tartalmazza. Az SQL LINQ-szolgáltató lefordítja ezt a metódust a megfelelő SQL beépített függvények hívására (ST_DISTANCE és ST_WITHIN, illetve).

Íme egy példa egy LINQ-lekérdezésre, amely megkeresi az Azure Cosmos-tárolóban található összes olyan dokumentumot, amelynek `location` értéke a megadott pont 30 km-es sugarán belül van a LINQ használatával.

**LINQ-lekérdezés távolsághoz**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Hasonlóképpen, az alábbi lekérdezéssel megkeresheti az összes olyan dokumentumot, amely `location` a megadott dobozon/sokszögen belül található.

**LINQ-lekérdezés a következőn belül:**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezdheti meg a térinformatikai támogatás használatát Azure Cosmos DBban, a következő lehetőségekkel:

* További információ a [Azure Cosmos db lekérdezésről](sql-query-getting-started.md)
* További információ a [térinformatikai és a GeoJSON-hely adatainak Azure Cosmos db](sql-query-geospatial-intro.md)
* További információ a [térbeli információk indexeléséről Azure Cosmos db](sql-query-geospatial-index.md)
