---
title: Térinformatikai és GeoJSON helye Azure Cosmos DB
description: Megtudhatja, hogyan hozhat létre térbeli objektumokat Azure Cosmos DB és az SQL API-val.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: b20c72ae3ed8a8fffa02fc3a2c86f9f73ba2663b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692130"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Térinformatikai és GeoJSON helye Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk a Azure Cosmos DB térinformatikai funkciójának bevezetését ismerteti. A térinformatikai indexelés dokumentációjának elolvasása után a következő kérdésekre kaphat választ:

* Hogyan a térbeli adatainak tárolása Azure Cosmos DB-ben?
* Hogyan lehet lekérdezni a térbeli adatAzure Cosmos DB az SQL és a LINQ szolgáltatásban?
* Hogyan engedélyezheti vagy letilthatja a térbeli indexelést a Azure Cosmos DBban?

## <a name="spatial-data-use-cases"></a>Térbeli adatfelhasználási esetek

A térinformatikai adatai gyakran tartalmaznak közelségi lekérdezéseket, például "a jelenlegi tartózkodási helyük közelében található összes kávézó megkeresése". Gyakori használati esetek:

* Térinformatikai elemzések, meghatározott marketing-kezdeményezések.
* Hely-alapú személyre szabás, több iparág, mint például a kiskereskedelmi és az egészségügyi szolgáltatások.
* Logisztikai fejlesztés, átviteli optimalizálás.
* Kockázatelemzés, különösen a biztosítási és pénzügyi vállalatok számára.
* A riasztások és értesítések esetén a helyzet ismerete.

## <a name="introduction-to-spatial-data"></a>A térbeli adatainak bemutatása

A térbeli adatmennyiségek a térben lévő objektumok helyzetét és alakját ismertetik. A legtöbb alkalmazásban ezek a föld és a térinformatikai adat objektumainak felelnek meg. A térbeli adat használható egy személy, egy hely vagy egy város határának, illetve egy-tó helyének a jelölésére.

A Azure Cosmos DB SQL API két térbeli adattípust támogat: a **geometria** adattípust és a **Földrajz** adattípust.

- A **geometria** típusa egy euklideszi (Flat) koordináta-rendszerbe tartozó adatoknak felel meg.
- A **földrajzi** típus a kör alakú koordináta-rendszerbeli adathalmazokat jelöli.

## <a name="supported-data-types"></a>Támogatott adattípusok

