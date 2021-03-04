---
title: Csempe réteg hozzáadása Android-térképekhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá egy csempe réteget egy térképhez. Egy olyan példát láthat, amely a Azure Maps Android SDK-t használja egy időjárási radar átfedésének egy térképhez való hozzáadásához.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 6a920dc222cae4aedd77b667644de317637bbb69
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047502"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Csempe réteg hozzáadása térképhez (Android SDK)

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a csempéket a térképeken a Azure Maps Android SDK használatával. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. Azure Maps csempe rendszerével kapcsolatos további információkért tekintse meg a [nagyítási szintek és a csempék rácsának](zoom-levels-and-tile-grid.md) dokumentációját.

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a képeket előre megjelenítheti és tárolhatja, mint bármely más rendszerkép egy kiszolgálón, a csempe rétegének értelmezési elnevezési konvenció használatával. Ezeket a képeket olyan dinamikus szolgáltatással is megjelenítheti, amely a képeket közel valós időben hozza létre. Az Azure Maps TileLayer osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat:

* X, Y, nagyítási jelölés – a nagyítási szint alapján az x az oszlop, az Y pedig a csempén lévő csempe sor pozíciója.
* Quadkey jelölés – x, y és nagyítási információ egyetlen karakterlánc-értékre, amely egy csempe egyedi azonosítója.
* A határoló mezőhöz kötött koordinátákkal megadható, hogy milyen formátumú képet kell megadni `{west},{south},{east},{north}` , amelyet általában a [web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms)használ.

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

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkből megtudhatja, hogyan jelenítheti meg a képek ábrázolását a térképeken.

> [!div class="nextstepaction"]
> [Képréteg](map-add-image-layer-android.md)
