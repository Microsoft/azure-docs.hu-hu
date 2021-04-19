---
title: Speciális szimulálteszköz-modell létrehozása – Azure| Microsoft Docs
description: Ebből az útmutatóból megtudhatja, hogyan hozhat létre fejlett eszközmodellt az Eszközszimulációs megoldásgyorsítóval való használatra.
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
ms.openlocfilehash: dc2eac4e784d4224581078348f12b231befe1f35
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714109"
---
# <a name="create-an-advanced-device-model"></a>Speciális eszközmodell létrehozása

Ez az útmutató az egyéni eszközmodellt definiáló JSON- és JavaScript-fájlokat ismerteti. A cikk néhány eszközmodell-definíciós mintafájlt tartalmaz, és bemutatja, hogyan töltheti fel őket az eszközszimulációs példányba. Fejlett eszközmodelleket hozhat létre, amelyek valósághűbb eszközviselkedést szimulálnak a teszteléshez.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseit követve szüksége lesz az Eszközszimuláció egy üzembe helyezett példányára az Azure-előfizetésében.

Ha még nem telepítette az Eszközszimulációt, tekintse meg az [Eszközszimuláció üzembe](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) helyezése a GitHubon.

### <a name="open-device-simulation"></a>Eszközszimuláció megnyitása

Ha még nem telepítette az Eszközszimulációt, tekintse meg az [Eszközszimuláció üzembe](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) helyezése a GitHubon.

## <a name="device-models"></a>Eszközmodellek

Minden szimulált eszköz egy adott eszközmodellhez tartozik, amely meghatározza a szimuláció működését. Ez a viselkedés magában foglalja, hogy milyen gyakran kell telemetriát küldeni, milyen típusú üzeneteket kell küldeni, valamint a támogatott metódusokat.

Eszközmodellt JSON-eszközdefiníciós fájl és JavaScript-fájlok készlete használatával definiálhat. Ezek a JavaScript-fájlok határozzák meg a szimuláció viselkedését, például a véletlenszerű telemetriát és a metódus logikáját.

Egy tipikus eszközmodell a következő:

* Egy JSON-fájl minden eszközmodellhez (például elevator.jsbe).
* Egy JavaScript-viselkedési szkriptfájl minden eszközmodellhez (például elevator-state.js)
* Egy JavaScript-metódus szkriptfájlja minden eszköz metódushoz (például elevator-go-down.js)

> [!NOTE]
> Nem minden eszközmodell határoz meg metódusokat. Ezért az eszközmodellek metódusszk szkriptekkel is lehetnek. Azonban minden eszközmodellnek kell egy viselkedési szkriptet tartalmazni.

## <a name="device-definition-file"></a>Eszközdefiníciós fájl

Minden eszközdefiníciós fájl tartalmazza a szimulált eszközmodell részleteit, beleértve a következő információkat:

* Eszközmodell neve: sztring.
* Protokoll: AMQP | MQTT | HTTP.
* Az eszköz kezdeti állapota.
* Milyen gyakran kell frissíteni az eszköz állapotát.
* Az eszköz állapotának frissítéséhez használt JavaScript-fájl.
* A küldenünk kell telemetriai üzenetek listája egy adott gyakorisággal.
* A telemetriai üzenetek sémája, amelyet a háttéralkalmazás a fogadott telemetria elemez.
* Az egyes metódusok szimulálására használható támogatott metódusok és JavaScript-fájl listája.

### <a name="file-schema"></a>Fájlséma

A sémaverzió mindig "1.0.0", és a fájl formátumára jellemző:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Eszközmodell leírása

A következő tulajdonságok írják le az eszközmodellt. Minden típus egyedi azonosítóval, szemantikai verzióval, névvel és leírással rendelkezik:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-protokoll

Az IoT-eszközök különböző protokollok használatával csatlakozhatnak. A szimuláció lehetővé teszi az **AMQP,** **MQTT** vagy **HTTP használatát:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Szimulált eszköz állapota

Minden szimulált eszköznek belső állapota van, amelyet meg kell határozni. Az állapot határozza meg a telemetriában jelentett tulajdonságokat is. A hűtőnek például lehet egy kezdeti állapota, például:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

A több érzékelővel is rendelkezik egy mozgó eszköz több tulajdonsággal, például:

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

Az eszközállapotot a szimulációs szolgáltatás a memóriában tartja, és a JavaScript-függvény bemeneteként adja meg. A JavaScript-függvény a következőt döntheti el:

* Az állapot figyelmen kívül hagyása és véletlenszerű adatok létrehozása.
* Az eszköz állapotának realisztikus módon való frissítése egy adott forgatókönyv esetében.

Az állapotot generáló függvény is bemenetként kap:

* Az eszköz azonosítója.
* Az eszköz modellje.
* Az aktuális időpont. Ez az érték lehetővé teszi különböző adatok eszköz és idő alapján való előállítását.

### <a name="generating-telemetry-messages"></a>Telemetriai üzenetek létrehozása

A szimulációs szolgáltatás több telemetriatípust is küldhet minden eszközhöz. A telemetria általában az eszköz állapotából származó adatokat is magában foglalja. Egy szimulált helyiség például 10 másodpercenként küldhet információt a hőmérsékletről és a páratartalomról. Figyelje meg a következő kódrészlet helyőrzőit, amelyeket a rendszer automatikusan az eszközállapot értékeire cserél:

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

A helyőrzők egy **speciális ${NAME}** szintaxist használnak, ahol a **NAME** a JavaScript fő függvénye által visszaadott eszközállapot-objektum **kulcsa.** A sztringeket meg kell idézni, míg a számokat nem.