Azure Cosmos DB támogatja a [GeoJSON-specifikáció](https://tools.ietf.org/html/rfc7946)alapján megjelenített térinformatikai pont adatok indexelését és lekérdezését. A GeoJSON-adatstruktúrák mindig érvényes JSON-objektumok, így a Azure Cosmos DB a speciális eszközök vagy könyvtárak nélkül tárolhatók és lehívhatók.

Azure Cosmos DB a következő térbeli adattípusokat támogatja:

- Pont
- LineString
- Sokszög
- MultiPolygon

### <a name="points"></a>Pont

Egy **pont** egyetlen helyet jelöl a térben. A térinformatikai adatokat tartalmazó pont a pontos helyet jelöli, amely egy élelmiszerbolt, egy kioszk, egy autó vagy egy város utcanév-címe lehet.  A pontok a GeoJSON (és Azure Cosmos DB) jelennek meg a koordináta-pár vagy a hosszúság és a földrajzi szélesség használatával.

Íme egy példa JSON egy ponthoz:

**Azure Cosmos DBi pontok**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

A térbeli adattípusokat beágyazhatja egy Azure Cosmos DB-dokumentumba, ahogy az ebben a példában látható, hogy a felhasználói profil tartalmazza a helyadatok:

**Azure Cosmos DBban tárolt hellyel rendelkező profil használata**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Geometriai koordináta-rendszer pontjai

A **geometria** adattípus esetében a GeoJSON-specifikáció meghatározza a vízszintes tengelyt és a második függőleges tengelyt.

### <a name="points-in-a-geography-coordinate-system"></a>Földrajzi koordináták rendszerbeli pontok

A **földrajzi** adattípus esetében a GeoJSON-specifikáció meghatározza az első és a szélességi fok értéket. A többi leképezési alkalmazáshoz hasonlóan a hosszúsági és a szélességi fok is szögben jelenik meg. A hosszúsági értékeket a Prime Meridian-ből mérjük, és 180 fok és 180,0 fok között kell mérni, a szélességi értékeket pedig az-90,0 fok és a 90,0 fok közé kell mérni.

Azure Cosmos DB értelmezi a WGS-84 hivatkozási rendszeren ábrázolt koordinátákat. A koordináta-hivatkozási rendszerek részletes ismertetését alább találja.

### <a name="linestrings"></a>Linestring

A **linestring** két vagy több pontból álló sorozatot jelöl, és az azokat összekapcsolási szegmenseket. A térinformatikai Linestring általában az autópályák és a folyók ábrázolására használják.

**Linestring a GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Sokszögek

A **sokszög** a csatlakoztatott pontok határa, amelyek egy lezárt LineString alkotnak. A sokszögeket általában olyan természetes formák ábrázolására használják, mint a tavak vagy a politikai illetékességek, például a városok és az Államok. Íme egy példa egy sokszögre a Azure Cosmos DBban:

**Sokszögek a GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 32, -5 ],
        [ 32, -4.7 ],
        [ 31.8, -4.7 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> A GeoJSON-specifikáció megköveteli, hogy érvényes sokszögek esetén a megadott koordináta-pároknak meg kell egyezniük az elsővel, hogy egy bezárt alakzatot hozzon létre.
>
> A sokszögben lévő pontokat a megfelelő sorrendben kell megadni. A megegyező sorrendben megadott sokszög a régió inverzét jelöli.
>
>

### <a name="multipolygons"></a>Multipolygon

A **Többsokszögű** tömb nulla vagy több sokszögből áll. A **Többsokszögű** oldalak nem lehetnek átfedésben a lapokkal, vagy nem lehetnek közös területek. Egy vagy több ponton is megjelenhetnek.

**Több sokszög a GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Koordináta-rendszerek

Mivel a föld alakja szabálytalan, a földrajzi térinformatikai adathalmazok koordinátái számos koordináta-hivatkozási rendszerben (CRS) jelennek meg, amelyek mindegyike saját hivatkozási kerettel és mértékegységgel rendelkezik. Például a "Britain országos hálózata" egy hivatkozási rendszer, amely az Egyesült Királyság számára pontos, de nem azon kívül.

A jelenleg használatban lévő legnépszerűbb CRS a  [WGS-84](https://earth-info.nga.mil/GandG/update/index.php)globális geodéziai rendszer. A GPS-eszközök és számos leképezési szolgáltatás, például a Google Maps és a Bing Maps API-k a WGS-84-et használják. Azure Cosmos DB támogatja a földrajzi térinformatikai adatai indexelését és lekérdezését kizárólag a WGS-84 CRS használatával.

## <a name="creating-documents-with-spatial-data"></a>Dokumentumok létrehozása térbeli adattal
Ha GeoJSON-értékeket tartalmazó dokumentumokat hoz létre, azok automatikusan egy térbeli indextel lesznek indexelve a tároló indexelési házirendjének megfelelően. Ha egy Azure Cosmos DB SDK-val dolgozik olyan dinamikusan gépelt nyelven, mint a Python vagy a Node.js, akkor érvényes GeoJSON kell létrehoznia.

**Térinformatikai adattal rendelkező dokumentum létrehozása Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Ha az SQL API-kkal dolgozik, a `Point` `LineString` névtéren belül a,, `Polygon` és `MultiPolygon` osztályok használatával `Microsoft.Azure.Cosmos.Spatial` ágyazhatja be a hely adatait az alkalmazásobjektumok között. Ezek az osztályok segítenek leegyszerűsíteni a térbeli adatainak szerializálását és deszerializálását a GeoJSON.

**Dokumentum létrehozása térinformatikai adattal a .NET-ben**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Ha nem rendelkezik a szélességi és a hosszúsági adatokkal, de a fizikai címeket vagy a hely nevét, például a várost vagy az országot/régiót, megkeresheti a tényleges koordinátákat egy olyan helymeghatározáshoz-szolgáltatás használatával, mint a Bing Maps REST Services. További információ a Bing Maps helymeghatározáshoz [itt](/bingmaps/rest-services/).

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezdheti meg a térinformatikai támogatás használatát Azure Cosmos DBban, a következő lehetőségekkel:

* További információ a [Azure Cosmos db lekérdezésről](sql-query-getting-started.md)
* További információ a [térbeli adatainak a Azure Cosmos db való lekérdezéséről](sql-query-geospatial-query.md)
* További információ a [térbeli információk indexeléséről Azure Cosmos db](sql-query-geospatial-index.md)