---
title: 'Oktatóanyag: geokerítésen létrehozása és eszközök nyomon követése Microsoft Azure térképen'
description: Útmutató a geokerítésen beállításához. Tekintse meg, hogyan követheti nyomon az eszközöket a geokerítésen viszonyítva a Azure Maps térbeli szolgáltatás használatával
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 759adea3cf34b79c76b6facec3bd4626ca54107e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625032"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Oktatóanyag: Geokerítés beállítása az Azure Maps használatával

Ez az oktatóanyag végigvezeti a Azure Maps geokerítésen-szolgáltatások létrehozásának és használatának alapjain. Ezt a következő forgatókönyv kontextusában hajtja végre:

*Az építkezési hely kezelőjének nyomon kell követnie a berendezéseket, ahogy belép, és elhagyja az építkezés területeit. Amikor egy berendezés kilép vagy beírja ezeket a kerületeket, a rendszer e-mailben értesítést küld az Operations Managernek.*

A Azure Maps számos szolgáltatást biztosít az építkezési terület bevezetéséhez és az onnan való kilépéshez szükséges berendezések nyomon követésének támogatásához. Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Töltse fel az [Geokerítések GeoJSON-adatok](geofence-geojson.md) körét, amelyek meghatározzák a figyelni kívánt építkezési területeket. Az [Adatfeltöltő API](/rest/api/maps/data/uploadpreview) -val geofences-koordinátákat tölthet fel a Azure Maps-fiókjába.
> * Állítson be két [logikai alkalmazást](../event-grid/handler-webhooks.md#logic-apps) , amely indításkor e-mailben értesítést küld az építkezési hely Operations managernek, amikor a berendezések belépnek és kilépnek a geokerítésen területről.
> * A [Azure Event Grid](../event-grid/overview.md) segítségével előfizethet a Azure Maps geokerítésen tartozó belépési és kilépési eseményekre. Két webhook esemény-előfizetést állít be, amelyek meghívja a két logikai alkalmazásban definiált HTTP-végpontokat. A Logic apps ezt követően elküldi a megfelelő e-mailes értesítéseket azokról a készülékekről, amelyek a geokerítésen kívülre kerülnek.
> * A [Search Geokerítésen Get API](/rest/api/maps/spatial/getgeofence) -val értesítéseket kaphat, amikor egy berendezés kilép, és belép a geokerítésen területekre.

## <a name="prerequisites"></a>Előfeltételek

1. [Hozzon létre egy Azure Maps fiókot](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="upload-geofencing-geojson-data"></a>Geokerítések GeoJSON-adatok feltöltése

Ebben az oktatóanyagban feltölti az a-t tartalmazó geokerítések GeoJSON-adatokkal `FeatureCollection` . A `FeatureCollection` két geofences tartalmaz, amelyek az építkezési helyen lévő sokszögeket határozzák meg. Az első geokerítésen nem rendelkezik időbeli lejárattal vagy korlátozásokkal. A másodikat csak munkaidőben lehet lekérdezni (9:00 – 5:00 PM a csendes-óceáni időzónában), és a továbbiakban nem lesz érvényes a 2022. január 1. után. A GeoJSON formátumával kapcsolatos további információkért lásd: [Geokerítések GeoJSON-adatok](geofence-geojson.md).

>[!TIP]
>Bármikor frissítheti a geokerítések adatait. További információ: [az Adatfeltöltő API](/rest/api/maps/data/uploadpreview).

1. Nyissa meg a Poster alkalmazást. A lap tetején válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet. Nevezze el a gyűjteményt, és válassza a **Létrehozás** lehetőséget.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés** gombra.

3. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a geokerítések-adatok Azure Mapsba való feltöltéséhez. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Az `geojson` URL-címben szereplő paraméter a feltöltött adatformátumot jelöli.

4. Válassza a **törzs** fület. Válassza a **RAW** lehetőséget, majd a **JSON** -t bemeneti formátumként. Másolja ki és illessze be az alábbi GeoJSON-beállításokat a **szövegtörzs** területére:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Kattintson a **Küldés** gombra, és várjon, amíg a rendszer feldolgozza a kérést. A kérelem befejezésekor lépjen a válasz **fejlécek** lapjára. Másolja a **hely** kulcsának értékét, amely a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Az API-hívás állapotának megtekintéséhez hozzon létre egy **Get** http-kérelmet a on `status URL` . A hitelesítéshez hozzá kell fűzni az elsődleges előfizetési kulcsot az URL-címhez. A **Get** kérelemnek a következő URL-címnek kell megjelennie:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Ha a **Get** HTTP-kérelem sikeresen befejeződik, a egy értéket ad vissza `resourceLocation` . A `resourceLocation` tartalmazza a `udid` feltöltött tartalom egyedi tartalmát. Mentse ezt a `udid` Get GEOKERÍTÉSEN API lekéréséhez az oktatóanyag utolsó szakaszában. A `resourceLocation` következő lépésben használhatja az URL-címet is az erőforrás metaadatainak lekéréséhez.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. A tartalom metaadatainak beolvasásához hozzon létre egy **Get** HTTP-kérést a `resourceLocation` 7. lépésben lekért URL-címen. Ügyeljen arra, hogy az elsődleges előfizetési kulcsot az URL-címhez fűzze a hitelesítéshez. A **Get** kérelemnek a következő URL-címnek kell megjelennie:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Ha a **Get** HTTP-kérelem sikeresen befejeződik, a válasz törzse a `udid` 7. lépésben megadott értéket fogja tartalmazni `resourceLocation` . Emellett a jövőben a tartalom eléréséhez és letöltéséhez szükséges helyet, valamint a tartalommal kapcsolatos egyéb metaadatokat is tartalmazza. Az általános válasz példája:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Munkafolyamatok létrehozása a Azure Logic Appsban

Ezután két [logikai alkalmazás](../event-grid/handler-webhooks.md#logic-apps) -végpontot hoz létre, amelyek e-mail-értesítést indítanak. A következő lépésekkel hozhatja létre az elsőt:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Azure Portal bal felső sarkában válassza az **erőforrás létrehozása** lehetőséget.

3. A **Keresés a piactéren** mezőbe írja be a **Logic app** kifejezést.

4. Az eredmények közül válassza a **logikai alkalmazás**  >  **létrehozása** lehetőséget.

5. A **logikai alkalmazás** lapon adja meg a következő értékeket:
    * Az ehhez a logikai alkalmazáshoz használni kívánt **előfizetés** .
    * Az **erőforráscsoport** neve ehhez a logikai alkalmazáshoz. Dönthet úgy, hogy **újat hoz létre** , vagy **meglévő erőforráscsoportot használ** .
    * A logikai alkalmazás **logikai alkalmazásának neve** . Ebben az esetben használja `Equipment-Enter` a nevet.

    Ebben az oktatóanyagban minden más értéket meg kell őrizni az alapértelmezett beállításokon.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Képernyőkép a logikai alkalmazás létrehozásáról.":::

6. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Tekintse át a beállításokat, és válassza a **Létrehozás** lehetőséget a telepítés elküldéséhez. Ha a telepítés sikeresen befejeződött, válassza az **Ugrás az erőforráshoz** lehetőséget. A **Logic app Designer alkalmazásban** végezhető el.

7. Válasszon ki egy trigger típust. Görgessen le a **Start with a Common trigger** szakaszhoz. Válassza ki **a HTTP-kérelem fogadásának időpontját**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Képernyőkép a logikai alkalmazás HTTP-triggerének létrehozásáról.":::

8. A Logic app Designer jobb felső sarkában válassza a **Mentés** lehetőséget. A **http post URL-cím** automatikusan létrejön. Mentse az URL-címet. Egy esemény-végpont létrehozásához a következő szakaszban szüksége lesz rá.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Képernyőkép a Logic app HTTP-kérelem URL-címéről és a JSON-ról.":::

9. Válassza az **+ új lépés** lehetőséget. Most válasszon egy műveletet. Írja be `outlook.com email` a keresőmezőbe a kifejezést. A **műveletek** listában görgessen le, és válassza az **E-mail küldése (v2)** lehetőséget.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Képernyőkép a Logic app Designer létrehozásáról.":::

10. Jelentkezzen be az Outlook-fiókjába. Ügyeljen arra, hogy az **Igen** lehetőség kiválasztásával engedélyezze a logikai alkalmazás számára a fiók elérését. Adja meg az e-mailek küldésére szolgáló mezőket.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Képernyőkép a logikai alkalmazás e-mail-küldési lépésének létrehozásáról.":::

    >[!TIP]
    > Az `geometryId` `deviceId` e-mail-értesítésekben a GeoJSON (például vagy) kérhető le. A Logic Apps konfigurálható úgy, hogy beolvassa a Event Grid által eljuttatott fájlokat. Az események e-mail-értesítésekbe való felhasználása és a Logic Apps konfigurálásával kapcsolatos információkért lásd [: oktatóanyag: e-mail-értesítések küldése az Azure IoT hub eseményekről Event Grid és Logic Apps használatával](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. A Logic app Designer bal felső sarkában válassza a **Mentés** lehetőséget.

Ha egy második logikai alkalmazást szeretne létrehozni, amely értesíti a kezelőt, ha a berendezés kilép az építkezési helyről, ismételje meg a 3-11. lépést. Nevezze el a logikai alkalmazást `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Azure Maps esemény-előfizetések létrehozása

A Azure Maps [három eseménytípus](../event-grid/event-schema-azure-maps.md)használatát támogatja. Itt két különböző esemény-előfizetést kell létrehoznia: egyet a geokerítésen-be, és egyet a geokerítésen kilépési eseményeihez.

A következő lépések bemutatják, hogyan hozhat létre esemény-előfizetést a geokerítésen az események megadásakor. A geokerítésen kilépési eseményekre való előfizetéshez hasonló módon ismételje meg a lépéseket.

1. Nyissa meg Azure Maps-fiókját. Az irányítópulton válassza az **előfizetések** lehetőséget. Válassza ki az előfizetés nevét, majd válassza az **események** lehetőséget a beállítások menüből.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Képernyőkép a Azure Maps-fiók eseményeiről.":::

2. Esemény-előfizetés létrehozásához válassza az események lap **+ esemény előfizetése** elemét.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Képernyőkép Azure Maps esemény-előfizetés létrehozásáról.":::

3. Az **esemény-előfizetés létrehozása** lapon adja meg a következő értékeket:
    * Az esemény-előfizetés **neve** .
    * Az **esemény sémájának** *Event Grid sémának* kell lennie.
    * Az esemény-előfizetéshez tartozó **Rendszertéma neve** , amely ebben az esetben a következő: `Contoso-Construction` .
    * Az **eseménytípus szűréséhez** válassza `Geofence Entered` az esemény típusa lehetőséget.
    * A **végpont típusa** beállításnál válassza a lehetőséget `Web Hook` .
    * A **végpont** esetében másolja a logikai alkalmazáshoz az előző szakaszban létrehozott végponthoz tartozó http-post URL-címet. Ha elfelejtette menteni, visszatérhet a Logic app Designerbe, és átmásolhatja a HTTP-trigger lépésből.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Képernyőkép a Azure Maps Events-előfizetés részleteiről.":::

4. Válassza a **Létrehozás** lehetőséget.

Ismételje meg a 1-4. lépést az előző szakaszban létrehozott logikai alkalmazás kilépési végpontján. A 3. lépésnél ügyeljen arra, hogy válassza ki `Geofence Exited` az esemény típusát.

## <a name="use-spatial-geofence-get-api"></a>Térbeli Geokerítésen Get API használata

A [térbeli Geokerítésen Get API](/rest/api/maps/spatial/getgeofence) használatával e-mailes értesítéseket küldhet az Operations managernek, amikor egy berendezés belép vagy kilép a geofences.

Minden berendezéshez tartozik egy `deviceId` . Ebben az oktatóanyagban egyetlen berendezést kell nyomon követnie, amely egyedi AZONOSÍTÓval rendelkezik `device_1` .

Az alábbi ábrán a berendezés öt helye látható a *kezdő* helyétől kezdve, ami valahol a geofences kívül esik. Ebben az oktatóanyagban a *kezdő* hely nem definiált, mert az adott helyen nem kérdezi le az eszközt.

Amikor lekérdezi a [térbeli Geokerítésen Get API](/rest/api/maps/spatial/getgeofence) -t egy olyan berendezési hellyel, amely a kezdeti geokerítésen bejegyzést vagy kilépést jelzi, Event Grid meghívja a megfelelő logikai alkalmazás-végpontot, hogy e-mail értesítést küldjön az Operations Managernek.

A következő részekben az API-kérelmek a berendezések öt különböző földrajzi koordinátáit használják.

![Geokerítésen-leképezés diagramja Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Berendezések helye 1 (47.638237,-122,132483)

1. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Legyen az *1. hely*. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Győződjön meg arról, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal van lecserélve, és `{udid}` az `udid` [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentette.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Kattintson a **Küldés** gombra. A válasz ablakban a következő GeoJSON jelennek meg.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

Az előző GeoJSON-válaszban a fő hely geokerítésen negatív távolság azt jelenti, hogy a berendezés a geokerítésen belül található. A alhelyről geokerítésen pozitív távolság azt jelenti, hogy a berendezés kívül esik a alhelyen geokerítésen. Mivel ez az első alkalom, hogy ez az eszköz a fő hely geokerítésen belül található, a `isEventPublished` paraméter értéke `true` . Az Operations Manager e-mailben értesítést kap arról, hogy a berendezés megadta a geokerítésen.

### <a name="location-2-4763800-122132531"></a>2. hely (47.63800,-122,132531)

1. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . A *2. helyet* tegye elérhetővé. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Győződjön meg arról, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal van lecserélve, és `{udid}` az `udid` [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentette.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON jelenik meg a válasz ablakban:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Az előző GeoJSON-válaszban a berendezés a fő hely geokerítésen maradt, és nem adta meg a geokerítésen alhelyét. Ennek eredményeképpen a paraméter a következőre `isEventPublished` van beállítva `false` , és az Operations Manager nem kap e-mail-értesítéseket.

### <a name="location-3-4763810783315048-12213336020708084"></a>3. hely (47.63810783315048,-122.13336020708084)

1. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Tegye meg a *3. helyet*. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Győződjön meg arról, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal van lecserélve, és `{udid}` az `udid` [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentette.

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON jelenik meg a válasz ablakban:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Az előző GeoJSON-válaszban a berendezés a fő hely geokerítésen maradt, de megadta a geokerítésen alhelyét. Ennek eredményeképpen a paraméter a következőre `isEventPublished` van beállítva: `true` . Az Operations Manager e-mail-értesítést kap, amely jelzi, hogy a berendezés geokerítésen adott meg.

>[!NOTE]
>Ha a berendezés munkaidő után átkerült az alhelyre, egyetlen eseményt sem tesz közzé, és az Operations Manager nem kap értesítést.  

### <a name="location-4-47637988-1221338344"></a>4. hely (47.637988,-122,1338344)

1. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . 4. legyen a *helye*. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Győződjön meg arról, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal van lecserélve, és `{udid}` az `udid` [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentette.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON jelenik meg a válasz ablakban:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Az előző GeoJSON-válaszban a berendezés a fő hely geokerítésen maradt, de kilépett a geokerítésen alhelyről. Figyelje meg azonban, hogy az `userTime` érték a `expiredTime` geokerítésen-adatként megadott módon van megadva. Ennek eredményeképpen a paraméter a (z) értékre `isEventPublished` van beállítva `false` , és az Operations Manager nem kap e-mail-értesítést.

### <a name="location-5-4763799--122134505"></a>5. hely (47,63799,-122,134505)

1. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . 5. tegye a *helyet*. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Győződjön meg arról, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal van lecserélve, és `{udid}` az `udid` [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentette.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON jelenik meg a válasz ablakban:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Az előző GeoJSON-válaszban a berendezés kilépett a fő hely geokerítésen. Ennek eredményeképpen a paraméter a (z) értékre `isEventPublished` van beállítva `true` , és az Operations Manager e-mail-értesítést kap arról, hogy a berendezés kilépett egy geokerítésen.


Az [e-mailes értesítéseket Event Grid és Logic apps is elküldheti](../event-grid/publish-iot-hub-events-to-logic-apps.md) , és a Event Grid a Azure Maps használatával megtekintheti [a támogatott események kezelőit](../event-grid/event-handlers.md) .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tartalomtípusok kezelése Azure Logic Appsban](../logic-apps/logic-apps-content-type.md)