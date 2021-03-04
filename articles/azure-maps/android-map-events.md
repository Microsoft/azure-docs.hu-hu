---
title: Leképezési események kezelése Android-térképeken | Microsoft Azure térképek
description: Megtudhatja, hogy mely események legyenek kirúgva, amikor a felhasználók a Maps szolgáltatással működnek Az összes támogatott térképi esemény listájának megtekintése. Tekintse meg, hogyan kezelheti az eseményeket az Azure Maps Android SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 86d1b9ec8a507a5cfaa5502efcb239bceabca665
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097346"
---
# <a name="interact-with-the-map-android-sdk"></a>Együttműködés a térképpel (Android SDK)

Ez a cikk bemutatja, hogyan használhatja a Maps Events Managert.

## <a name="interact-with-the-map"></a>A térképpel való interakció

A Térkép az összes eseményt a `events` tulajdonságán keresztül kezeli. A következő táblázat felsorolja az összes támogatott Térkép-eseményt.

| Esemény                  | Eseménykezelő formátuma | Leírás |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Az utolsó olyan keret után következik be, amely után a Térkép "tétlen" állapotba kerül:<ul><li>Nincs folyamatban kamera-átmenet.</li><li>A jelenleg kért csempék betöltődik.</li><li>Minden áttűnési/átmenetes animáció befejeződött.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Az egyik nézetről a másikra történő animált áttérés során többször is elindítható a felhasználói interakció vagy módszerek eredményeképpen. |
| `OnCameraMoveCanceled` | `()`                 | A kamera felé irányuló továbbítási kérelem megszakítva. |
| `OnCameraMoveStarted`  | `(int reason)`       | Közvetlenül a Térkép megkezdése előtt az egyik nézetről a másikra való áttérést a felhasználói interakció vagy módszerek eredményeképpen lehet megkezdeni. Az `reason` esemény-figyelő argumentuma egész értéket ad vissza, amely részletesen ismerteti a kamera mozgásának módját. Az alábbi lista a lehetséges okokat ismerteti:<ul><li>1: kézmozdulat</li><li>2: fejlesztői animáció</li><li>3: API-animáció</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | A Térkép megnyomásakor és a térképen való kikapcsolásakor a rendszer kilőtte a térképet. |
| `OnFeatureClick`       | `(List<Feature>)`    | A rendszer a Térkép megnyomásakor és a Szolgáltatáson megjelenő pontokon való kiadása után felgyújtotta.  |
| `OnLayerAdded` | `(Layer layer)` | Egy réteg a térképhez való hozzáadásakor aktiválódik. |
| `OnLayerRemoved` | `(Layer layer)` | Akkor aktiválódik, amikor eltávolít egy réteget a térképről. |
| `OnLoaded` | `()` | Rögtön az összes szükséges erőforrás letöltését követően, a Térkép első vizuálisan teljes renderelése történt. |
| `OnLongClick`          | `(double lat, double lon)` | A Térkép lenyomásakor, egy pillanatra, majd a térképen megjelenő ponton jelenik meg. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | A rendszer a Térkép lenyomásakor, egy pillanatra, majd a szolgáltatás ugyanazon pontján jelenik meg. |
| `OnReady`              | `(AzureMap map)`     | Akkor következik be, amikor a Térkép betöltődik, vagy ha az alkalmazás tájolásának változása megváltozik, és a minimálisan szükséges leképezési erőforrások betöltődik, és a Térkép készen áll a programozással való együttműködésre. |
| `OnSourceAdded` | `(Source source)` | Egy `DataSource` vagy `VectorTileSource` a térképhez való hozzáadásakor aktiválódik. |
| `OnSourceRemoved` | `(Source source)` | A rendszer kilőtte a `DataSource` `VectorTileSource` térképet, vagy eltávolítják azt. |
| `OnStyleChange` | `()` | Akkor következik be, amikor a Térkép stílusa betöltődik vagy módosul. |

A következő kód bemutatja, hogyan adhatja hozzá a `OnClick` , `OnFeatureClick` és az `OnCameraMove` eseményeket a térképhez.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

További információ: [Navigálás a Térkép](how-to-use-android-map-control-library.md#navigating-the-map) dokumentációjában, hogyan lehet kommunikálni a Térkép és a trigger események használatával.

## <a name="scope-feature-events-to-layer"></a>Hatókör-szolgáltatási események rétegbe

Amikor hozzáadja a `OnFeatureClick` vagy az `OnLongFeatureClick` eseményeket a térképhez, a rétegbeli példány vagy a réteg azonosítója második paraméterként adható át. Egy réteg átadásakor az esemény csak akkor indul el, ha az esemény az adott rétegen következik be. A rétegekre hatókörrel rendelkező eseményeket a szimbólum, a buborék, a vonal és a sokszög rétegek támogatják.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Navigálás a térképen](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
