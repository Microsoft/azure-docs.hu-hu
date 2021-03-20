---
title: Fejlett szimulált eszköz modell létrehozása – Azure | Microsoft Docs
description: Ebben a útmutatóban megtudhatja, hogyan hozhat létre egy speciális eszköz modellt az eszköz-szimulációs megoldás-gyorsító használatához.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 58891bdb4601744129c97454f43202d3ba07b709
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852557"
---
# <a name="create-an-advanced-device-model"></a>Speciális eszközmodell létrehozása

Ez a útmutató az egyéni eszköz modelljét definiáló JSON-és JavaScript-fájlokat ismerteti. A cikk néhány minta típusú eszköz-definíciós fájlt tartalmaz, és bemutatja, hogyan töltheti fel őket az eszköz szimulációs példányára. Speciális eszköz-modelleket hozhat létre, amelyek reális eszköz-viselkedést szimulálnak a teszteléshez.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseit követve az Azure-előfizetésében az eszköz-szimuláció üzembe helyezett példánya szükséges.

Ha még nem telepítette az eszköz-szimulációt, tekintse meg a következőt: [eszköz-szimulációs telepítés](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) a githubon.

### <a name="open-device-simulation"></a>Eszközszimuláció megnyitása

Ha még nem telepítette az eszköz-szimulációt, tekintse meg a következőt: [eszköz-szimulációs telepítés](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) a githubon.

## <a name="device-models"></a>Eszközmodellek

Minden szimulált eszköz egy adott eszköz modelljéhez tartozik, amely meghatározza a szimulációs viselkedést. Ez a viselkedés magában foglalja a telemetria küldésének gyakoriságát, a küldendő üzenetek típusát és a támogatott metódusokat.

Az eszköz modelljét JSON-eszköz definíciós fájlja és JavaScript-fájlok készlete alapján határozhatja meg. Ezek a JavaScript-fájlok határozzák meg a szimulációs viselkedést, például a véletlenszerű telemetria és a metódus logikáját.

Egy tipikus eszköz modellje:

* Egy JSON-fájl minden eszköz-modellhez (például elevator.json).
* Egy JavaScript-viselkedési parancsfájl minden eszköz-modellhez (például elevator-state.js)
* Egy JavaScript-metódus parancsfájlja minden eszköz-metódushoz (például elevator-go-down.js)

> [!NOTE]
> Nem minden eszköz modell definiál metódusokat. Ezért előfordulhat, hogy az eszköz modellje nem rendelkezik metódus-parancsfájlokkal. Azonban minden eszköz modellnek rendelkeznie kell egy viselkedési parancsfájllal.

## <a name="device-definition-file"></a>Eszköz definíciós fájlja

Minden eszköz definíciós fájlja egy szimulált eszköz modelljének részleteit tartalmazza, beleértve a következő információkat:

* Eszköz modell neve: karakterlánc.
* Protokoll: AMQP | MQTT | HTTP.
* A kezdeti eszköz állapota.
* Milyen gyakran frissíti az eszköz állapotát.
* Az eszköz állapotának frissítéséhez használandó JavaScript-fájl.
* A küldendő telemetria-üzenetek listája, amelyek mindegyike egy adott gyakorisággal rendelkezik.
* A telemetria-üzenetek sémája, amelyet a háttérbeli alkalmazás használ a kapott telemetria elemzéséhez.
* A támogatott metódusok és az egyes módszerek szimulálásához használandó JavaScript-fájl listája.

### <a name="file-schema"></a>Fájl sémája

A séma verziószáma mindig "1.0.0", és a fájl formátumára vonatkozik:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Eszköz modell leírása

A következő tulajdonságok leírják az eszköz modelljét. Mindegyik típus egyedi azonosítóval, szemantikai verzióval, névvel és leírással rendelkezik:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT protokoll

A IoT-eszközök különböző protokollokkal csatlakozhatnak. A szimuláció a **AMQP**, a **MQTT** vagy a **http** használatát teszi lehetővé:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Szimulált eszköz állapota

Minden szimulált eszköz belső állapotú, amelyet meg kell határozni. Az állapot meghatározza azokat a tulajdonságokat is, amelyek a telemetria jelentésekben szerepelhetnek. Előfordulhat például, hogy egy hűtőnek van egy kezdeti állapota, például:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Több érzékelővel rendelkező mozgó eszköz több tulajdonsággal rendelkezhet, például:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

A szimulációs szolgáltatás a memóriában tárolja az eszköz állapotát, és bemenetként adja meg a JavaScript-függvényt. A JavaScript-függvény dönthet úgy, hogy:

* Az állapot figyelmen kívül hagyásához és véletlenszerű adatmennyiség létrehozásához.
* Az eszköz állapotának frissítése valamilyen reális módon egy adott forgatókönyv esetén.

Az állapotot létrehozó függvény bemenetként is kap:

* Az eszköz azonosítója.
* Az eszköz modellje.
* Az aktuális idő. Ez az érték lehetővé teszi, hogy az eszköz és az idő alapján különböző adategységeket lehessen készíteni.

### <a name="generating-telemetry-messages"></a>Telemetria-üzenetek generálása

A szimulációs szolgáltatás több telemetria-típust is küldhet az egyes eszközökhöz. A telemetria jellemzően az eszköz állapotáról származó adatokra vonatkozik. Előfordulhat például, hogy egy szimulált helyiség 10 másodpercenként adatokat küld a hőmérsékletről és a nedvességről. Jegyezze fel a következő kódrészletben található helyőrzőket, amelyek automatikusan az eszköz állapotában szereplő értékekkel lesznek lecserélve:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

