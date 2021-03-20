---
title: Képsablonok a Azure Maps web SDK-ban | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá képikonokat és mintázattal kitöltött sokszögeket a Maps-hez a Azure Maps web SDK használatával. Tekintse meg a rendelkezésre álló képet és a kitöltési minta sablonokat.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895681"
---
# <a name="how-to-use-image-templates"></a>Rendszerképsablonok használata

A képek HTML-jelölővel és a Azure Maps web SDK-n belüli különböző rétegekkel is használhatók:

 - A szimbólumok rétegei képikont ábrázoló pontokat adhatnak a térképen. A szimbólumok a sorok elérési útja mentén is megjeleníthető.
 - A sokszög rétegek kitöltési minta képpel is megjeleníthető. 
 - A HTML-jelölők képek és más HTML-elemek használatával adhatnak ki pontokat.

A megfelelő teljesítmény biztosítása érdekében a renderelés előtt töltse be a lemezképeket a Térkép rendszerképéhez tartozó sprite-erőforrásba. A SymbolLayer [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)előre betölt néhány jelölő képet néhány színben a Térkép képének sprite-ba, alapértelmezés szerint. Ezek a jelölő képek és egyebek SVG-sablonokként érhetők el. Felhasználhatók egyéni léptékű rendszerképek létrehozására, vagy ügyfél elsődleges és másodlagos színként való használatára. Összesen 42 képsablonok vannak megadva: 27 szimbólum ikon és 15 sokszög kitöltési minta.

A képsablonok a függvény használatával adhatók hozzá a Térkép-rendszerkép sprite-erőforrásaihoz `map.imageSprite.createFromTemplate` . Ez a függvény legfeljebb öt paraméter átadását teszi lehetővé;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Az `id` egy egyedi azonosító, amelyet Ön hozott létre. A hozzá `id` van rendelve a képhez, amikor a program hozzáadja a Maps-képhez a sprite-hoz. Használja ezt az azonosítót a rétegek között annak meghatározásához, hogy melyik képerőforrást szeretné megjeleníteni. Az `templateName` határozza meg, hogy melyik képsablont kell használni. A `color` beállítás megadja a rendszerkép elsődleges színét, és a `secondaryColor` beállítások a rendszerkép másodlagos színét állítja be. A `scale` beállítással méretezhető a Képsablon, mielőtt alkalmazza azt a képen látható sprite-ra. Ha a képet a rendszer a képre alkalmazza a sprite-ra, a rendszer PNG-re konvertálja. A ropogós renderelés érdekében érdemes a képsablont a sprite-ba felvenni, mint a rétegben való méretezéshez.

Ez a függvény aszinkron módon tölti be a képet a sprite-ba. Így egy olyan ígéretet ad vissza, amely megvárhatja, hogy a függvény befejeződjön.

A következő kód bemutatja, hogyan hozhat létre egy rendszerképet az egyik beépített sablonból, és hogyan használhatja azt egy szimbólum réteggel.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Képsablon használata szimbólum réteggel

Miután betöltötte a képsablont a térképi képen a sprite-ba, a képerőforrás-AZONOSÍTÓra hivatkozva szimbólumként is megjeleníthető `image` `iconOptions` .

Az alábbi minta egy szimbólum réteget jelenít meg a `marker-flat` képsablonban egy kékeszöld elsődleges színnel és egy fehér másodlagos színnel. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Beépített ikon sablonnal rendelkező szimbólum réteg" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>szimbólum réteget a beépített ikon sablonnal</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Képsablon használata a vonalak elérési útja mentén

Miután betöltötte a képsablont a térképi képen a sprite-ba, az egy vonal elérési útján jeleníthető meg egy LineString egy adatforráshoz való hozzáadásával, valamint egy szimbólum-réteg és egy kapcsoló használatával, a `lineSpacing` képerőforrás azonosítójának hivatkozásával a `image` th lehetőségnél `iconOptions` . 

Az alábbi minta egy rózsaszín vonalat jelenít meg a térképen, és egy szimbólum réteget használ a `car` Képsablon használatával, egy Vagány kék elsődleges színnel és egy fehér másodlagos színnel. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Beépített ikon sablonnal rendelkező vonal réteg" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
A CodePen-on Azure Maps () használatával megtekintheti a Pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>line réteget a beépített ikon sablonnal</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

> [!TIP]
> Ha a Képsablon felfelé mutat, állítsa a `rotation` szimbólum réteg ikonját a 90 értékre, ha azt szeretné, hogy a sor azonos irányba mutasson.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Képsablon használata sokszög réteggel

