---
title: 'Oktatóanyag: Az IoT térelemzési szolgáltatásának | Microsoft Azure Maps'
description: Oktatóanyag a IoT Hub Maps Microsoft Azure API-okkal való integrálásához
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9ebc6e266c93e55bc250e8450356f8b695dd9080
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714991"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Oktatóanyag: IoT térbeli elemzések megvalósítása a Azure Maps

IoT-forgatókönyvekben gyakori a térben és időben előforduló releváns események rögzítése és nyomon követése. Ilyenek például a flottakezelés, az eszközkövetés, a mobilitás és az intelligens város alkalmazások. Ez az oktatóanyag végigvezeti egy olyan megoldáson, amely nyomon követi a használt autókölcsönzési mozgásokat a Azure Maps API-k használatával.

Ebben az oktatóanyagban a következőt fogja:

> [!div class="checklist"]
> * Hozzon létre egy Azure-tárfiókot az autókövetési adatok naplózására.
> * Töltsön fel egy geokerítést a Azure Maps Data szolgáltatás (előzetes verzió) a Data Upload API használatával.
> * Hub létrehozása a Azure IoT Hub és eszköz regisztrálása.
> * Függvény létrehozása a Azure Functions, üzleti logika megvalósítása térbeli Azure Maps alapján.
> * Feliratkozás Az IoT-eszköz telemetriai eseményeire az Azure-függvényből az Azure Event Grid.
> * Szűrje a telemetriai eseményeket az üzenet-IoT Hub használatával.

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. [Hozzon létre egy Azure Maps fiókot.](quick-demo-map-app.md#create-an-azure-maps-account)

3. [Szerezzen be egy elsődleges előfizetői kulcsot,](quick-demo-map-app.md#get-the-primary-key-for-your-account)más néven az elsődleges kulcsot vagy az előfizetői kulcsot. További információ: Hitelesítés [kezelése a Azure Maps.](how-to-manage-authentication.md)

4. [Hozzon létre egy erőforráscsoportot.](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) Ebben az oktatóanyagban az erőforráscsoportnak a *ContosoRental* nevet adhatja, de bármilyen nevet kiválaszthat.

5. Töltse le [a rentalCarSimulation C# projektet.](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)

Ez az oktatóanyag a [Postman alkalmazást](https://www.postman.com/) használja, de más API-fejlesztési környezetet is választhat.

## <a name="use-case-rental-car-tracking"></a>Eset: autókölcsönzés nyomon követése

Tegyük fel, hogy egy autókölcsönző vállalat szeretné naplózni a személyautók helyinformációit, a megtett távolságot és az autókölcsönzők aktuális állapotát. A vállalat akkor is tárolni szeretné ezt az információt, amikor egy autó elhagyja a megfelelő engedélyezett földrajzi régiót.

A kölcsönzési autók olyan IoT-eszközökkel vannak ellátva, amelyek rendszeresen küldenek telemetriai adatokat a IoT Hub. A telemetria tartalmazza az aktuális helyet, és jelzi, hogy az autó motorja fut-e. Az eszközhely sémája megfelel a [térinformatikai Plug and Play IoT-adatsémának.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md) A kölcsönző autó eszköz-telemetriasémája a következő JSON-kódhoz hasonlít:

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

Ebben az oktatóanyagban csak egy járművel fog nyomon követni. Az Azure-szolgáltatások beállítása után le kell töltenie a [rentalCarSimulation C#-projektet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) a járműszimulátor futtatásához. Az eseményektől a függvények végrehajtásáig a teljes folyamatot a következő lépések foglalják össze:

1. A járműben található eszköz telemetriai adatokat küld a IoT Hub.

2. Ha az autómotor fut, a hub közzéteszi a telemetriai adatokat a Event Grid.

3. Az Azure-függvények az eszköz telemetriai eseményeire való esemény-előfizetés miatt aktiválódnak.

4. A függvény naplózza a jármű-eszköz helyének koordinátáit, az esemény idejét és az eszköz azonosítóját. Ezután a [Spatial Geofence Get API használatával](/rest/api/maps/spatial/getgeofence) határozza meg, hogy az autó a geokerítésen kívülre hajtott-e. Ha a geokerítés határain kívülre esik, a függvény az eseménytől kapott helyadatokat egy blobtárolóban tárolja. A függvény a [](/rest/api/maps/search/getsearchaddressreverse) Keresési cím megfordítása lekérdezésével a koordináta helyét egy utcacímre fordítja le, és az eszköz többi helyadatával együtt tárolja azt.

Az alábbi ábra a rendszer magas szintű áttekintését mutatja be.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="A rendszer áttekintésének ábrája.":::

Az alábbi ábra kékkel emeli ki a geokerítési területet. A kölcsönzési autó útvonalát egy zöld vonal jelzi.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="A geokerítés útvonalát bemutató ábra.":::

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Az autós szabálysértés nyomkövetési adatainak tárolásához hozzon létre egy általános célú [v2-tárfiókot](../storage/common/storage-account-overview.md) az erőforráscsoportban. Ha még nem hozott létre erőforráscsoportot, kövesse az erőforráscsoport [létrehozására vonatkozó utasításokat.](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) Ebben az oktatóanyagban a ContosoRental nevet fogja biztosítani az *erőforráscsoportnak.*

Tárfiók létrehozásához kövesse a [tárfiók létrehozására vonatkozó utasításokat.](../storage/common/storage-account-create.md?tabs=azure-portal) Ebben az oktatóanyagban a tárfióknak a *contosorentalstorage* nevet használhatja, de általában bármit elnevezést adhat neki.

A tárfiók sikeres létrehozása után létre kell hoznia egy tárolót a naplózási adatok tárolásához.

1. Ugrás az újonnan létrehozott tárfiókra. Az **Alapvető szolgáltatások szakaszban** válassza a **Tárolók hivatkozást.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Képernyőkép a Blob Storage tárolóiról.":::

2. A bal felső sarokban válassza a **+ Tároló lehetőséget.** A böngésző jobb oldalán megjelenik egy panel. A tárolónak adja a *contoso-rental-logs nevet,* majd válassza a **Létrehozás lehetőséget.**

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Képernyőkép a blobtároló létrehozásáról.":::

3. A tárfiók **Hozzáférési kulcsok** paneljére kattintva  másolja ki  a Tárfiók neve és a Kulcs értékét a **key1 szakaszban.** Mindkét értékre szüksége lesz az "Azure-függvény létrehozása és új előfizetés Event Grid" szakaszban.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Képernyőkép a tárfiók nevének és kulcsának másolásról.":::

## <a name="upload-a-geofence"></a>Geokerítés feltöltése

Ezután a [Postman alkalmazással töltse](https://www.getpostman.com) fel [a geokerítést](./geofence-geojson.md) a Azure Maps. A geokerítés határozza meg a kölcsönzési jármű engedélyezett földrajzi területe. Az Azure-függvény geokerítésének használatával fogja megállapítani, hogy egy autó a geokerítési területen kívülre helyezték-e.

Kövesse az alábbi lépéseket a geokerítés feltöltéséhez a Azure Maps Data Upload API használatával: 

1. Nyissa meg a Postman alkalmazást, és válassza az Új **lehetőséget.** Az Új **létrehozása ablakban** válassza a Gyűjtemény **lehetőséget.** Nevezze el a gyűjteményt, és válassza a **Létrehozás lehetőséget.**

2. A kérelem létrehozásához válassza ismét az **Új** lehetőséget. Az Új **létrehozása ablakban** válassza a **Kérelem** lehetőséget, és adja meg a kérelem nevét. Válassza ki az előző lépésben létrehozott gyűjteményt, majd válassza a **Mentés lehetőséget.**

3. Válassza ki **a POST** HTTP-metódust a szerkesztő lapon, és írja be a következő URL-címet a geokerítés feltöltéséhez az Adatfeltöltési API-ba. Cserélje le a `{subscription-key}` helyére az elsődleges előfizetői kulcsot.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Az URL-címben a paraméterrel szembeni érték a feltöltött `geojson` `dataFormat` adatok formátumát jelöli.

4. A **bemeneti formátumnál** válassza a Body raw (Nyers törzs) lehetőséget, majd a legördülő listából válassza a  >   **JSON** lehetőséget. [Nyissa meg a JSON-adatfájlt,](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)és másolja a JSON-t a törzs szakaszba. Kattintson a **Küldés** gombra.

5. Válassza **a Küldés lehetőséget,** és várjon, amíg a kérés feldolgozásra vár. A kérés befejezése után a válasz **Fejlécek** lapját kell látnia. Másolja ki a **Hely kulcs értékét,** amely a `status URL` következő: .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Az API-hívás állapotának ellenőrzéshez hozzon létre **egy GET** HTTP-kérést a `status URL` címen. A hitelesítéshez hozzá kell fűzni az elsődleges előfizetői kulcsot az URL-címhez. A **GET kérésnek** a következő URL-címhez kell hasonlónak lennie:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Ha a **GET** HTTP-kérés sikeresen befejeződik, egy értéket ad `resourceLocation` vissza. A `resourceLocation` tartalmazza a feltöltött tartalom egyedi `udid` azonosítóját. Másolja ki `udid` ezt az oktatóanyag későbbi felhasználásra.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

IoT Hub biztonságos és megbízható kétirányú kommunikációt tesz lehetővé egy IoT-alkalmazás és az által felügyelt eszközök között. Ebben az oktatóanyagban információkat szeretne lekért a járművön található eszközről a kölcsönző autó helyének meghatározásához. Ebben a szakaszban egy IoT Hubot hoz létre a *ContosoRental* erőforráscsoportban. Ez a központ felelős az eszköz telemetriai eseményeinek közzétételéért.

> [!NOTE]
> Az eszköz telemetriai eseményeinek a Event Grid jelenleg előzetes verzióban érhetőek el. Ez a funkció a következők kivételével minden régióban elérhető: az USA keleti régiója, az USA nyugati régiója, Nyugat-Európa, Azure Government, Azure China 21Vianet és az Azure Germany.

IoT Hub a *ContosoRental* erőforráscsoportban való létrehozásához kövesse az IoT Hub [létrehozása lépéseit.](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub)

## <a name="register-a-device-in-your-iot-hub"></a>Eszköz regisztrálása az IoT Hubon

Az eszközök csak akkor tudnak csatlakozni az IoT Hubhoz, ha regisztrálva vannak az IoT Hub identitásjegyzékében. Itt egyetlen, *InVehicleDevice nevű* eszközt fog létrehozni. Az eszköz IoT Hubon belüli létrehozásához és regisztrálásához kövesse az új eszköz [regisztrálása az IoT Hubban lépéseit.](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub) Győződjön meg arról, hogy az eszköz elsődleges kapcsolati sztringjére másolt. Erre később még szüksége lesz.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Függvény létrehozása és új Event Grid hozzáadása

Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi kis kód ("függvények") futtatását anélkül, hogy explicit módon ki kellene kiépítenünk vagy kezelnünk kellene a számítási infrastruktúrát. További információ: [Azure Functions.](../azure-functions/functions-overview.md)

Egy függvényt egy adott esemény aktivál. Itt egy olyan függvényt fog létrehozni, amelyet egy eseményindító Event Grid aktivál. Eseményindító és függvény közötti kapcsolat létrehozásához hozzon létre egy esemény-előfizetést az IoT Hub telemetriai eseményeihez. Eszköz telemetriaeseménye esetén a függvény végpontként lesz meghívva, és megkapja a korábban az eszközre vonatkozó adatokat, amelyet korábban regisztrált IoT Hub.

A függvény a [következő C#-szkriptkódot fogja tartalmazni:](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Most állítsa be az Azure-függvényt.

1. A Azure Portal válassza az **Erőforrás létrehozása lehetőséget.** A **keresőmezőbe írja be** a függvényalkalmazás szöveget. Válassza **a Függvényalkalmazás létrehozása**  >  **lehetőséget.**

1. A **Függvényalkalmazás létrehozása lapon** nevezze el a függvényalkalmazást. Az **Erőforráscsoport alatt** válassza a **ContosoRental** lehetőséget a legördülő listából. A **Futásidejű verem mezőben** válassza a .NET **Core lehetőséget.** A lap alján válassza a **Tovább:** Üzemeltetési >.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Képernyőkép egy függvényalkalmazás létrehozásáról.":::

1. A **Tárfiók mezőben** válassza ki az Azure Storage-fiók létrehozása során létrehozott [tárfiókot.](#create-an-azure-storage-account) Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Tekintse át a függvényalkalmazás részleteit, és válassza a **Létrehozás lehetőséget.**

1. Az alkalmazás létrehozása után hozzá kell adni egy függvényt. Ugrás a függvényalkalmazásra. Válassza a **Függvények panelt.** Az oldal tetején válassza a **+ Hozzáadás lehetőséget.** Megjelenik a függvénysablon panelje. Görgessen le a panelen, és válassza **a Azure Event Grid eseményindítót.**

     >[!IMPORTANT]
    > Az **Azure Event Hub-eseményindító** és a **Azure Event Grid eseményindító sablonjainak** neve hasonló. Győződjön meg arról, hogy a **Azure Event Grid trigger sablont választja.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Képernyőkép egy függvény létrehozásáról.":::

1. Nevezze el a függvényt. Ebben az oktatóanyagban a *GetGeoFunction* nevet fogja használni, de általában bármilyen nevet használhat. Válassza a **Függvény létrehozása lehetőséget.**

1. A bal oldali menüben válassza a **Kód + Teszt panelt.** Másolja és illessze be [a C#-szkriptet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) a kódablakba.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kód függvényablakba való beillesztésének másolása/képernyőképe.":::

1. A C#-kódban cserélje le a következő paramétereket:
    * Cserélje **SUBSCRIPTION_KEY** a Azure Maps fiók elsődleges előfizetői kulcsára.
    * Cserélje **le az UDID-t** a Geokerítés feltöltése `udid` helyére. [](#upload-a-geofence)
    * A `CreateBlobAsync` szkript függvénye eseményenként létrehoz egy blobot az adattárfiókban. Cserélje le **ACCESS_KEY**, **ACCOUNT_NAME**, **és STORAGE_CONTAINER_NAME** helyére a tárfiók hozzáférési kulcsát, fióknevét és adattárolóját. Ezek az értékek a tárfiók létrehozásakor jönnek létre az [Azure Storage-fiók létrehozása során.](#create-an-azure-storage-account)

1. A bal oldali menüben válassza az **Integráció panelt.** A **Event Grid válassza az Eseményindító** lehetőséget. Írja be az eseményindító nevét *(eventGridEvent)* és válassza a Create Event Grid subscription (Előfizetés **létrehozása) lehetőséget.**

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Képernyőkép az esemény-előfizetés hozzáadásáról.":::

1. Adja meg az előfizetés adatait. Nevezze el az esemény-előfizetést. Az **Eseményséma mezőben** válassza **a Event Grid lehetőséget.** A **Témakörtípusok beállításnál** válassza a **Azure IoT Hub lehetőséget.** Az **Erőforráscsoport mezőben** válassza ki az oktatóanyag elején létrehozott erőforráscsoportot. Az **Erőforrás** mezőben válassza ki az "Azure IoT Hub létrehozása" központban létrehozott IoT Hubot. A **Szűrés eseménytípusokra beállításnál válassza** az Eszköz **telemetriai adatok lehetőséget.**

   Miután kiválasztotta ezeket a beállításokat, látni fogja, hogy a **Témakör típusa** a következőre **változik: IoT Hub.** A **Rendszertémakör neve** alatt használhatja ugyanazt a nevet, mint az erőforrás. Végül a Végpont **részletei szakaszban** válassza a Végpont **kiválasztása lehetőséget.** Fogadja el az összes beállítást, majd válassza **a Kijelölés megerősítése lehetőséget.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Képernyőkép az esemény-előfizetés létrehozásáról.":::

1. Tekintse át a beállításokat. Győződjön meg arról, hogy a végpont adja meg a szakasz elején létrehozott függvényt. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Képernyőkép az esemény-előfizetés létrehozására való megerősítésről.":::

1. Most visszatér az Eseményindító szerkesztése **panelre.** Kattintson a **Mentés** gombra.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Események szűrése üzenet-IoT Hub használatával

Amikor hozzáad egy Event Grid-előfizetést az Azure-függvényhez, a rendszer automatikusan létrehoz egy üzenetküldési útvonalat a megadott IoT-központban. Az üzenetek útválasztása lehetővé teszi a különböző adattípusok különböző végpontokra való átirányítását. Például irányíthatja az eszköz telemetriai üzeneteit, az eszköz életciklusának eseményeit és az ikereszköz-módosítási eseményeket. További információ: [Üzenet-IoT Hub használata.](../iot-hub/iot-hub-devguide-messages-d2c.md)

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Az IoT Hub üzenet-útválasztásának képernyőképe.":::

A példaforgatókönyvben csak akkor szeretne üzeneteket kapni, amikor a kölcsönzési autó mozgásban van. Hozzon létre egy útválasztási lekérdezést az események szűréséhez, ahol `Engine` a tulajdonság **értéke "ON"**. Útválasztási lekérdezés létrehozásához válassza ki a **RouteToEventGrid** útvonalat, és cserélje le az **Útválasztási** lekérdezést **az "Engine='ON" névre.** Kattintson a **Mentés** gombra. Az IoT Hub mostantól csak azokat az eszköz-telemetriai adatokat teszi közzé, amelyeken a motor be van kapcsolódva.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Az útválasztási üzenetek szűrésének képernyőképe.":::

>[!TIP]
>Az IoT-eszközökről a felhőbe küldött üzenetek többféleképpen is lekérdezhetőek. Az üzenet-útválasztás szintaxisával kapcsolatos további információkért lásd: [IoT Hub üzenet-útválasztás.](../iot-hub/iot-hub-devguide-routing-query-syntax.md)

## <a name="send-telemetry-data-to-iot-hub"></a>Telemetriai adatok küldése a IoT Hub

Ha az Azure-függvény fut, mostantól telemetriai adatokat küldhet az IoT Hubnak, amely továbbítja őket a Event Grid. Egy C#-alkalmazással szimulálhatja egy bérautó járműben használt eszközének helyadatokat. Az alkalmazás futtatásához a .NET Core SDK 2.1.0-s vagy újabb rendszernek kell futnia. Kövesse az alábbi lépéseket a szimulált telemetriai adatok IoT Hubra való elküldéhez:

1. Ha még nem tette meg, töltse le a [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# projektet.

2. Nyissa meg a fájlt egy ön által választott szövegszerkesztőben, és cserélje le a értékét arra, amit az eszköz `simulatedCar.cs` `connectionString` regisztrálójában mentett. Mentse a fájl módosításait.

3. Győződjön meg arról, hogy a .NET Core telepítve van a gépen. A helyi terminálablakban kattintson a C#-projekt gyökérmappára, és futtassa a következő parancsot a szimulálteszköz-alkalmazáshoz szükséges csomagok telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

4. Ugyanezen a terminálon futtassa a következő parancsot a autós szimulációs alkalmazás felépítéséhez és futtatásához:

    ```cmd/sh
    dotnet run
    ```


  A helyi terminálnak az alábbihoz hasonlónak kell lennie.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Képernyőkép a terminálkimenetről.":::

Ha most megnyitja a Blob Storage-tárolót, négy blobot láthat azon helyekhez, ahol a jármű a geokerítésen kívül volt.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Képernyőkép a tárolóban lévő blobok megtekintéséről.":::

Az alábbi térkép négy jármű földrajzi helyét mutatja be a geokerítésen kívül. A rendszer rendszeres időközönként naplózza az egyes helyeket.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Képernyőkép a szabálysértési térképről.":::

## <a name="explore-azure-maps-and-iot"></a>Ismerkedés Azure Maps IoT-vel

Az oktatóanyagban Azure Maps API-k megismerheti a következőt:

* [Keresési cím fordított címének lekeresése](/rest/api/maps/search/getsearchaddressreverse)
* [Geokerítés lekérte](/rest/api/maps/spatial/getgeofence)

A REST API-k teljes Azure Maps lásd:

* [Azure Maps REST API-k](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Az IoT-hez Azure-minősítéssel rendelkező eszközök listáját a következő dokumentumban lehet lekérte:

* [Azure-tanúsítvánnyal rendelkező eszközök](https://devicecatalog.azure.com/)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek olyan erőforrások, amelyekhez tisztításra lenne szükség.

## <a name="next-steps"></a>Következő lépések

Az eszközről a felhőbe küldött telemetriai adatokkal és az egyéb megoldásokkal kapcsolatos további információkért lásd:


> [!div class="nextstepaction"]
> [Telemetria küldése egy eszközről](../iot-hub/quickstart-send-telemetry-dotnet.md)