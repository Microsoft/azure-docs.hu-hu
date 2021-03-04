---
title: Heat Map-réteg hozzáadása Android-térképekhez | Microsoft Azure térképek
description: Megtudhatja, hogyan hozhat létre hő-térképet. Tekintse meg, hogyan adhat hozzá egy Heat Map-réteget egy térképhez az Azure MapsAndroid SDK használatával. Megtudhatja, hogyan szabhatja testre a Heat Térkép rétegeit.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100185"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Heat Map-réteg hozzáadása (Android SDK)

A Heat Maps (más néven a pont sűrűsége) az adatvizualizáció típusa. A rendszer az adatok sűrűségét ábrázolja egy színtartományon belül, és a "gyors elérésű pontok" megjelenítését jeleníti meg a térképen. A Heat Maps nagyszerű módja a nagy mennyiségű pontot tartalmazó adatkészletek megjelenítésének. 

Több tízezer pont megjelenítésével a szimbólumok a legtöbb térképi területre vonatkozhatnak. Ebben az esetben valószínű, hogy számos szimbólum átfedésben van. Megnehezíti az információk jobb megismerését. Ugyanakkor ha ugyanezt az adatkészletet szeretné megjeleníteni, a hő-térkép segítségével könnyen megtekintheti az egyes adatpontok sűrűségét és relatív sűrűségét.

A Heat Maps számos különböző forgatókönyvben használható, beleértve a következőket:

- **Hőmérsékleti információ**: a két adatpont közötti hőmérséklet-érték közelítését biztosítja.
- A **zaj-érzékelők adatai**: nem csak az érzékelőt tartalmazó zaj erősségét mutatja, de a távolságon keresztül is biztosítható az elszóródás. Előfordulhat, hogy a zajszint egy adott helyen nem magas. Ha a zaj lefedettségi területe több érzékelővel átfedésben van, lehetséges, hogy ez az átfedési terület magasabb zajszintet eredményezhet. Így az átfedésben lévő terület látható lesz a Heat térképen.
- **GPS-nyomkövetés**: magában foglalja a sebességét súlyozott magassági térképként, ahol az egyes adatpontok intenzitása a sebességen alapul. Ez a funkció például lehetővé teszi, hogy megtekintse a jármű sebességének helyét.

