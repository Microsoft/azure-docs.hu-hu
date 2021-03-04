---
title: Sokszög-kihúzási réteg hozzáadása térképhez | Microsoft Azure térképek
description: Sokszög-kihúzási réteg hozzáadása a Microsoft Azure Maps web SDK-hoz.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 19675a92101ed1a13b07bc1a4039701cd029a020
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044085"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Sokszög-kihúzási réteg hozzáadása a térképhez

Ebből a cikkből megtudhatja, hogyan használhatja a sokszög-kihúzási réteget, hogy a `Polygon` és a `MultiPolygon` szolgáltatáshoz tartozó geometriákat extrudált alakzatokként jelenítse A Azure Maps web SDK támogatja a kör geometriáinak megjelenítését a [kiterjesztett GeoJSON sémában](extend-geojson.md#circle)meghatározottak szerint. Ezek a körök a térképen való megjelenítéskor sokszögekre alakíthatók. Az Atlas becsomagolásakor az összes funkció geometriája könnyen frissíthető [. Alakzat](/javascript/api/azure-maps-control/atlas.shape) osztálya

## <a name="use-a-polygon-extrusion-layer"></a>Sokszög kihúzási rétegének használata

A [sokszög kihúzási rétegének](/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer) összekötése egy adatforrással. Ezután betöltve a térképen. A sokszög kihúzási rétege `Polygon` kihúzott alakzatként jeleníti meg a és a funkciók területét `MultiPolygon` . A `height` `base` sokszög kihúzási rétegének és tulajdonságai határozzák meg az extrudált alakzat területének és magasságának alaptávolságát **méterben**. A következő kód bemutatja, hogyan hozható létre sokszög, hogyan adható hozzá egy adatforráshoz, és Hogyan jeleníthető meg a sokszög kihúzási rétegének osztálya alapján.

> [!Note]
> A `base` sokszög kihúzási rétegében megadott érték nem lehet kisebb, mint az `height` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudált sokszög" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>extrudált sokszögét</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.</iframe>

## <a name="add-data-driven-polygons"></a>Adatvezérelt sokszögek hozzáadása

A choropleth-leképezések a sokszög kihúzási réteg használatával állíthatók be. A `height` `fillColor` kihúzási réteg és a tulajdonságok tulajdonságát állítsa a `Polygon` és a `MultiPolygon` szolgáltatás geometriájában található statisztikai változó mértékére. Az alábbi mintakód a Egyesült Államok extrudált choropleth-térképét jeleníti meg, a népsűrűség alapján.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudált choropleth Térkép" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/eYYYNox'>extrudált choropleth térképét</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Kör hozzáadása a térképhez

Azure Maps a GeoJSON séma egy kiterjesztett verzióját használja, amely az [itt](./extend-geojson.md#circle)feljegyzett körök definícióját adja meg. Egy extrudált kör megjeleníthető a térképen úgy, hogy létrehoz egy `point` tulajdonsággal rendelkező szolgáltatást, `subType` `Circle` valamint egy számozott `Radius` tulajdonságot, amely a sugarat a **méterben** jelöli. Például:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

A Azure Maps web SDK ezeket a `Point` funkciókat `Polygon` a motorháztető alatt lévő funkciókba konvertálja. Ezek a `Point` funkciók a térképen a sokszög-kihúzási réteg használatával jeleníthetők meg, ahogy az alábbi mintakód is mutatja.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A drone-légtér sokszöge" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>a tollat</a> a Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>-on.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Sokszög kihúzási rétegének testreszabása

A sokszög kihúzási rétegének számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg Azure Maps () által <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> tollat a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Sokszög](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [sokszög kihúzási rétege](/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)

További források:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON specifikációs bővítmény](extend-geojson.md#circle)