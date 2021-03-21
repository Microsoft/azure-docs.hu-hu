---
title: Felugró ablak hozzáadása egy ponthoz a térképen | Microsoft Azure térképek
description: Ismerkedjen meg a felugró ablakokkal, a felugró sablonokkal és az előugró eseményekkel Azure Maps. Megtudhatja, hogyan adhat hozzá egy előugró pontot egy térképhez, és hogyan használhatja fel és testreszabhatja a felugró ablakokat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 903574a8c8696f10d9903a685bab8d12b3e6395f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891036"
---
# <a name="add-a-popup-to-the-map"></a>Felugró ablak hozzáadása a térképhez

Ez a cikk bemutatja, hogyan adhat hozzá egy előugró pontot egy térképen.

## <a name="understand-the-code"></a>A kód értelmezése

A következő kód egy szimbólum réteget használva hozzáadja a térképhez egy pont funkciót, amely rendelkezik `name` `description` a és a tulajdonsággal. Az [előugró osztály](/javascript/api/azure-maps-control/atlas.popup) egy példánya jön létre, de nem jelenik meg. Az Egérgombok az előugró ablak megnyitásának és bezárásának elindításához lesznek hozzáadva a szimbólum réteghez. Ha a jelölő szimbólum látható, az előugró ablak `position` tulajdonsága a jelölő pozíciójának megfelelően frissül, és a `content` beállítás egy olyan HTML-vel frissül, amely betakarja az  `name` `description` egérmutatót a pont funkció és tulajdonságai között. Ekkor megjelenik a felugró ablak a térképen a `open` függvény használatával.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak hozzáadása a Azure Maps használatával' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a következőt: Azure Maps Azure Maps () <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>használatával</a> a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Felugró ablak újrahasználata több ponttal

Vannak olyan esetek, amikor a legjobb módszer egy előugró ablak létrehozása és újbóli felhasználása. Előfordulhat például, hogy nagy számú ponttal rendelkezik, és egyszerre csak egy előugró ablak jelenik meg. A felugró ablak újrafelhasználásával jelentősen csökken az alkalmazás által létrehozott DOM-elemek száma, ami jobb teljesítményt nyújt. Az alábbi minta 3 pontos funkciókat hoz létre. Ha bármelyikre kattint, megjelenik egy előugró ablak, amely az adott pont funkciójának tartalmát jeleníti meg.

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak újrahasználata több PIN-kód használatával' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on Azure Maps () által <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>több PIN</a> -kód használatával <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="customizing-a-popup"></a>Felugró ablak testreszabása

Alapértelmezés szerint az előugró ablak fehér háttérrel, a lenti mutató nyílra, valamint a jobb felső sarokban található Bezárás gombra mutat. A következő minta a háttérszínt fekete színnel változtatja meg a `fillColor` felugró ablak beállításával. A Bezárás gomb eltávolításához állítsa a `CloseButton` beállítást hamis értékre. Az előugró ablak HTML-tartalma 10 képpont kitöltését használja az előugró ablak széleiből. A szöveg fehér színű, így szépen megjelenik a fekete háttéren.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Testreszabott előugró ablak" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollal <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>testreszabott előugró ablakokat</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Felugró sablonok hozzáadása a térképhez

Az előugró sablonok segítségével egyszerűen hozhat létre adatvezérelt elrendezéseket az előugró ablakokhoz. Az alábbi szakaszokban számos előugró sablon használható a formázott tartalom létrehozásához a szolgáltatások tulajdonságai segítségével.

> [!NOTE]
> Alapértelmezés szerint az összes megjelenített tartalom az előugró sablon használatával lesz feldolgozva egy iframe-kereten belül, biztonsági szolgáltatásként. Vannak azonban korlátozások:
>
> - A rendszer letiltja az összes parancsfájlt, űrlapot, mutató zárolást és felső navigációs funkciót. A hivatkozások egy új lapon megnyithatók, amikor rákattintanak. 
> - Azok a régebbi böngészők, amelyek nem támogatják a `srcdoc` paramétert az IFRAME-eszközökön, csak kis mennyiségű tartalom megjelenítésére lesznek korlátozva.
> 
> Ha megbízik az előugró ablakban betöltött adataiban, és esetleg szeretné, hogy az előugró ablakban betöltött parancsfájlok hozzáférjenek az alkalmazáshoz, ezt letilthatja, ha a felugró sablonok beállítás hamis értékre van állítva `sandboxContent` . 

### <a name="string-template"></a>Karakterlánc-sablon

A karakterlánc-sablon a szolgáltatás tulajdonságainak értékeivel helyettesíti a helyőrzőket. A szolgáltatás tulajdonságait nem kell karakterlánc típusú értékhez rendelni. Például `value1` egy egész számot tárol. A rendszer ezeket az értékeket továbbítja a Content (tartalom) tulajdonságának `popupTemplate` . 

Ez a `numberFormat` beállítás határozza meg a megjelenítendő szám formátumát. Ha a `numberFormat` nincs megadva, akkor a kód az előugró sablonok dátumának formátumát fogja használni. A `numberFormat` beállítás a [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) függvény használatával formázza a számokat. Nagy számok formázásához érdemes lehet a `numberFormat` [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)függvényeket használni. Az alábbi kódrészlet például a `maximumFractionDigits` tört számjegyek számának két értékre való korlátozását használja.

