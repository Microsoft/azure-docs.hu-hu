---
title: Kezelő Térkép eseményei | Microsoft Azure térképek
description: Megtudhatja, hogy mely események legyenek kirúgva, amikor a felhasználók a Maps szolgáltatással működnek Az összes támogatott térképi esemény listájának megtekintése. Tekintse meg, hogyan kezelheti az eseményeket az Azure Maps web SDK használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8121ceb68bdea9332316a9508bf6f4731e05b0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890785"
---
# <a name="interact-with-the-map"></a>A térképpel való interakció

Ez a cikk bemutatja, hogyan használhatja a [map Events osztályt](/javascript/api/azure-maps-control/atlas.map#events). A tulajdonság kiemeli az eseményeket a térképen és a Térkép különböző rétegein. Az eseményeket kiemelheti a HTML-jelölővel való interakció során is.

## <a name="interact-with-the-map"></a>A térképpel való interakció

Játsszon a lenti térképsel, és tekintse meg a jobb oldalon látható, a megfelelő egérmutatóval jelölt eseményeket. A **js lapra** kattintva megtekintheti és szerkesztheti a JavaScript-kódot. A CodePen-on található kód módosításához kattintson a **Szerkesztés a CodePen** elemre.

<br/>

<iframe height='600' scrolling='no' title='Interakció a térképsel – egér eseményei' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>a Térkép – az egér eseményei</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>A Térkép rétegekkel való interakció

A következő kód kiemeli a kilőtt eseményt, amikor a szimbólum réteggel lép kapcsolatba. A szimbólum, a buborék, a vonal és a sokszög réteg egyaránt támogatja ugyanezeket az eseményeket. A Heat Térkép és a csempe réteg nem támogatja ezeket az eseményeket.

<br/>

<iframe height='600' scrolling='no' title='Interakció a térképekkel – rétegbeli események' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a CodePen-on Azure Maps () című témakört a <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Térkép – réteg eseményeivel</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="interact-with-html-marker"></a>Interakció a HTML-jelölővel

A következő kód hozzáadja a JavaScript-leképezési eseményeket egy HTML-jelölőhöz. Kiemeli továbbá azon események nevét is, amelyek a HTML-jelölővel való interakció során felkészülnek.

<br/>

<iframe height='500' scrolling='no' title='Interakció a Térkép-HTML-jelölő eseményeivel' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a CodePen-on Azure Maps () által a <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Térkép-HTML jelölő eseményeivel kommunikáló</a> tollat <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

A következő táblázat felsorolja az összes támogatott leképezési osztály eseményét.

| Esemény               | Description |
|---------------------|-------------|
| `boxzoomend`        | A "Box zoom" interakció befejezése után.|
| `boxzoomstart`      | A "Box zoom" interakció indításakor indul el.|
| `click`             | Egy mutató eszköz megnyomásakor és a térképen való kikapcsolásakor jelenik meg.|
| `close`             | Akkor aktiválódik, ha a felugró ablak manuálisan vagy programozott módon van lezárva.|
| `contextmenu`       | Akkor következik be, amikor rákattintanak az egér jobb oldali gombjára.|
| `data`              | Az adatleképezések adatbetöltésekor vagy módosításakor következik be. |
| `dataadded`         | Akkor aktiválódik, amikor alakzatokat adnak hozzá a-hoz `DataSource` .|
| `dataremoved`       | A rendszer eltávolítja az alakzatokat a alkalmazásból `DataSource` .|
| `datasourceupdated` | Az objektum frissítésekor felgyújtották `DataSource` .|
| `dblclick`          | Akkor következik be, amikor egy mutató eszköz kétszer kattint a térképen megjelenő ponton.|
| `drag`              | A Térkép, az előugró ablak vagy a HTML-jelölő "húzás a serpenyőbe" művelete során ismételten felmerül.|
| `dragend`           | Ha egy "húzás a serpenyőbe" művelet befejeződik, a Térkép, az előugró ablak vagy a HTML-jelölő jelenik meg.|
| `dragstart`         | Az "húzza a serpenyőbe" interakciót a Térkép, az előugró ablak vagy a HTML-jelölő használatával kezdi meg.|
| `error`             | Hiba bekövetkezésekor következik be.|
| `idle`              | <p>Az utolsó olyan keret után következik be, amely után a Térkép "tétlen" állapotba kerül:<ul><li>Nincs folyamatban kamera-átmenet.</li><li>A jelenleg kért csempék betöltődik.</li><li>Minden áttűnési/átmenetes animáció befejeződött.</li></ul></p>|
| `keydown`           | A billentyű lenyomásakor aktiválódik.|
| `keypress`          | Egy tipizálható karaktert (ANSI-kulcsot) előállító kulcs megnyomásakor.|
| `keyup`             | Kulcs felszabadításakor.|
| `layeradded`        | Egy réteg a térképhez való hozzáadásakor aktiválódik.|
| `layerremoved`      | Akkor aktiválódik, amikor eltávolít egy réteget a térképről.|
| `load`              | Rögtön az összes szükséges erőforrás letöltését követően, a Térkép első vizuálisan teljes renderelése történt.|
| `mousedown`         | Akkor aktiválódik, amikor egy mutatóeszköz megnyomásra kerül a térképen, vagy egy elem tetején van.|
| `mouseenter`        | A rendszer a Térkép vagy egy elem fölé helyezi a mutató eszközt. |
| `mouseleave`        | Egy mutató eszköz kimozgatásakor a Térkép vagy egy elem. |
| `mousemove`         | Akkor aktiválódik, amikor egy mutató eszközt helyeznek át a térképen vagy egy elemen belül.|
| `mouseout`          | Ha egy pont eszköz elhagyja a Térkép vászonját, a kihagy egy elemet.|
| `mouseover`         | A Térkép vagy egy elem fölé helyezi a mutató eszközt.|
| `mouseup`           | Akkor aktiválódik, amikor egy mutató eszköz megjelenik a térképen vagy egy elem tetején.|
| `move`              | Az egyik nézetről a másikra történő animált áttérés során többször is elindítható a felhasználói interakció vagy módszerek eredményeképpen.|
| `moveend`           | Közvetlenül azután, hogy a Térkép befejezte a váltást az egyik nézetről a másikra, a felhasználói interakció vagy módszerek eredményeképpen.|
| `movestart`         | Közvetlenül a Térkép megkezdése előtt az egyik nézetről a másikra való áttérést a felhasználói interakció vagy módszerek eredményeképpen lehet megkezdeni.|
| `open`              | Akkor aktiválódik, amikor a felugró ablak manuálisan vagy programozott módon van megnyitva.|
| `pitch`             | Akkor következik be, amikor a Térkép szurok (billenés) megváltozik a felhasználói interakció vagy módszerek eredményeképpen.|
| `pitchend`          | Közvetlenül a térképi szurok (billenés) befejezése után a rendszer a felhasználói interakció vagy metódusok eredményeképpen változtatja meg a módosítást.|
| `pitchstart`        | Minden alkalommal, amikor a Térkép szurok (billenés) megkezdi a változást a felhasználói interakció vagy módszerek eredményeképpen.|
| `ready`             | Akkor aktiválódik, ha a minimálisan szükséges leképezési erőforrások betöltődik, mielőtt a Térkép készen áll a programozással való együttműködésre.|
| `render`            | <p>A Térkép a képernyőre való rajzolásakor következik be, a következők eredményeképpen:<ul><li>A Térkép pozíciójának, nagyításának, magasságának vagy megjelölésének módosítása</li><li>A Térkép stílusának módosítása</li><li>A `DataSource` forrás módosítása</li><li>Vektoros csempe, GeoJSON-fájl, karakterjel vagy sprite betöltése.</li></ul></p>|
| `resize`            | Közvetlenül a Térkép átméretezése után következik be.|
| `rotate`            | Többször is elindítható a "húzás a forgatáshoz" interakció során.|
| `rotateend`         | A "húzás a forgatáshoz" művelet véget ér.|
| `rotatestart`       | A "húzás az elforgatásra" művelet elindul.|
| `shapechanged`      | Egy alakzat objektum tulajdonságának módosításakor aktiválódik.|
| `sourcedata`        | Akkor következik be, amikor a Térkép egyik forrása betöltődik vagy módosul, beleértve a forrás betöltéséhez vagy módosításához tartozó csempe betöltését. |
| `sourceadded`       | Egy `DataSource` vagy `VectorTileSource` a térképhez való hozzáadásakor aktiválódik.|
| `sourceremoved`     | A rendszer kilőtte a `DataSource` `VectorTileSource` térképet, vagy eltávolítják azt.|
| `styledata`         | Akkor következik be, amikor a Térkép stílusa betöltődik vagy módosul.|
| `styleimagemissing` | Akkor aktiválódik, amikor egy réteg megpróbál betölteni egy rendszerképet a nem létező rendszerképből. |
| `tokenacquired`     | Azure Active Directory hozzáférési jogkivonat beszerzése után aktiválódik.|
| `touchcancel`       | Akkor `touchcancel` következik be, amikor esemény történik a térképen.|
| `touchend`          | Akkor `touchend` következik be, amikor esemény történik a térképen.|
| `touchmove`         | Akkor `touchmove` következik be, amikor esemény történik a térképen.|
| `touchstart`        | Akkor `touchstart` következik be, amikor esemény történik a térképen.|
| `wheel`             | Akkor következik be, amikor egy görgős esemény történik a térképen.|
| `zoom`              | A felhasználói interakció vagy módszerek eredményeképpen az egyik nagyítási szintről a másikra történő animált áttérés során többször is elindítható.|
| `zoomend`           | Közvetlenül a Térkép befejezése után az egyik nagyítási szintről a másikra való áttérést a felhasználói interakció vagy módszerek eredményeképpen végezheti el.|
| `zoomstart`         | Közvetlenül a Térkép megkezdése előtt az egyik nagyítási szintről a másikra vált, a felhasználói interakció vagy módszerek eredményeképpen.|


## <a name="next-steps"></a>Következő lépések

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [A Azure Maps Services modul használata](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kódminták](/samples/browse/?products=azure-maps)