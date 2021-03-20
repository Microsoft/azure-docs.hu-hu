---
title: Rajzolási eszközök interakciós típusai és billentyűparancsok a térképen | Microsoft Azure térképek
description: Alakzatok rajzolása és szerkesztése egér, érintőképernyő vagy billentyűzet használatával a Microsoft Azure Maps web SDK-ban
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895817"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>A rajzolási eszközök modulban található interakciós típusok és billentyűparancsok

Ez a cikk az alakzatok rajzolásának és szerkesztésének különböző módjait ismerteti egérrel, érintőképernyővel vagy billentyűparancsokkal.

A rajzolási kezelő három különböző módszert támogat a térképekkel való interakciók rajzolásához.

* `click` – A koordinátákat a rendszer az egér vagy érintés kattintásakor adja hozzá.
* `freehand ` – A koordinátákat akkor adja hozzá a rendszer, amikor az egérmutatót a Térkép fölé húzza.
* `hybrid` – A koordinátákat akkor adja hozzá a rendszer, ha az egér vagy a érintés kattint, vagy húzza a húzást.

## <a name="how-to-draw-shapes"></a>Alakzatok rajzolása

 Bármely alakzat rajzolása előtt állítsa a `drawingMode` rajzolási vezérlőt egy támogatott rajzolási beállításra. Ez a beállítás programozható vagy meghívható az eszköztár egyik rajzolási gombjának megnyomásával. A rajzolási mód engedélyezve marad, még egy alakzat rajzolása után is, így könnyen megrajzolhatja az azonos típusú további alakzatokat. Programozott módon állíthatja be a rajzolási módot üresjárati állapotra. Vagy váltson tétlen állapotba úgy, hogy az eszköztár aktuális rajzolási módok gombjára kattint.

A következő fejezetek körvonalazzák az alakzatok térképre rajzolt különböző módszereit.

### <a name="how-to-draw-a-point"></a>Pont rajzolása

Ha a Rajzfigyelő `draw-point` rajzolási módban van, a következő műveleteket végezheti el a Térkép pontjainak rajzolásához. Ezek a módszerek az összes interakciós móddal együtt működnek.

**Rajzolás indítása**
 - Kattintson a bal egérgombra, vagy érintse meg a térképet, és adjon hozzá egy pontot a térképhez. 
 - Ha az egér a Térkép fölé esik, nyomja le a `F` billentyűt, és az egérmutató koordinátáiban egy pontot fog hozzáadni. Ez a módszer nagyobb pontosságot biztosít a pontnak a térképhez való hozzáadásához. Az egér bal oldali gombjának lenyomása miatt kevesebb mozgás lesz az egérrel.
 - Ha `F` További pontokat szeretne hozzáadni a térképhez, kattintson a gombra, érintse meg vagy nyomja meg a gombot.
 
**Rajzolás befejezése**
 - Kattintson a Rajzolás eszköztár bármelyik gombjára. 
 - Programozott módon állíthatja be a rajzolási módot. 
 - Nyomja le a `C` billentyűt.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` billentyűt.

### <a name="how-to-draw-a-line"></a>Vonal rajzolása

Ha a rajzolási `draw-line` mód fut, a következő műveleteket végezheti el a térképen lévő pontok rajzolásához a kapcsolati mód függvényében.

**Rajzolás indítása**
 - Kattintási mód
   * Kattintson a bal egérgombra, vagy érintse meg a térképet, hogy egy sor minden pontját hozzáadja a térképen. Az egyes kattintásokhoz vagy érintésekhez egy koordináta kerül a sorba. 
   * Ha az egér a Térkép fölé esik, nyomja le a `F` billentyűt, és az egérmutató koordinátáiban egy pontot fog hozzáadni. Ez a módszer nagyobb pontosságot biztosít a pontnak a térképhez való hozzáadásához. Az egér bal oldali gombjának lenyomása miatt kevesebb mozgás lesz az egérrel.
   * Tartsa a kattintást, amíg az összes kívánt pont hozzá nem lett adva a sorhoz.
 - Szabadkézi mód
   * Nyomja le a bal oldali egérgombot, vagy koppintson a térképre, és húzza az egeret, vagy koppintson a pontra. A koordinátákat a rendszer hozzáadja az egérrel vagy érintési ponttal a Térkép körül. Amint megtörtént az egér vagy az érintéses esemény elindítása, a rendszer befejezi a rajzolást. A koordináták hozzáadásának gyakoriságát a rajzolási kezelők beállítás határozza meg `freehandInterval` .
 - Hibrid mód
   * A kattintások és a szabadkézi módszerek közötti váltás a kívánt módon, egyetlen sor rajzolásakor. Például kattintson a néhány pontra, majd az egér húzásával adjon hozzá egy csomó pontot, majd kattintson a továbbiak elemre. 

**Rajzolás befejezése**
 - Hibrid/kattintásos mód
   * Kattintson duplán a térképre az utolsó ponton. 
   * Kattintson a Rajzolás eszköztár bármelyik gombjára. 
   * Programozott módon állíthatja be a rajzolási módot. 
 - Szabadkézi mód
   * Adja meg az egérgombot vagy az érintési pontot.
 - Nyomja le a `C` billentyűt.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` billentyűt.