Miután betöltötte a képsablont a térképi képen a sprite-ba, a képerőforrás-AZONOSÍTÓra hivatkozva kitöltési mintaként jelenítheti meg a `fillPattern` réteget.

Az alábbi minta egy sokszög réteget jelenít meg a `dot` képsablonnal, vörös elsődleges színnel és transzparens másodlagos színnel.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A sokszög kitöltése beépített ikon sablonnal" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>kitöltés sokszögét a beépített ikon sablonnal</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Kitöltési minták másodlagos színének beállítása megkönnyíti az alapul szolgáló Térkép megtekintését, így továbbra is az elsődleges mintát fogja biztosítani. 

## <a name="use-an-image-template-with-an-html-marker"></a>Képsablon használata HTML-jelölővel

Egy Képsablon lekérhető a függvény használatával `altas.getImageTemplate` , és a HTML-jelölő tartalmának használata. A sablon átadható a `htmlContent` jelölőnek, majd testreszabható a `color` , `secondaryColor` , és `text` kapcsolók használatával.

Az alábbi minta `marker-arrow` egy vörös elsődleges színnel, egy rózsaszín másodlagos színnel és egy "00" szöveges értékkel rendelkező sablont használ.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-jelölő beépített ikon sablonnal" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML-jelölőt a beépített ikon sablonnal</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> A képsablonok a térképen kívül is használhatók. A getImageTemplate funciton egy olyan SVG-karakterláncot ad vissza, amely helyőrzőket tartalmaz; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Cserélje le ezeket a helyőrző értékeket egy érvényes SVG-karakterlánc létrehozásához. Ezután hozzáadhatja az SVG-karakterláncot közvetlenül a HTML DOM-hoz, vagy átalakíthatja egy adaturi-ba, és beszúrhatja egy képcímkébe. Például:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Egyéni újrafelhasználható sablonok létrehozása

Ha az alkalmazás ugyanazt az ikont használja különböző ikonokkal, vagy ha olyan modult hoz létre, amely további képsablonokat ad hozzá, egyszerűen hozzáadhatja és lekérheti ezeket az ikonokat a Azure Maps web SDK-ból. Használja a következő statikus függvényeket a `atlas` névtérben.

| Name | Visszatérési típus | Description | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Hozzáadja az egyéni SVG-képsablont az Atlas-névtérhez. |
|  `getImageTemplate(templateName: string, scale?: number)`| sztring | Egy SVG-sablon beolvasása név alapján. |
| `getAllImageTemplateNames()` | karakterlánc [] |  Egy SVG-sablon beolvasása név alapján. |

Az SVG-képsablonok a következő helyőrző értékeket támogatják:

| Helyőrző | Description |
|-|-|
| `{color}` | Az elsődleges szín. | 
| `{secondaryColor}` | A másodlagos szín | 
| `{scale}` | Az SVG-képet png-képpé alakítja a rendszer, amikor hozzáadja őket a Térkép képéhez sprite. Ez a helyőrző felhasználható a sablon méretezésére a konvertálás előtt, így biztosítva, hogy a rendszer világosan megjelenítse. | 
| `{text}` | A szöveg megjelenítésének helye HTML-jelölővel való használatkor. |

Az alábbi példa bemutatja, hogyan hozhat létre SVG-sablont, és hogyan adhatja hozzá a Azure Maps web SDK-hoz újrafelhasználható ikon sablonként. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Egyéni ikon-sablon hozzáadása az Atlas-névtérhez" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Egyéni ikon-sablon hozzáadása az Atlas-névtérhez</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) lehetőséget a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Képsablonok listája

Ez a táblázat felsorolja az Azure Maps web SDK-ban jelenleg elérhető összes rendszerkép-sablont. A sablon neve az egyes rendszerképeknél nagyobb. Alapértelmezés szerint az elsődleges szín kék színű, a másodlagos szín pedig fehér. Ahhoz, hogy a másodlagos szín könnyebben látható legyen a fehér háttéren, a következő képeknél a másodlagos szín feketére van állítva.

