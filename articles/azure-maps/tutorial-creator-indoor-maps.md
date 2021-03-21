---
title: 'Oktatóanyag: a Microsoft Azure Maps Creator (előzetes verzió) használata beltéri térképek létrehozásához'
description: Oktatóanyag a Microsoft Azure Maps Creator (előzetes verzió) használatáról beltéri térképek létrehozásához
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ac53dab29feddd36a95b8b2b041caaf5c3598d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720137"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>Oktatóanyag: a Creator (előzetes verzió) használata beltéri térképek létrehozásához

> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az oktatóanyag bemutatja, hogyan hozhat létre beltéri térképeket. Ebből az oktatóanyagból megtudhatja, hogyan használhatja az API-t a következőkre:

> [!div class="checklist"]
> * A beltéri Térkép rajzolási csomag feltöltése
> * A rajzolási csomag átalakítása leképezési adatként
> * Adatkészlet létrehozása a Térkép adataiból
> * Tileset létrehozása az adatkészlet adataiból
> * Az Azure Maps web Feature Service (WFS) API lekérdezése a térképi funkciók megismeréséhez
> * Szolgáltatás stateset létrehozása a térképi funkciók és az adatkészlet adatai alapján
> * A szolgáltatás stateset frissítése

## <a name="prerequisites"></a>Előfeltételek

Beltéri térképek létrehozása:

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.
3. [Létrehozó (előzetes) erőforrás létrehozása](how-to-manage-creator.md)
4. Töltse le a [minta rajzolási csomagot](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip).

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

