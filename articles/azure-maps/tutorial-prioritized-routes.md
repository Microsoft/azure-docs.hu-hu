---
title: 'Oktatóanyag: több útvonal megkeresése utazási mód alapján | Microsoft Azure térképek'
description: 'Oktatóanyag a Azure Maps használatáról az adott utazási módokhoz tartozó útvonalak megtalálásához. Lásd: több útvonal megjelenítése a Maps-ben.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 5e7f38e4bbc95e04bf54f8f7a2dd80e21891e8df
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215493"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Oktatóanyag: útvonalak keresése és megjelenítése különböző utazási módokhoz Azure Maps használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Maps [Route Service](/rest/api/maps/route) -t és a [Térkép vezérlőelemet](./how-to-use-map-control.md) a személygépkocsik és a kereskedelmi gépjárművek (kamionok) útvonali irányának megjelenítésére a `USHazmatClass2` rakomány típusával. Emellett végigvezeti Önt a valós idejű adatforgalmi adatok térképeken való megjelenítésének módján. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Térkép vezérlőelem létrehozása és megjelenítése egy weblapon
> * Valós idejű adatforgalmi adatok leképezése
> * Privát és kereskedelmi jármű útvonalának kérése és megjelenítése térképeken

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. [Hozzon létre egy Azure Maps fiókot](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

A minta teljes forráskódját [itt](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)szerezheti be. [Itt](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)találhat egy élő mintát.

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Új weblap létrehozása a térképkezelési API használatával

A következő lépések bemutatják, hogyan hozhatja létre és jelenítheti meg a Térkép vezérlőelemet egy weblapon.

1. A helyi gépén hozzon létre egy új fájlt **MapTruckRoute.html** néven.
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

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Térkép vezérlőelem alapszintű leképezése":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Valós idejű adatforgalmi adatok leképezése

1. Fűzze hozzá a következő JavaScript-kódot a `GetMap` függvényhez. Ez a kód implementálja a Térkép vezérlőelem `ready` eseménykezelőjét. Az oktatóanyagban szereplő kód további része az `ready` eseménykezelőbe kerül.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    A térképi eseménykezelőben a térképen a forgalmi folyamat beállítása a (z) értékre `ready` van állítva `relative` , amely az út sebessége a szabad folyamathoz képest. További forgalmi lehetőségekért lásd: [TrafficOptions Interface](/javascript/api/azure-maps-control/atlas.trafficoptions).

2. Mentse a **MapTruckRoute.html** fájlt, és frissítse a lapot a böngészőben. Ha bármely városba, például a Los Angeles-be nagyítja, látni fogja, hogy az utcák a jelenlegi forgalmi flow-adatokkal jelennek meg.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Térkép forgalmának megtekintése":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Útvonal megjelenítési megjelenítésének meghatározása

Ebben az oktatóanyagban két útvonalat számítunk ki és jelenítünk meg a térképen. A rendszer az első útvonalat fogja kiszámítani egy saját jármű (autó) számára. A második útvonal a kereskedelmi jármű (tehergépkocsi) számára lesz kiszámítva, hogy megjelenjen az eredmények közötti különbség. A leképezés során a Térkép egy szimbólum ikont jelenít meg az útvonal kezdő-és végpontja számára, és az egyes útvonalak elérési útjához különböző színekkel rendelkező vonalvezetési vonalakat mutat. A sorok hozzáadásával kapcsolatos további információkért lásd: [vonal réteg hozzáadása térképhez](map-add-line-layer.md). További információ a szimbólumok rétegeiről: [szimbólum hozzáadása egy térképhez](map-add-pin.md).

1. A Map Control `ready` eseménykezelőben fűzze hozzá a következő kódot.

    ```JavaScript

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
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

    ```


    A Map Control `ready` eseménykezelőben létrejön egy adatforrás, amely az útvonalat az elejétől a végéig tárolja. A [kifejezések](data-driven-style-expressions-web-sdk.md) használatával lehet beolvasni a vonal szélességét és színét az útválasztási sor funkciójának tulajdonságaiban. Annak biztosítása érdekében, hogy az útválasztási vonal ne fedje fel a közúti címkéket, egy második paramétert adtunk át a következő értékkel: `'labels'` .

    Ezután létrejön egy szimbólum-réteg, amely az adatforráshoz van csatolva. Ez a réteg határozza meg a kezdő és a záró pontok megjelenítésének módját. A kifejezések hozzá lettek adva, hogy beolvassák az ikon képének és a szöveg feliratának adatait az egyes pontok objektumainak tulajdonságaiban. A kifejezésekkel kapcsolatos további információkért lásd: [adatvezérelt stílusú kifejezések](data-driven-style-expressions-web-sdk.md).

2. Állítsa be a kezdőpontot fiktív vállalatként, melynek neve: fabrikam, a végpont pedig Microsoft Office.  A Map Control `ready` eseménykezelőben fűzze hozzá a következő kódot.


    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Ez a kód két [GeoJSON pontot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre a kezdő és a végpontok jelölésére, amelyek ezután hozzáadódnak az adatforráshoz.

    A kód utolsó blokkja beállítja a kamera nézetét a kezdő és a végpont szélességi és hosszúsági értékének használatával. Az indulási és célpontokat a rendszer hozzáadja az adatforráshoz. Az indulási és célpontokhoz tartozó határolókeret kiszámítása az `atlas.data.BoundingBox.fromData` függvénnyel történik. Ezzel a határolókeret használatával állíthatja be a térképes kamerák nézetét a teljes útvonalon a `map.setCamera` függvénnyel. A kitöltés bekerül a szimbólum ikonjainak pixel dimenzióinak kompenzálására. A Map Control setCamera tulajdonságával kapcsolatos további információkért lásd: [setCamera (CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

3. Mentse **TruckRoute.html** -t, és frissítse a böngészőt. A Térkép most már a Seattle-i középpontban van. A könnycsepp kék PIN-kód a kezdőpontot jelöli. A kerek kék PIN-kód a végpontot jelöli.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Az indulási és célpontokat jelölő térkép megtekintése":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Privát és kereskedelmi jármű útvonalának kérése és megjelenítése térképeken

Ebből a szakaszból megtudhatja, hogyan használhatja a Azure Maps Route Service-t az egyik pontról a másikra, a szállítási mód alapján. Kétféle átviteli módot fogunk használni: kamion és autó.

>[!TIP]
>Az útválasztási szolgáltatás API-kat biztosít a *leggyorsabb*, *legrövidebb*, *Eco* vagy *izgalmas* útvonalak tervezéséhez a távolság, a forgalmi feltételek és a felhasznált átviteli mód alapján. A szolgáltatás azt is lehetővé teszi, hogy a felhasználók a korábbi forgalmi feltételek alapján tervezzék meg a jövőbeli útvonalakat. A felhasználók megtekinthetik az útvonal időtartamának előrejelzését egy adott időpontra vonatkozóan. További információkért lásd: [Route Directions API beszerzése](/rest/api/maps/route/getroutedirections).

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

2. A hitelesítő adatok és az URL-cím beállítása után adja hozzá a következő JavaScript-kódot egy teherautó útvonal-útvonal létrehozásához az elejétől a végéig. Ez az útvonal létrejön, és megjelenik egy olyan teherautónál, amely `USHazmatClass2` osztályozott rakományt végez.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    A fenti kód a Azure Maps Route szolgáltatást a [Azure Maps Route Directions API](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-)-n keresztül kérdezi le. Ezután a rendszer kinyeri az GeoJSON a metódus használatával kinyert válaszból `geojson.getFeatures()` . Végül az útválasztási sor hozzá lesz adva az adatforráshoz. A 0 indexnél hozzáadjuk azt annak érdekében, hogy a teherautó útvonala az adatforrás bármely más sora előtt legyen megjelenítve, mert a tehergépkocsi-útvonal kiszámítása gyakran lassabb lesz, mint az autó útvonalának kiszámítása. Ha az autó útvonala után hozzáadja az adatforráshoz a Truck Route sort, akkor azt a rendszer megjeleníti. A rendszer két tulajdonságot ad hozzá a Truck Route-sorhoz: egy kék körvonal színét és egy kilenc képpont vastagságát.

    >[!TIP]
    > A Azure Maps Route Directions API összes lehetséges beállításának és értékének megjelenítéséhez lásd: az [útválasztási utasítások URI-paraméterei](/rest/api/maps/route/postroutedirections#uri-parameters).

3. Most fűzze hozzá a következő JavaScript-kódot az autóhoz tartozó útvonal létrehozásához.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    A fenti kód a Azure Maps Routing szolgáltatást a  [Azure Maps Route Directions API](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) metódussal kérdezi le. Ezután a rendszer kinyeri az GeoJSON a metódus használatával kinyert válaszból `geojson.getFeatures()` . Végül az útválasztási sor hozzá lesz adva az adatforráshoz. A rendszer két tulajdonságot ad hozzá a Truck Route-sorhoz: egy lila ecsetvonás színét és egy öt képpont vastagságú ecsetvonást.

4. Mentse a **TruckRoute.html** fájlt, és frissítse a böngészőt. A térképnek ekkor a teherautó-és autós útvonalakat kell megjelenítenie.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Magán-és haszongépjármű-útvonalak egy térképen az Azure Route Service":::

    A teherautó útvonala sűrű kék vonal használatával jelenik meg. Az autó útvonala egy vékony lila vonal használatával jelenik meg. Az autó útvonala az I-90-n keresztül halad végig Washington államon keresztül, és a lakossági területeken lévő alagutakon keresztül halad. Mivel az alagutak megközelítik a lakossági területeket, a veszélyes hulladékok mennyisége korlátozott. A tehergépkocsi útvonala, amely megadja a `USHazmatClass2` rakomány típusát, egy másik autópálya használatára van irányítva.

A minta teljes forráskódját [itt](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)szerezheti be. [Itt](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)találhat egy élő mintát.

Az [adatvezérelt stílusú kifejezéseket is használhatja](data-driven-style-expressions-web-sdk.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyag azt mutatja be, hogyan hozható létre egy egyszerű tároló-lokátor a Azure Maps használatával.

> [!div class="nextstepaction"]
> [Tároló-lokátor létrehozása Azure Maps használatával](./tutorial-create-store-locator.md)