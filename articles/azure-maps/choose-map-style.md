---
title: A Azure Maps webes térképkezelés stílusának módosítása
description: Megtudhatja, hogyan módosíthatja a Térkép stílusát és beállításait. Megtudhatja, hogyan adhat hozzá a Style Picker vezérlőelemet Azure Maps térképhez, hogy a felhasználók különböző stílusok között válthatnak.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 556e265cc0d1aae33823185ec98d23f191ed1694
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680028"
---
# <a name="change-the-style-of-the-map"></a>A térkép stílusának módosítása

A Térkép vezérlőelem több különböző térképi [stílust](/javascript/api/azure-maps-control/atlas.styleoptions) és [alapszintű leképezési stílust](supported-map-styles.md)támogat. A Térkép vezérlőelem inicializálásakor minden stílus beállítható. A stílusokat a Térkép vezérlőelem funkciójának használatával is megadhatja `setStyle` . Ez a cikk bemutatja, hogyan használhatja ezeket a stílusokat a Térkép megjelenésének testreszabásához. Azt is megtudhatja, hogyan valósítja meg a Style Picker vezérlőelemet a térképen. A Style Picker vezérlőelem lehetővé teszi a felhasználó számára a különböző alapstílusok közötti váltást.

## <a name="set-map-style-options"></a>Térkép stílusa beállításainak megadása

A stílus beállításai a webes vezérlők inicializálásakor állíthatók be. A stílus beállításait a Térkép vezérlőelem függvényének meghívásával is frissítheti `setStyle` . Az összes rendelkezésre álló stílus beállítás megjelenítéséhez lásd: [stílus beállításai](/javascript/api/azure-maps-control/atlas.styleoptions).

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

Az alábbi eszköz azt mutatja be, hogy a különböző stílusú beállítások Hogyan változnak a Térkép megjelenítésének módjával. Ha szeretné megtekinteni a 3D-s épületeket, a nagyítást egy nagyobb városhoz közelítheti meg.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Térkép stílusa beállításai" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Térkép stílusának beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Alapszintű Térkép stílusának beállítása

A Térkép vezérlőelemet a web SDK-ban elérhető [alaptérképi stílusok](supported-map-styles.md) egyikével is inicializálhatja. Ezután a `setStyle` függvény használatával frissítheti az alapstílust egy másik Térkép stílusa alapján.

### <a name="set-a-base-map-style-on-initialization"></a>Alapszintű Térkép stílusának beállítása inicializáláskor

A Térkép vezérlőelem alapstílusai az inicializálás során állíthatók be. A következő kódban a `style` Térkép vezérlőelem beállítása az [ `grayscale_dark` alapszintű Térkép stílusára](supported-map-styles.md#grayscale_dark)van beállítva.  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='A stílus beállítása a Térkép betöltéséhez' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a Azure Maps () alapján a CodePen-on a <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>stílus betöltésére vonatkozó beállítást</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

### <a name="update-the-base-map-style"></a>Az alapszintű Térkép stílusának frissítése

Az alapszintű Térkép stílusa a függvénnyel frissíthető, `setStyle` és beállíthatja, `style` hogy egy másik alapszintű térképi stílusra váltson, vagy további stílust adjon hozzá.

```javascript
map.setStyle({ style: 'satellite' });
```

A következő kódban a térképi példány betöltését követően a Térkép stílusa a `grayscale_dark` `satellite` [setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) függvény használatával frissül.

<br/>

<iframe height='500' scrolling='no' title='A stílus frissítése' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stílus frissítése</a> Azure Maps () használatával a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>A Style Picker vezérlőelem hozzáadása

A Style Picker Control egy könnyen használható gomb, amely a felhasználó által az alapstílusok közötti váltásra használható.

A Style Picker két különböző elrendezési lehetőséggel rendelkezik: `icon` és `list` . Emellett a Style Picker lehetővé teszi két különböző stílusú választó vezérlési lehetőség kiválasztását `style` : `light` és `dark` . Ebben a példában a Style Picker az `icon` elrendezést használja, és az alaptérképi stílusok kiválasztási listáját jeleníti meg az ikonok formájában. A Style Control választó a következő alapkészleteket tartalmazza: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . A Style Picker vezérlési lehetőségeivel kapcsolatos további információkért lásd a [stílus-vezérlési beállítások](/javascript/api/azure-maps-control/atlas.stylecontroloptions)című témakört.

Az alábbi képen az elrendezésben megjelenő Style Picker vezérlőelem látható `icon` .

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Style Picker ikon elrendezése":::

Az alábbi képen az elrendezésben megjelenő Style Picker vezérlőelem látható `list` .

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Style Picker-lista elrendezése":::

> [!IMPORTANT]
> Alapértelmezés szerint a Style Picker vezérlő felsorolja az Azure Maps S0 díjszabási szintjénél elérhető összes stílust. Ha csökkenteni szeretné a listában szereplő stílusok számát, adja át a listában megjeleníteni kívánt stílusok tömbjét a `mapStyle` Style Picker (stílus kiválasztása) lehetőséggel. Ha S1-et használ, és az összes elérhető stílust meg szeretné jeleníteni, állítsa a `mapStyles` Style Picker kapcsolót a következőre: `"all"` .

A következő kód bemutatja, hogyan bírálhatja felül az alapértelmezett `mapStyles` alapstílusok listáját. Ebben a példában a `mapStyles` Style Picker vezérlőelem által megjelenített alapstílusokat szeretnénk listázni.

<br/>

<iframe height='500' scrolling='no' title='A Style Picker hozzáadása' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Style Picker hozzáadásával</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Térképvezérlők hozzáadása](map-add-controls.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)
