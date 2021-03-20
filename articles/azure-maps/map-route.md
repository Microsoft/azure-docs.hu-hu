---
title: Útvonal irányának megjelenítése a térképen | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a térképen két hely közötti irányokat a Microsoft Azure Maps web SDK használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890717"
---
# <a name="show-directions-from-a-to-b"></a>Útvonal megjelenítése A-ból B-be

Ebből a cikkből megtudhatja, hogyan hajthat végre egy útvonal-kérést, és hogyan jelenítheti meg az útvonalat a térképen.

Ezt kétféleképpen teheti meg. Az első módszer az [Azure Maps Route API](/rest/api/maps/route/getroutedirections) lekérdezése egy szolgáltatási modulon keresztül. A második módszer az [API beolvasása](https://fetch.spec.whatwg.org/) , hogy keresési kérést végezzen a [Azure Maps Route API](/rest/api/maps/route/getroutedirections)-hoz. Az alábbiakban mindkét módszert ismertetjük.

## <a name="query-the-route-via-service-module"></a>Az útvonal lekérdezése a Service Module használatával

<iframe height='500' scrolling='no' title='Irányok megjelenítése a-tól B-re egy térképen (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/RBZbep/'>-tól B-be a térképeken (szolgáltatási modul)</a> a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>-on Azure Maps ().
</iframe>

A fenti kódban az első blokk létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz egy `TokenCredential` -t a hozzáférési jogkivonattal Azure Maps HTTP-kérések hitelesítéséhez. Ezután továbbítja a- `TokenCredential` t, `atlas.service.MapsURL.newPipeline()` és létrehoz egy [folyamat](/javascript/api/azure-maps-rest/atlas.service.pipeline) -példányt. A a `routeURL` Azure Maps [Route](/rest/api/maps/route) műveletekhez tartozó URL-címet jelöli.

A kód harmadik blokkja létrehoz egy [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) objektumot a térképhez, és hozzáadja azt.

A kód negyedik blokkja a kezdő [és végponti](/javascript/api/azure-maps-control/atlas.data.point) objektumokat hozza létre, és hozzáadja őket az adatforrás-objektumhoz.

A vonal a LineString egyik [funkciója](/javascript/api/azure-maps-control/atlas.data.feature) . A [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) az  [adatforrásban](/javascript/api/azure-maps-control/atlas.source.datasource) sorokként burkolt vonalakat jeleníti meg a térképen. A kód negyedik blokkja létrehoz egy sor réteget a térképhez, és hozzáadja azt. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

A [szimbólum-réteg](/javascript/api/azure-maps-control/atlas.layer.symbollayer) szövegeket vagy ikonokat [használ a pont](/javascript/api/azure-maps-control/atlas.source.datasource)-alapú adatforrásba becsomagolt megjelenítéshez. A szöveg vagy az ikon szimbólumként jelenik meg a térképen. A kód ötödik blokkja egy szimbólum réteget hoz létre, és hozzáadja a térképhez.

A kód hatodik blokkja lekérdezi a Azure Maps útválasztási szolgáltatást, amely a [szolgáltatás modul](how-to-use-services-module.md)részét képezi. A RouteURL [calculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) metódusa a kezdő-és végpontok közötti útvonal lekérésére szolgál. Ezután a válaszból kinyert egy GeoJSON-gyűjteményt, amely a metódussal lett kibontva, `geojson.getFeatures()` és a rendszer hozzáadja az adatforráshoz. Ezután a választ útvonalként jeleníti meg a térképen. További információ a vonal a térképhez való hozzáadásáról: [vonal hozzáadása a térképhez](map-add-line-layer.md).

A kód utolsó blokkja a Térkép [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával állítja be a leképezés határait.

Az útvonal-lekérdezés, az adatforrás, a szimbólum, a vonal és a kamera határai az esemény- [figyelőn](/javascript/api/azure-maps-control/atlas.map#events)belül jönnek létre. A kód szerkezete biztosítja, hogy az eredmények csak a Térkép teljes betöltése után jelenjenek meg.

## <a name="query-the-route-via-fetch-api"></a>Az útvonal lekérdezése a fetch API használatával

<iframe height='500' scrolling='no' title='Az A és B közötti útvonalak megjelenítése térképen' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a (z) Azure Maps () alapján a CodePen-on található <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>, a-tól B-ig mutató utasításokat</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz és hozzáadja a térképhez egy [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) objektumot.

A harmadik kódú blokk létrehozza az útvonal kezdő és célhelyét. Ezután hozzáadja azokat az adatforráshoz. A [addPins](/javascript/api/azure-maps-control/atlas.map)használatával kapcsolatos utasításokért tekintse [meg a térképen a PIN-kód hozzáadása](map-add-pin.md) című témakört.

A [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) az  [adatforrásban](/javascript/api/azure-maps-control/atlas.source.datasource) sorokként burkolt vonalakat jeleníti meg a térképen. A kód negyedik blokkja létrehoz egy sor réteget a térképhez, és hozzáadja azt. Lásd: egy sor rétegének tulajdonságai a következő helyen: [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

A [szimbólum-réteg](/javascript/api/azure-maps-control/atlas.layer.symbollayer) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az [adatforrásban](/javascript/api/azure-maps-control/atlas.source.datasource) szimbólumként a térképen. A kód ötödik blokkja egy szimbólum réteget hoz létre, és hozzáadja a térképhez. Tekintse meg a szimbólumok rétegének tulajdonságait a következő helyen: [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

A következő kódrészlet létrehozza `SouthWest` és `NorthEast` rámutat a kezdő és a célhelyről, és beállítja a Térkép határait a Térkép [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságának használatával.

A kód utolsó blokkja a [beolvasás API](https://fetch.spec.whatwg.org/) -t használja a [Azure Maps Route API](/rest/api/maps/route/getroutedirections)-ra irányuló keresési kérések elvégzéséhez. A rendszer elemzi a választ. Ha a válasz sikeres volt, a szélességi és a hosszúsági adatokat a rendszer egy sor létrehozásához használja a pontok összekapcsolásával. Ekkor a rendszer hozzáadja az adatforráshoz a sor-adatforrást, hogy az útvonalat a térképen jelenítse meg. Útmutatásért lásd: [vonal hozzáadása a térképen](map-add-line-layer.md) .

Az útvonal-lekérdezés, az adatforrás, a szimbólum, a vonal és a kamera határai az esemény- [figyelőn](/javascript/api/azure-maps-control/atlas.map#events)belül jönnek létre. Azt szeretnénk, hogy az eredmények a Térkép teljes betöltése után is megjelenjenek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások az útválasztási szolgáltatás használatához](how-to-use-best-practices-for-search.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](/javascript/api/azure-maps-control/atlas.map)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Forgalom megjelenítése a térképen](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakció a Térkép-egér eseményeivel](./map-events.md)