**Szimbólum ikon sablonjai**

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      jelölő – vastag
   :::column-end:::
   :::column span="":::
      jelölő – kör
   :::column-end:::
   :::column span="":::
      jelölő – lapos
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![jelölő ikon](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![jelölő – vastag ikon](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![jelölő – kör ikon](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![jelölő – lapos ikon](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      jelölő – négyzet
   :::column-end:::
   :::column span="":::
      jelölő-négyzet-fürt
   :::column-end:::
   :::column span="":::
      jelölő – nyíl
   :::column-end:::
   :::column span="":::
      jelölő – golyós PIN-kód
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![jelölő – négyzet ikon](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![jelölő – négyzet-fürt ikon](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![jelölő – nyíl ikon](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![jelölő-labda-PIN ikon](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      jelölő – négyzet kerekített
   :::column-end:::
   :::column span="":::
      jelölő-négyzet-kerekített fürt
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      jelölő – háromszög
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![jelölő – négyzet alakú lekerekített ikon](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![jelölő-négyzet-lekerekített fürt ikonja](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![jelölő ikon](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![jelölő – háromszög ikon](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      háromszög
   :::column-end:::
   :::column span="":::
      háromszög – vastag
   :::column-end:::
   :::column span="":::
      háromszög – nyíl fel
   :::column-end:::
   :::column span="":::
      háromszög – nyíl balra
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![háromszög ikon](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![háromszög – vastag ikon](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![háromszög-nyíl ikon](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![háromszög – nyíl bal oldali ikon](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      hatszög
   :::column-end:::
   :::column span="":::
      hatszög – vastag
   :::column-end:::
   :::column span="":::
      hatszög – lekerekített
   :::column-end:::
   :::column span="":::
      hatszög – kerekített – vastag
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![hatszög ikon](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![hatszögletű – vastag ikon](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![hatszög – lekerekített ikon](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![hatszög – kerekített vastagságú ikon](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      PIN
   :::column-end:::
   :::column span="":::
      rögzítési kör
   :::column-end:::
   :::column span="":::
      lekerekített négyzet
   :::column-end:::
   :::column span="":::
      lekerekített négyzetes vastag
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![gombostű ikon](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![rögzítési kör ikonja](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![lekerekített négyzet ikon](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![lekerekített négyzetes vastagságú ikon](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      nyíl fel
   :::column-end:::
   :::column span="":::
      nyíl – vékony
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![nyíl ikon](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![nyíl – vékony ikon](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![autós ikon](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Sokszög kitöltési mintájának sablonjai**

:::row:::
   :::column span="":::
      Checker
   :::column-end:::
   :::column span="":::
      ellenőr – elforgatva
   :::column-end:::
   :::column span="":::
      körök
   :::column-end:::
   :::column span="":::
      körökre lefoglalt
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ellenőrzési ikon](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![ellenőr által elforgatott ikon](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![körök ikon](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![körökre kihelyezett ikon](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      átlós vonalak – felfelé
   :::column-end:::
   :::column span="":::
      átlós vonalak – lefelé
   :::column-end:::
   :::column span="":::
      átlós sávok – felfelé
   :::column-end:::
   :::column span="":::
      átlós sávok – lefelé
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![átlós vonalak – felfelé ikon](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![átlós vonalak – lefelé ikon](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![átlós sávok – ikon](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![átlós sáv – lefelé ikon](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      rács – vonalak
   :::column-end:::
   :::column span="":::
      elforgatva – rács – vonalak
   :::column-end:::
   :::column span="":::
      elforgatva – Grid-Stripes
   :::column-end:::
   :::column span="":::
      x – kitöltés
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![rács – vonalak ikon](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![elforgatva – rács – vonalak ikon](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![elforgatva – Grid-Stripes ikon](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x-Fill ikon](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      Zig-Zag
   :::column-end:::
   :::column span="":::
      Zig-Zag-vertikális
   :::column-end:::
   :::column span="":::
      pontok
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Zig-Zag ikon](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![Zig-Zag-függőleges ikon](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![pontok ikon](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**Előre feltöltött képikonok**

A Térkép előre betölti az ikonok készletét a Maps-rendszerkép sprite-ban a `marker` , `pin` és a sablonok használatával `pin-round` . Ezek az ikonok neve és a színértékük az alábbi táblázatban látható.

| ikon neve | szín | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Kipróbálás most eszköz

Az alábbi eszközzel különböző módokon jelenítheti meg a különböző beépített képsablonokat, és testre szabhatja az elsődleges és másodlagos színeket és a méretezést.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ikon sablon beállításai" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>ikon sablonjának beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Atlas-névtér](/javascript/api/azure-maps-control/atlas#functions
)

A következő cikkekből megtudhatja, hogy miként használhatók a képsablonok:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML-készítők hozzáadása](map-add-bubble-layer.md)