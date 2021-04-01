---
title: Rajzolási eszköz eseményei | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá rajzolási eszköztárat térképekhez Microsoft Azure Maps web SDK használatával
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089343"
---
# <a name="drawing-tool-events"></a>Rajzolási eszköz eseményei

Térképeken a rajzolási eszközök használatakor hasznos lehet reagálni bizonyos eseményekre, amikor a felhasználó a térképen rajzol. Ez a táblázat felsorolja az osztály által támogatott összes eseményt `DrawingManager` .

| Esemény | Description |
|-------|-------------|
| `drawingchanged` | Az alakzatban lévő bármely koordináta hozzáadása vagy módosítása történt. | 
| `drawingchanging` | Akkor aktiválódik, amikor megjelenik egy alakzat előnézeti koordinátái. Ez az esemény például többször is kigyullad, mivel a rendszer a koordinátákat húzza. | 
| `drawingcomplete` | Egy alakzat szerkesztési módból való befejezése vagy kilépésekor fellépett. |
| `drawingmodechanged` | A rajzolási mód módosult. Az új rajzolási mód átkerül az eseménykezelőbe. |
| `drawingstarted` | Akkor aktiválódik, amikor a felhasználó megkezdi az alakzat rajzolását, vagy szerkesztési módba helyezi az alakzatot.  |

Az alábbi kód azt mutatja be, hogyan működik a rajzolási eszközök moduljának eseményei. Rajzoljon alakzatokat a térképen, és tekintse meg az események tüzet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási eszközök eseményei" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>rajzolási eszközeinek eseményeit</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Példák

Lássuk néhány olyan gyakori forgatókönyvet, amely a rajzolási eszközök eseményeit használja.

### <a name="select-points-in-polygon-area"></a>Pontok kijelölése a sokszög területen

Ez a kód azt mutatja be, hogyan lehet figyelni a felhasználói rajzok alakzatainak eseményeit. Ebben a példában a kód a sokszögek, a téglalapok és a körök alakzatait figyeli. Ezután meghatározza, hogy a térképen mely adatpontok vannak a rajzolt területen. Az `drawingcomplete` esemény a Select Logic aktiválására szolgál. A válassza ki a logikát, a kód a térképen lévő összes adatponton keresztül hurkot. Ellenőrzi, hogy van-e metszéspontja a pontnak és a rajzolt alakzat területének. Ez a példa a nyílt forráskódú [Turf.js](https://turfjs.org/) könyvtár használatát teszi lehetővé a térbeli metszetek számításának végrehajtásához.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A rajzolt sokszög területének kijelölése" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/XWJdeja'>rajzolt sokszög területen</a> a CodePen-on lévő Azure Maps () elemnél <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rajzolás és keresés a sokszög területén

Ez a kód egy alakzat területén belüli érdeklődési pontokat keres, miután a felhasználó befejezte az alakzat rajzolását. A kód módosításához és végrehajtásához kattintson a keret jobb felső sarkában található "szerkesztés a kódban" elemre. Az `drawingcomplete` esemény a keresési logika elindítására szolgál. Ha a felhasználó téglalapot vagy sokszöget rajzol, a rendszer a geometrián belüli keresést hajtja végre. Ha kört rajzol, a rendszer a sugár és a középpont pozícióját használja egy érdekes hely kereséséhez. Az `drawingmodechanged` esemény segítségével megállapítható, hogy mikor vált a felhasználó a rajzolási módba, és ez az esemény törli a vásznat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolás és keresés a sokszög területén" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>, és keressen rá a sokszög területén</a> Azure Maps () elemre <a href='https://codepen.io/azuremaps'>@azuremaps</a> a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Mérési eszköz létrehozása

Az alábbi kód azt mutatja be, hogy a rajzolási események hogyan használhatók mérési eszköz létrehozásához. A az `drawingchanging` alakzat figyelésére szolgál, ahogy a rajzolás folyamatban van. Ahogy a felhasználó áthelyezi az egeret, a rendszer kiszámítja az alakzat dimenzióit. Az `drawingcomplete` esemény a végső számítást végzi az alakzaton a rajzolást követően. Az `drawingmodechanged` esemény segítségével határozható meg, hogy a felhasználó mikor váltson rajzolási módba. Emellett az  `drawingmodechanged` esemény törli a vásznat, és törli a régi mérési adatokat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mérési eszköz" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>mérési eszközét</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Formázott adatok lekérése](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Az interakciók típusai és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a Services modulról:

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

További példák a kód megadására:

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)
