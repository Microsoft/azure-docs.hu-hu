---
title: Rajzolási eszközök eszköztárának hozzáadása a leképezéshez | Microsoft Azure térképek
description: Rajzolási eszköztár hozzáadása térképhez Azure Maps web SDK használatával
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896395"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Rajzolási eszközök eszköztárának hozzáadása térképhez

Ez a cikk bemutatja, hogyan használhatja a rajzolási eszközök modult, és hogyan jelenítheti meg a rajzolás eszköztárat a térképen. A [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) vezérlő hozzáadja a rajzolás eszköztárat a térképen. Megtudhatja, hogyan hozhat létre térképeket csak egy és minden rajzolási eszközzel, és hogyan szabhatja testre a rajzolási alakzatok megjelenítését a rajzolási kezelőben.

## <a name="add-drawing-toolbar"></a>Rajzolási eszköztár hozzáadása

A következő kód létrehozza a rajzolási kezelő egy példányát, és megjeleníti az eszköztárat a térképen.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Alább látható a fenti funkciók teljes futtatására szolgáló kód mintája:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási eszköztár hozzáadása" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Rajzolás eszköztár hozzáadásával</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Megjelenő eszköztár beállításainak korlátozása

A következő kód létrehoz egy példányt a rajzolás-kezelőből, és megjeleníti az eszköztárat, amely csak egy sokszög alakú rajzolási eszközzel jelenik meg a térképen. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Alább látható a fenti funkciók teljes futtatására szolgáló kód mintája:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög rajzolási eszköz hozzáadása" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>sokszög rajzolási eszköz hozzáadása</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Rajzolási renderelési stílus módosítása

A rajzolt alakzatok stílusa testreszabható úgy, hogy a (z) függvény használatával beolvassa a Rajzfigyelő mögöttes rétegeit `drawingManager.getLayers()` , majd beállítja a beállításokat az egyes rétegeken. A Koordinátákban megjelenő fogópontok az alakzatok szerkesztésekor HTML-jelölők. A húzási fogópontok stílusa testreszabható a HTML-jelölő beállításainak átadásával a `dragHandleStyle` rajzobjektum és a `secondaryDragHandleStyle` beállítások között.  

A következő kód beolvassa a rajzolási rétegeket a projektmenedzsertől, és módosítja a rajzolási stílus módosítására vonatkozó beállításokat. Ebben az esetben a pontok kék jelölő ikonnal jelennek meg. A sorok piros és négy képpont szélesek lesznek. A sokszögek zöld kitöltési színnel és narancssárga körvonalsal fognak rendelkezni. Ezután megváltoztatja a húzási fogópontok stílusát négyzet alakú ikonként. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Alább látható a fenti funkciók teljes futtatására szolgáló kód mintája:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási renderelési stílus módosítása" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>módosításának rajzolási stílusát</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Formázott adatok lekérése](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Rajzolási eseményekre való reakció](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Az interakciók típusai és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)