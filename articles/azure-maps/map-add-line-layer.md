---
title: Vonal rétegének hozzáadása térképhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá vonalakat a térképekhez. Tekintse meg a Azure Maps web SDK-t használó példákat, amelyekkel vonalakat adhat hozzá a térképekhez, és testreszabhatja a vonalakat szimbólumokkal és színátmenetekkel.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: fedf1bc37661100404106859d3aef52b3eddf06d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048137"
---
# <a name="add-a-line-layer-to-the-map"></a>Vonal rétegének hozzáadása a térképhez

A vonal réteggel `LineString` `MultiLineString` görbékként és útvonalakként jeleníthető meg a térképen. A sorok és a szolgáltatások körvonalainak megjelenítéséhez egy sor réteg is használható `Polygon` `MultiPolygon` . Az adatforrások egy vonal réteghez csatlakoznak, így biztosítva a rendereléshez szükséges adatforrásokat. 

> [!TIP]
> A sorok alapértelmezés szerint a sokszögek koordinátáit, valamint egy adatforrás sorait jelenítik meg. Ha korlátozni szeretné a réteget úgy, hogy az csak az LineString funkciókat jelenítse meg, állítsa be a `filter` réteg tulajdonságát a (vagy) értékre, `['==', ['geometry-type'], 'LineString']` `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` Ha MultiLineString funkciókat is szeretne használni.

A következő kód bemutatja, hogyan lehet sort létrehozni. Adja hozzá a sort egy adatforráshoz, majd jelenítse meg egy vonal réteggel a [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) osztály használatával.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Vonal hozzáadása térképhez' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on lévő Azure Maps () <a href='https://codepen.io/azuremaps/pen/qomaKv/'>, és adjon hozzá egy sort</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

A [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) stílusú és [adatvezérelt stílusú kifejezéseket](data-driven-style-expressions-web-sdk.md)használó vonalak lehetnek.

## <a name="add-symbols-along-a-line"></a>Szimbólumok felvétele a vonal mentén

Ez a minta bemutatja, hogyan adhat hozzá nyíl ikonokat a Térkép egyik sorához. Ha szimbólum réteget használ, állítsa a "elhelyezés" lehetőséget a "line" értékre. Ez a beállítás megjeleníti a jeleket a vonal mentén, és elforgatja az ikonokat (0 fok = jobb).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nyíl megjelenítése a vonal mentén" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/drBJwX/'>mutató nyilat a vonal mentén</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos testreszabható képsablont biztosít, amelyeket a szimbólum rétegben használhat. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Körvonal átmenetének hozzáadása sorba

Egyetlen ecsetvonás színét is alkalmazhatja egy sorba. A színátmenetes vonalakat is kitöltheti úgy, hogy az egyik vonalszakaszból a következő sorra váltson átmenetet. A vonalak közötti átmenetek például az idő és a távolság, illetve a csatlakoztatott objektumok különböző hőmérsékletei közötti változások ábrázolására használhatók. Ahhoz, hogy ezt a funkciót egy sorba lehessen alkalmazni, az adatforráshoz `lineMetrics` be kell állítani a beállítást `true` , majd egy színátmenetes kifejezést is át lehet adni a `strokeColor` sor kapcsolójának. A stroke Gradient kifejezésnek hivatkoznia kell arra az `['line-progress']` adatkifejezésre, amely a számított vonal metrikáit a kifejezésre teszi elérhetővé.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ecsetvonás-átmenetes vonal" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
A CodePen-on Azure Maps () használatával megtekintheti a tollvonási <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>átmenetet</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="customize-a-line-layer"></a>Vonal rétegének testreszabása

A vonal rétegének számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Vonal rétegének beállításai' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Azure Maps () <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>parancssori rétegbeli beállításait</a> a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)