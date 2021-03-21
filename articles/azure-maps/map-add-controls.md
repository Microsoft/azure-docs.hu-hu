---
title: Vezérlőelemek hozzáadása térképhez | Microsoft Azure térképek
description: Nagyítás-vezérlés, pitch Control, a Control és a Style Picker hozzáadása Microsoft Azure Maps térképekhez.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: bbcfe4935843a00bc5274273e79f4ffc72dc25ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051055"
---
# <a name="add-controls-to-a-map"></a>Vezérlőelemek hozzáadása térképhez

Ez a cikk bemutatja, hogyan adhat hozzá vezérlőket térképekhez. Azt is megtudhatja, hogyan hozhat létre térképet az összes vezérlővel és a [Style Picker](./choose-map-style.md).

## <a name="add-zoom-control"></a>Nagyítási vezérlő hozzáadása

A nagyítás-vezérlés gombokkal bővítheti a térképet és a nagyítást. A következő mintakód létrehozza a [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) osztály egy példányát, és hozzáadja a Térkép jobb alsó sarkához.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Nagyítási vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Azure Maps () <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>nagyítási vezérlőt</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> a <a href='https://codepen.io'>CodePen</a>-on való hozzáadását ismertető tollat.
</iframe>

## <a name="add-pitch-control"></a>Pitch vezérlőelem hozzáadása

A Pitch vezérlő gombok hozzáadásával megdöntheti, hogy a szurok a horizonthoz viszonyítva legyen-e leképezve. A következő mintakód a [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) osztály egy példányát hozza létre. Hozzáadja a PitchControl a Térkép jobb felső sarkához.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Pitch vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>a tollat a</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>-on Azure Maps () használatával.
</iframe>

## <a name="add-compass-control"></a>Iránytű vezérlőelem hozzáadása

Az iránytű vezérlőelem egy gombot helyez el a Térkép elforgatásához. A következő mintakód létrehozza a [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) osztály egy példányát, és hozzáadja a térkép bal alsó sarkához.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.CompassControl(), {
    position: 'bottom-left'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Forgatás vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on Azure Maps () <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>elforgatási vezérlő hozzáadásával</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="a-map-with-all-controls"></a>Térkép az összes vezérlővel

Több vezérlő helyezhető egy tömbbe, és a térképhez mind egyszerre, mind a Térkép ugyanazon területén helyezhető el, hogy egyszerűbbé váljon a fejlesztés. A következő lépés a szabványos navigációs vezérlőket hozzáadja a térképhez ezzel a módszerrel.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

A következő mintakód hozzáadja a nagyítás, az iránytű, a szurok és a stílus választó vezérlőket a Térkép jobb felső sarkához. Figyelje meg, hogy a rendszer hogyan automatikusan verembe. A parancsfájlban lévő vezérlőelem-objektumok sorrendje azt diktálja, hogy milyen sorrendben jelenjenek meg a térképen. Ha módosítani szeretné a vezérlőelemek sorrendjét a térképen, módosíthatja a sorrendjét a tömbben.

<br/>

<iframe height='500' scrolling='no' title='Térkép az összes vezérlővel' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a térképes tollat az <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>összes vezérlővel</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A Style Picker vezérlőt a [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) osztály határozza meg. A Style Picker vezérlő használatával kapcsolatos további információkért lásd: [Térkép stílusának kiválasztása](choose-map-style.md).

## <a name="customize-controls"></a>Vezérlők testreszabása

Itt található egy eszköz, amellyel kipróbálhatja a vezérlők testreszabásának különböző lehetőségeit.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Navigációs vezérlőelem beállításai" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a CodePen-on Azure Maps () által <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>felügyelt tollas Navigálás beállításait</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Ha testreszabott navigációs vezérlőket szeretne létrehozni, hozzon létre egy osztályt, amely kiterjeszti az `atlas.Control` osztályból, vagy hozzon létre egy HTML-elemet, és helyezze a Térkép div fölé. Ez a felhasználói felületi vezérlő hívja `setCamera` meg a Maps függvényt a Térkép áthelyezéséhez. 

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Iránytű vezérlő](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

A következő cikkekben talál teljes kódot:

> [!div class="nextstepaction"]
> [Gombostű hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)