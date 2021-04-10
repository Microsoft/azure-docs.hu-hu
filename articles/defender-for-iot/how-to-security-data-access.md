---
title: A biztonsági & vonatkozó javaslati információinak elérése
description: Ismerje meg, hogyan férhet hozzá a biztonsági riasztásokhoz és a javaslati adataihoz a Defender for IoT használata esetén.
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 160f7c014c890f5d8c4dd6366d3acca70f21ad11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781687"
---
# <a name="access-your-security-data"></a>Hozzáférés a biztonsági adataihoz

A IoT Defender a biztonsági riasztásokat, a javaslatokat és a nyers biztonsági adatait tárolja (ha úgy dönt, hogy menti azt) a Log Analytics munkaterületen.

## <a name="log-analytics"></a>Log Analytics

Annak konfigurálása, hogy mely Log Analytics munkaterület legyen használatban:

1. Nyissa meg az IoT hubot.
1. Kattintson a **Beállítások** panelre a **Biztonság** szakaszban.
1. Kattintson **az adatgyűjtés** lehetőségre, és módosítsa a log Analytics munkaterület konfigurációját.

A riasztások és javaslatok elérése a Log Analytics munkaterületen a konfiguráció után:

1. Válasszon ki egy riasztást vagy javaslatot a Defender for IoT.
1. Kattintson a **további vizsgálat** elemre, majd kattintson **ide a riasztást tartalmazó eszközök megtekintéséhez kattintson ide, és tekintse meg a DeviceID oszlopot**.

A Log Analytics adatainak lekérdezésével kapcsolatos részletekért lásd: Ismerkedés [a lekérdezésekkel a log Analyticsban](../azure-monitor/logs/get-started-queries.md).

## <a name="security-alerts"></a>Biztonsági riasztások

A biztonsági riasztásokat a _AzureSecurityOfThings. SecurityAlert_ táblában tárolja a Defender for IoT megoldáshoz konfigurált log Analytics munkaterületen.

Számos hasznos lekérdezést adtunk meg a biztonsági riasztások felfedezésének megkezdéséhez.

### <a name="sample-records"></a>Példák a rekordokra

Néhány véletlenszerű rekord kiválasztása

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Leírás                                             | Extendedproperties példányt paraméterként                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11 – 18T18:10:29.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | A találgatásos támadás sikeres volt           | A találgatásos támadás az eszközön sikeres volt.        |    {"Teljes forrás címe": "[ \" 10.165.12.18: \" ]", "user Names": "[ \" \" ]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11 – 19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Sikeres helyi bejelentkezés az eszközön      | Az eszköz sikeres helyi bejelentkezést észlelt     | {"Távoli címe": "?", "távoli port": "", "helyi port": "", "login shell": "/bin/Su", "bejelentkezési folyamat azonosítója": "28207", "Felhasználónév": "támadó", "DeviceId": "IoT-Device-Linux"} |
| 2018-11 – 19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Sikertelen helyi bejelentkezési kísérlet az eszközön  | A rendszer sikertelen helyi bejelentkezési kísérletet észlelt az eszközön. |    {"Távoli címe": "?", "távoli port": "", "helyi port": "", "login shell": "/bin/Su", "bejelentkezési folyamat azonosítója": "22644", "Felhasználónév": "támadó", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Eszköz összefoglalása

Az elmúlt héten észlelt különböző biztonsági riasztások számának lekérése IoT Hub, eszköz, riasztás súlyossága, riasztás típusa szerint csoportosítva.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Darabszám |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | A találgatásos támadás sikeres volt           | 9   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | Sikertelen helyi bejelentkezési kísérlet az eszközön  | 242 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Sikeres helyi bejelentkezés az eszközön      | 31  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | Kriptográfiai érme bányász                     | 4   |

### <a name="iot-hub-summary"></a>IoT hub – összefoglalás

Válassza ki az elmúlt héten riasztásokkal rendelkező különböző eszközök számát, IoT Hub, riasztás súlyossága, riasztás típusa

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Magas          | A találgatásos támadás sikeres volt           | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Sikertelen helyi bejelentkezési kísérlet az eszközön  | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Magas          | Sikeres helyi bejelentkezés az eszközön      | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Kriptográfiai érme bányász                     | 1          |

## <a name="security-recommendations"></a>Biztonsági javaslatok

A biztonsági javaslatokat a Defender for IoT megoldáshoz konfigurált Log Analytics munkaterület _AzureSecurityOfThings. SecurityRecommendation_ táblájában tárolja a rendszer.

Számos hasznos lekérdezést kaptunk a biztonsági javaslatok megkezdésének megkönnyítéséhez.

### <a name="sample-records"></a>Példák a rekordokra

Néhány véletlenszerű rekord kiválasztása

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Leírás | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes | Aktív | A bemeneti láncban található engedékeny tűzfalszabályok találhatók | A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz | {"Szabályok": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes | Aktív | A bemeneti láncban található engedékeny tűzfalszabályok találhatók | A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz | {"Szabályok": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>Eszköz összefoglalása

Szerezze be a különböző aktív biztonsági javaslatok számát IoT Hub, az eszköz, az ajánlás súlyossága és a típus szerint csoportosítva.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Darabszám |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | 2   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | 1 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | 1  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | 4   |

## <a name="next-steps"></a>Következő lépések

- Olvassa el a Defender for IoT [áttekintését](overview.md)
- Tudnivalók a IoT-architektúra Defender- [architektúráról](architecture.md)
- [A Defender IoT-riasztások](concept-security-alerts.md) megismerése és megismerése
- A Defender megismerése és megismerése [IoT-javaslatokkal](concept-recommendations.md)