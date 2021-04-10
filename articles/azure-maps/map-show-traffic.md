---
title: Térkép forgalmának megjelenítése | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá adatforgalmi adatokat a térképekhez. Ismerje meg a flow adatait, és tekintse meg, hogyan vehet fel incidenseket és adatfolyamatokat a Maps-be a Azure Maps web SDK használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b6fcf39e05c4649503f0b7a80aadaaa16df24674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98599559"
---
# <a name="show-traffic-on-the-map"></a>Forgalom megjelenítése a térképen

A Azure Mapsban kétféle forgalmi adatok érhetők el:

- Incidensek – olyan pont-és vonal-alapú adatmennyiségből állnak, mint például az építőipar, a közúti bezárások és a balesetek.
- Flow-adatok – mérőszámokat biztosít a forgalom forgalmáról az utakon. Gyakran a forgalmi forgalomra vonatkozó adatokat használják az utak színezésére. A színek attól függnek, hogy mekkora forgalom lassul le a folyamaton, a sebességkorlátozás vagy más metrika alapján. Négy érték adható át a `flow` Térkép forgalmi lehetőségéhez.

    |Folyamat értéke | Leírás|
    | :-- | :-- |
    | `none` | Nem jeleníti meg a forgalmi adatokat a térképen |
    | `relative` | Az út szabad áramlási sebességéhez viszonyított forgalmi adatokat jeleníti meg |
    | `relative-delay` | A várt átlagos késésnél lassabb területeket jelenít meg. |
    | `absolute` | Az összes jármű teljes sebességét jeleníti meg az úton |

A következő kód bemutatja, hogyan jelenítheti meg a forgalmi adatokat a térképen.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Térkép forgalmának megjelenítése' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A CodePen-on található Azure Maps () használatával megtekintheti az <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>adatforgalom megjelenítése a térképen</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="traffic-overlay-options"></a>Forgalom átfedési beállításai

A következő eszköz lehetővé teszi a különböző forgalom átfedési beállításainak közötti váltást, hogy megtekintse a renderelés változásait. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Forgalom átfedési beállításai" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>forgalom átfedési beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Forgalmi vezérlők hozzáadása

Két különböző forgalmi vezérlő adható hozzá a térképhez. Az első vezérlőelem, `TrafficControl` amely egy váltógomb hozzáadására szolgál, amellyel be-és kikapcsolhatja a forgalmat. A vezérlő beállításai lehetővé teszik a forgalom megjelenítéséhez használandó forgalmi beállítások megadását. Alapértelmezés szerint ez a vezérlő a relatív adatforgalom és az incidensek adatokat jeleníti meg, azonban ezt megváltoztathatja az abszolút forgalom megjelenítéséhez, és ha szükséges, incidensek nélkül. A második vezérlő `TrafficLegendControl` egy adatforgalmi jelmagyarázatot hoz létre a térképhez, amely segít a felhasználóknak megérteni, hogy mit jelent a színkódok elérési útja. Ez a vezérlő csak akkor jelenik meg a térképen, ha a forgalmi flow-adatok megjelennek a térképen, és minden más időpontban el lesznek rejtve.

A következő kód bemutatja, hogyan adhatja hozzá a forgalmi vezérlőket a térképhez.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Forgalmi vezérlők" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>Traffic Controls</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

Fejlessze felhasználói élményeit:

> [!div class="nextstepaction"]
> [Térkép interakciója az egér eseményeivel](map-events.md)

> [!div class="nextstepaction"]
> [Akadálymentes Térkép létrehozása](map-accessibility.md)

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)