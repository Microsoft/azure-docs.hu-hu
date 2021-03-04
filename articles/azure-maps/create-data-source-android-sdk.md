---
title: Adatforrás létrehozása Android-térképekhez | Microsoft Azure térképek
description: 'Ismerje meg, hogyan hozhat létre egy adatforrást térképekhez. Ismerkedjen meg az Azure Maps Android SDK által használt adatforrásokkal: GeoJSON-források és vektoros csempék.'
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e870134e2ecd431aa3e5c02638120027f0d47df2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101460"
---
# <a name="create-a-data-source-android-sdk"></a>Adatforrás létrehozása (Android SDK)

Az Azure Maps Android SDK adatforrásokban tárolja az adatforrásokat. Az adatforrások használata optimalizálja az adatműveleteket a lekérdezéshez és a megjelenítéshez. Jelenleg két típusú adatforrás létezik:

- **GeoJSON forrás**: a nyers helyadatok helyi kezelése GeoJSON formátumban. A kis-és közepes adatkészletek esetében is jó (több százezer alakzat).
- **Vektoros csempe forrása**: a térképes mozaikrendszer alapján betölti az aktuális leképezési nézethez tartozó vektorgrafikus csempéket. Ideális nagy-és nagyméretű adatkészletekhez (millió vagy több milliárd alakzat).

## <a name="geojson-data-source"></a>GeoJSON-adatforrás

Azure Maps a GeoJSON-t használja elsődleges adatmodelljeinek egyike. A GeoJSON egy nyílt térinformatikai standard módszer, amely a térinformatikai adatformátumot jelöli. A Azure Maps Android SDK-ban elérhető GeoJSON osztályok egyszerűen létrehozhatók és szerializálják a GeoJSON-adatbázisokat. Betöltheti és tárolhatja a GeoJSON az `DataSource` osztályban, és a rétegek használatával jelenítheti meg őket. A következő kód bemutatja, hogyan hozhatók létre GeoJSON-objektumok a Azure Mapsban.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

::: zone-end

Azt is megteheti, hogy a tulajdonságok betölthetők egy JsonObject, majd a szolgáltatás létrehozásakor bekerülnek a funkcióba az alább látható módon.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

Miután létrehozta a GeoJSON szolgáltatást, egy adatforrást a Térkép tulajdonságával adhat hozzá a térképhez `sources` . A következő kód bemutatja, hogyan hozhat létre `DataSource` , adhat hozzá a térképhez, és hozzáadhat egy szolgáltatást az adatforráshoz.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

A következő kód bemutatja, hogyan hozhat létre GeoJSON szolgáltatást, FeatureCollection és geometriákat.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>GeoJSON szerializálása és deszerializálása

A szolgáltatás-gyűjtemény, a funkció és a geometria osztályok mind rendelkeznek `fromJson()` és `toJson()` statikus metódusokkal rendelkeznek, amelyek segítséget nyújtanak a szerializáláshoz. A metódus által átadott formázott érvényes JSON-karakterlánc `fromJson()` létrehozza a geometriai objektumot. Ez a `fromJson()` módszer azt is jelenti, hogy Gson vagy más szerializálási/deszerializálási stratégiákat is használhat. A következő kód bemutatja, hogyan készíthet sztringesített GeoJSON funkciót, és deszerializálhatja azt a Feature osztályba, majd visszaszerializálhatja egy GeoJSON karakterláncba.

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>GeoJSON-adatok importálása a web vagy az assets mappából

A legtöbb GeoJSON-fájl FeatureCollection tartalmaz. GeoJSON-fájlok olvasása karakterláncként, és a `FeatureCollection.fromJson` metódus használatával deszerializálhatja azt.

A következő kód egy újrafelhasználható osztály, amely a webes vagy helyi eszközök mappából karakterláncként importálja az adatok importálását, és visszaküldi azt a felhasználói felületi szálra egy visszahívási függvény használatával.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

Az alábbi kód azt mutatja be, hogyan használhatja ezt a segédprogramot a GeoJSON-adattípusok karakterláncként való importálásához, és visszahívással visszaküldheti a felhasználói felületi szálra. A visszahívás során a sztringek GeoJSON szerializálható a szolgáltatásba, és az adatforráshoz is hozzáadhatók. Ha szeretné, frissítse a Maps kamerát, hogy az az adatterületre koncentráljon.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

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
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