A helyőrzők a **$ {Name}** speciális szintaxist használják, ahol a **név** a JavaScript **Main** függvény által visszaadott Eszközállapot-objektum kulcsa. A sztringeket idézőjelek közé kell állítani, míg a számok nem.

#### <a name="message-schema"></a>Üzenet sémája

Minden üzenet típusának jól definiált sémával kell rendelkeznie. Az üzenet sémája IoT Hub is közzé van téve, így a háttérbeli alkalmazások újra felhasználhatják az adatokat a bejövő telemetria értelmezéséhez.

A séma támogatja a JSON formátumot, amely lehetővé teszi az egyszerű elemzést, átalakítást és elemzést több rendszer és szolgáltatás között.

A sémában felsorolt mezők a következő típusokból állhatnak:

* Objektum – szerializált JSON használatával
* Bináris – Base64 használatával szerializált
* Szöveg
* Logikai
* Egész szám
* Dupla
* DateTime

### <a name="supported-methods"></a>Támogatott módszerek

A szimulált eszközök reagálnak a metódus hívására is, ebben az esetben bizonyos logikát hajtanak végre, és választ nyújtanak. A szimulációhoz hasonlóan a metódus logikáját egy JavaScript-fájlban tárolja a rendszer, és az eszköz állapota is használható. Például:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Eszköz-definíciós fájl létrehozása

Ebben a útmutatóban megtudhatja, hogyan hozhat létre egy eszköz modellt a drone számára. A drone véletlenszerűen veszi körül a koordinátákat a hely és a magasság beállításával.

Másolja a következő JSON-t egy szövegszerkesztőbe, és mentse **drone.jsként**.

### <a name="device-definition-json-example"></a>Eszköz-definíció JSON-példa

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Viselkedési parancsfájlok fájljai

A viselkedési parancsfájlban található kód a dronet helyezi át. A szkript megváltoztatja a drone jogosultságszint-emelését és helyét az eszköz memóriájában lévő állapotának módosításával.

A JavaScript-fájloknak rendelkeznie kell egy **fő** függvénnyel, amely két paramétert fogad el:

* Három tulajdonságot tartalmazó **környezeti** objektum:
    * a **currentTime** az **éééé-hh-dd'T'HH: PP: sszzz** karakterlánc formátumban kell megadni.
    * **deviceId**. Példa: **szimulált. lift. 123**.
    * **deviceModel**. Például: **lift**.
* Egy **állapot** -objektum, amely az előző hívás függvényében visszaadott érték. Az eszköz állapotát a szimulációs szolgáltatás tartja karban, és telemetria-üzenetek létrehozásához használja.

A **fő** függvény az új eszköz állapotát adja vissza. Például:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Viselkedési parancsfájl létrehozása

Másolja a következő JavaScriptet egy szövegszerkesztőbe, és mentse **drone-state.jsként**.

### <a name="device-model-javascript-simulation-example"></a>Példa az eszköz modell JavaScript-szimulációra

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Metódus parancsfájl-fájljának létrehozása

A metódus parancsfájljai hasonlók a viselkedési parancsfájlokhoz. Definiálják az eszköz viselkedését, amikor egy adott felhőt az eszköz metódusának hívnak.

A drone-visszahívás parancsfájl beállítja a drone koordinátáit egy rögzített pontra, hogy szimulálja a hazai hazatérést.

Másolja a következő JavaScriptet egy szövegszerkesztőbe, és mentse **droneRecall-method.jsként**.

### <a name="device-model-javascript-simulation-example"></a>Példa az eszköz modell JavaScript-szimulációra

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Parancsfájlok hibakeresése

Habár nem csatolhat hibakeresőt egy futó viselkedési fájlhoz, a **log** függvénnyel adatokat írhat a szolgáltatás naplójába. Szintaktikai hibák esetén a tolmács meghibásodik, és a kivételre vonatkozó adatokat ír a naplóba.

Naplózási példa:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Speciális eszköz modell üzembe helyezése

A speciális eszköz modell üzembe helyezéséhez töltse fel az eszköz szimulációs példányának fájljait:

A menüsávon válassza az **Eszközmodellek** gombot. Az **eszközök modelljei** lap az eszköz-szimuláció ezen példányában elérhető modelleket sorolja fel:

![Eszközmodellek](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kattintson az **+ Eszközmodellek hozzáadása** elemre a lap jobb felső sarkában:

![Eszközmodell hozzáadása](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kattintson a **speciális** elemre a speciális eszköz modell lapjának megnyitásához:

![Speciális lap](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kattintson a **Tallózás** gombra, és válassza ki a létrehozott JSON-és JavaScript-fájlokat. Ügyeljen arra, hogy mindhárom fájlt kiválassza. Ha egy fájl hiányzik, az érvényesítés sikertelen lesz:

![Fájlok tallózása](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Ha a fájlok ellenőrzése sikeres, kattintson a **Save (Mentés** ) gombra, és az eszköz modellje készen áll a szimulációban való használatra. Ellenkező esetben javítsa ki a hibákat, és töltse fel újra a fájlokat:

![Mentés](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megismerte az eszköz-szimulációban használt eszköz-modell fájljait, valamint a speciális eszköz modell létrehozását. Következő lépésként érdemes megvizsgálni, hogyan [használhatók a Time Series Insights az eszköz-szimulációs megoldás-gyorssegédből eljuttatott telemetria megjelenítéséhez](./iot-accelerators-device-simulation-time-series-insights.md).