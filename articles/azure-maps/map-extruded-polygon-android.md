---
title: Sokszög-kihúzási réteg hozzáadása Android-térképhez | Microsoft Azure térképek
description: Sokszög-kihúzási réteg hozzáadása a Microsoft Azure Maps Android SDK-hoz.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054830"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>Sokszög-kihúzási réteg hozzáadása a térképhez (Android SDK)

Ebből a cikkből megtudhatja, hogyan használhatja a sokszög-kihúzási réteget, hogy a `Polygon` és a `MultiPolygon` szolgáltatáshoz tartozó geometriákat extrudált alakzatokként jelenítse

## <a name="use-a-polygon-extrusion-layer"></a>Sokszög kihúzási rétegének használata

A sokszög kihúzási rétegének összekötése egy adatforrással. Ezután betöltve a térképen. A sokszög kihúzási rétege `Polygon` kihúzott alakzatként jeleníti meg a és a funkciók területét `MultiPolygon` . A `height` `base` sokszög kihúzási rétegének és tulajdonságai határozzák meg az extrudált alakzat területének és magasságának alaptávolságát **méterben**. A következő kód bemutatja, hogyan hozható létre sokszög, hogyan adható hozzá egy adatforráshoz, és Hogyan jeleníthető meg a sokszög kihúzási rétegének osztálya alapján.

> [!Note]
> A `base` sokszög kihúzási rétegében megadott érték nem lehet kisebb, mint az `height` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

Az alábbi képernyőfelvételen látható, hogy a fenti kód egy sokszög kihúzási rétegének használatával függőlegesen tépő.

![Leképezés a sokszög használatával függőlegesen, sokszög-kihúzási réteggel](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>Adatvezérelt sokszögek hozzáadása

A choropleth-leképezések a sokszög kihúzási réteg használatával állíthatók be. A `height` `fillColor` kihúzási réteg és a tulajdonságok tulajdonságát állítsa a `Polygon` és a `MultiPolygon` szolgáltatás geometriájában található statisztikai változó mértékére. Az alábbi mintakód a Egyesült Államok extrudált choropleth-térképét jeleníti meg, a népsűrűség alapján.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

Az alábbi képernyőképen egy choropleth Térkép látható, amely színes és függőlegesen húzott sokszögként van kiterjesztve a népességi sűrűség alapján.

![Az Egyesült államokbeli Államok choropleth térképe színes és függőlegesen húzott sokszögek, a populáció sűrűsége alapján](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