#### <a name="message-schema"></a>Üzenetséma

Minden üzenettípusnak jól meghatározott sémával kell lennie. Az üzenetsémát a rendszer a IoT Hub is közzéteszi, hogy a háttéralkalmazások újra felhasználják az információkat a bejövő telemetria értelmezéséhez.

A séma támogatja a JSON-formátumot, amely számos rendszerben és szolgáltatásban egyszerű elemzést, átalakítást és elemzést tesz lehetővé.

A sémában felsorolt mezők a következő típusúak:

* Objektum – JSON használatával szerializálva
* Bináris – base64 használatával szerializálva
* Szöveg
* Logikai
* Egész szám
* Dupla
* DateTime

### <a name="supported-methods"></a>Támogatott metódusok

A szimulált eszközök a metódushívásra is reagálhatnak, amely esetben valamilyen logikát hajt végre, és bizonyos választ biztosítanak. A szimulációhoz hasonlóan a metódus logikája is egy JavaScript-fájlban van tárolva, és képes interakcióba lépni az eszköz állapotával. Például:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Eszközdefiníciós fájl létrehozása

Ez az útmutató bemutatja, hogyan hozhat létre eszközmodellt egy drónhoz. A drón véletlenszerűen a földrajzi helyet és a magasságot megváltoztató koordináták kezdeti készletében fog elsiklni.

Másolja a következő JSON-t egy szövegszerkesztőbe, és mentsedrone.js **fájlként.**

### <a name="device-definition-json-example"></a>Eszközdefiníciós JSON-példa

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

## <a name="behavior-script-files"></a>Viselkedési szkriptfájlok

A viselkedési szkriptfájlban található kód áthelyezi a drónt. A szkript úgy módosítja a drón jogosultságszint-emelését és helyét, hogy módosítja az eszköz memóriaállapotát.

A JavaScript-fájloknak egy **fő függvényt** kell tartalmaznak, amely két paramétert fogad el:

* Egy **környezeti** objektum, amely három tulajdonságot tartalmaz:
    * **currentTime** sztringként, **yyyy-MM-dd'T'HH:mm:sszzz formátumban.**
    * **deviceId ( eszközazonosító).** Például: **Simulated.Elevator.123**.
    * **deviceModel ( eszközmodell).** Például: **Lift.**
* Egy **állapotobjektum,** amely az előző hívásban a függvény által visszaadott érték. Ezt az eszközállapotot a szimulációs szolgáltatás tartja fenn, és telemetriai üzenetek létrehozásához használja.

A **fő** függvény az új eszközállapotot adja vissza. Például:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Viselkedési szkriptfájl létrehozása

Másolja be az alábbi JavaScriptet egy szövegszerkesztőbe, és mentse **drone-state.js.**

### <a name="device-model-javascript-simulation-example"></a>Példa JavaScript-szimulációs eszközmodellre

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

## <a name="create-a-method-script-file"></a>Metódus parancsfájljának létrehozása

A metódusszk szkriptek hasonlóak a viselkedési szkriptekhez. Ezek határozzák meg az eszköz viselkedését egy adott felhő–eszköz metódus hívata esetén.

A drón visszahívási szkript egy rögzített pontra állítja be a drón koordinátáit, hogy szimulálja a visszatérő drónt.

Másolja be az alábbi JavaScriptet egy szövegszerkesztőbe, és mentse **droneRecall-method.js.**

### <a name="device-model-javascript-simulation-example"></a>Példa JavaScript-szimulációs eszközmodellre

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

## <a name="debugging-script-files"></a>Szkriptfájlok hibakeresése

Bár nem csatolhat hibakeresőt egy futó viselkedési fájlhoz, a naplófunkvénnyel is írhat adatokat a **szolgáltatásnaplóba.** Szintaktikai hibák esetén az értelmező meghibásodik, és adatokat ír a kivételről a naplóba.

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

## <a name="deploy-an-advanced-device-model"></a>Speciális eszközmodell üzembe helyezése

A speciális eszközmodell üzembe helyezéséhez fel kell töltenie az Eszközszimulációs példány fájljait:

A menüsávon válassza az **Eszközmodellek** gombot. Az **Eszközmodellek** oldal felsorolja az eszközszimuláció ezen példányában elérhető eszközmodelleket:

![Eszközmodellek](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kattintson az **+ Eszközmodellek hozzáadása** elemre a lap jobb felső sarkában:

![Eszközmodell hozzáadása](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kattintson **a Speciális** elemre a speciális eszközmodell lap megnyitásához:

![Speciális lap](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kattintson **a Tallózás gombra,** és válassza ki a létrehozott JSON- és JavaScript-fájlokat. Ne feledjük, hogy mindhárom fájlt ki kell választani. Ha valamelyik fájl hiányzik, az érvényesítés sikertelen lesz:

![Fájlok tallózása](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Ha a fájlok átesnek az ellenőrzésen, kattintson a **Mentés gombra,** és az eszközmodell készen áll a szimulációban való használatra. Egyéb esetben javítsa ki a hibákat, és töltse újra a fájlokat:

![Mentés](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban az Eszközszimulációban használt eszközmodell-fájlokról és a speciális eszközmodellek létrehozásáról tanult. A következő lépésként megismerheti, hogyan vizualizálhatja a Time Series Insights eszközszimulációs megoldásgyorsítóból küldött [telemetriai adatokat.](./iot-accelerators-device-simulation-time-series-insights.md)