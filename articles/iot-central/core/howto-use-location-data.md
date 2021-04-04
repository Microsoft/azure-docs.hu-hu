---
title: Helyadatok használata Azure IoT Central-megoldásokban
description: Megtudhatja, hogyan használhatók a IoT Central alkalmazáshoz csatlakoztatott eszközről továbbított helyadatok. Helyadatok ábrázolása térképre vagy geokerítések-szabályok létrehozása.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98128021"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Helyadatok használata Azure IoT Central-megoldásokban

Ez a cikk bemutatja, hogyan használhatók a helyadatok egy IoT Central alkalmazásban. A IoT Centralhoz csatlakozó eszközök telemetria adatfolyamként küldhetik a tartózkodási helyet, vagy használhatnak egy eszköz tulajdonságot a helyadatok jelentéséhez.

A megoldás-szerkesztő a következő helyre tudja használni a helyadatok:

* A jelentett hely ábrázolása egy térképen.
* A telemetria hely előzményeinek megjelenítése
* Hozzon létre geokerítések-szabályokat egy operátor értesítéséhez, amikor egy eszköz belép vagy kilép egy adott régióból.

## <a name="add-location-capabilities-to-a-device-template"></a>Hely képességeinek hozzáadása egy eszköz sablonhoz

Az alábbi képernyőfelvételen egy eszköz-tulajdonságra és a telemetria használó típusra mutató példák láthatók. A definíciók a **hely** szemantikai típusát és a **térinformatikai** séma típusát használják:

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="A Location Property definícióját ábrázoló képernyőfelvétel az eszköz sablonjában":::

A fenti képességekhez tartozó [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definíciói az alábbi kódrészlethez hasonlóan néznek ki:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> A **geopoint** séma típusa nem része a [DTDL specifikációjának](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). A IoT Central jelenleg a **geopoint** séma típusát és a **hely** szemantikai típusát támogatja a visszamenőleges kompatibilitás érdekében.

## <a name="send-location-data-from-a-device"></a>Helyadatok küldése egy eszközről

Amikor egy eszköz adatokat küld az előző szakaszban látható **DeviceLocation** tulajdonsághoz, a hasznos adatok a következő JSON-kódrészletre hasonlítanak:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Amikor egy eszköz adatokat küld az előző szakaszban látható **követési** telemetria, az adattartalom a következő JSON-kódrészlethez hasonlít:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Eszköz helyének megjelenítése

A helyadatok több helyen is megjeleníthetők a IoT Central alkalmazásban. Például az egyes eszközökhöz vagy az irányítópultokhoz társított nézetekben.

Egy eszköz nézetének létrehozásakor kiválaszthatja, hogy a térképen megjelenjen-e a hely, vagy megjelenítheti az egyes értékeket:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="A helyadatok megjelenítését bemutató képernyőkép":::

A Térkép csempéket hozzáadhat egy irányítópulthoz egy vagy több eszköz helyének ábrázolásához. Ha Térkép csempét ad hozzá a hely telemetria megjelenítéséhez, akkor a helyet egy adott időszakban ábrázolhatja. Az alábbi képernyőfelvételen egy szimulált eszköz által jelentett hely látható az elmúlt 30 percben:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="A helyadatok megjelenítésére szolgáló irányítópultot bemutató képernyőkép":::

## <a name="create-a-geofencing-rule"></a>Geokerítések-szabály létrehozása

A Location telemetria használatával olyan geokerítések-szabályt hozhat létre, amely riasztást generál, ha egy eszköz téglalap alakú területről helyez át vagy onnan. Az alábbi képernyőfelvételen egy olyan szabály látható, amely négy feltételt használ egy téglalap alakú terület a szélességi és a hosszúsági értékekkel való definiálásához. A szabály egy e-mailt hoz létre, amikor az eszköz a téglalap alakú területre lép:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Geokerítések-szabály definícióját megjelenítő képernyőkép":::

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja a tulajdonságokat az Azure IoT Central alkalmazásban, lásd:

* [Is található adattartalom](concepts-telemetry-properties-commands.md)
* [Ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central-alkalmazással](tutorial-connect-device.md)