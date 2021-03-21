---
title: 'Oktatóanyag: útvonal megkeresése egy helyhez | Microsoft Azure térképek'
description: 'Útmutató a hasznos pontokra mutató útvonal megkereséséhez. Lásd: a címek koordinátáinak beállítása és a Azure Maps útvonal-szolgáltatás lekérdezése a pont irányára.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 1f1647766eeae917f7a2266ebb8f00fdfab3452e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210580"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Oktatóanyag: útvonal-utasítások megjelenítése a Azure Maps Route Service és a Map Control használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Maps [Route Service API](/rest/api/maps/route) -t és a [Térkép vezérlőelemet](./how-to-use-map-control.md) az útvonal irányának az elejétől a végéig történő megjelenítéséhez. Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * A Térkép vezérlőelem létrehozása és megjelenítése egy weblapon. 
> * Az útvonal megjelenítési megjelenítésének meghatározása a [szimbólum rétegeinek](map-add-pin.md) és a [sorok rétegeinek](map-add-line-layer.md)definiálásával.
> * Hozzon létre és adjon hozzá GeoJSON objektumokat a térképhez a kezdő-és végpontok ábrázolásához.
> * Útvonal irányának beolvasása a kezdő és a záró pontról az [Route Directions API beolvasása](/rest/api/maps/route/getroutedirections)paranccsal.

A minta teljes forráskódját [itt](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)szerezheti be. [Itt](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)találhat egy élő mintát.

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Térkép vezérlőelem létrehozása és megjelenítése

A következő lépések bemutatják, hogyan hozhatja létre és jelenítheti meg a Térkép vezérlőelemet egy weblapon.

1. A helyi gépén hozzon létre egy új fájlt **MapRoute.html** néven.
2. Másolja/illessze be a következő HTML-kódot a fájlba.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    A HTML-fejléc tartalmazza az Azure térképkezelés Library által üzemeltetett CSS-és JavaScript-forrásfájlokat. A törzs `onload` eseménye meghívja a `GetMap` függvényt. A következő lépésben hozzáadjuk a Térkép vezérlőelem inicializálási kódját.

3. Adja hozzá az alábbi JavaScript-kódot a `GetMap` függvényhez. Cserélje le a karakterláncot `<Your Azure Maps Key>` arra az elsődleges kulcsra, amelyet a Maps-fiókból másolt.

    ```JavaScript
   //Instantiate a map object
   var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
   });
   ```

4. Mentse a fájlt, és nyissa meg a böngészőben. Egy egyszerű üzenet jelenik meg.

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Térkép vezérlőelem alapszintű leképezése":::

## <a name="define-route-display-rendering"></a>Útvonal megjelenítési megjelenítésének meghatározása

Ebben az oktatóanyagban az útvonalat egy vonal réteggel fogjuk megjeleníteni. A kezdő és a végpontok szimbólum réteggel lesznek megjelenítve. A sorok hozzáadásával kapcsolatos további információkért lásd: [vonal réteg hozzáadása térképhez](map-add-line-layer.md). További információ a szimbólumok rétegeiről: [szimbólum hozzáadása egy térképhez](map-add-pin.md).