## <a name="vector-tile-source"></a>Vektoros csempe forrása

A vektoros csempék forrása leírja, hogyan lehet hozzáférni a vektoros csempék rétegéhez. Használja az `VectorTileSource` osztályt a vektoros csempe forrásának létrehozásához. A vektoros csempe rétegei hasonlóak a csempék rétegeihez, de nem azonosak. A csempe réteg egy raszteres rendszerkép. A vektoros csempe rétegek a **PBF** formátumában tömörített fájlok. Ez a tömörített fájl vektoros leképezési és egy vagy több réteget tartalmaz. A fájl az egyes rétegek stílusa alapján megjeleníthető és stílusú lehet az ügyfélen. A vektoros csempén lévő információk pontok, vonalak és sokszögek formájában található földrajzi funkciókat tartalmaznak. A raszteres csempe rétegei helyett több előnye van a vektoros csempék használatának:

- A vektoros csempék fájlmérete általában sokkal kisebb, mint egy egyenértékű raszteres csempe. Így kevesebb sávszélesség van használatban. Kisebb késést, gyorsabb térképet és jobb felhasználói élményt jelent.
- Mivel a rendszer a vektoros csempéket jeleníti meg az ügyfélen, alkalmazkodik a megjelenő eszköz feloldásához. Ennek eredményeképpen a megjelenített térképek jól definiálva jelennek meg, a Crystal Clear címkékkel.
- A vektoros leképezésekben lévő adatstílus módosítása nem igényli újra az adatletöltést, mivel az új stílus alkalmazható az ügyfélen. Ezzel szemben a raszteres csempék rétegének módosítása általában a-kiszolgálóról származó csempék betöltését igényli, majd az új stílust alkalmazza.
- Mivel az adat vektoros formában lett továbbítva, az adatelőkészítéshez kevesebb kiszolgálóoldali feldolgozás szükséges. Ennek eredményeképpen az újabb adatértékek gyorsabban elérhetővé tehetők.

