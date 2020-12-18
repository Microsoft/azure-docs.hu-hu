---
title: Sokszög réteg hozzáadása Android-térképekhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá sokszögeket vagy köröket a térképekhez. Tekintse meg, hogyan szabhatja testre a geometriai alakzatokat a Azure Maps Android SDK használatával, és hogy azok könnyen frissíthetők és karbantarthatóak legyenek.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679469"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Sokszög réteg hozzáadása a térképhez (Android SDK)

Ez a cikk bemutatja, hogyan jelenítheti meg a `Polygon` Térkép területeit és `MultiPolygon` funkcióit a térképen a sokszög réteg használatával.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania. A cikkben szereplő kódrészletek behelyezhetők a Maps `onReady` Event handlerbe.

## <a name="use-a-polygon-layer"></a>Sokszög réteg használata

Ha egy sokszög réteg egy adatforráshoz van csatlakoztatva, és a térképen töltődik be, az a terület és a funkciók között jelenik meg `Polygon` `MultiPolygon` . Sokszög létrehozásához vegye fel azt egy adatforrásba, majd a osztály használatával jelenítse meg azt egy sokszög réteggel `PolygonLayer` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

Az alábbi képernyőfelvételen a sokszög területét ábrázoló fenti kód látható a sokszög réteg használatával.

![Sokszög a kitöltési résszel](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Sokszög és vonal rétegek együttes használata

A sokszögek körvonalának megjelenítéséhez egy vonal réteget kell használni. A következő mintakód az előző példához hasonló sokszöget jelenít meg, de most egy sor réteget is felvesz. Ez a vonal az adatforráshoz csatlakozó második réteg.  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

Az alábbi képernyőképen a fenti kód jelenik meg, amely egy sokszöget jelenít meg a körvonalan egy sor réteg használatával.

![Sokszög a kitöltési résszel és a körvonalas megjelenítéssel](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Ha egy sokszöget egy vonal réteggel rajzol, ügyeljen arra, hogy a sokszögekben lévő összes gyűrűt lezárja úgy, hogy minden egyes tömb ugyanazzal a kezdési és végponti ponttal legyen. Ha ez nem történik meg, akkor előfordulhat, hogy a réteg nem csatlakozik a sokszög utolsó pontjához az első ponthoz.

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)
