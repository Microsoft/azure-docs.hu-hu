---
title: Adatforrások létrehozása térképhez Microsoft Azure térképeken
description: 'Ismerje meg, hogyan hozhat létre egy adatforrást térképekhez. Ismerkedjen meg a Azure Maps web SDK által használt adatforrásokkal: GeoJSON-források és vektoros csempék.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678858"
---
# <a name="create-a-data-source"></a>Adatforrás létrehozása

A Azure Maps web SDK adatforrásokban tárolja az adatforrásokat. Az adatforrások használata optimalizálja az adatműveleteket a lekérdezéshez és a megjelenítéshez. Jelenleg két típusú adatforrás létezik:

* **GeoJSON forrás**: a nyers helyadatok helyi kezelése GeoJSON formátumban. A kis-és közepes adatkészletek esetében is jó (több százezer alakzat).
* **Vektoros csempe forrása**: a térképes mozaikrendszer alapján betölti az aktuális leképezési nézethez tartozó vektorgrafikus csempéket. Ideális nagy-és nagyméretű adatkészletekhez (millió vagy több milliárd alakzat).

## <a name="geojson-data-source"></a>GeoJSON-adatforrás

A GeoJSON-alapú adatforrás az osztály használatával helyileg tölti be és tárolja az adattárolást `DataSource` . A GeoJSON adatai manuálisan hozhatók létre vagy hozhatók létre az [Atlas.](/javascript/api/azure-maps-control/atlas.data) adatnévtérben található segítő osztályok használatával. Az `DataSource` osztály a helyi vagy távoli GeoJSON-fájlok importálására szolgáló függvényeket biztosít. A távoli GeoJSON-fájlokat egy CORs-kompatibilis végponton kell tárolni. Az `DataSource` osztály a fürtszolgáltatási pontokra vonatkozó adatgyűjtési funkciókat biztosít. Az és az adatkezelési szolgáltatással egyszerűen hozzáadhatók, eltávolíthatók és frissíthetők az `DataSource` osztályok. A következő kód azt mutatja be, hogyan hozhatók létre GeoJSON-adatkészletek Azure Mapsban.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

A létrehozás után az adatforrások hozzáadhatók a térképhez a `map.sources` tulajdonságon keresztül, amely egy [SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager). A következő kód bemutatja, hogyan hozhat létre `DataSource` és adhat hozzá a térképhez.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

A következő kód az GeoJSON különböző módjait mutatja be a-ben `DataSource` .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> Tegyük fel, hogy az a összes adatértékét felül szeretné írni `DataSource` . Ha a `clear` then függvényeket hívja meg `add` , akkor a Térkép kétszer is elvégezhető, ami egy kis késleltetést eredményezhet. Ehelyett használja a `setShapes` függvényt, amely eltávolítja és lecseréli az adatforrásban lévő összes adatát, és csak a Térkép egyetlen újbóli megjelenítését indítja el.

## <a name="vector-tile-source"></a>Vektoros csempe forrása

A vektoros csempék forrása leírja, hogyan lehet hozzáférni a vektoros csempék rétegéhez. A [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) osztály használatával hozza létre a vektoros csempe forrását. A vektoros csempe rétegei hasonlóak a csempék rétegeihez, de nem azonosak. A csempe réteg egy raszteres rendszerkép. A vektoros csempe rétegek a **PBF** formátumában tömörített fájlok. Ez a tömörített fájl vektoros leképezési és egy vagy több réteget tartalmaz. A fájl az egyes rétegek stílusa alapján megjeleníthető és stílusú lehet az ügyfélen. A vektoros csempén lévő információk pontok, vonalak és sokszögek formájában található földrajzi funkciókat tartalmaznak. A raszteres csempe rétegei helyett több előnye van a vektoros csempék használatának:

* A vektoros csempék fájlmérete általában sokkal kisebb, mint egy egyenértékű raszteres csempe. Így kevesebb sávszélesség van használatban. Kisebb késést, gyorsabb térképet és jobb felhasználói élményt jelent.
* Mivel a rendszer a vektoros csempéket jeleníti meg az ügyfélen, alkalmazkodik a megjelenő eszköz feloldásához. Ennek eredményeképpen a megjelenített térképek jól definiálva jelennek meg, a Crystal Clear címkékkel.
* A vektoros leképezésekben lévő adatstílus módosítása nem igényli újra az adatletöltést, mivel az új stílus alkalmazható az ügyfélen. Ezzel szemben a raszteres csempék rétegének módosítása általában a-kiszolgálóról származó csempék betöltését igényli, majd az új stílust alkalmazza.
* Mivel az adat vektoros formában lett továbbítva, az adatelőkészítéshez kevesebb kiszolgálóoldali feldolgozás szükséges. Ennek eredményeképpen az újabb adatértékek gyorsabban elérhetővé tehetők.

