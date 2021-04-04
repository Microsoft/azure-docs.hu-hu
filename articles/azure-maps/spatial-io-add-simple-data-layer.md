---
title: Egyszerű adatréteg hozzáadása | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá egy egyszerű adatréteget a Azure Maps web SDK által biztosított térbeli IO-modul használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896157"
---
# <a name="add-a-simple-data-layer"></a>Egyszerű adatréteg hozzáadása

A térbeli IO-modul egy `SimpleDataLayer` osztályt biztosít. Ez az osztály megkönnyíti a Térkép stílusú funkcióinak megjelenítését. Akár olyan adatkészleteket is tartalmazhat, amelyek stílus tulajdonságokkal és adatkészletekkel rendelkeznek, amelyek vegyes geometriai típusokat tartalmaznak. Az egyszerű adatréteg ezt a funkciót több renderelési réteg körbefuttatásával és a stílus kifejezések használatával éri el. A stílus kifejezések a becsomagolt rétegekben található szolgáltatások általános stílusának tulajdonságait keresik. A `atlas.io.read` függvény és a `atlas.io.write` függvény ezeket a tulajdonságokat használja a stílusok egy támogatott fájlformátumba való olvasására és írására. A tulajdonságok támogatott fájlformátumhoz való hozzáadása után a fájl különböző célokra használható. A fájl használható például a Térkép stílusú funkcióinak megjelenítésére.

A Styling funkciói mellett a a `SimpleDataLayer` beépített előugró funkciókat is tartalmaz egy előugró sablonnal. Az előugró ablak akkor jelenik meg, ha egy szolgáltatásra kattint. Ha szükséges, az alapértelmezett előugró funkció le is tiltható. Ez a réteg támogatja a fürtözött adattárolást is. Ha egy fürtre kattint, a Térkép nagyítja a fürtöt, és kibontja az egyes pontokra és alfürtökre.

Az `SimpleDataLayer` osztály olyan nagyméretű adathalmazokon használható, amelyek számos geometriai típussal rendelkeznek, és számos stílust alkalmaznak a szolgáltatásokra. Ez az osztály felhasználja a Style kifejezéseket tartalmazó hat rétegből álló terhelést. Ezért vannak olyan esetek, amikor hatékonyabban használják az alapvető renderelési rétegeket. Például egy alapszintű réteg használatával megjelenítheti a különböző geometriai típusokat és néhány stílust a szolgáltatásban

## <a name="use-a-simple-data-layer"></a>Egyszerű adatréteg használata

Az `SimpleDataLayer` osztály a többi renderelési réteghez hasonlóan használatos. Az alábbi kód bemutatja, hogyan használható egy egyszerű adatréteg a térképen:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Szolgáltatások hozzáadása az adatforráshoz. Ezután az egyszerű adatrétegből megtudhatja, hogyan jeleníti meg a legjobb funkciókat. Az egyes funkciók stílusa a funkció tulajdonságaiként állítható be. A következő kód egy GeoJSON pont funkciót mutat be, amely a `color` tulajdonságra van beállítva `red` . 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

A következő kód a fenti pont funkciót jeleníti meg az egyszerű adatréteg használatával. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Az egyszerű adatréteg használata" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Lásd a tollat <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>az egyszerű adatréteget</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az egyszerű adatréteg valódi ereje a következőket tartalmazhatja:

- Az adatforrás számos különböző típusú funkciót tartalmaz; vagy
- Az adathalmaz funkciói több stílust is magukban foglalnak. vagy
- Nem biztos benne, hogy pontosan mit tartalmaz az adathalmaz.

Például az XML-adatcsatornák elemzésekor előfordulhat, hogy nem ismeri a funkciók pontos stílusait és geometriai típusait. A következő minta az egyszerű adatréteg erejét mutatja be egy KML-fájl funkcióinak megjelenítésével. Emellett az egyszerű adatréteg-osztály különböző lehetőségeit is bemutatja.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Az adatrétegek egyszerű beállításai" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>egyszerű adatrétegének beállításait</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Ez az egyszerű adatréteg a [felugró sablon](map-add-popup.md#add-popup-templates-to-the-map) osztály segítségével jeleníti meg a KML-buborékokat vagy a funkciók tulajdonságait táblázatként. Alapértelmezés szerint az előugró ablakban megjelenített összes tartalom egy iframe-kereten belül, biztonsági szolgáltatásként fog megjelenni. Vannak azonban korlátozások:
>
> - A rendszer letiltja az összes parancsfájlt, űrlapot, mutató zárolást és felső navigációs funkciót. A hivatkozások egy új lapon megnyithatók, amikor rákattintanak. 
> - Azok a régebbi böngészők, amelyek nem támogatják a `srcdoc` paramétert az IFRAME-eszközökön, csak kis mennyiségű tartalom megjelenítésére lesznek korlátozva.
> 
> Ha megbízik az előugró ablakban betöltött adataiban, és esetleg szeretné, hogy az előugró ablakban betöltött parancsfájlok hozzáférjenek az alkalmazáshoz, ezt letilthatja, ha a felugró sablonok beállítás hamis értékre van állítva `sandboxContent` . 

