---
title: 'Oktatóanyag: a IoT térbeli elemzésének megvalósítása | Microsoft Azure térképek'
description: Oktatóanyag a IoT Hub integrálásához a Microsoft Azure Maps Service API-kkal
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b5c65035f8b51b53f617d4562fe1982f53f0deec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678272"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Oktatóanyag: a IoT térbeli elemzés megvalósítása Azure Maps használatával

Egy IoT-forgatókönyv esetében gyakori, hogy rögzítse és nyomon követhesse a térben és időben előforduló releváns eseményeket. Ilyenek például a flotta kezelése, az eszközök nyomon követése, a mobilitás és az intelligens városi alkalmazások. Ez az oktatóanyag végigvezeti egy olyan megoldáson, amely a Azure Maps API-k használatával követi nyomon a használatban lévő autóbérlési mozgást.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Hozzon létre egy Azure Storage-fiókot az autó követési adatnaplójának megjelenítéséhez.
> * Töltsön fel egy geokerítésen a Azure Maps adatszolgáltatásba (előzetes verzió) az adatfeltöltő API használatával.
> * Hozzon létre egy hubot az Azure IoT Hubban, és regisztrálja az eszközt.
> * Hozzon létre egy függvényt Azure Functionsban, és alkalmazza az üzleti logikát Azure Maps térbeli elemzés alapján.
> * Fizessen elő a IoT Device telemetria-események az Azure-függvényből Azure Event Grid használatával.
> * A telemetria események szűrése IoT Hub üzenet-útválasztás használatával.

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. [Hozzon létre egy Azure Maps fiókot](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. További információ: [a hitelesítés kezelése Azure Mapsban](how-to-manage-authentication.md).

4. [Hozzon létre egy erőforráscsoportot](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). Ebben az oktatóanyagban az erőforráscsoport- *ContosoRental* nevezjük el, de tetszőleges nevet választhat.

5. Töltse le a [RentalCarSimulation C# projektet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="use-case-rental-car-tracking"></a>Használati eset: autóbérlés követése

Tegyük fel, hogy egy autókölcsönző cég szeretné naplózni a tartózkodási hely adatait, a megtett távolságot és a futó állapotot a bérelt autók számára. A vállalat ezen adatokat is tárolni kívánja, amikor egy autó elhagyja a megfelelő, jóváhagyott földrajzi régiót.

A bérelt autók olyan IoT-eszközökkel vannak ellátva, amelyek rendszeresen küldenek telemetria-IoT Hub. A telemetria tartalmazza az aktuális helyet, és jelzi, hogy az autó motorja fut-e. Az eszköz helyének sémája megfelel a [térinformatikai IoT Plug and Play sémájának](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). A bérleti autó eszközének telemetria sémája a következő JSON-kódra hasonlít:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

Ebben az oktatóanyagban csak egy járművet követhet nyomon. Az Azure-szolgáltatások beállítása után le kell töltenie a [RentalCarSimulation C# projektet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) a Vehicle Simulator futtatásához. A teljes folyamatot, az eseményektől a végrehajtásig, a következő lépésekben összegzi:

1. A járművön belüli eszköz telemetria adatokat küld IoT Hub.

2. Ha az autó motorja fut, a központ közzéteszi a telemetria-információkat a Event Grid.

3. Az Azure-függvényt a rendszer az telemetria eseményeinek esemény-előfizetése miatt indítja el.

4. A függvény naplózza a jármű eszközének földrajzi koordinátáit, az esemény időpontját és az eszköz AZONOSÍTÓját. Ezután a [térbeli Geokerítésen Get API](/rest/api/maps/spatial/getgeofence) -val megállapítja, hogy az autó a geokerítésen kívül vezetett-e. Ha a geokerítésen határain kívül utazott, a függvény az eseménytől kapott helyadatok egy blob-tárolóba történő tárolásához. A függvény lekérdezi a [keresési címeket fordított](/rest/api/maps/search/getsearchaddressreverse) értékre, hogy lefordítsa a koordináta helyét egy utcanév-címnek, és az eszköz többi helyével együtt tárolja.

Az alábbi ábrán a rendszer magas szintű áttekintése látható.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="A rendszerek áttekintésének ábrája.":::

Az alábbi ábra kiemeli a geokerítésen területét. A bérelt autó útvonalát zöld vonallal jelölik.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="A geokerítésen útvonalat ábrázoló ábra":::

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Az autó megsértési követési adatainak tárolásához hozzon létre egy [általános célú v2-es Storage-fiókot](../storage/common/storage-account-overview.md#general-purpose-v2-accounts) az erőforráscsoporthoz. Ha még nem hozott létre erőforráscsoportot, kövesse az [erőforráscsoport létrehozása](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)című témakör utasításait. Ebben az oktatóanyagban az erőforráscsoport *ContosoRental* nevet fogja megtekinteni.

A Storage-fiók létrehozásához kövesse a Storage- [fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)című témakör utasításait. Ebben az oktatóanyagban nevezze el a Storage-fiókot *contosorentalstorage*, de általában bármilyen nevet megadhat.

Ha sikeresen létrehozta a Storage-fiókját, létre kell hoznia egy tárolót a naplózási adatai tárolásához.

1. Lépjen az újonnan létrehozott Storage-fiókra. Az **alapvető** erőforrások szakaszban válassza a **tárolók** hivatkozást.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Képernyőfelvétel a blob Storage tárolói számára.":::

2. A bal felső sarokban válassza a **+ tároló** elemet. Megjelenik egy panel a böngésző jobb oldalán. Nevezze el a tároló *contoso-Rental-logs* nevet, és válassza a **Létrehozás** lehetőséget.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Képernyőkép a blob-tároló létrehozásáról.":::

3. Nyissa meg a Storage-fiók **hozzáférési kulcsok** paneljét, és másolja ki a **Storage-fiók nevét** és a **kulcs** értékét a **key1** szakaszban. Mindkét értékre szüksége lesz az "Azure-függvény létrehozása és Event Grid előfizetés hozzáadása" szakaszban.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Képernyőkép a Storage-fiók nevének és kulcsának másolásáról.":::

## <a name="upload-a-geofence"></a>Geokerítésen feltöltése

Ezután a [Poster alkalmazással](https://www.getpostman.com) [töltse fel a geokerítésen](./geofence-geojson.md) a Azure Mapsba. A geokerítésen határozza meg a bérelt jármű számára az illetékes földrajzi területét. Az Azure-függvény geokerítésen fogja használni annak megállapítására, hogy az autó a geokerítésen területén kívül mozgott-e.

Az alábbi lépéseket követve feltöltheti a geokerítésen a Azure Maps adatfeltöltő API használatával: 

1. Nyissa meg a Poster alkalmazást, és válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet. Nevezze el a gyűjteményt, és válassza a **Létrehozás** lehetőséget.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új ablak létrehozása** ablakban válassza a **kérelem** lehetőséget, majd adja meg a kérelem nevét. Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés** gombra.

3. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a geokerítésen az adatfeltöltő API-ba való feltöltéséhez. Győződjön meg arról, hogy az `{subscription-key}` elsődleges előfizetési kulccsal van lecserélve.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Az URL-cím elérési útja alatt a `geojson` paraméterrel megadott érték a `dataFormat` feltöltött adatformátumot jelöli.

4. A bemeneti formátumhoz válassza a **szövegtörzs**  >  **RAW** lehetőséget, majd a legördülő listából válassza a **JSON** elemet. [Nyissa meg a JSON-adatfájlt](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4), és másolja a JSON-t a törzs szakaszba. Kattintson a **Küldés** gombra.

5. Kattintson a **Küldés** gombra, és várjon, amíg a rendszer feldolgozza a kérést. A kérés befejeződése után lépjen a válasz **headers (fejlécek** ) lapjára. Másolja a **hely** kulcsának értékét, amely a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Az API-hívás állapotának megtekintéséhez hozzon létre egy **Get** http-kérelmet a on `status URL` . A hitelesítéshez hozzá kell fűzni az elsődleges előfizetési kulcsot az URL-címhez. A **Get** kérelemnek a következő URL-címnek kell megjelennie:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Ha a **Get** HTTP-kérelem sikeresen befejeződik, a egy értéket ad vissza `resourceLocation` . A `resourceLocation` tartalmazza a `udid` feltöltött tartalom egyedi tartalmát. Másolja ezt `udid` az oktatóanyagban a későbbi használatra.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A IoT Hub biztonságos és megbízható kétirányú kommunikációt tesz lehetővé egy IoT-alkalmazás és az általa kezelt eszközök között. Ebben az oktatóanyagban információt szeretne kapni a járműről származó eszközről, hogy meghatározza a bérelt autó helyét. Ebben a szakaszban egy IoT hubot hoz létre a *ContosoRental* erőforráscsoporthoz. Ez a központ felelős az eszköz telemetria eseményeinek közzétételéhez.

> [!NOTE]
> Az telemetria események Event Gridon való közzétételének lehetősége jelenleg előzetes verzióban érhető el. Ez a funkció az összes régióban elérhető, a következők kivételével: USA keleti régiója, USA nyugati régiója, Nyugat-Európa, Azure Government, Azure China 21Vianet és Azure Germany.

Az IoT hub *ContosoRental* -erőforráscsoporthoz való létrehozásához kövesse az [IoT hub létrehozása](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub)című témakör lépéseit.

## <a name="register-a-device-in-your-iot-hub"></a>Eszköz regisztrálása az IoT hub-ban

Az eszközök nem tudnak csatlakozni az IoT hubhoz, kivéve, ha az IoT hub Identity registryben vannak regisztrálva. Itt létre kell hoznia egy *InVehicleDevice* nevű egyetlen eszközt. Az eszköz az IoT hub-ban való létrehozásához és regisztrálásához kövesse az [új eszköz regisztrálása az IoT hub-ban](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub)című témakör lépéseit. Ügyeljen arra, hogy az eszköz elsődleges kapcsolódási sztringjét másolja. Erre később még szüksége lesz.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Függvény létrehozása és Event Grid-előfizetés hozzáadása

A Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi a kis kódrészletek (functions) futtatását anélkül, hogy explicit módon kellene kiépíteni vagy kezelni a számítási infrastruktúrát. További információ: [Azure functions](../azure-functions/functions-overview.md).

Egy függvényt egy adott esemény indít el. Itt létre fog hozni egy Event Grid trigger által aktivált függvényt. Hozzon létre kapcsolatot az eseményindító és a függvény között egy esemény-előfizetés létrehozásával IoT Hub eszköz telemetria eseményeihez. Ha egy eszköz telemetria esemény következik be, a rendszer végpontként hívja meg a függvényt, és a korábban a IoT Hub-ban regisztrált eszközre vonatkozó adatokat fogadja.

Itt látható a [függvény által tartalmazott C#-szkript](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Most állítsa be az Azure-függvényt.

1. Az Azure Portal irányítópulton válassza az **erőforrás létrehozása** lehetőséget. A keresés szövegmezőbe írja be a **függvényalkalmazás** kifejezést. Válassza a **függvényalkalmazás**  >  **Létrehozás** elemet.

1. A **függvényalkalmazás** létrehozás lapon nevezze el a Function alkalmazást. Az **erőforráscsoport** területen válassza a **ContosoRental** lehetőséget a legördülő listából. Válassza ki a **.net Core** -t **futtatókörnyezeti veremként**. A lap alján válassza a következő lehetőséget **: >üzemeltetése**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Képernyőkép a Function-alkalmazás létrehozásáról.":::

1. A **Storage-fiók** területen válassza ki az [Azure Storage-fiók létrehozása](#create-an-azure-storage-account)lapon létrehozott Storage-fiókot. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Tekintse át a Function alkalmazás részleteit, és válassza a **Létrehozás** lehetőséget.

1. Az alkalmazás létrehozása után hozzá kell adnia egy függvényt. Nyissa meg a Function alkalmazást. Válassza a **függvények** ablaktáblát. A lap tetején válassza a **+ Hozzáadás** lehetőséget. Megjelenik a függvény sablon paneljén. Görgessen le a panelre, és válassza a **Azure Event Grid trigger** lehetőséget.

     >[!IMPORTANT]
    > Az **Azure Event hub eseményindítója** és a **Azure Event Grid eseményindító** -sablonjai hasonló névvel rendelkeznek. Győződjön meg arról, hogy a **Azure Event Grid trigger** sablont választotta.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Képernyőkép a függvény létrehozásáról.":::

1. Adja meg a függvény nevét. Ebben az oktatóanyagban a nevet, a *GetGeoFunction*, de általában bármilyen nevet használhat. Válassza a **létrehozási függvény** lehetőséget.

1. A bal oldali menüben válassza a **kód + teszt** panelt. Másolja és illessze be a [C#-szkriptet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) a kód ablakába.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Másolási/képernyőkép-kód beillesztése a függvény ablakába.":::

1. A C#-kódban cserélje le a következő paramétereket:
    * Cserélje le a **SUBSCRIPTION_KEYt** a Azure Maps fiók elsődleges előfizetési kulcsára.
    * Cserélje le a **UDID** -t a `udid` [geokerítésen feltöltésével](#upload-a-geofence)feltöltött geokerítésen.
    * A `CreateBlobAsync` parancsfájlban szereplő függvény egy blobot hoz létre az adattároló-fiókban eseményként. Cserélje le a **ACCESS_KEY**, **ACCOUNT_NAME** és **STORAGE_CONTAINER_NAME** a Storage-fiók hozzáférési kulcsára, a fiók nevére és az adattároló-tárolóra. Ezek az értékek akkor jöttek létre, amikor létrehozta a Storage-fiókot az [Azure Storage-fiók létrehozásakor](#create-an-azure-storage-account).

1. A bal oldali menüben válassza az **integráció** panelt. Válassza ki **Event Grid triggert** a diagramon. Írja be az trigger nevét, a *eventGridEvent*, majd válassza a **Event Grid előfizetés létrehozása** lehetőséget.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Képernyőkép az esemény-előfizetés hozzáadásáról.":::

1. Adja meg az előfizetés részleteit. Adja meg az esemény-előfizetés nevét. Az **Event Schema** esetében válassza **Event Grid sémát**. A **témakörök típusainál** válassza az **Azure IoT hub-fiókok** lehetőséget. Az **erőforráscsoport** területen válassza ki az oktatóanyag elején létrehozott erőforráscsoportot. Az **erőforrás** mezőben válassza ki az "Azure IoT hub létrehozása" című IoT. A **szűrés eseménytípus** esetében válassza az **eszköz telemetria** lehetőséget.

   Miután kiválasztotta ezeket a beállításokat, a **témakör típusa** módosítás **IoT hubra**. A **Rendszertéma neve** beállításnál használhatja az erőforrás nevét. Végül a **végpont részletei** szakaszban válassza a **végpont kiválasztása** lehetőséget. Fogadja el az összes beállítást, és válassza a **kijelölés megerősítése** lehetőséget.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Képernyőkép az esemény-előfizetés létrehozásáról.":::

1. Tekintse át a beállításokat. Győződjön meg arról, hogy a végpont a szakasz elején létrehozott függvényt adja meg. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Képernyőkép az esemény-előfizetés létrehozásának megerősítéséről.":::

1. Most visszatért az **trigger szerkesztése** panelre. Kattintson a **Mentés** gombra.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Események szűrése IoT Hub üzenet-útválasztás használatával

Ha Event Grid-előfizetést ad hozzá az Azure-függvényhez, a rendszer automatikusan létrehoz egy üzenetküldési útvonalat a megadott IoT-központban. Az üzenet-útválasztás lehetővé teszi különböző adattípusok különböző végpontokhoz való továbbítását. Átirányíthatja például az eszköz telemetria-üzeneteit, az eszközök életciklusával kapcsolatos eseményeket és az eszközök kettős változási eseményeit. További információ: [IoT hub üzenet-útválasztás használata](../iot-hub/iot-hub-devguide-messages-d2c.md).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Képernyőkép az IoT hub üzenet-útválasztásáról.":::

A példában csak a bérelt autó áthelyezése után szeretne üzeneteket fogadni. Hozzon létre egy útválasztási lekérdezést az események szűréséhez, ahol a `Engine` tulajdonság értéke **"on"**. Útválasztási lekérdezés létrehozásához válassza ki a **RouteToEventGrid** útvonalat, és cserélje le az **útválasztási lekérdezést** a **"motor ="** értékre a következőn: "". Kattintson a **Mentés** gombra. Most az IoT hub csak az eszköz telemetria teszi közzé, ahol a motor be van kapcsolva.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Képernyőkép az útválasztási üzenetek szűréséről.":::

>[!TIP]
>Az eszközről a felhőbe irányuló üzenetek IoT többféleképpen is lekérdezhető. Az üzenet-útválasztási szintaxissal kapcsolatos további információkért lásd: [IoT hub üzenet-útválasztás](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

## <a name="send-telemetry-data-to-iot-hub"></a>Telemetria-IoT Hub küldése

Ha az Azure-függvény fut, most telemetria-adatait is elküldheti az IoT hubhoz, amely átirányítja a Event Grid. C#-alkalmazás használatával szimulálhatja egy bérelt autó járműbeli eszközének tartózkodási helyét. Az alkalmazás futtatásához szüksége lesz a fejlesztői számítógép .NET Core SDK 2.1.0 vagy újabb verziójára. Az alábbi lépések végrehajtásával szimulált telemetria-adatküldést küldhet az IoT hubhoz:

1. Ha még nem tette meg, töltse le a [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# projektet.

2. Nyissa meg a `simulatedCar.cs` fájlt egy tetszőleges szövegszerkesztőben, és cserélje le a értékét az `connectionString` eszköz regisztrálásakor mentettre. Mentse a fájl módosításait.

3. Győződjön meg arról, hogy a .NET Core telepítve van a gépen. A helyi terminál ablakban nyissa meg a C# projekt gyökérkönyvtárát, és futtassa a következő parancsot a szükséges csomagok telepítéséhez a szimulált eszköz alkalmazáshoz:

    ```cmd/sh
    dotnet restore
    ```

4. Ugyanebben a terminálban futtassa a következő parancsot az autókölcsönző szimulációs alkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```


  A helyi terminálnak az alábbihoz hasonlóan kell kinéznie.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="A terminál kimenetének képernyőképe.":::

Ha most megnyitja a blob Storage-tárolót, négy blobot láthat a helyekhez, ahol a jármű kívül esik a geokerítésen.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Képernyőkép a tárolóban lévő Blobok megtekintéséről.":::

A következő Térkép a geokerítésen kívüli négy jármű elhelyezési pontját mutatja be. Minden hely naplózása rendszeres időközönként történik.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Képernyőkép a szabálysértési térképről.":::

## <a name="explore-azure-maps-and-iot"></a>A Azure Maps és a IoT megismerése

Az oktatóanyagban használt Azure Maps API-k megismeréséhez lásd:

* [Fordított keresési címek lekérése](/rest/api/maps/search/getsearchaddressreverse)
* [Geokerítésen beolvasása](/rest/api/maps/spatial/getgeofence)

A Azure Maps REST API-k teljes listájáért lásd:

* [Azure Maps REST API-k](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

A IoT Azure minősítéssel rendelkező eszközök listájának lekéréséhez látogasson el a következő webhelyre:

* [Azure Certified-eszközök](https://catalog.azureiotsolutions.com/)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az eszközről a felhőbe irányuló telemetria küldéséről, és fordítva, tekintse meg a következőt:


> [!div class="nextstepaction"]
> [Telemetria küldése egy eszközről](../iot-hub/quickstart-send-telemetry-dotnet.md)