Azure Maps betartja a [Mapbox Vector csempe specifikációját](https://github.com/mapbox/vector-tile-spec), amely egy nyílt szabvány. Azure Maps a következő vektoros szolgáltatásokat nyújtja a platform részeként:

- A Road csempe [dokumentációjának](/rest/api/maps/renderv2/getmaptilepreview)  |  [adatformátuma – részletek](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Forgalmi incidensek [dokumentációjának](/rest/api/maps/traffic/gettrafficincidenttile)  |  [adatformátuma – részletek](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- A forgalmi folyamat [dokumentációjának](/rest/api/maps/traffic/gettrafficflowtile)  |  [adatformátumának részletei](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- A Azure Maps Creator (előzetes verzió) lehetővé teszi az egyéni vektoros csempék létrehozását és elérését a [csempe Render v2](/rest/api/maps/renderv2/getmaptilepreview) használatával.

> [!TIP]
> Ha vektoros vagy raszteres képcsempéket használ a Azure Maps Render szolgáltatásból a web SDK-val, a `atlas.microsoft.com` helyőrzőre cserélheti `{azMapsDomain}` . Ezt a helyőrzőt a Térkép ugyanazokkal a tartománnyal helyettesíti, és a rendszer automatikusan hozzáfűzi ugyanazokat a hitelesítési adatokat is. Ez nagymértékben leegyszerűsíti a Azure Active Directory hitelesítés használatakor a renderelési szolgáltatással történő hitelesítést.

Ha a térképen a vektoros csempe forrásának adatait szeretné megjeleníteni, a forrást csatlakoztathatja az egyik adatmegjelenítési réteghez. A vektoros forrást használó összes rétegnek meg kell adnia egy `sourceLayer` értéket a beállításokban. A következő kód betölti a Azure Maps Traffic flow Vector csempe szolgáltatást vektoros csempe-forrásként, majd a térképen egy vonal réteget használva jeleníti meg. Ez a vektoros csempés forrás egyetlen adatkészlettel rendelkezik, amely a "forgalom flow" nevű rétegben található. Ebben az adatkészletben az adathalmazban található sorokra vonatkozó tulajdonság egy nevű tulajdonsággal rendelkezik, `traffic_level` amely a kód kiválasztásához és a sorok méretének méretezéséhez használatos.

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vektor csempe vonalának rétege" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>Vector csempe vonal réteget</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Adatforrás csatlakoztatása réteghez

Az adatmegjelenítés a térképen renderelési rétegek használatával történik. Egyetlen adatforrás hivatkozhat egy vagy több renderelési rétegre. A következő megjelenítési rétegekhez adatforrásra van szükség:

* [Buborék réteg](map-add-bubble-layer.md) – a térképre méretezett körökként jeleníti meg a pontok mennyiségét.
* [Szimbólum réteg](map-add-pin.md) – megjelenítheti az adatpontokat ikonként vagy szövegként.
* [Hő-Térkép réteg](map-add-heat-map-layer.md) – megjeleníti az adatpontok sűrűségének hő-térképét.
* [Vonal réteg](map-add-shape.md) – vonalak renderelése és a sokszögek körvonalának megjelenítése. 
* [Sokszög réteg](map-add-shape.md) – egy sokszög területének kitöltése folytonos színnel vagy képmintázattal.

Az alábbi kód bemutatja, hogyan hozhat létre egy adatforrást, hogyan adhatja hozzá a térképhez, és hogyan csatlakoztathatja azt egy buborék-réteghez. Ezután importálja a GeoJSON pont adatait egy távoli helyről az adatforrásba. 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Vannak olyan renderelési rétegek, amelyek nem csatlakoznak ezekhez az adatforrásokhoz, de közvetlenül betöltik a rendereléshez szükséges adatkereteket. 

* [Képréteg](map-add-image-layer.md) – egyetlen rendszerképet fedi le a térképen, és a sarkokat megadott koordináták halmazához köti.
* [Csempe réteg](map-add-tile-layer.md) – a Térkép tetején lévő raszteres csempe réteget rendeli.

## <a name="one-data-source-with-multiple-layers"></a>Egy adatforrás több réteggel

Több réteg is csatlakoztatható egyetlen adatforráshoz. Számos különböző forgatókönyv létezik, amelyben ez a lehetőség hasznos lehet. Vegyük például azt a forgatókönyvet, amelyben a felhasználó sokszöget rajzol. A sokszög területének megjelenítéséhez és kitöltéséhez a felhasználó hozzáadja a pontokat a térképhez. Ha egy stílusú sort ad hozzá a sokszög körvonalához, az megkönnyíti a sokszög széleinek megjelenítését a felhasználó által rajzolt módon. A sokszögben lévő egyes helyzetek megfelelő szerkesztéséhez hozzáadhatunk egy fogantyút, például egy PIN-kódot vagy egy jelölőt az egyes pozíciók fölött.

![Több réteget ábrázoló Térkép egyetlen adatforrásból származó adatok megjelenítéséhez](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

A legtöbb leképezési platform esetében szükség van egy sokszög-objektumra, egy sor objektumra és egy PIN-kódra a sokszög minden egyes pozíciójában. Ahogy a sokszög módosítva lett, manuálisan kell frissítenie a vonalat és a PIN-ket, ami gyorsan összetett lehet.

A Azure Maps esetében mindössze egyetlen sokszögre van szükség az adatforrásban az alábbi kódban látható módon.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> Ha a függvény használatával rétegeket ad hozzá a térképhez `map.layers.add` , a meglévő réteg azonosítóját vagy példányát második paraméterként is átadhatja. Ez azt jelzi, hogy a Térkép beszúrja a meglévő réteg alá felvett új réteget. A rétegbeli AZONOSÍTÓk átadása mellett a metódus a következő értékeket is támogatja.
>
> * `"labels"` – Beszúrja az új réteget a Térkép feliratának rétegeibe.
> * `"transit"` – Beszúrja az új réteget a térképi út és az átviteli rétegek alá.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](map-add-popup.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Hő-Térkép hozzáadása](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kódminták](/samples/browse/?products=azure-maps)