## <a name="default-supported-style-properties"></a>Alapértelmezett támogatott stílus tulajdonságai

Ahogy azt korábban említettük, az egyszerű adatréteg több alapvető renderelési réteget is betakar: buborék, szimbólum, vonal, sokszög és extrudált sokszög. Ezután kifejezések használatával keres érvényes stílusú tulajdonságokat az egyes szolgáltatásokban.

A Azure Maps és a GitHub stílusú tulajdonságok a támogatott tulajdonságok két fő halmaza. A különböző Azure Maps-rétegbeli beállítások többsége az egyszerű adatrétegben található szolgáltatások stílusának tulajdonságaiként támogatott. Bizonyos rétegbeli beállításokhoz kifejezések lettek hozzáadva, amelyek támogatják a GitHub által leggyakrabban használt stílusú tulajdonságokat. Ezeket a tulajdonságokat a [GitHub GeoJSON-leképezési támogatása](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)határozza meg, és a platformon belül tárolt és megjelenített GeoJSON-fájlok stílusához használatosak. A GitHub összes stílusának tulajdonsága támogatott az egyszerű adatrétegben, a `marker-symbol` stílus tulajdonságai kivételével.

Ha az olvasó egy kevésbé gyakori Style tulajdonságon keresztül érkezik, a rendszer a legközelebbi Azure Maps Style tulajdonságra konvertálja. Emellett az alapértelmezett stílusú kifejezések felülbírálása az `getLayers` egyszerű adatréteg funkciójának használatával és az egyes rétegek beállításainak frissítésével végezhető el.

A következő szakaszokban részletesen ismertetjük az egyszerű adatréteg által támogatott alapértelmezett stílusú tulajdonságokat. A támogatott tulajdonságnév sorrendje szintén a tulajdonság prioritása. Ha ugyanahhoz a réteghez két stílus van definiálva, akkor a lista első elemének magasabb a prioritása. A színek bármely CSS3 színértéket tartalmazhatnak; HEXADECIMÁLIS, RGB, RGBA, HSL, HSLA vagy named Color érték.

### <a name="bubble-layer-style-properties"></a>Buborék réteg stílusa tulajdonságai

Ha a szolgáltatás egy `Point` vagy a `MultiPoint` , és a szolgáltatás nem rendelkezik olyan `image` tulajdonsággal, amelyet egyéni ikonként kíván használni a pont szimbólumként való megjelenítéséhez, akkor a funkció a következővel lesz megjelenítve: `BubbleLayer` .

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] a `size` és az `scale` értékek skaláris értéknek minősülnek, és megszorozzák őket `8`

\[2 \] Ha a GitHub `marker-size` beállítás meg van adva, a rendszer a következő értékeket fogja használni a RADIUS-hez.

| Jelölő mérete | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

A fürtöket a buborék réteg is megjeleníti. Alapértelmezés szerint a fürt sugara a következőre van beállítva: `16` . A fürt színe a fürtben lévő pontok számától függ, az alábbiakban meghatározottak szerint:

| pontok száma | Szín    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Szimbólum stílusa tulajdonságai

Ha egy szolgáltatás egy `Point` vagy a `MultiPoint` , és a szolgáltatás, és rendelkezik egy `image` tulajdonsággal, amelyet egyéni ikonként használhat a pont szimbólumként való megjelenítéséhez, akkor a funkció a következővel lesz megjelenítve: `SymbolLayer` .

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] Ha a GitHub `marker-size` beállítás meg van adva, akkor az ikon mérete beállításnál a következő értékeket fogja használni a rendszer.

| Jelölő mérete | Szimbólumméret |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Ha a pont funkció egy fürt, a `point_count_abbreviated` tulajdonság szövegként jelenik meg. Egyetlen rendszerkép sem jelenik meg.

### <a name="line-style-properties"></a>Vonalstílus tulajdonságai

Ha a szolgáltatás egy,, vagy rendszerű `LineString` `MultiLineString` `Polygon` `MultiPolygon` , akkor a funkció a-val lesz megjelenítve `LineLayer` .

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Sokszög stílusa tulajdonságai

Ha a funkció a `Polygon` vagy a `MultiPolygon` , és a szolgáltatás nem rendelkezik `height` tulajdonsággal, vagy a `height` tulajdonság nulla, akkor a funkció a-val lesz megjelenítve `PolygonLayer` .

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Extrudált sokszög stílusának tulajdonságai

Ha a funkció egy `Polygon` vagy a `MultiPolygon` , és a `height` tulajdonság értéke 0-nál nagyobb, akkor a szolgáltatás egy értékkel jelenik meg `PolygonExtrusionLayer` .

| Réteg beállítása | Támogatott tulajdonságok neve (i) | Alapértelmezett érték |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Térbeli adatok beolvasása és írása](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC-térképréteg hozzáadása](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Kapcsolódás WFS-szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alapvető műveletek előnyeinek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátumokra vonatkozó részletek](spatial-io-supported-data-format-details.md)