>[!IMPORTANT]
> Előfordulhat, hogy a dokumentumban szereplő API URL-címeket a létrehozó erőforrás helye alapján kell módosítani. További részletekért lásd: [hozzáférés a Creator Serviceshez](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Rajzfájl feltöltése

Az [Adatfeltöltő API](/rest/api/maps/data/uploadpreview) -val feltöltheti a rajzobjektumot Azure Maps erőforrásokra.

Az adatfeltöltő API egy hosszú ideig futó tranzakció, amely megvalósítja az itt definiált mintát. A művelet befejezését követően a használatával `udid` elérheti a feltöltött csomagot a konvertáláshoz. Az alábbi lépésekkel szerezheti be a következőt: `udid` .

1. Nyissa meg a Poster alkalmazást. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés** gombra.

3. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a rajzfájl a Azure Maps szolgáltatásba való feltöltéséhez. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. A **fejlécek** lapon a kulcs értékét kell megadnia `Content-Type` . A rajzolási csomag egy tömörített mappa, ezért használja az `application/octet-stream` értéket. A **törzs** lapon válassza a **bináris** elemet. Kattintson a **fájl kiválasztása** lehetőségre, és válasszon egy rajzfájl-csomagot.

     ![adatkezelés](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Kattintson a kék **Küldés** gombra, és várjon, amíg a rendszer feldolgozza a kérést. A kérés befejeződése után lépjen a válasz **fejlécek** lapjára. Másolja a **hely** kulcsának értékét, amely a `status URL` .

6. Az API-hívás állapotának megtekintéséhez hozzon létre egy **Get** http-kérelmet a on `status URL` . A hitelesítéshez hozzá kell fűzni az elsődleges előfizetési kulcsot az URL-címhez. A **Get** kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Ha a **Get** HTTP-kérelem sikeresen befejeződik, a visszaadja a-t `resourceLocation` . A `resourceLocation` tartalmazza a `udid` feltöltött tartalom egyedi tartalmát. A `resourceLocation` következő lépésben használhatja az URL-címet is az erőforrás metaadatainak lekéréséhez.

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

9. Ha a HTTP-kérés **beolvasása** sikeresen befejeződött, a válasz törzse tartalmazza a `udid` `resourceLocation` 7. lépésben megadott helyet, a tartalom elérésének és letöltésének helyét, valamint a tartalommal kapcsolatos egyéb metaadatokat, például a létrehozott/frissített dátumot, a méretet stb. Az általános válasz példája:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Rajzfájl konvertálása

 Most, hogy a rajzfájl feltöltése megtörtént, a `udid` rendszer a feltöltött csomagra konvertálja a csomagot a leképezési adatként. Az átalakítási API egy hosszú ideig futó tranzakciót használ, amely megvalósítja az [itt](creator-long-running-operation.md)definiált mintát. Ha a művelet befejeződik, a `conversionId` segítségével elérheti a konvertált adategységeket. Az alábbi lépésekkel szerezheti be a következőt: `conversionId` .

1. Válassza az **Új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a feltöltött rajzfájl leképezési adataiba való átalakításához. Használja a `udid` csomagot a feltöltött csomaghoz.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > Előfordulhat, hogy a dokumentumban szereplő API URL-címeket a létrehozó erőforrás helye alapján kell módosítani. További részletekért lásd: [hozzáférés a Creator Serviceshez (előzetes verzió) ](how-to-manage-creator.md#access-to-creator-services).
    > Ha hibakódot kap, győződjön meg `"RequiresCreatorResource"` arról, hogy [Azure Maps létrehozó erőforrást](how-to-manage-creator.md) Azure Maps fiókjában.

3. Kattintson a **Küldés** gombra, és várjon, amíg a rendszer feldolgozza a kérést. A kérés befejeződése után lépjen a válasz **fejlécek** lapjára, és keresse meg a **hely** kulcsát. Másolja ki a **hely** kulcsának értékét, amely a `status URL` konverziós kérelemhez tartozó érték. Ezt a következő lépésben fogja használni.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="A helykód értékének másolása":::

4. Indítson el **egy új http** -metódust a Building (szerkesztő) lapon. Fűzze hozzá Azure Maps elsődleges előfizetési kulcsát a következőhöz: `status URL` . Hozzon igénybe egy **Get** -kérést a `status URL` 3. lépésben átmásolt helyen. A a `status URL` következő URL-címhez hasonlít:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Ha az átalakítási folyamat még nem fejeződött be, a következő JSON-válaszhoz hasonlóan láthatja:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Miután a kérés sikeresen befejeződik, megjelenik egy sikeres állapotjelző üzenet a válasz törzsében.  Másolja az `conversionId` elemet a `resourceLocation` konvertált csomag URL-címéből. A `conversionId` más API-k használják az átalakított térképi információ elérésére.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>A Poster alkalmazás nem támogatja natív módon a HTTP hosszú ideig futó kérelmeket. Ennek eredményeképpen hosszú késleltetést tapasztalhat, miközben a **Get** -kérést az állapot URL-címére küldi.  Várjon körülbelül harminc másodpercig, és ismételje meg a **Küldés** gombot, amíg a válasz sikeres vagy sikertelen lesz.

A minta rajzolási csomagot hibák vagy figyelmeztetések nélkül kell konvertálni. Ha azonban a saját rajzzal kapcsolatos hibákat vagy figyelmeztetéseket kap, a JSON-válaszban megjelenik egy hivatkozás, amely a [Rajzolási hibák](drawing-error-visualizer.md)megjelenítésére szolgál. A rajzolási hibák láthatóvá tétele lehetővé teszi a hibák és a figyelmeztetések részleteinek vizsgálatát. Az átalakítási hibák és a figyelmeztetések feloldásával kapcsolatos javaslatokért lásd a [rajz átalakításával kapcsolatos hibákat és a figyelmeztetéseket](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Adathalmaz létrehozása

Az adatkészlet térképi funkciók, például épületek, szintek és szobák gyűjteménye. Adatkészlet létrehozásához használja az [adatkészlet létrehozása API](/rest/api/maps/dataset/createpreview)-t. Az adatkészlet létrehozása API veszi át a `conversionId` konvertált rajzfájl értékét, és visszaadja a `datasetId` létrehozott adatkészlet egy részét. Az alábbi lépések bemutatják, hogyan hozhat létre adatkészletet.

1. A Poster alkalmazásban válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. Hozzon létre **post** -kérést az [adatkészlet Create API](/rest/api/maps/dataset/createpreview) -hoz egy új adatkészlet létrehozásához. A kérelem elküldése előtt fűzze hozzá az előfizetési kulcsot és a `conversionId` -t az `conversionId` 5. lépésben az átalakítási folyamat során kapott értékhez.  A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Szerezze be a `statusURL` Válasz **fejlécek** **helye** kulcsát.

4. Szerezze be a **Get** -kérelmet a (z) `statusURL` beszerzéséhez `datasetId` . Fűzze hozzá Azure Maps elsődleges előfizetési kulcsát a hitelesítéshez. A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Ha a **Get** HTTP-kérelem sikeresen befejeződik, a válasz fejléce a létrehozott adatkészlet számára is tartalmazni fogja a `datasetId` következőt:. Másolja a `datasetId` . A `datasetId` tileset létrehozásához a-t kell használnia.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Tileset létrehozása

A tileset a térképen megjelenített vektoros csempék halmaza. A tilesets a meglévő adatkészletekből jönnek létre. Egy tileset azonban független attól a DataSet adatkészlettől, amelyről forrásból származik. Ha törli az adatkészletet, a tileset továbbra is fennáll. Tileset létrehozásához kövesse az alábbi lépéseket:

1. A Poster alkalmazásban válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. Tegye a **post** kérést a Builder (szerkesztő) lapon. A kérelem URL-címének a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Hozzon igénybe egy **Get** -kérést a `statusURL` tileset. Fűzze hozzá Azure Maps elsődleges előfizetési kulcsát a hitelesítéshez. A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Ha a **Get** HTTP-kérelem sikeresen befejeződik, a válasz fejléce a `tilesetId` létrehozott tileset fogja tartalmazni. Másolja a `tilesetId` .

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Adatkészletek lekérdezése a WFS API-val

 Az adatkészleteket a  [WFS API](/rest/api/maps/wfs)használatával kérdezheti le. A WFS API-val lekérdezheti a szolgáltatások gyűjteményeit, egy adott gyűjteményt vagy egy szolgáltatás **azonosítóját** tartalmazó adott szolgáltatást. A szolgáltatás **azonosítója** egyedileg azonosítja a szolgáltatást az adatkészleten belül. A rendszer például annak azonosítására szolgál, hogy melyik szolgáltatási állapotot kell frissíteni egy adott stateset.

1. A Poster alkalmazásban válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. **Get** kéréssel megtekintheti az adatkészletben található gyűjtemények listáját. Cserélje le `<dataset-id>` a helyére a t `datasetId` . A helyőrző helyett használja a Azure Maps elsődleges kulcsát. A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. A GeoJSON formátumban fog szerepelni a válasz törzse, és az adatkészlet összes gyűjteményét fogja tartalmazni. Az egyszerűség kedvéért a példa csak a gyűjteményt jeleníti meg `unit` . Ha meg szeretné tekinteni az összes gyűjteményt tartalmazó példát, tekintse meg a [Collections API WFS leírását](/rest/api/maps/wfs/collectiondescriptionpreview). Ha többet szeretne megtudni a gyűjteményekről, kattintson a elemen belüli URL-címek bármelyikére `link` .

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. **Get** -kérést készíthet a `unit` szolgáltatás-gyűjteményekhez.  Cserélje le `{datasetId}` a helyére a t `datasetId` . A helyőrző helyett használja a Azure Maps elsődleges kulcsát. A válasz törzse fogja tartalmazni a gyűjtemény összes funkcióját `unit` . A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Másolja `id` ki egy olyan egység szolgáltatás szolgáltatását, amely dinamikusan módosítható stílusú tulajdonságokkal rendelkezik.  Mivel az egység kihasználtsági állapota és hőmérséklete dinamikusan frissíthető, ezt `id` a funkciót a következő szakaszban fogjuk használni. A következő példában a funkció a `id` "UNIT26". Ennek a funkciónak a stílusa a következő: állapotok, és a funkció segítségével stateset teheti.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Szolgáltatás stateset létrehozása

1. A Poster alkalmazásban válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. Hozzon létre **post** -kérelmet a [create Stateset API](/rest/api/maps/featurestate/createstatesetpreview)-hoz. Használja a `datasetId` módosítani kívánt állapotot tartalmazó adatkészletet. A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. A **post** kérelem **fejlécében** állítsa a következőre `Content-Type` : `application/json` . A **törzsben** adja meg az alábbi nyers JSON-stílusokat a és az állapot változásainak megfelelően `occupied` `temperature` . Ha elkészült, kattintson a **Küldés** gombra.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Másolja a `statesetId` elemet a válasz törzséről.

5. Hozzon létre egy **post** -kérést az állapot frissítéséhez: adja át a statesetId és a funkciót a `ID` Azure Maps előfizetési kulcsával. A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. A **post** kérelem **fejlécében** állítsa a következőre `Content-Type` : `application/json` . A **post** kérelem **törzsében** másolja ki és illessze be a JSON-t az alábbi mintában.

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

    >[!NOTE]
    > A frissítés csak akkor lesz mentve, ha az idő feladási bélyegzője az előző kérelem időbélyegzője után van. Bármilyen, a létrehozás során korábban konfigurált kulcsnév átadható.

7. A sikeres frissítés után HTTP-állapotkódot fog kapni `200 OK` . Ha egy beltéri térképhez  [dinamikus stílus van megvalósítva](indoor-map-dynamic-styling.md) , a frissítés a megadott időbélyegzőn jelenik meg a megjelenített térképen.

A szolgáltatások [beolvasása API](/rest/api/maps/featurestate/getstatespreview) lehetővé teszi egy szolgáltatás állapotának lekérését a funkciójának használatával `ID` . A stateset és erőforrásait a [szolgáltatás állapota törlés API](/rest/api/maps/featurestate/deletestatesetpreview)használatával is törölheti.

Ha többet szeretne megtudni a jelen cikkben tárgyalt különböző Azure Maps Creator-szolgáltatásokról (előzetes verzió), a [Creator beltéri térképek](creator-indoor-maps.md)című témakörben talál további információt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

A beltéri térképek modul használatának megismeréséhez tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A beltéri térképek modul használata](how-to-use-indoor-module.md)