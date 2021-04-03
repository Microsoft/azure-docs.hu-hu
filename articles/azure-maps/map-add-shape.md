---
title: Sokszög réteg hozzáadása térképhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá sokszögeket vagy köröket a térképekhez. Tekintse meg, hogyan szabhatja testre a geometriai alakzatokat a Azure Maps web SDK használatával, és hogy azok könnyen frissíthetők és karbantarthatók legyenek.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890997"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Sokszög réteg hozzáadása a térképhez

Ez a cikk bemutatja, hogyan jelenítheti meg a `Polygon` Térkép területeit és `MultiPolygon` funkcióit a térképen a sokszög réteg használatával. A Azure Maps web SDK támogatja a kör alakú geometriák létrehozását is a [kiterjesztett GeoJSON sémában](extend-geojson.md#circle)definiált módon. Ezeket a köröket a rendszer sokszögekre alakítja át a térképen való megjelenítéskor. Az Atlas használatával egyszerűen frissíthető az összes szolgáltatás geometriája [. Alakzat](/javascript/api/azure-maps-control/atlas.shape) osztálya

## <a name="use-a-polygon-layer"></a>Sokszög réteg használata 

Ha egy sokszög réteg egy adatforráshoz van csatlakoztatva, és a térképen töltődik be, az a terület és a funkciók között jelenik meg `Polygon` `MultiPolygon` . Sokszög létrehozásához vegye fel azt egy adatforrásba, majd a [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) osztály használatával jelenítse meg a sokszög réteget.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Alább látható a fenti kód teljes és működő mintája.

<br/>

<iframe height='500' scrolling='no' title='Sokszög hozzáadása térképhez ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>sokszög hozzáadása </a> Azure Maps () használatával a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Sokszög és vonal rétegek együttes használata

A sokszögek körvonalának megjelenítéséhez egy vonal réteget kell használni. A következő mintakód az előző példához hasonló sokszöget jelenít meg, de most egy sor réteget is felvesz. Ez a vonal az adatforráshoz csatlakozó második réteg.  

<br/>

<iframe height='500' scrolling='no' title='Sokszög és vonal réteg a sokszög hozzáadásához' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az CodePen-on Azure Maps () használatával <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>vegye fel a sokszöget a toll és a vonal rétegben</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Sokszög kitöltése mintázattal

A sokszög színekkel való kitöltése mellett a sokszög kitöltéséhez használhat képmintázatot is. Helyezzen be egy képmintázatot a Maps-rendszerkép sprite-erőforrásaiba, majd hivatkozzon erre a képre a `fillPattern` sokszög rétegének tulajdonságával.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltési mintája" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>sokszögének kitöltésére szolgáló mintát</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket kitöltési mintázatként használhat. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

## <a name="customize-a-polygon-layer"></a>Sokszög-réteg testreszabása

A sokszög rétegnek csak néhány stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg Azure Maps () által <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> tollat a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Azure Maps a GeoJSON séma egy kiterjesztett verzióját használja, amely a körök definícióját tartalmazza, ahogy az [itt](extend-geojson.md#circle)látható. Egy kör a térképen egy funkció létrehozásával jelenik meg `Point` . Ez egy olyan `Point` tulajdonsággal rendelkezik, amely egy értékkel rendelkezik, és egy olyan `subType` tulajdonságot tartalmaz, `"Circle"` `radius` amely egy szám, amely a sugarat méterben jelöli. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

A Azure Maps web SDK ezeket a `Point` funkciókat `Polygon` szolgáltatásokra konvertálja. Ezután ezek a funkciók a térképen a sokszög és a vonal rétegek használatával jelennek meg, ahogy az alábbi mintakód is mutatja.

<br/>

<iframe height='500' scrolling='no' title='Kör hozzáadása térképhez' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on lévő Azure Maps () <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>, és adjon hozzá egy kört egy térképhez</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Geometria egyszerű frissítése

Egy `Shape` osztály egy [geometriát](/javascript/api/azure-maps-control/atlas.data.geometry) vagy [szolgáltatást](/javascript/api/azure-maps-control/atlas.data.feature) csomagol, és megkönnyíti ezeknek a funkcióknak a frissítését és karbantartását. Egy alakzat változójának létrehozásához adjon át egy geometriát vagy egy tulajdonságot az alakzat konstruktorának.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Az alábbi mintakód bemutatja, hogyan lehet kör alakú GeoJSON objektumot becsomagolni egy Shape osztállyal. Az alakzatban lévő RADIUS-változások értékeként a kör automatikusan a térképen jelenik meg.

<br/>

<iframe height='500' scrolling='no' title='Alakzat tulajdonságainak frissítése' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>frissítési alakzatának tulajdonságait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Sokszög](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

További források:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON specifikációs bővítmény](extend-geojson.md#circle)