> [!Note]
> A karakterlánc-sablonnak csak egy módja lehet képek megjelenítésére. Először is a karakterlánc-sablonnak rendelkeznie kell egy képcímkével. A képcímkének átadott értéknek a rendszerkép URL-címének kell lennie. Ezt követően a karakterlánc-sablonnak `isImage` igaz értékre kell állítania a következőben: `HyperLinkFormatOptions` . A `isImage` beállítással megadható, hogy a hiperhivatkozás egy képhez legyen betöltve, és a hiperhivatkozás betöltődik egy képcímkébe. Ha a hiperhivatkozásra kattint, megnyílik a rendszerkép.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>PropertyInfo-sablon

A PropertyInfo sablon a szolgáltatás elérhető tulajdonságait jeleníti meg. A `label` beállítás megadja a felhasználónak megjelenítendő szöveget. Ha `label` nincs megadva, a hiperhivatkozás megjelenik. Ha pedig a hiperhivatkozás egy rendszerkép, az "ALT" címkéhez rendelt érték jelenik meg. Az `dateFormat` Megadja a dátum formátumát, és ha nincs megadva a dátumformátum, a dátum karakterláncként jelenik meg. A lehetőség `hyperlinkFormat` kattintható hivatkozásokat jelenít meg, hasonlóan a `email` választható e-mail-címek megjelenítéséhez is használható.

Mielőtt a PropertyInfo sablon megjeleníti a tulajdonságokat a végfelhasználónak, rekurzív módon ellenőrzi, hogy a tulajdonságok valóban meg vannak-e adva a szolgáltatáshoz. A stílus és a cím tulajdonságainak megjelenítését is figyelmen kívül hagyja. Például nem fog megjelenni,,, `color` `size` `anchor` `strokeOpacity` és `visibility` . Tehát ha a tulajdonságok elérési útjának ellenőrzése befejeződött a háttérben, a PropertyInfo sablon táblázatos formátumban jeleníti meg a tartalmat.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Több tartalmi sablon

A szolgáltatás a karakterlánc-sablon és a PropertyInfo-sablon együttes használatával is megjelenítheti a tartalmakat. Ebben az esetben a karakterlánc-sablon a helyőrző értékeket fehér háttérként jeleníti meg.  A és a PropertyInfo-sablon egy teljes szélességű képet jelenít meg egy táblában. A példában szereplő tulajdonságok hasonlóak az előző mintákban ismertetett tulajdonságokhoz.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Meghatározott sablon nélküli pontok

Ha az előugró sablon nincs definiálva karakterlánc-sablonként, PropertyInfo-sablonként vagy mindkettő kombinációja, akkor az alapértelmezett beállításokat használja. Ha a `title` és az `description` egyetlen hozzárendelt tulajdonság, a felugró sablon fehér hátteret, a jobb felső sarokban található bezárás gombot jeleníti meg. A kis-és közepes képernyőkön pedig az alján egy nyíl látható. Az alapértelmezett beállítások egy táblázatban jelennek meg az összes, a (z `title` ) és a (z `description` ) kivételével. Még ha az alapértelmezett beállításokra is visszaesik, az előugró sablon programozott módon is kezelhető. Például a felhasználók kikapcsolhatják a hiperhivatkozások észlelését, és az alapértelmezett beállítások továbbra is érvényesek lesznek a többi tulajdonságra.

Kattintson a pontokra a térképen a CodePen. A térképen a következő felugró sablonok mindegyike látható: karakterlánc-sablon, PropertyInfo-sablon és több tartalom sablon. A sablonok az alapértelmezett beállításokkal való megjelenítésének módját is három pont mutatja.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg Azure Maps () által <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> tollat a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Felbukkanó sablon újbóli felhasználása

A felugró ablak újrafelhasználásához hasonlóan felhasználhatja a felugró sablonokat is. Ez a megközelítés akkor lehet hasznos, ha egyszerre csak egy előugró sablont szeretne megjeleníteni, több pontra. Az előugró sablon újrafelhasználásával csökken az alkalmazás által létrehozott DOM-elemek száma, ami javítja az alkalmazás teljesítményét. A következő minta ugyanazt a felugró sablont használja három pontra. Ha bármelyikre kattint, megjelenik egy előugró ablak, amely az adott pont funkciójának tartalmát jeleníti meg.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg Azure Maps () által <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> tollat a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Felugró események

Az előugró ablakok megnyithatók, lezárhatók és áthúzhatók. Az előugró osztály olyan eseményeket biztosít, amelyek segítségével a fejlesztők reagálni tudnak ezekre az eseményekre. Az alábbi példa azt mutatja be, hogy a felhasználó mikor nyitja meg, zárja be vagy húzza az előugró ablakokat. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Felugró események" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>előugró eseményeit</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Felugró ablak](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

A következő nagyszerű cikkekből megtudhatja a kódok teljes mintáit:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML-jelölő hozzáadása](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)