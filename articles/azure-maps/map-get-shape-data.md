---
title: Adatok beolvasása a térképen lévő alakzatokból | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan szerezheti be az adatalakzatokat térképeken a Microsoft Azure Maps web SDK használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ddb8009e544ede82d1c56d112950ff247a87380c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890700"
---
# <a name="get-shape-data"></a>Formázott adatok lekérése

Ez a cikk bemutatja, hogyan kérheti le a térképre rajzolt alakzatok mennyiségét. A **drawingManager. getSource ()** függvényt használjuk a [rajzolási kezelőn](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--)belül. A rajzolt alakzatok geojson-adatok kinyerése és a máshol való használata különböző forgatókönyveket mutat be.  


## <a name="get-data-from-drawn-shape"></a>Adatok lekérése rajzolt alakzatból

A következő függvény beolvassa a rajzolt alakzat forrásául szolgáló adatokat, és megjeleníti a képernyőre. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Az alábbiakban a teljes futó kód minta látható, ahol rajzolhat egy alakzatot a funkció teszteléséhez:

<br/>

<iframe height="686" title="Formázott adatok lekérése" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>a tollat</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Rajzolási eseményekre való reakció](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Az interakciók típusai és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)