> [!TIP]
> Az adatforrásban lévő összes geometriá koordinátáit alapértelmezés szerint a térképi rétegek rétege jeleníti meg. Ha úgy szeretné korlátozni a réteget, hogy csak a pont geometriai funkcióit jelenítse meg, állítsa a réteg beállítását a következőre: `filter` `eq(geometryType(), "Point")` . Ha azt szeretné, hogy a multipoint-funkciók is szerepeljenek, állítsa a réteg beállítását a következőre: `filter` `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania. A cikkben szereplő kódrészletek behelyezhetők a Maps `onReady` Event handlerbe.

## <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Ha a pontok egy adatforrását szeretné megjeleníteni hő-hozzárendelésként, adja át az adatforrást az osztály egy példányának `HeatMapLayer` , és adja hozzá a térképhez.

A következő mintakód betölt egy GeoJSON-hírcsatornát az elmúlt héten, és hő-térképként jeleníti meg őket. Az egyes adatpontok minden nagyítási szinten 10 képpontos sugarat jelenítenek meg. A jobb felhasználói élmény érdekében a hő Térkép a címke réteg alatt van, így a feliratok jól láthatók maradnak. Az ebben a példában szereplő adatok az [USGS földrengés veszélyei programból](https://earthquake.usgs.gov/)származnak. Ez a minta a webes adatok GeoJSON tölti be az adatforrás [létrehozása](create-data-source-android-sdk.md) dokumentumban megadott adatimportálási segédprogram kódjának blokkját használva.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

Az alábbi képernyőfelvételen egy Térkép betöltését láthatja a fenti kóddal.

![Térkép a legutóbbi földrengések Heat Map-rétegével](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>A Heat Map-réteg testreszabása

Az előző példában a sugár-és opacitási beállítások megadásával testre szabta a Heat térképet. A Heat Map réteg számos testreszabási lehetőséget kínál, többek között a következőket:

- `heatmapRadius`: Az egyes adatpontok megjelenítéséhez használandó képpont sugarat határozza meg. A sugarat rögzített számként vagy kifejezésként is beállíthatja. Egy kifejezés használatával méretezheti a sugarat a nagyítási szint alapján, és a térképen egy konzisztens térbeli terület (például egy 5 mérföldes sugár) látható.
- `heatmapColor`: Megadja, hogyan legyen színezetve a Heat map. A színátmenetek a Heat Maps gyakori funkciói. A hatás egy kifejezéssel valósítható meg `interpolate` . Használhat egy kifejezést is a `step` Heat Térkép színezéséhez, a sűrűséget pedig vizuálisan kibontva olyan tartományokra, amelyek egy kontúr vagy egy radar stílusú térképhez hasonlítanak. Ezek a színpalettaek a minimális és a maximális sűrűség értékének színét határozzák meg.

  A Heat Maps színértékeit kifejezésként adja meg az `heatmapDensity` értéken. Annak a területnek a színe, amelyben nem szerepelnek az adathalmazok a "interpoláció" kifejezés 0. indexében, vagy egy "lépcsőzetes" kifejezés alapértelmezett színe. Ennek az értéknek a használatával megadhatja a háttér színét. Ez az érték gyakran átlátszó vagy félig átlátszó feketére van állítva. 

  Példák a színkifejezésekre:

  | Interpoláció színe kifejezés | Lépcsőzetes szín kifejezése |
  |--------------------------------|--------------------------|
  | Interpolálás<br/>&nbsp;&nbsp;&nbsp;&nbsp;lineáris (), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (0, szín (szín. TRANSZPARENS)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (0.01, Color (Color. MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (0,5, Color (parseColor ("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (1, szín (parseColor ("#00c3ff")))<br/>)` | . lépés<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;szín (szín. TRANSZPARENS),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (0,01, Color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (0,25, Color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (0.5, szín (szín. zöld)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (0.5, szín (szín. sárga)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Leállítás (1, szín (szín. piros))<br/>) |

- `heatmapOpacity`: Azt határozza meg, hogy a Heat Térkép rétege milyen átlátszatlan vagy átlátszó legyen.
- `heatmapIntensity`: Szorzót alkalmaz az egyes adatpontok súlyozására, hogy növelje a hő összesített intenzitását. Az adatpontok súlyozásával különbséget okoz, így könnyebben láthatóvá válik.
- `heatmapWeight`: Alapértelmezés szerint az összes adatponthoz 1 érték tartozik, és a súlyozása egyenlő. A súlyozási beállítás szorzóként funkcionál, és beállíthatja számként vagy kifejezésként. Ha egy szám a súlyozáshoz van beállítva, akkor a rendszer az egyes adatpontokat a térképen kétszer helyezi el. Ha például a súlyozás értéke `2` , akkor a sűrűség páros. Ha a súlyozási beállítást egy számra állítja, akkor a rendszer hasonló módon jeleníti meg a hő-hozzárendelést az intenzitás lehetőség használatával.

  Ha azonban egy kifejezést használ, az egyes adatpontok súlyozása az egyes adatpontok tulajdonságai alapján lehetséges. Tegyük fel például, hogy az egyes adatpontok földrengést jelentenek. A magnitúdó értéke az egyes földrengés-adatpontok fontos mérőszáma volt. A földrengések minden alkalommal történnek, de a legtöbb esetben alacsony a jelentősége, és a rendszer nem észlelt. Egy kifejezésben szereplő magnitúdó értékkel rendelheti hozzá a súlyozást az egyes adatpontokhoz. A súlyozás kiosztásához a magnitúdó értékének használatával jobban áttekinthető a földrengések jelentősége a hő-térképen belül.
- `minZoom` és `maxZoom` : a nagyítási szint tartománya, ahol a réteget meg kell jeleníteni.
- `filter`: A forrásból beolvasott és a rétegben megjelenített szűrési kifejezés.
- `sourceLayer`: Ha a réteghez csatlakoztatott adatforrás egy vektoros csempe forrása, meg kell adni a vektor csempén belüli forrás réteget.
- `visible`: Elrejti vagy megjeleníti a réteget.

Az alábbi példa egy olyan Heat-Térképet mutat be, amelyben egy vonalhajózási interpolációs kifejezést használunk egy sima színátmenet létrehozásához. Az `mag` egyes adatpontok súlyozásának vagy relevanciájának beállításához az adatpontban definiált tulajdonság exponenciális interpolációval történik.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

Az alábbi képernyőfelvételen a fenti egyéni hő-leképező réteg látható az előző Heat Map-példából származó adatok használatával.

![Térkép a legutóbbi földrengések egyéni Heat Map-rétegével](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Konzisztens nagyítású Heat-Térkép

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. A Térkép nagyítása után az adatösszesítések együtt, a Heat Map-réteg pedig eltérőnek tűnik. A következő videó mutatja a Heat Térkép alapértelmezett viselkedését, ahol a Térkép nagyításakor a rendszer a képpont sugarat is fenntartja.

![Animáció, amely egy térképes nagyítást mutat egy Heat térképes réteggel, amely konzisztens képpont méretű](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Egy `zoom` kifejezéssel méretezheti a sugarat az egyes nagyítási szintekhez, így az egyes adatpontok a Térkép ugyanazon fizikai területére terjednek ki. Ez a kifejezés a Heat Map réteget statikus és konzisztensvé teszi. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint.

A sugár skálázása úgy, hogy az minden nagyítási szinten megduplázódik, létrehoz egy hő-térképet, amely minden nagyítási szinten konzisztensnek tűnik. Ha alkalmazni szeretné ezt a skálázást, használja a ( `zoom` 2 `exponential interpolation` . alap) kifejezést, a minimális nagyítási szinthez pedig a képpont sugarat, a maximális nagyítási szinthez pedig a következő mintában látható méretet `2 * Math.pow(2, minZoom - maxZoom)` . A Térkép nagyításával megtekintheti, hogy a Heat Térkép hogyan méretezhető a nagyítási szinttel.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

Az alábbi videó egy, a fenti kódot futtató Térképet mutat be, amely méretezi a sugarat, miközben a Térkép nagyítása folyamatban van, hogy konzisztens hő-leképezést hozzon létre a nagyítási szintek között.

![Animáció, amely egy térképes nagyítást mutat egy Heat térképes réteggel, amely konzisztens földrajzi méretet mutat](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Következő lépések

A térképekhez hozzáadandó további példákat a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-android-sdk.md)
