---
title: Csempe réteg hozzáadása térképhez | Microsoft Azure térképek
description: Ismerje meg, hogyan hozhatja ki a képeket a térképeken. A Azure Maps web SDK-val egy olyan csempe réteget adhat hozzá, amely egy térképre mutató időjárási radar átfedését tartalmazza.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e0fda77be23f6ea16d5e64b5d4796813c53f0e94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608102"
---
# <a name="add-a-tile-layer-to-a-map"></a>Csemperéteg hozzáadása térképhez

Ez a cikk bemutatja, hogyan fedi le a csempéket a térképre. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. A Azure Maps csempe rendszerével kapcsolatos további információkért lásd: [nagyítási szintek és csempék rácsa](zoom-levels-and-tile-grid.md).

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a lemezképeket lehet előre megjeleníteni vagy dinamikusan megjeleníteni. Az előre megjelenített lemezképek, mint a kiszolgálók bármely más lemezképe, egy elnevezési konvenció használatával, amely a csempe rétegének ismerete. A dinamikusan renderelt képek egy szolgáltatás használatával töltik be a képeket valós időben. Az Azure Maps [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat:

* X, Y, nagyítás jelölése – az X az oszlop, az Y a csempe rácsában lévő csempe sora, a nagyítási szint pedig a nagyítási szint alapján van megadva.
* Quadkey-jelölés – az x, y és nagyítási adatokat egyetlen karakterlánc-értékre kombinálja. Ez a karakterlánc-érték egyetlen csempe egyedi azonosítója lesz.
* Határolókeret – a határolókeret koordinátáit tartalmazó képet ad meg: `{west},{south},{east},{north}` . Ezt a formátumot általában a [webes leképezési szolgáltatások (WMS)](https://www.opengeospatial.org/standards/wms)használják.

> [!TIP]
> A [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, a vektoros adatok csempe rétegként is megjeleníthető. Ha a vektoros adatmegjelenítést csempe rétegként jeleníti meg, a Térkép vezérlőelemnek csak az általuk képviselt adatmennyiségnél kisebb méretű csempéket kell betöltenie. Ezt a technikát általában több millió adatsor megjelenítésére használják a térképen.

A csempe rétegbe átadott csempe URL-címnek http vagy HTTPS URL-címnek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonhoz, amely a következő paramétereket használja:

* `{x}` -A csempe X pozíciója `{y}`A és a is szükséges `{z}` .
* `{y}` -A csempe Y pozíciója `{x}`A és a is szükséges `{z}` .
* `{z}` – A csempe nagyítási szintje `{x}`A és a is szükséges `{y}` .
* `{quadkey}` – Csempe quadkey-azonosítója a Bing Maps csempe rendszerelnevezési konvenciója alapján.
* `{bbox-epsg-3857}` – Egy határolókeret karakterlánca, amely a `{west},{south},{east},{north}` EPSG 3857 térbeli hivatkozási rendszer formátumát jelöli.
* `{subdomain}` – Az altartomány értékeinek helyőrzője, ha meg van adva, a `subdomain` hozzá lesz adva.
* `{azMapsDomain}` – A Térkép által használt értékekkel összehangolja a tartományt és a csempe-kérelmek hitelesítését.

## <a name="add-a-tile-layer"></a>Mozaikréteg hozzáadása

 Ez a minta bemutatja, hogyan hozhat létre csempéket tartalmazó csempe réteget. Ez a példa az x, y, zoom csempe rendszerét használja. a csempe rétegének forrása a OpenSeaMap- [projekt](https://openseamap.org/index.php), amely a tömegből származó tengeri diagramokat tartalmazza. A radar-információk megtekintésekor ideális esetben a felhasználók egyértelműen megtekinthetik a városok feliratait, ahogy azok a térképen navigálnak. Ez a viselkedés úgy valósítható meg, ha beszúrja a csempe réteget a `labels` réteg alá.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Réteg csempe X, Y és Z használatával' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>csempe réteget X, Y és Z használatával</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) értékkel a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC webes leképezési szolgáltatás (WMS) hozzáadása

A webes leképezési szolgáltatás (WMTS) egy Nyílt térinformatikai konzorcium (OGC) szabvány a térképes adatképek kiszolgálásához. Ebben a formátumban számos nyitott adatkészletet lehet használni, amelyeket az Azure Maps használatával használhat. Ez a típusú szolgáltatás használható csempe réteggel, ha a szolgáltatás támogatja a `EPSG:3857` koordináta-hivatkozási rendszer (CRS) használatát. A WMS szolgáltatás használatakor a szélességi és a magassági paramétereket a szolgáltatás által támogatott értékre állítsa be, ügyeljen arra, hogy ugyanazt az értéket adja meg a `tileSize` beállításban. A formázott URL-címben állítsa be a `BBOX` szolgáltatás paraméterét a `{bbox-epsg-3857}` helyőrzővel.

Az alábbi képernyőfelvételen a fenti kód látható, amely a Földtani adatok webes leképezési szolgáltatását fedi le az [Egyesült államokbeli geológiai felmérésből (USGS)](https://mrdata.usgs.gov/) , a címkék alatt.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="WMS csempe réteg" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BapjZqr'>WMS csempe réteget</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC web-Mapping csempe szolgáltatás (WMTS) hozzáadása

A web-Mapping csempe szolgáltatás (WMTS) egy Nyílt térinformatikai konzorcium (OGC) szabvány, amely csempe alapú átfedéseket szolgál a Maps-hez. Ebben a formátumban számos nyitott adatkészletet lehet használni, amelyeket az Azure Maps használatával használhat. Ez a típusú szolgáltatás használható csempe réteggel, ha a szolgáltatás támogatja a `EPSG:3857` vagy `GoogleMapsCompatible` koordináta hivatkozási rendszerét (CRS). WMTS szolgáltatás használata esetén a szélességi és a magassági paramétereket a szolgáltatás által támogatott értékre állítsa be, ügyeljen arra, hogy ugyanazt az értéket adja meg a `tileSize` beállításban. A formázott URL-címben cserélje le a következő helyőrzőket ennek megfelelően:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

Az alábbi képernyőfelvételen látható, hogy a fenti kód egy webes leképezési csempe-szolgáltatást ábrázol, amely az [Egyesült államokbeli geológiai felmérés (USGS) országos térképe](https://viewer.nationalmap.gov/services/) , amely az utak és a címkék alatt található.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="WMTS csempe réteg" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BapjZVY'>WMTS csempe réteget</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Csempe rétegének testreszabása

A csempe réteg osztályának számos stílusa van. Itt látható egy eszköz, amellyel kipróbálhatja őket.

<br/>

<iframe height='700' scrolling='no' title='Csempe rétegének beállításai' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>csempe rétegének beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Rendszerképréteg hozzáadása](./map-add-image-layer.md)