### <a name="how-to-draw-a-polygon"></a>Sokszög rajzolása

Ha a rajzolási `draw-polygon` mód fut, a következő műveleteket végezheti el a térképen lévő pontok rajzolásához a kapcsolati mód függvényében.

**Rajzolás indítása**
 - Kattintási mód
   * Kattintson a bal egérgombra, vagy érintse meg a térképet a sokszög minden pontjának a térképen való hozzáadásához. Az egyes kattintásokhoz vagy érintésekhez egy koordináta kerül a sokszögbe. 
   * Ha az egér a Térkép fölé esik, nyomja le a `F` billentyűt, és az egérmutató koordinátáiban egy pontot fog hozzáadni. Ez a módszer nagyobb pontosságot biztosít a pontnak a térképhez való hozzáadásához. Az egér bal oldali gombjának lenyomása miatt kevesebb mozgás lesz az egérrel.
   * Tartsa a kattintást, amíg az összes kívánt pont hozzá nem lett adva a sokszöghez.
 - Szabadkézi mód
   * Nyomja le a bal oldali egérgombot, vagy koppintson a térképre, és húzza az egeret, vagy koppintson a pontra. A koordinátákat a rendszer hozzáadja a sokszöghez, mert az egér vagy az érintési pont mozog a Térkép körül. Amint megtörtént az egér vagy az érintéses esemény elindítása, a rendszer befejezi a rajzolást. A koordináták hozzáadásának gyakoriságát a rajzolási kezelők beállítás határozza meg `freehandInterval` .
 - Hibrid mód
   * A kattintások és a szabadkézi módszerek közötti váltás a kívánt módon, egyetlen sokszög rajzolásakor. Például kattintson a néhány pontra, majd az egér húzásával adjon hozzá egy csomó pontot, majd kattintson a továbbiak elemre. 

**Rajzolás befejezése**
 - Hibrid/kattintásos mód
   * Kattintson duplán a térképre az utolsó ponton. 
   * Kattintson a sokszög első pontjára.
   * Kattintson a Rajzolás eszköztár bármelyik gombjára. 
   * Programozott módon állíthatja be a rajzolási módot. 
 - Szabadkézi mód
   * Adja meg az egérgombot vagy az érintési pontot.
 - Nyomja le a `C` billentyűt.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` billentyűt.

### <a name="how-to-draw-a-rectangle"></a>Négyszög rajzolása

Ha a rajzolási `draw-rectangle` mód fut, a következő műveleteket végezheti el a térképen lévő pontok rajzolásához a kapcsolati mód függvényében. A generált alakzat a [téglalapok kiterjesztett GeoJSON-specifikációját](extend-geojson.md#rectangle)követi.

**Rajzolás indítása**
 - Nyomja le a bal oldali egérgombot, vagy koppintson a térképen a téglalap első sarkának hozzáadásához, majd húzza a mutatót a téglalap létrehozásához. 

**Rajzolás befejezése**
 - Adja meg az egérgombot vagy az érintési pontot.
 - Programozott módon állíthatja be a rajzolási módot. 
 - Nyomja le a `C` billentyűt.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` billentyűt.

### <a name="how-to-draw-a-circle"></a>Kör rajzolása

Ha a rajzolási `draw-circle` mód fut, a következő műveleteket végezheti el a térképen lévő pontok rajzolásához a kapcsolati mód függvényében. A generált alakzat a [körök kibővített GeoJSON-specifikációját](extend-geojson.md#circle)fogja követni.

**Rajzolás indítása**
 - Nyomja le a bal oldali egérgombot, vagy koppintson a térképen a kör középpontjának hozzáadásához, és húzza a köröket egy sugárba. 

**Rajzolás befejezése**
 - Adja meg az egérgombot vagy az érintési pontot.
 - Programozott módon állíthatja be a rajzolási módot. 
 - Nyomja le a `C` billentyűt.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` billentyűt.

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

A rajzolási eszközök támogatják a billentyűparancsokat. Ezek a billentyűparancsok akkor működőképesek, ha a Térkép fókuszban van.

| Kulcs      | Művelet                            |
|----------|-----------------------------------|
| `C` | Befejezi a folyamatban lévő összes rajzot, és a rajzolási módot Üresjáratra állítja. A fókusz a legfelső szintű Térkép elemre lép.  |
| `Escape` | Megszakítja a folyamatban lévő összes rajzot, és a rajzolási módot Üresjáratra állítja. A fókusz a legfelső szintű Térkép elemre lép.  |
| `F` | Egy pontot, vonalat vagy sokszöget tartalmazó koordinátákat helyez el, ha az egér a Térkép fölé esik. A térképre kattintáskor vagy hibrid módban való kattintással egyenértékű művelet. Ez a parancsikon pontosabb és gyorsabb rajzok használatát teszi lehetővé. Az egér mozgatásához és a többi gomb megnyomásához használhatja az egeret a lenyomásos kézmozdulattal. |

## <a name="next-steps"></a>Következő lépések

További információ a rajzolási eszközök modul osztályairól:

> [!div class="nextstepaction"]
> [Rajzolási kezelő](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)