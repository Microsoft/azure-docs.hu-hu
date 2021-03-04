---
title: 'Oktatóanyag: GeoJSON-alapú adatbetöltések Azure Maps Android SDK-ba | Microsoft Azure térképek'
description: Útmutató a GeoJSON adatfájljának a Azure Maps Android Map SDK-ba való betöltéséhez.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 8300a7c120ce816c8068a88fa69f4f978fa664ca
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034506"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Oktatóanyag: GeoJSON-alapú adatterhelések betöltése Azure Maps Android SDK-ba

Ez az oktatóanyag végigvezeti a GeoJSON fájljának a Azure Maps Android SDK-ba való importálásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Maps hozzáadása Android-alkalmazásokhoz.
> * Hozzon létre egy adatforrást, és töltsön be egy GeoJSON-fájlt egy helyi fájlból vagy a webről.
> * Jelenítse meg az adatpontot a térképen.

## <a name="prerequisites"></a>Előfeltételek

1. Fejezze be a gyors üzembe helyezési útmutatót [: hozzon létre egy Android-alkalmazást](quick-android-map.md). Ez az oktatóanyag az ebben a rövid útmutatóban használt kódot fogja kiterjeszteni.
2. Töltse le a GeoJSON fájlhoz tartozó [mintavételi pontokat](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) .

### <a name="import-geojson-data-from-web-or-assets-folder"></a>GeoJSON-adatok importálása a web vagy az assets mappából

A legtöbb GeoJSON-fájl az a-ben lévő összes adattal becsomagolja `FeatureCollection` . Ezt szem előtt tartva, ha a GeoJSON-fájlokat karakterláncként tölti be az alkalmazásba, a rendszer átadhatja őket a szolgáltatás gyűjteményének statikus `fromJson` metódusának, amely deszerializálja a karakterláncot egy olyan GeoJSON `FeatureCollection` -objektumba, amelyet hozzáadhat a térképhez.

A következő lépések bemutatják, hogyan importálhat egy GeoJSON-fájlt az alkalmazásba, és deszerializálhatja azt GeoJSON `FeatureCollection` objektumként.

1. Fejezze be a gyors üzembe helyezési útmutatót [: hozzon létre egy Android-alkalmazást](quick-android-map.md) az alkalmazás tetején az alábbi lépések alapján.
2. Az Android Studio projekt paneljén kattintson a jobb gombbal az **alkalmazás** mappára, és válassza a következőt: `New > Folder > Assets Folder` .
3. Húzza a GeoJSON-fájlhoz tartozó [mintavételi pontokat](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) az eszközök mappába.

::: zone pivot="programming-language-java-android"

4. Hozzon létre egy új, **utils. Java** nevű fájlt, és adja hozzá a következő kódot a fájlhoz. Ez a kód egy nevű statikus metódust biztosít `importData` , amely aszinkron módon importál egy fájlt az `assets` alkalmazás mappájából, vagy a webről egy URL-címet karakterláncként, és visszaküldi a felhasználói felületi szálra egy egyszerű visszahívási módszer használatával.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. Nyissa meg a **MainActivity. Java** fájlt, és adja hozzá a következő kódot az esemény visszahívásához `mapControl.onReady` , ez a `onCreate` metóduson belül található. Ez a kód az importálási segédprogramot használja a fájl karakterláncként való **SamplePoiDataSet.js** olvasásához, majd a osztály statikus metódusának használatával deszerializálja a szolgáltatás-gyűjteményként `fromJson` `FeatureCollection` . Ez a kód azt is kiszámítja, hogy a rendszer a szolgáltatás gyűjteményében lévő összes adathoz tartozó határolókeret területét is kiszámolja, és ezzel beállítja a Térkép kameráját, hogy az adataira összpontosítsanak.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
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

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Az adatforrások GeoJSON betöltésére szolgáló kód használatával most meg kell adniuk, hogyan jelenjenek meg az adatai a térképen. A pontok adatmegjelenítésének számos különböző megjelenítési rétege van; A [buborék réteget](map-add-bubble-layer-android.md), a [Symbol réteget](how-to-add-symbol-to-android-map.md)és a [Heat Térkép réteget](map-add-heat-map-layer-android.md) a leggyakrabban használt rétegek alkotják. Adja hozzá a következő kódot, hogy a visszahívás során egy buborék rétegben jelenítse meg az adott `mapControl.onReady` eseményt az esemény importálásához szükséges kód alapján.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. Hozzon létre egy új, **utils. kt** nevű fájlt, és adja hozzá a következő kódot a fájlhoz. Ez a kód egy nevű statikus metódust biztosít `importData` , amely aszinkron módon importál egy fájlt az `assets` alkalmazás mappájából, vagy a webről egy URL-címet karakterláncként, és visszaküldi a felhasználói felületi szálra egy egyszerű visszahívási módszer használatával.

    ```kotlin
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;

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

5. Nyissa meg a **MainActivity. kt** fájlt, és adja hozzá a következő kódot az esemény visszahívásához `mapControl.onReady` , ez a `onCreate` metóduson belül található. Ez a kód az importálási segédprogramot használja a fájl karakterláncként való **SamplePoiDataSet.js** olvasásához, majd a osztály statikus metódusának használatával deszerializálja a szolgáltatás-gyűjteményként `fromJson` `FeatureCollection` . Ez a kód azt is kiszámítja, hogy a rendszer a szolgáltatás gyűjteményében lévő összes adathoz tartozó határolókeret területét is kiszámolja, és ezzel beállítja a Térkép kameráját, hogy az adataira összpontosítsanak.

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

6. Az adatforrások GeoJSON betöltésére szolgáló kód használatával most meg kell adniuk, hogyan jelenjenek meg az adatai a térképen. A pontok adatmegjelenítésének számos különböző megjelenítési rétege van; A [buborék réteget](map-add-bubble-layer-android.md), a [Symbol réteget](how-to-add-symbol-to-android-map.md)és a [Heat Térkép réteget](map-add-heat-map-layer-android.md) a leggyakrabban használt rétegek alkotják. Adja hozzá a következő kódot, hogy a visszahívás során egy buborék rétegben jelenítse meg az adott `mapControl.onReady` eseményt az esemény importálásához szükséges kód alapján.

    ```kotlin
    //Create a layer and add it to the map.
    val layer = new BubbleLayer(source)
    map.layers.add(layer)
    ```

::: zone-end

7. Futtassa az alkalmazást. A Térkép az Egyesült Államokra koncentrál, és a GeoJSON-fájl minden egyes helyére behelyezett körök láthatók.

    ![Az USA-beli Térkép GeoJSON-fájlból származó adatokkal](media/tutorial-load-geojson-file-android/android-import-geojson.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő lépésekkel távolíthatja el az ebben az oktatóanyagban található erőforrásokat:

1. Android Studio bezárásához és a létrehozott alkalmazás törléséhez.
2. Ha egy külső eszközön tesztelte az alkalmazást, távolítsa el az alkalmazást az eszközről.

## <a name="next-steps"></a>Következő lépések

További példakódok és az interaktív kódolási felület bemutatása:

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Funkcióinformációk megjelenítése](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
