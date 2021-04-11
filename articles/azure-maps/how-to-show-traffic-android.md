---
title: Forgalmi adatok megjelenítése az Android-térképeken | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a forgalmi adatokat egy térképen a Microsoft Azure Maps Android SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aabe246c343537a42c33d3eaad0bfae3989022fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604515"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Forgalmi adatok megjelenítése a térképen (Android SDK)

A flow-adatok és az incidensek az adatok a térképen megjeleníthető kétféle forgalmi adatok. Ez az útmutató bemutatja, hogyan jelenítheti meg mindkét típusú forgalmi adatokat. Az incidensek olyan pont-és vonal-alapú adatmennyiségből állnak, mint a konstrukciók, a közúti bezárások és a balesetek. A flow adatai az úton lévő forgalom forgalmára vonatkozó metrikákat jelenítenek meg.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania. A cikkben szereplő kódrészletek behelyezhetők a Maps `onReady` Event handlerbe.

## <a name="show-traffic-on-the-map"></a>Forgalom megjelenítése a térképen

A Azure Mapsban kétféle forgalmi adatok érhetők el:

- Incidensek – olyan pont-és vonal-alapú adatmennyiségből állnak, mint például az építőipar, a közúti bezárások és a balesetek.
- Flow-adatok – mérőszámokat biztosít a forgalom forgalmáról az utakon. Gyakran a forgalmi forgalomra vonatkozó adatokat használják az utak színezésére. A színek attól függnek, hogy mekkora forgalom lassul le a folyamaton, a sebességkorlátozás vagy más metrika alapján. Négy érték adható át a `flow` Térkép forgalmi lehetőségéhez.

    |Folyamat értéke | Leírás|
    | :-- | :-- |
    | Forgalomáramlási. NONE | Nem jeleníti meg a forgalmi adatokat a térképen |
    | Forgalomáramlási. relatív | Az út szabad áramlási sebességéhez viszonyított forgalmi adatokat jeleníti meg |
    | TrafficFlow.RELATIVE_DELAY | A várt átlagos késésnél lassabb területeket jelenít meg. |
    | Forgalomáramlási. ABSOLUTE | Az összes jármű teljes sebességét jeleníti meg az úton |

A következő kód bemutatja, hogyan jelenítheti meg a forgalmi adatokat a térképen.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

Az alábbi képernyőfelvételen a fenti kódot ábrázoló valós idejű forgalmi információk láthatók a térképen.

![Valós idejű forgalmi adatokat megjelenítő Térkép](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Forgalmi incidens részleteinek beolvasása

A forgalmi incidens részleteit az incidens a térképen való megjelenítéséhez használt funkció tulajdonságai között érheti el. A forgalmi incidenseket a rendszer a Azure Maps Traffic incidens Vector csempe szolgáltatással adja hozzá a térképhez. A vektoros csempék adatformátuma, ha [itt van dokumentálva](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). A következő kód egy Click eseményt helyez el a térképhez, és lekéri a forgalmi incidens funkcióját, amelyre rákattintott, és egy Toast-üzenetet jelenít meg a részletek közül.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

Az alábbi képernyőfelvételen a fenti kód a valós idejű forgalomra vonatkozó információkat jeleníti meg a térképen egy olyan, az incidensek megjelenítését jelző üzenettel.

![A valós idejű forgalmi információk megjelenítése egy Toast üzenet megjelenítésével – incidens részletei](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Következő lépések

A következő útmutatókból megtudhatja, hogyan adhat hozzá további információkat a térképhez:

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](how-to-add-tile-layer-android-map.md)
