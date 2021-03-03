---
title: Dinamikus stílus implementálása Azure Maps Creator (előzetes verzió) beltéri térképeken
description: Ismerje meg, hogyan valósítható meg a dinamikus stílus a Creator (előzetes) beltéri térképeken
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a23c492d4a81703c0dc6612928a56b5b31d52cae
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726315"
---
# <a name="implement-dynamic-styling-for-creator-preview-indoor-maps"></a>Dinamikus stílus implementálása a Creator (előzetes) beltéri térképeken

> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps Creator [szolgáltatás állapotának szolgáltatása](/rest/api/maps/featurestate) lehetővé teszi a stílusok alkalmazását a beltéri Térkép adatfunkcióinak dinamikus tulajdonságai alapján.  Például megjelenítheti a létesítmény-tárgyalókat egy adott színnel, hogy tükrözze a kihasználtsági állapotot. Ebben a cikkben bemutatjuk, hogyan lehet dinamikusan megjeleníteni a beltéri Térkép funkciókat a [szolgáltatás állapotának](/rest/api/maps/featurestate) és a [beltéri webes modulnak](how-to-use-indoor-module.md)a használatával.

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps-fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.
3. [Létrehozó (előzetes) erőforrás létrehozása](how-to-manage-creator.md)
4. Töltse le a [minta rajzolási csomagot](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Hozzon létre egy fedett térképet](tutorial-creator-indoor-maps.md) a és a beszerzéséhez `tilesetId` `statesetId` .
6. Hozzon létre egy webalkalmazást a [beltéri Térkép modul használatának](how-to-use-indoor-module.md)lépéseit követve.

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="implement-dynamic-styling"></a>Dinamikus stílus implementálása

Az előfeltételek teljesítése után egy egyszerű webalkalmazást kell konfigurálnia az előfizetési kulccsal, `tilesetId` és `statesetId` .

### <a name="select-features"></a>Funkciók kiválasztása

A dinamikus formázás megvalósításához a szolgáltatásnak, például egy értekezletnek vagy egy konferenciateremnek kell megfelelnie `id` . A szolgáltatás `id` segítségével frissítheti a szolgáltatás dinamikus tulajdonságát vagy *állapotát* . Az adatkészletekben definiált funkciók megtekintéséhez a következő módszerek egyikét használhatja:

* WFS API (webszolgáltatás szolgáltatás). Az adatkészleteket a WFS API használatával kérdezheti le. A WFS követi a Nyílt térinformatikai konzorcium API-funkciókat. A WFS API hasznos lehet egy adatkészleten belüli szolgáltatások lekérdezéséhez. A WFS használatával például megkeresheti az adott létesítmény és a padló szintjének összes közepes méretű tárgyalóját.

* Testreszabott kód implementálása, amely lehetővé teszi a felhasználó számára, hogy a webalkalmazás használatával kiválassza a térképen a szolgáltatásokat. Ebben a cikkben ezt a lehetőséget fogjuk használni.  

A következő szkript implementálja az egérkattintási eseményt. A kód a kattintási pont alapján kérdezi le a szolgáltatást `id` . Az alkalmazásban beillesztheti a kódot a Indoor Manager-kódrészlet alá. Futtassa az alkalmazást, és keresse meg a konzolt a rákattintott pont funkciójának beszerzéséhez `id` .

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

A [beltéri Térkép létrehozása](tutorial-creator-indoor-maps.md) oktatóanyag a funkció stateset konfigurálta, hogy fogadja az állapot-frissítéseket `occupancy` .

A következő szakaszban az Office kihasználtsági *állapotát* állíthatja be `UNIT26` `true` . az Office `UNIT27` beállítása a következőre lesz beállítva: `false` .

### <a name="set-occupancy-status"></a>Foglaltság állapotának beállítása

 Most frissítjük a két iroda állapotát, `UNIT26` és `UNIT27` :

1. A Poster alkalmazásban válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. Az állapot frissítéséhez használja a [Feature Update States API](/rest/api/maps/featurestate/updatestatespreview) -t. Adja át a stateset AZONOSÍTÓját és a `UNIT26` két egység egyikét. Fűzze hozzá Azure Maps előfizetési kulcsát. Itt látható az állapot frissítésére szolgáló **post** -kérelem URL-címe:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. A **post** kérelem **fejlécében** állítsa a következőre `Content-Type` : `application/json` . A **post** kérelem **törzsében** írja be a következő nyers JSON-t a szolgáltatás frissítéseivel. A frissítés csak akkor lesz mentve, ha a közzétett időbélyegző az előző szolgáltatás állapotának frissítési kérelmében a szolgáltatáshoz használt időbélyegző után van megadva `ID` . Adja át a "foglalt" `keyName` értéket az értékének frissítéséhez.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. Ismételje meg a 2. és a 3. lépést `UNIT27` a következő JSON használatával.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Dinamikus stílusok megjelenítése térképeken

A böngészőben korábban megnyitott webalkalmazásnak most már tükröznie kell a Térkép funkcióinak frissített állapotát. `UNIT27`a (142) zöld színűnek kell lennie, és `UNIT26` (143) piros színnel kell megjelennie.

![Szabad hely a zöld és a foglalt helyiségben vörös színnel](./media/indoor-map-dynamic-styling/room-state.png)

[Lásd: élő bemutató](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Következő lépések

További információért olvassa el a következőket:

> [!div class="nextstepaction"]
> [Creator (előzetes verzió) beltéri leképezéshez](creator-indoor-maps.md)

Tekintse meg a cikkben említett API-kra mutató hivatkozásokat:

> [!div class="nextstepaction"]
> [Adatok feltöltése](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Adatátalakítás](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Adathalmaz](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Szolgáltatás állapotának beállítása](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS szolgáltatás](creator-indoor-maps.md#web-feature-service-api)