1. Fűzze hozzá a következő JavaScript-kódot a `GetMap` függvényhez. Ez a kód implementálja a Térkép vezérlőelem `ready` eseménykezelőjét. Az oktatóanyagban szereplő kód további része az `ready` eseménykezelőbe kerül.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```

    A Map Control `ready` eseménykezelőben létrejön egy adatforrás, amely az útvonalat az elejétől a végpontig tárolja. Ha meg szeretné határozni, hogyan legyenek megjelenítve az útválasztási vonal, egy sor réteget hoz létre és csatol az adatforráshoz.  Annak biztosítása érdekében, hogy az útválasztási vonal ne fedje fel a közúti címkéket, egy második paramétert adtunk át a következő értékkel: `'labels'` .

    Ezután létrejön egy szimbólum-réteg, amely az adatforráshoz van csatolva. Ez a réteg határozza meg a kezdő és a záró pontok megjelenítésének módját. A kifejezések hozzá lettek adva, hogy beolvassák az ikon képének és a szöveg feliratának adatait az egyes pontok objektumainak tulajdonságaiban. A kifejezésekkel kapcsolatos további információkért lásd: [adatvezérelt stílusú kifejezések](data-driven-style-expressions-web-sdk.md).

2. Állítsa be a kezdőpontot a Microsoft számára, és a végpontot a Seattle-ben lévő benzinkútnál.  A Map Control `ready` eseménykezelőben fűzze hozzá a következő kódot.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Ez a kód két [GeoJSON pontot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre a kezdő és a végpontok jelölésére, amelyek ezután hozzáadódnak az adatforráshoz. 

    A kód utolsó blokkja beállítja a kamera nézetét a kezdő és a végpont szélességi és hosszúsági értékének használatával. Az indulási és célpontokat a rendszer hozzáadja az adatforráshoz. Az indulási és célpontokhoz tartozó határolókeret kiszámítása az `atlas.data.BoundingBox.fromData` függvénnyel történik. Ezzel a határolókeret használatával állíthatja be a térképes kamerák nézetét a teljes útvonalon a `map.setCamera` függvénnyel. A kitöltés bekerül a szimbólum ikonjainak pixel dimenzióinak kompenzálására. A Map Control setCamera tulajdonságával kapcsolatos további információkért lásd: [setCamera (CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

3. Mentse **MapRoute.html** -t, és frissítse a böngészőt. A Térkép most már a Seattle-i középpontban van. A könnycsepp kék PIN-kód a kezdőpontot jelöli. A kerek kék PIN-kód a végpontot jelöli.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Útvonalak megnézetének kezdete és vége a térképen":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Útvonal irányának beolvasása

Ebből a szakaszból megtudhatja, hogyan használhatja a Azure Maps Route Directions API-t az útvonal irányának lekérésére és az egyik pontról a másikra való érkezés várható időpontjára.

>[!TIP]
>A Azure Maps Route Services API-kat kínál az útvonalak megtervezéséhez különböző útvonal-típusok alapján, például a *leggyorsabb*, *legrövidebb*, *Eco* vagy *izgalmas* útvonalakon a távolság, a forgalmi feltételek és a felhasznált átviteli mód alapján. A szolgáltatás azt is lehetővé teszi, hogy a felhasználók a korábbi forgalmi feltételek alapján tervezzék meg a jövőbeli útvonalakat. A felhasználók megtekinthetik az útvonal időtartamának előrejelzését egy adott időpontra vonatkozóan. További információkért lásd: [Route Directions API beszerzése](/rest/api/maps/route/getroutedirections).

1. A `GetMap` függvényben a vezérlő `ready` eseménykezelőján belül adja hozzá a következőt a JavaScript-kódhoz.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   A `SubscriptionKeyCredential` létrehoz egy `SubscriptionKeyCredentialPolicy` -t az előfizetési kulccsal Azure Maps HTTP-kérések hitelesítéséhez. A `atlas.service.MapsURL.newPipeline()` veszi a `SubscriptionKeyCredential` szabályzatot, és létrehoz egy [folyamat](/javascript/api/azure-maps-rest/atlas.service.pipeline) -példányt. A a `routeURL` Azure Maps [Route](/rest/api/maps/route) műveletekhez tartozó URL-címet jelöli.

2. A hitelesítő adatok és az URL-cím beállítása után fűzze hozzá a következő kódot a vezérlő `ready` eseménykezelőjában. Ez a kód létrehozza az útvonalat a kezdőponttól a végpontig. A a `routeURL` Azure Maps Route Service API-t kéri az útvonal irányának kiszámításához. Ezután a válaszból Kinyer egy GeoJSON-gyűjteményt, `geojson.getFeatures()` és hozzáadja az adatforráshoz.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Mentse a **MapRoute.html** fájlt, és frissítse a webböngészőt. A térképnek most az elejétől a végpontig kell megjelenítenie az útvonalat.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Azure Map Control és Route Service":::

A minta teljes forráskódját [itt](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)szerezheti be. [Itt](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)találhat egy élő mintát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyag bemutatja, hogyan hozhat létre egy korlátozásokkal rendelkező útvonal-lekérdezést, például az utazási módot vagy a rakomány típusát. Ezután több útvonal is megjeleníthető ugyanazon a térképen.

> [!div class="nextstepaction"]
> [Útvonalak keresése különböző utazási módokhoz](./tutorial-prioritized-routes.md)