---
title: Szimbólum-réteg hozzáadása az Android Maps-hez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá egy jelölőt egy térképhez. Egy olyan példát láthat, amely a Azure Maps Android SDK-t használja egy adatforrásból származó, pont-alapú adatokból álló szimbólum réteg hozzáadásához.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: edb758469a06dcb7914025ea449b9d952e939533
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097210"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Szimbólum réteg hozzáadása (Android SDK)

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a pontok adatait egy adatforrásból szimbólumként szolgáló rétegként a Azure Maps Android SDK használatával. A szimbólum-rétegek megjelenítési pontok képként és szövegként jelennek meg a térképen.

> [!TIP]
> A szimbólumok alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha úgy szeretné korlátozni a réteget, hogy csak a pont geometriai funkcióit jelenítse meg, állítsa a réteg beállítását a következőre: `filter` `eq(geometryType(), "Point")` . Ha azt szeretné, hogy a multipoint-funkciók is szerepeljenek, állítsa a réteg beállítását a következőre: `filter` `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania. A cikkben szereplő kódrészletek behelyezhetők a Maps `onReady` Event handlerbe.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Ahhoz, hogy hozzá lehessen adni egy szimbólum réteget a térképhez, néhány lépést végre kell hajtania. Először hozzon létre egy adatforrást, és adja hozzá a térképhez. Hozzon létre egy szimbólum réteget. Ezután továbbítsa az adatforrást a szimbólum rétegbe az adatoknak az adatforrásból való lekéréséhez. Végül vegyen fel egy adatforrást az adatforrásba, hogy valami legyen megjelenítve.

Az alábbi kód azt mutatja be, hogy mit kell hozzáadni a térképhez a betöltését követően. Ez a minta egy pontot jelenít meg a térképen egy szimbólum réteg használatával.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

A térképhez három különböző típusú pont-adattípust lehet hozzáadni:

- GeoJSON pont geometriája – ez az objektum csak egy pont koordinátáit tartalmazza, semmi más. A `Point.fromLngLat` statikus metódus használatával egyszerűen hozhatók létre ezek az objektumok.
- GeoJSON multipoint geometria – Ez az objektum több pont koordinátáit tartalmazza, és semmi mást nem. Adja át a pontok tömbjét az `MultiPoint` osztálynak az objektumok létrehozásához.
- GeoJSON funkció – ez az objektum bármilyen GeoJSON geometriát és olyan tulajdonságokat tartalmaz, amelyek a geometriához társított metaadatokat tartalmaznak.

További információkért lásd az [adatforrások létrehozása](create-data-source-android-sdk.md) című dokumentumot az adatok létrehozásához és a térképhez való hozzáadásához.

Az alábbi mintakód egy GeoJSON pontot hoz létre, és átadja azt a GeoJSON szolgáltatásnak, és `title` hozzáadott egy értéket a tulajdonságaihoz. A `title` tulajdonság a Térkép szimbólum ikonja fölötti szövegként jelenik meg.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Az alábbi képernyőfelvételen látható, hogy a fenti kód egy tépő egy ikon és egy szöveges címke használatával.

![Leképezés a ponttal megjelenített ponttal egy szimbólum-réteggel, amely egy adott pont funkció ikonját és szöveges címkéjét jeleníti meg](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Alapértelmezés szerint a szimbólum-rétegek az átfedésben lévő szimbólumok elrejtésével optimalizálja a szimbólumok megjelenítését. A nagyításhoz a rejtett szimbólumok láthatóvá válnak. Ha le szeretné tiltani ezt a funkciót, és az összes szimbólumot le szeretné jeleníteni, állítsa a és a beállításokat a következőre: `iconAllowOverlap` `textAllowOverlap` `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egyéni ikon hozzáadása egy szimbólum réteghez

A szimbólumok rétegei a WebGL használatával jelennek meg. Ennek megfelelően az összes erőforrást, például ikonokat be kell tölteni a WebGL-környezetbe. Ez a minta bemutatja, hogyan adhat hozzá egyéni ikont a Térkép erőforrásaihoz. Ezt az ikont ezután a rendszer a Térkép egyéni szimbólumával jeleníti meg. A `textField` szimbólum réteg tulajdonságának meg kell adni egy kifejezést. Ebben az esetben a hőmérséklet tulajdonságot szeretnénk megjeleníteni. Mivel a hőmérséklet egy szám, azt karakterlánccá kell konvertálni. Emellett szeretnénk hozzáfűzni a "°F" kifejezést. Ehhez az összefűzéshez kifejezés használható. `concat(Expression.toString(get("temperature")), literal("°F"))`.

::: zone pivot="programming-language-java-android"

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

Ebben a példában a következő rendszerkép be lett töltve az alkalmazás megrajzolható mappájába.

| ![Rain záporok ikonjának képe](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

A következő képernyőfelvételen a fenti kód látható, amely egy egyéni ikon és egy tépő használatával formázott szöveg címkét tartalmaz.

![Térkép megjelenítése az adott pont szolgáltatás egyéni ikonját és formázott szöveges címkéjét ábrázoló szimbólum-réteggel](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Ha csak szimbólum réteget szeretne megjeleníteni, elrejtheti a ikont az `iconImage` ikon beállításainak tulajdonságának beállításával `"none"` .

## <a name="modify-symbol-colors"></a>Szimbólum színének módosítása

A Azure Maps Android SDK az alapértelmezett jelölő ikonjának előre definiált színvariációit tartalmazza. Például `marker-red` átadható `iconImage` egy szimbólum rétege, amely a jelölő ikon piros verzióját jeleníti meg az adott rétegben.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

Az alábbi táblázat felsorolja az összes elérhető rendszerkép-nevet. Ezen jelölők mindegyike lekéri a színeit a felülbírálható színforrásokból. A jelölő fő kitöltési színének felülbírálása mellett. Vegye figyelembe azonban, hogy az egyik jelölő színének felülbírálása az ikont használó összes rétegre érvényes lesz.

| Ikon neve | Szín erőforrásának neve |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

Az összes jelölő szegélyének színét a színerőforrás neve alapján is felülbírálhatja `mapcontrol_marker_border` . A jelölők színei felülbírálható az alkalmazás fájljában azonos nevű szín hozzáadásával `colors.xml` . Például a következő `colors.xml` fájl az alapértelmezett jelölő színének fényes zöld színűvé tétele lenne.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

A következő az alapértelmezett jelölő vektor XML-fájljának módosított verziója, amely az alapértelmezett jelölő további egyéni verzióinak létrehozásához módosítható. A módosított verziót hozzáadhatja az `drawable` alkalmazás mappájához, és hozzáadhatja a Maps-képhez a sprite használatával `map.images.add` , majd használhatja azt egy szimbólum réteggel.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Funkcióinformációk megjelenítése](display-feature-information-android.md)
