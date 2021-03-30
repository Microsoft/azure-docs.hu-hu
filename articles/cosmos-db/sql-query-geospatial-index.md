---
title: Térinformatikai adatindexek indexelése Azure Cosmos DB
description: Térbeli adatainak indexelése Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336214"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Térinformatikai adatindexek indexelése Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB adatbázis-motorját úgy terveztük, hogy valóban séma-agnosztikus legyen, és első osztályú támogatást nyújtson a JSON-hoz. A Azure Cosmos DB írásra optimalizált adatbázismotor natív módon értelmezi a GeoJSON standardban ábrázolt térbeli adatokra vonatkozó információkat.

Dióhéjban a geometria a geodéziai Koordinátákból egy 2D-síkra van kialakítva, majd fokozatosan, egy **quadtree** használatával osztva a cellákba. Ezek a cellák az 1D-re vannak leképezve a cella helye alapján a Hilbert belül, amely megőrzi a pontok helyi **kitöltését**. Emellett, ha a helyadatok indexelve lettek, egy **mozaikként** ismert folyamaton halad át, azaz az adott helyet keresztező összes cella azonosítható és kulcsként tárolódik a Azure Cosmos db indexben. A lekérdezés időpontjában az argumentumok, például a pontok és a sokszögek a megfelelő cella-azonosító tartományok kinyerésére szolgálnak, majd az adatok az indexből való lekéréséhez használatosak.

Ha olyan indexelési házirendet ad meg, amely tartalmazza a `/*` (minden elérési út) térbeli indexét, akkor a tárolóban található összes adathalmaz a hatékony térbeli lekérdezésekhez van indexelve.

> [!NOTE]
> Azure Cosmos DB támogatja a pontok, Linestring, sokszögek és többsokszögek indexelését. Ha indexeli az egyik ilyen típusú típust, a rendszer automatikusan indexeli az összes többi típust. Más szóval, ha a sokszögeket indexeli, akkor a pontok, a Linestring és a többsokszögek is indexelve lesznek. Az új térbeli típus indexelése nem befolyásolja az RU-díj vagy az index méretét, kivéve, ha érvényes GeoJSON-adattal rendelkezik.

## <a name="modifying-geospatial-configuration"></a>Térinformatikai konfiguráció módosítása

A tárolóban a **térinformatikai konfiguráció** meghatározza, hogy a térbeli adatai hogyan lesznek indexelve. Válasszon egy **térinformatikai konfigurációt** egy tárolóban: földrajz vagy geometria.

Válthat a **földrajzi** és a **geometriai** térbeli típus között a Azure Portal. Fontos, hogy a geometriai térbeli típusra váltás előtt hozzon létre egy [érvényes térbeli geometria-indexelési házirendet egy határoló mezővel](#geometry-data-indexing-examples) .

A következő témakörben állíthatja be a **térinformatikai konfigurációt** a Azure Portal **adatkezelő** belül:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Térinformatikai konfiguráció beállítása":::

A (z) a `geospatialConfig` .net SDK-ban is módosítható a **térinformatikai konfiguráció** beállításához:

Ha nincs megadva, a `geospatialConfig` alapértelmezés szerint a földrajzi adattípust fogja megadni. Ha módosítja a `geospatialConfig` -t, a tárolóban lévő összes meglévő térinformatikai elem újraindexelve lesz.

Íme egy példa a térinformatikai adattípus módosítására `geometry` a tulajdonság beállításával `geospatialConfig` és a **boundingBox** hozzáadásával:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Földrajzi adatindexelési példák

A következő JSON-kódrészlet egy indexelési házirendet mutat be, amelyen engedélyezve van a **földrajzi** adattípusú térbeli indexelés. A földrajzi adattípussal rendelkező térbeli értékek esetében érvényes, és indexeli az GeoJSON pontot, a sokszöget, a többsokszögű vagy a LineString dokumentumokat a térbeli lekérdezésekhez. Ha az indexelési házirendet a Azure Portal használatával módosítja, megadhatja a következő JSON-t az indexelési házirendhez, hogy engedélyezze a térbeli indexelést a tárolón:

**Tároló-indexelési házirend JSON földrajzi térbeli indexeléssel**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Ha a hely GeoJSON értéke helytelen formátumú vagy érvénytelen, akkor nem lesz indexelve a térbeli lekérdezésekhez. A hely értékeit ST_ISVALID és ST_ISVALIDDETAILED használatával ellenőrizheti.

Az [indexelési házirendet](how-to-manage-indexing-policy.md) az Azure CLI, a PowerShell vagy bármely SDK használatával is módosíthatja.

## <a name="geometry-data-indexing-examples"></a>Geometriai adatok indexelésére vonatkozó példák

Ha a **geometria** adattípusa hasonló a földrajzi adattípushoz, a megfelelő elérési utakat és típusokat kell megadnia az indexhez. Emellett az indexelési házirendben is meg kell adnia az indexet, `boundingBox` hogy jelezze az adott elérési útra vonatkozó kívánt területeket. Minden földrajzi útvonalhoz saját szükséges `boundingBox` .

A határoló mező a következő tulajdonságokat tartalmazza:

- **xmin**: a minimálisan indexelt x koordináta
- **ymin**: a minimális indexelt y koordináta
- **Xmax**: a maximális indexelt x koordináta
- **Ymax**: a maximális indexelt y koordináta

A határolókeret megadása kötelező, mert a geometriai adat egy olyan gépet foglal, amely végtelen lehet. A térbeli indexek esetében azonban véges terület szükséges. A **földrajzi** adattípushoz a föld a határ, és nem kell megadnia egy határoló mezőt.

Hozzon létre egy határoló mezőt, amely az összes (vagy a legtöbb) adatát tartalmazza. A térbeli indexet csak a teljes mértékben a határolókeret belsejében lévő objektumokra kiszámított műveletek lesznek képesek használni. Ha a határolókeret mérete nagyobb, mint amennyi szükséges, negatív hatással lesz a lekérdezés teljesítményére.

Az alábbi példa olyan indexelési házirendet mutat be, amely a **geometriai** adatoknak a **geospatialConfig** beállítását a következőre állítja `geometry` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

A fenti indexelési házirend **boundingBox** (-10, 10) tartalmaz x koordinátákat és (-20, 20) az y koordinátákhoz. A fenti indexelési szabályzattal rendelkező tároló az összes olyan pontot, sokszöget, többsokszögű és Linestring indexeli, amely teljes egészében ebben a régióban található.

> [!NOTE]
> Ha egy adattípusú tárolóhoz próbál felvenni egy **boundingBox** tartalmazó indexelési házirendet `geography` , a művelet sikertelen lesz. A BoundingBox hozzáadása előtt módosítania kell a tároló **geospatialConfig** `geometry` .  A tároló térinformatikai adattípusának kiválasztását megelőzően vagy azt követően módosíthatja az indexelési szabályzat hátralévő részét (például az elérési utakat és típusokat).

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezdheti meg a térinformatikai támogatás használatát Azure Cosmos DBban, a következő lehetőségekkel:

* További információ a [Azure Cosmos db lekérdezésről](sql-query-getting-started.md)
* További információ a [térbeli adatainak a Azure Cosmos db való lekérdezéséről](sql-query-geospatial-query.md)
* További információ a [térinformatikai és a GeoJSON-hely adatainak Azure Cosmos db](sql-query-geospatial-intro.md)
