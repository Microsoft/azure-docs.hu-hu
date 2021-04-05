---
title: Buborék réteg hozzáadása térképhez | Microsoft Azure térképek
description: Megtudhatja, hogyan jelenítheti meg a pontokat a térképeken rögzített méretű körökkel. Tekintse meg, hogyan adhat hozzá és szabhat testre buborék-réteget erre a célra a Azure Maps web SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895328"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Buborék réteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan jelenítheti meg a pontok adatait egy adatforrásból egy Térkép buborék rétegében. A buborék rétegek egy rögzített képpont sugarú körként jelenítik meg a térképen lévő köröket. 

> [!TIP]
> A buborék rétegek alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha korlátozni szeretné a réteget úgy, hogy az csak a pont geometriai funkcióit jelenítse meg, állítsa a `filter` réteg tulajdonságát a (vagy) értékre, `['==', ['geometry-type'], 'Point']` Ha a `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` multipoint-szolgáltatásokat is szeretné használni.

## <a name="add-a-bubble-layer"></a>Buborékréteg hozzáadása

A következő kód egy tömböt tölt be egy adatforrásba. Ezután összekapcsolja az adatpontokat egy [buborék rétegben](/javascript/api/azure-maps-control/atlas.layer.bubblelayer). A buborék réteg az egyes buborékok sugarait öt képponttal és fehér kitöltési színnel jeleníti meg. Valamint a kék színű körvonal és a körvonal vastagsága hat képpont. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer adatforrása' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer Adatforrását</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Címkék megjelenítése buborék réteggel

Ez a kód azt mutatja be, hogyan használható egy pont a térképen a buborék réteg használatával. És hogyan lehet egy címke megjelenítéséhez szimbólum-réteget használni. A szimbólum réteg ikonjának elrejtéséhez állítsa az `image` ikon beállításainak tulajdonságát a következőre: `'none'` .

<br/>

<iframe height='500' scrolling='no' title='Többrétegű adatforrás' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>többrétegű Adatforrást</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Buborék-réteg testreszabása

A buborék rétegnek csak néhány stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Buborék réteg beállításai' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a tollas <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>buborék rétegének beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Kódminták](/samples/browse/?products=azure-maps)