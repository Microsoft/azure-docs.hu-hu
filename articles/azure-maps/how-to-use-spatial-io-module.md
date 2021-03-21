---
title: A Azure Maps térbeli IO-modul használata | Microsoft Azure térképek
description: Megtudhatja, hogyan használhatja a Azure Maps web SDK által biztosított térbeli i/o-modult. Ez a modul robusztus funkciókat biztosít annak érdekében, hogy a fejlesztők könnyebben integrálhatók a térbeli adatainak a Azure Maps web SDK-val.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: cd64c80acceb1542c080fc45efbce59f287d448a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680683"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>A Azure Maps térbeli IO-modul használata

A Azure Maps web SDK a **térbeli IO-modult** biztosítja, amely a térbeli és a Azure Maps web SDK-val együtt JavaScript vagy írógéppel használatával integrálja a térbeli adategységeket. A modul robusztus funkciói lehetővé teszik a fejlesztők számára a következőket:

- [Gyakori térbeli adatfájlok olvasása és írása](spatial-io-read-write-spatial-data.md). A támogatott fájlformátumok közé tartozik a KML, a KMZ, a GPX, a GeoRSS, a GML, a GeoJSON és a CSV fájl, amely térbeli adatokat tartalmazó oszlopokat tartalmaz. A Well-Known szöveget (WKT) is támogatja.
- [Csatlakozhat nyílt térinformatikai konzorcium (OGC) szolgáltatáshoz, és integrálhatja az Azure Maps web SDK-val. Az overlay web Map Services (WMS) és a web Map csempe-szolgáltatások (WMTS) rétegként jelennek meg a térképen](spatial-io-add-ogc-map-layer.md).
- [Adatlekérdezés a webes szolgáltatások (WFS) szolgáltatásban](spatial-io-connect-wfs-service.md).
- Olyan [összetett adatkészleteket tartalmazhat, amelyek a stílussal kapcsolatos információkat tartalmaznak, és a minimális kódok használatával automatikusan renderelést végeznek](spatial-io-add-simple-data-layer.md).
- [Nagy sebességű XML-és tagolt fájl-olvasó és-író osztályok kihasználása](spatial-io-core-operations.md).

Ebből az útmutatóból megtudhatja, hogyan integrálhatja és használhatja a térbeli IO-modult egy webalkalmazásban.

Ez a videó áttekintést nyújt a térbeli IO-modulról a Azure Maps web SDK-ban.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> Csak olyan adatok és szolgáltatások használata, amelyek egy megbízható forrásból származnak, különösen akkor, ha egy másik tartományban hivatkozik rá. A térbeli IO-modul a kockázatok minimalizálására tesz lépéseket, azonban a legbiztonságosabb megközelítés nem teszi lehetővé, hogy az alkalmazás danagerous-adatai bekezdődjön. 

## <a name="prerequisites"></a>Előfeltételek

A térbeli i/o-modul használatához [Azure Maps fiókot kell létrehoznia](./quick-demo-map-app.md#create-an-azure-maps-account) , és [be kell szereznie a fiókjához tartozó elsődleges előfizetési kulcsot](./quick-demo-map-app.md#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>A térbeli IO-modul telepítése

A Azure Maps térbeli i/o-modult a két lehetőség egyikének használatával töltheti be:

* A Azure Maps térbeli IO-modul globálisan üzemeltetett Azure CDN. Ehhez a beállításhoz a `<head>` HTML-fájl elemében a JavaScriptre mutató hivatkozást adhat hozzá.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Az [Azure-Maps-térbeli-IO](https://www.npmjs.com/package/azure-maps-spatial-io) forráskódja helyileg tölthető be, majd az alkalmazással is üzemeltethető. Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz. Ehhez a beállításhoz használja a következő parancsot a csomag telepítéséhez:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Ezután adjon hozzá egy hivatkozást a JavaScripthez a `<head>` HTML-dokumentum elemében:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>A térbeli IO-modul használata

1. Hozzon létre egy új HTML-fájlt.

2. Töltse be a Azure Maps web SDK-t, és inicializálja a Térkép vezérlőelemet. A részletekért tekintse meg a [Azure Maps Map Control](./how-to-use-map-control.md) útmutatót. Ha elkészült ezzel a lépéssel, a HTML-fájlnak így kell kinéznie:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Töltse be a Azure Maps térbeli IO-modult. Ehhez a gyakorlathoz használja a CDN-t a Azure Maps térbeli IO-modulhoz. Adja hozzá az alábbi hivatkozást a `<head>` HTML-fájl eleméhez:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicializálja a `datasource` -t, és adja hozzá az adatforrást a térképhez. Inicializálja a `layer` -t, és adja hozzá az adatforrást a Térkép réteghez. Ezután jelenítse meg az adatforrást és a réteget is. Mielőtt lefelé görgetve megtekinti a teljes kódot a következő lépésben, gondolja át az adatforrások és a rétegbeli kódrészletek legjobb helyeit. Ne felejtse el, hogy a Térkép programozott módosítása előtt várnia kell, amíg a Térkép erőforrás elkészült.

    ```javascript
    var datasource, layer;
    ```

    és

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. A HTML-kódnak a következő kódhoz hasonlóan kell kinéznie. Ez a minta azt mutatja be, hogyan lehet XML-fájlt olvasni egy URL-címről. Ezután töltse be és jelenítse meg a fájl funkciójának a térképen való megjelenítését. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Ne felejtse el lecserélni az `<Your Azure Maps Key>` elsődleges kulcsra. Nyissa meg a HTML-fájlt, és az alábbi képhez hasonló eredményeket fog látni:

    <center>

    ![Térbeli adatértékek – példa](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Következő lépések

Az itt bemutatott funkció csak a térbeli IO-modul számos funkciójának egyike. Az alábbi útmutatókból megtudhatja, hogyan használhatja más funkciókat a térbeli IO-modulban:

> [!div class="nextstepaction"]
> [Egyszerű adatréteg hozzáadása](spatial-io-add-simple-data-layer.md)

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

Tekintse át a Azure Maps térbeli IO dokumentációját:

> [!div class="nextstepaction"]
> [Azure Maps térbeli IO-csomag](/javascript/api/azure-maps-spatial-io/)