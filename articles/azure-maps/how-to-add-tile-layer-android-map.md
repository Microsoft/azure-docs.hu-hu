---
title: Csempe réteg hozzáadása Android-térképekhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá egy csempe réteget egy térképhez. Egy olyan példát láthat, amely a Azure Maps Android SDK-t használja egy időjárási radar átfedésének egy térképhez való hozzáadásához.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608983"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Csempe réteg hozzáadása térképhez (Android SDK)

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a csempéket a térképeken a Azure Maps Android SDK használatával. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. Azure Maps csempe rendszerével kapcsolatos további információkért tekintse meg a [nagyítási szintek és a csempék rácsának](zoom-levels-and-tile-grid.md) dokumentációját.

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a képeket előre megjelenítheti és tárolhatja, mint bármely más rendszerkép egy kiszolgálón, a csempe rétegének értelmezési elnevezési konvenció használatával. Ezeket a képeket olyan dinamikus szolgáltatással is megjelenítheti, amely a képeket közel valós időben hozza létre. Az Azure Maps TileLayer osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat:

* X, Y, nagyítási jelölés – a nagyítási szint alapján az x az oszlop, az Y pedig a csempén lévő csempe sor pozíciója.
* Quadkey jelölés – x, y és nagyítási információ egyetlen karakterlánc-értékre, amely egy csempe egyedi azonosítója.
* A határoló mezőhöz kötött koordinátákat a rendszer a képformátum megadására használhatja `{west},{south},{east},{north}` , amelyet általában a [webes leképezési szolgáltatások (WMS)](https://www.opengeospatial.org/standards/wms)használnak.

> [!TIP]
> A TileLayer nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, de a vektoros adatok csempe rétegként is megjeleníthető. A vektoros adattároló rétegként való megjelenítésével a Térkép vezérlőelem csak a csempék betöltését igényli, ami sokkal kisebb lehet a fájlméretnél, mint az általuk képviselt adatmennyiség. Ezt a technikát sokan használják, akiknek több millió sornyi adatsort kell megjeleníteniük a térképen.

A csempe rétegbe átadott csempe URL-címének HTTP/HTTPS URL-címnek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonhoz, amely a következő paramétereket használja:

* `{x}` -A csempe X pozíciója `{y}`A és a is szükséges `{z}` .
* `{y}` -A csempe Y pozíciója `{x}`A és a is szükséges `{z}` .
* `{z}` – A csempe nagyítási szintje `{x}`A és a is szükséges `{y}` .
* `{quadkey}` – Csempe quadkey-azonosítója a Bing Maps csempe rendszerelnevezési konvenciója alapján.
* `{bbox-epsg-3857}` – Egy határolókeret karakterlánca, amely a `{west},{south},{east},{north}` EPSG 3857 térbeli hivatkozási rendszer formátumát jelöli.
* `{subdomain}` – A altartomány értékeinek helyőrzője, ha meg van adva az altartomány értéke.
* `azmapsdomain.invalid` – A Térkép által használt értékekkel összehangolja a tartományt és a csempe-kérelmek hitelesítését. Akkor használja, ha Azure Maps által üzemeltetett csempe-szolgáltatást hív meg.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő folyamat elvégzéséhez telepítenie kell [Azure Maps Android SDK](how-to-use-android-map-control-library.md) -t egy Térkép betöltéséhez.

## <a name="add-a-tile-layer-to-the-map"></a>Csempe réteg hozzáadása a térképhez

Ez a minta bemutatja, hogyan hozhat létre csempéket tartalmazó csempe réteget. Ez a példa az "x, y, zoom" csempe rendszerét használja. Ennek a csempének a forrása a [OpenSeaMap projekt](https://openseamap.org/index.php), amely a tömegből származó tengeri diagramokat tartalmazza. A csempék rétegeinek megtekintésekor érdemes lehet tisztán látni a térképen található városok címkéit. Ez a viselkedés úgy érhető el, ha beszúrja a csempe réteget a Térkép feliratának rétegeibe.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Az alábbi képernyőfelvételen a fenti kód látható, amely egy sötét szürkeárnyalatos stílusú térképen jeleníti meg a hajózási adatok csempe rétegét.

![Az Android-Térkép csempét megjelenítő réteg](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC webes leképezési szolgáltatás (WMS) hozzáadása

A webes leképezési szolgáltatás (WMTS) egy Nyílt térinformatikai konzorcium (OGC) szabvány a térképes adatképek kiszolgálásához. Ebben a formátumban számos nyitott adatkészletet lehet használni, amelyeket az Azure Maps használatával használhat. Ez a típusú szolgáltatás használható csempe réteggel, ha a szolgáltatás támogatja a `EPSG:3857` koordináta-hivatkozási rendszer (CRS) használatát. A WMS szolgáltatás használatakor a szélességi és a magassági paramétereket a szolgáltatás által támogatott értékre állítsa be, ügyeljen arra, hogy ugyanazt az értéket adja meg a `tileSize` beállításban. A formázott URL-címben állítsa be a `BBOX` szolgáltatás paraméterét a `{bbox-epsg-3857}` helyőrzővel.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

Az alábbi képernyőfelvételen a fenti kód látható, amely a Földtani adatok webes leképezési szolgáltatását fedi le az [Egyesült államokbeli geológiai felmérésből (USGS)](https://mrdata.usgs.gov/) , a címkék alatt.

![A WMS csempe réteget megjelenítő Android-Térkép](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC web-Mapping csempe szolgáltatás (WMTS) hozzáadása

A web-Mapping csempe szolgáltatás (WMTS) egy Nyílt térinformatikai konzorcium (OGC) szabvány, amely csempe alapú átfedéseket szolgál a Maps-hez. Ebben a formátumban számos nyitott adatkészletet lehet használni, amelyeket az Azure Maps használatával használhat. Ez a típusú szolgáltatás használható csempe réteggel, ha a szolgáltatás támogatja a `EPSG:3857` vagy `GoogleMapsCompatible` koordináta hivatkozási rendszerét (CRS). WMTS szolgáltatás használata esetén a szélességi és a magassági paramétereket a szolgáltatás által támogatott értékre állítsa be, ügyeljen arra, hogy ugyanazt az értéket adja meg a `tileSize` beállításban. A formázott URL-címben cserélje le a következő helyőrzőket ennek megfelelően:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

Az alábbi képernyőfelvételen látható, hogy a fenti kód egy webes leképezési csempe-szolgáltatást ábrázol, amely az [Egyesült államokbeli geológiai felmérés (USGS) országos térképe](https://viewer.nationalmap.gov/services/) , amely az utak és a címkék alatt található.

![Az Android-Térkép WMTS csempe-rétegét jeleníti meg](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkből megtudhatja, hogyan jelenítheti meg a képek ábrázolását a térképeken.

> [!div class="nextstepaction"]
> [Képréteg](map-add-image-layer-android.md)
