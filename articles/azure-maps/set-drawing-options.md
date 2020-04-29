---
title: Rajzolási eszközök modulja | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a rajzolási beállításokat a Microsoft Azure Maps web SDK használatával
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334316"
---
# <a name="use-the-drawing-tools-module"></a>A Rajzeszközök modul használata

A Azure Maps web SDK egy *Rajzeszközök modult*biztosít. Ez a modul megkönnyíti a Térkép alakzatainak rajzolását és szerkesztését egy beviteli eszköz, például egy egér vagy egy érintőképernyő használatával. Ennek a modulnak a fő osztálya a [rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). A projektmenedzser biztosítja a Térkép alakzatainak rajzolásához és szerkesztéséhez szükséges összes képességet. Közvetlenül is használható, és integrálva van egy egyéni eszköztár felhasználói felülettel. Használhatja a beépített [rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) osztályt is. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>A rajzolási eszközök modul betöltése egy weblapon

1. Hozzon létre egy új HTML-fájlt, és [a szokásos módon implementálja a térképet](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Töltse be a Azure Maps rajzolási eszközök modulját. Kétféleképpen is betöltheti:
    - Használja az Azure Maps Services modul globálisan üzemeltetett, Azure Content Delivery Network verzióját. Adja hozzá a JavaScript és a CSS stíluslap hivatkozását `<head>` a fájl eleméhez:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Vagy a Azure Maps web SDK forráskódját helyileg is betöltheti az [Azure-Maps-Draw-Tools](https://www.npmjs.com/package/azure-maps-drawing-tools) NPM csomag használatával, majd üzemeltetheti azt az alkalmazással. Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz. Használja ezt a parancsot:
    
        > **NPM telepítése az Azure-Maps-Draw-Tools**
    
        Ezután adjon hozzá egy hivatkozást a JavaScript és a CSS stíluslapra a `<head>` fájl elemében:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>A rajzolási kezelő közvetlen használata

Miután betöltötte a rajzolási eszközök modult az alkalmazásba, engedélyezheti a rajzolási és szerkesztési funkciókat a [rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)használatával. Megadhatja, hogy a rendszer hogyan hozza létre a rajzolási beállításokat, vagy `drawingManager.setOptions()` a függvényt használja.

### <a name="set-the-drawing-mode"></a>Rajzolási mód beállítása

A következő kód létrehoz egy példányt a rajzolás-kezelőben, és beállítja a rajzolási **mód** beállítást. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Az alábbi kód egy teljes körű futtatási példát mutat be, amely bemutatja, hogyan állítható be a rajzolási mód a rajzoló kezelőben. Kattintson a térképre a sokszög rajzolásának megkezdéséhez.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög rajzolása" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>sokszög rajzolásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>A beavatkozás típusának beállítása

A rajzolási kezelő három különböző módszert támogat a térképekkel való interakciók rajzolásához.

* `click`– A koordinátákat a rendszer az egér vagy érintés kattintásakor adja hozzá.
* `freehand `– A koordinátákat akkor adja hozzá a rendszer, amikor az egérmutatót a Térkép fölé húzza. 
* `hybrid`– A koordinátákat akkor adja hozzá a rendszer, ha az egér vagy a érintés kattint, vagy húzza a húzást.

A következő kód lehetővé teszi a sokszög rajzolási módját, és beállítja a rajzolási interakció típusát, amelyet a projektmenedzsernek `freehand`be kell tartania. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Ez a mintakód a sokszög rajzolásának funkcióit valósítja meg a térképen. Csak tartsa lenyomva a bal egérgombot, és húzza a körülötte lévőket szabadon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Szabad oldali rajzolás" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a> a tollat a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) függvényt a <a href='https://codepen.io'>CodePen</a>-on.
</iframe>


### <a name="customizing-drawing-options"></a>Rajzolási beállítások testreszabása

Az előző példák azt mutatták be, hogyan lehet testre szabni a rajzolási beállításokat a rajzobjektum példányának összeállítása során. A rajzolási beállítások a `drawingManager.setOptions()` függvény használatával is megadhatók. Az alábbi eszköz segítségével kipróbálhatja a rajzolási beállítások testreszabását a setOptions függvénnyel.

<br/>

<iframe height="685" title="A rajzolási kezelő testreszabása" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>a tollat</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Rajzolási eszköztár hozzáadása](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Formázott adatok lekérése](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Rajzolási eseményekre való reakció](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Az interakciók típusai és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
