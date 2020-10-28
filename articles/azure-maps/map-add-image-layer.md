---
title: Képréteg hozzáadása térképhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá képeket a térképekhez. Tekintse meg, hogyan szabhatja testre a képrétegeket és az átfedésben lévő rendszerképeket a Azure Maps web SDK használatával a koordináták rögzített csoportjain.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 43de832095e2c2dd674a156da914ed26f1e472d0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892944"
---
# <a name="add-an-image-layer-to-a-map"></a>Képréteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan fedi le a képeket egy rögzített koordináta-készletbe. Íme néhány példa a térképeken áthelyezhető különböző képtípusokra:

* A herékből rögzített képek
* Alaprajzok építése
* Korábbi vagy egyéb speciális térképi képek
* A feladatok webhelyeinek tervrajzai
* Időjárási radar-rendszerképek

> [!TIP]
> Az [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) egyszerű módot biztosít a képek térképre való átfedésére. Vegye figyelembe, hogy a böngészők nehezen tudnak betölteni egy nagyméretű képet. Ebben az esetben érdemes a képet csempére feltörni, és [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)betölteni őket a térképbe.

A rendszerkép réteg a következő képformátumokat támogatja:

- JPEG
- PNG
- BMP
- GIF (nincs animáció)

## <a name="add-an-image-layer"></a>Rendszerképréteg hozzáadása

A következő kód egy, a térképen található [Newark, New Jersey, 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) -as Térkép képét fedi le. Egy [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) úgy jön létre, hogy átadja egy rendszerkép URL-címét, és a négy sarkának koordinátáit adja meg `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` .

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Itt látható az előző kód teljes futtatási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű képréteg' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/eQodRo/'>egyszerű Képrétegét</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML-fájl importálása a felszín alatti átfedésként

Ez a minta azt mutatja be, hogyan adhat hozzá a KML-terepi átfedési adatokat képrétegként a térképen. A KML-borítók Észak-, Dél-, Kelet-és Nyugat-koordinátákat biztosítanak, és az óramutató járásával megegyező irányú rotációt tartalmaznak. A képréteg azonban a rendszerkép minden sarkánál koordinátákat vár. Ebben a példában a KML-terület a Chartres-székesegyházhoz van kiszervezve, és a [wikimediaből](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)származik.

A kód a `getCoordinatesFromEdges` [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) osztály statikus függvényét használja. Kiszámítja a rendszerkép négy sarkát a KML terület Észak-, déli, keleti, nyugati és rotációs információi alapján.

<br/>

<iframe height='500' scrolling='no' title='KML-terep átfedése képrétegként' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az CodePen-on Azure Maps () <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>képrétegként</a> tekintse meg a KML-terepen látható tollat <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Képréteg testreszabása

A képréteg számos stílust tartalmaz. Az alábbi eszköz segítségével kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Képréteg beállításai' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Képrétegének beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](./map-add-tile-layer.md)