Azure Maps betartja a [Mapbox Vector csempe specifikációját](https://github.com/mapbox/vector-tile-spec), amely egy nyílt szabvány. Azure Maps a következő vektoros szolgáltatásokat nyújtja a platform részeként:

- A Road csempe [dokumentációjának](/rest/api/maps/renderv2/getmaptilepreview)  |  [adatformátuma – részletek](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Forgalmi incidensek [dokumentációjának](/rest/api/maps/traffic/gettrafficincidenttile)  |  [adatformátuma – részletek](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- A forgalmi folyamat [dokumentációjának](/rest/api/maps/traffic/gettrafficflowtile)  |  [adatformátumának részletei](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- A Azure Maps Creator Emellett lehetővé teszi az egyéni vektoros csempék létrehozását és elérését a [csempe renderelése v2](/rest/api/maps/renderv2/getmaptilepreview) használatával.

> [!TIP]
> Ha vektoros vagy raszteres képcsempéket használ a Azure Maps Render szolgáltatásból a web SDK-val, a `atlas.microsoft.com` helyőrzőre cserélheti `azmapsdomain.invalid` . Ezt a helyőrzőt a Térkép ugyanazokkal a tartománnyal helyettesíti, és a rendszer automatikusan hozzáfűzi ugyanazokat a hitelesítési adatokat is. Ez nagymértékben leegyszerűsíti a Azure Active Directory hitelesítés használatakor a renderelési szolgáltatással történő hitelesítést.

Ha a térképen a vektoros csempe forrásának adatait szeretné megjeleníteni, a forrást csatlakoztathatja az egyik adatmegjelenítési réteghez. A vektoros forrást használó összes rétegnek meg kell adnia egy `sourceLayer` értéket a beállításokban. A következő kód betölti a Azure Maps Traffic flow Vector csempe szolgáltatást vektoros csempe-forrásként, majd a térképen egy vonal réteget használva jeleníti meg. Ez a vektoros csempés forrás egyetlen adatkészlettel rendelkezik, amely a "forgalom flow" nevű rétegben található. Ebben az adatkészletben az adathalmazban található sorokra vonatkozó tulajdonság egy nevű tulajdonsággal rendelkezik, `traffic_level` amely a kód kiválasztásához és a sorok méretének méretezéséhez használatos.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![Térkép színkódolt úton lévő vonalakkal a forgalmi folyamatok szintjeinek megjelenítéséhez](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Adatforrás csatlakoztatása réteghez

Az adatmegjelenítés a térképen renderelési rétegek használatával történik. Egyetlen adatforrás hivatkozhat egy vagy több renderelési rétegre. A következő megjelenítési rétegekhez adatforrásra van szükség:

- [Buborék réteg](map-add-bubble-layer-android.md) – a térképre méretezett körökként jeleníti meg a pontok mennyiségét.
- [Szimbólum réteg](how-to-add-symbol-to-android-map.md) – megjelenítheti az adatpontokat ikonként vagy szövegként.
- [Hő-Térkép réteg](map-add-heat-map-layer-android.md) – megjeleníti az adatpontok sűrűségének hő-térképét.
- [Vonal réteg](android-map-add-line-layer.md) – vonalak renderelése és a sokszögek körvonalának megjelenítése.
- [Sokszög réteg](how-to-add-shapes-to-android-map.md) – egy sokszög területének kitöltése folytonos színnel vagy képmintázattal.

Az alábbi kód bemutatja, hogyan hozhat létre egy adatforrást, hogyan adhatja hozzá a térképhez, és hogyan csatlakoztathatja azt egy buborék-réteghez. Ezután importálja a GeoJSON pont adatait egy távoli helyről az adatforrásba.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

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

//Create a layer that defines how to render points in the data source and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
        val fc = FeatureCollection.fromJson(result!!)
    
        //Add the feature collection to the data source.
        dataSource.add(fc)
    
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

Vannak olyan renderelési rétegek, amelyek nem csatlakoznak ezekhez az adatforrásokhoz, de közvetlenül betöltik a rendereléshez szükséges adatkereteket.

- [Csempe réteg](how-to-add-tile-layer-android-map.md) – a Térkép tetején lévő raszteres csempe réteget rendeli.

## <a name="one-data-source-with-multiple-layers"></a>Egy adatforrás több réteggel

Több réteg is csatlakoztatható egyetlen adatforráshoz. Számos különböző forgatókönyv létezik, amelyben ez a lehetőség hasznos lehet. Vegyük például azt a forgatókönyvet, amelyben a felhasználó sokszöget rajzol. A sokszög területének megjelenítéséhez és kitöltéséhez a felhasználó hozzáadja a pontokat a térképhez. Ha egy stílusú sort ad hozzá a sokszög körvonalához, az megkönnyíti a sokszög széleinek megjelenítését a felhasználó által rajzolt módon. A sokszögben lévő egyes helyzetek megfelelő szerkesztéséhez hozzáadhatunk egy fogantyút, például egy PIN-kódot vagy egy jelölőt az egyes pozíciók fölött.

![Több réteget ábrázoló Térkép egyetlen adatforrásból származó adatok megjelenítéséhez](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

A legtöbb leképezési platform esetében szükség van egy sokszög-objektumra, egy sor objektumra és egy PIN-kódra a sokszög minden egyes pozíciójában. Ahogy a sokszög módosítva lett, manuálisan kell frissítenie a vonalat és a PIN-ket, ami gyorsan összetett lehet.

A Azure Maps esetében mindössze egyetlen sokszögre van szükség az adatforrásban az alábbi kódban látható módon.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> Ha a metódus használatával rétegeket ad hozzá a térképhez `map.layers.add` , a meglévő réteg azonosítóját vagy példányát második paraméterként is átadhatja. Ez azt jelzi, hogy a Térkép beszúrja a meglévő réteg alá felvett új réteget. A rétegbeli AZONOSÍTÓk átadása mellett a metódus a következő értékeket is támogatja.
>
> - `"labels"` – Beszúrja az új réteget a Térkép feliratának rétegeibe.
> - `"transit"` – Beszúrja az új réteget a térképi út és az átviteli rétegek alá.

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Hő-Térkép hozzáadása](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Web SDK-kód minták](/samples/browse/?products=azure-maps)