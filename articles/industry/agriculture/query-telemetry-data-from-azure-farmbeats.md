---
title: Feldolgozott telemetria-adatok lekérdezése
description: Ez a cikk ismerteti, hogyan lehet lekérdezni a betöltött telemetria-információkat.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677489"
---
# <a name="query-ingested-telemetry-data"></a>Feldolgozott telemetria-adatok lekérdezése

Ez a cikk azt ismerteti, hogyan lehet lekérdezni a betöltött érzékelő adatait az Azure FarmBeats.

Az adatok betöltése eszközök internetes hálózata (IoT) erőforrásokból, például az eszközökről és érzékelőkről gyakori forgatókönyv a FarmBeats-ben. Metaadatokat hozhat létre az eszközök és érzékelők számára, majd a korábbi adatokat kanonikus formátumban FarmBeats. Ha az érzékelőre vonatkozó információk elérhetők a FarmBeats-Datahub, akkor ugyanezt lekérdezheti a gyakorlatban hasznosítható elemzések és modellek létrehozásához.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt folytatná ezt a cikket, győződjön meg róla, hogy telepítette a FarmBeats és a betöltött érzékelő telemetria adatait a IoT-eszközökről a FarmBeats.

Az érzékelő telemetria-információinak betöltéséhez látogasson el a [korábbi telemetria-adatbevitelre](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Mielőtt továbblépne, meg kell győződnie arról, hogy jól ismeri a FarmBeats REST API-kat, mivel az API-k használatával kérdezi le a betöltött telemetria. A FarmBeats API-kkal kapcsolatos további információkért lásd: [FARMBEATS REST API](rest-api-in-azure-farmbeats.md)-k. Győződjön meg **arról, hogy képes API-kérelmeket készíteni a FarmBeats Datahub-végpontjának**.

## <a name="query-ingested-sensor-telemetry-data"></a>Betöltött érzékelő telemetria-beli lekérdezése

A FarmBeats kétféleképpen férhet hozzá a telemetria-adatokhoz, és lekérdezéseket végezhet le:

- API és
- Time Series Insights (ÁME).

### <a name="query-using-rest-api"></a>Lekérdezés REST API használatával

Kövesse a következő lépéseket a betöltött érzékelő telemetria-adatok lekérdezéséhez a FarmBeats REST API-k használatával:

1. Azonosítsa az Önt érdeklő érzékelőt. Ezt úgy teheti meg, hogy lekéri a GET kérelmet a/Sensor API-ra.

> [!NOTE]
> Az érdekelt érzékelő objektum **azonosítója** és **sensorModelId** .

2. Hozzon végre egy GET/{ID}/SensorModel API-t a **sensorModelId** az 1. lépésben feljegyzett módon. Az "érzékelő modell" az érzékelőből származó betöltött telemetria kapcsolatos összes metaadatot és részletet tartalmaz. Például az érzékelő **modell** objektumon belül az érzékelő **mértéke** az érzékelő által küldött és a milyen típusokra és egységekre vonatkozó adatokkal rendelkezik. Példa:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Jegyezze fel az érzékelő modell GET/{ID} hívásának válaszát.

3. Tegye fel a/Telemetry API-t a következő bemeneti adattartalommal

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. A/Telemetry API válasza a következőhöz hasonlóan fog kinézni:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
A fenti példában szereplő válaszban a lekérdezett érzékelő telemetria két időbélyeg ("moist_soil_last") és a jelentett telemetria értékeit tartalmazza a két időbélyegben. A jelentett értékek típusának és egységének értelmezéséhez a kapcsolódó érzékelő modellre kell hivatkoznia (a 2. lépésben leírtak szerint).

### <a name="query-using-azure-time-series-insights-tsi"></a>Lekérdezés Azure Time Series Insights (ÁME) használatával

A FarmBeats [Azure Time Series Insights (ÁME)](https://azure.microsoft.com/services/time-series-insights/) használatával teszi lehetővé az adatok betöltését, tárolását, lekérdezését és megjelenítését a IoT-skálán – a nagy mértékben környezetfüggő és idősorozatra optimalizált adatok.

A rendszer telemetria-adatkérést fogad egy EventHub, majd feldolgozza és leküldi a FarmBeats erőforráscsoport belüli ÁME-környezetbe. Az adatok közvetlenül az ÁME-ből kérhetők le. További információ: az [ÁME dokumentációja](../../time-series-insights/time-series-insights-explorer.md)

Kövesse az alábbi lépéseket az ÁME-beli adatmegjelenítéshez:

1. Nyissa meg az **Azure Portal**  >  **FarmBeats DataHub erőforráscsoportot** > válassza a **Time Series Insights** Environment (ÁME-XXXX) > **adatelérési házirendek** lehetőséget. Felhasználó hozzáadása olvasó vagy közreműködői hozzáféréssel.
2. Nyissa meg **Time Series Insights** környezet **Áttekintés** lapját (ÁME-XXXX), és válassza ki a **Time Series Insights Explorer URL-címét**. Most már láthatóvá teheti a betöltött telemetria.

A telemetria tárolása, lekérdezése és megjelenítése mellett az ÁME is lehetővé teszi az integrációt egy Power BI irányítópulton. További információ: [itt]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Következő lépések

Mostantól lekérdezte az érzékelő adatait az Azure FarmBeats-példányból. Most megismerheti, hogyan [hozhatja](generate-maps-in-azure-farmbeats.md#generate-maps) ki a térképeket a farmokhoz.