---
title: HTML-jelölő hozzáadása a térképhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá HTML-jelölőket a térképekhez. Tekintse meg, hogyan szabhatja testre a jelölőket a Azure Maps web SDK használatával, és hogyan adhat hozzá előugró ablakokat és egérmutatókat a jelölőhöz.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 1c4367e2a649f4e239e2dab374afc4fb867e517b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891193"
---
# <a name="add-html-markers-to-the-map"></a>HTML-jelölők hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy egyéni HTML-fájlt, például egy képfájlt a térképhez HTML-jelölőként.

> [!NOTE]
> A HTML-jelölők nem kapcsolódnak az adatforrásokhoz. Ehelyett az elhelyezési adatok közvetlenül a jelölőhöz kerülnek, a jelölő pedig a Maps `markers` tulajdonsághoz kerül, amely egy [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager).

> [!IMPORTANT]
> A Azure Maps webes vezérlő legtöbb rétegével ellentétben, amely WebGL használ a rendereléshez, a HTML-jelölők hagyományos DOM-elemeket használnak a rendereléshez. Így az oldalhoz hozzáadott HTML-jelölők, annál több DOM-elem van. Néhány száz HTML-jelölő hozzáadását követően a teljesítmény csökkenhet. Nagyobb adatkészletek esetében érdemes lehet az adatait csoportosítani vagy szimbólumot vagy buborék réteget használni.

## <a name="add-an-html-marker"></a>HTML-jelölő hozzáadása

A [HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker) osztály alapértelmezett stílusú. Testreszabhatja a jelölőt a jelölő színének és szövegének megadásával. A HTML-jelölő osztály alapértelmezett stílusa egy SVG-sablon, amely tartalmaz egy `{color}` és egy `{text}` helyőrzőt. A gyors testreszabáshoz adja meg a szín és a szöveg tulajdonságokat a HTML-jelölő beállításai között. 

A következő kód egy HTML-jelölőt hoz létre, és a Color tulajdonságot "DodgerBlue" értékre állítja, a Text tulajdonság pedig "10". Az előugró ablak a jelölőhöz van csatolva, és az `click` esemény a felugró ablak láthatóságának váltására szolgál.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő hozzáadása térképhez' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat egy <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>HTML-jelölő hozzáadásával</a> Azure Maps () használatával a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>SVG-sablon alapján létrehozott HTML-jelölő létrehozása

A `htmlContent` HTML-jelölő alapértelmezett értéke egy SVG-sablon, amelyben a mappák `{color}` és `{text}` a benne található. Létrehozhat egyéni SVG-karakterláncokat, és felveheti ugyanezeket a helyőrzőket az SVG-be, így a `color` jelölő és a `text` beállítások is módosíthatják ezeket a helyőrzőket az SVG-ben.

<br/>

<iframe height='500' scrolling='no' title='HTML-jelölő egyéni SVG-sablonnal' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML-jelölőjét egyéni SVG-sablonnal</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK számos SVG-képsablont biztosít, amelyek HTML-jelölők használatával használhatók. További információ: a [képsablonokkal](how-to-use-image-templates-web-sdk.md) kapcsolatos dokumentum használata.

## <a name="add-a-css-styled-html-marker"></a>CSS stílusú HTML-jelölő hozzáadása

A HTML-jelölők egyik előnye, hogy a CSS használatával számos nagyszerű testreszabási lehetőség érhető el. Ebben a példában a HtmlMarker tartalma HTML és CSS, amely egy animált PIN-kódot hoz létre, amely a helyére és a impulzusokra esik.

<br/>

<iframe height='500' scrolling='no' title='HTML-adatforrás' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML-Adatforrást</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Mozgatható HTML-jelölők

Ez a minta bemutatja, hogyan lehet egy HTML-jelölőt áthúzni. A HTML-jelölők támogatása `drag` , `dragstart` és `dragend` események.

<br/>

<iframe height='500' scrolling='no' title='Mozgatható HTML-jelölő' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollra <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>HÚZHATÓ HTML-jelölőt</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Egér-események hozzáadása HTML-jelölőhöz

Ezek a minták azt mutatják be, hogyan adhat hozzá egeret, és áthúzhatja az eseményeket egy HTML-jelölőbe.

<br/>

<iframe height='500' scrolling='no' title='Egér-események hozzáadása HTML-jelölőhöz' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat az <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>egérmutató hozzáadásával a HTML-jelölőket</a> a következő helyen: Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager)

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Rendszerképsablonok használata](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](./map-add-bubble-layer.md)