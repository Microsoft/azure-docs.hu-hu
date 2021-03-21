---
title: Azure Service Health értesítések küldése webhookok használatával
description: Személyre szabott értesítések küldése a szolgáltatás állapotával kapcsolatos eseményekről a meglévő probléma-felügyeleti rendszerre.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: a70c4fcd01a1a95c598d980004ee60292a6cf24b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594622"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Rendszerállapot-értesítések konfigurálása a probléma-felügyeleti rendszerekhez webhook használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhat Azure Service Health riasztásokat az adatküldés webhookokon keresztül a meglévő értesítési rendszeren való elküldéséhez.

Service Health riasztásokat úgy konfigurálhatja, hogy SMS vagy e-mailben értesítse Önt, ha egy Azure-szolgáltatás incidense érinti Önt.

Előfordulhat azonban, hogy már rendelkezik egy meglévő külső értesítési rendszerrel, amelyet használni szeretne. Ez a cikk a webhook hasznos adatainak legfontosabb részeit azonosítja. Továbbá leírja, hogyan hozhatók létre egyéni riasztások, amelyek értesítik a kapcsolódó szolgáltatási problémák előfordulásáról.

Ha előre konfigurált integrációt szeretne használni, tekintse meg a következőt:
* [Riasztások konfigurálása a ServiceNow](service-health-alert-webhook-servicenow.md)
* [Riasztások konfigurálása a PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Riasztások konfigurálása a OpsGenie](service-health-alert-webhook-opsgenie.md)

**Bemutató videó megtekintése:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Egyéni értesítés konfigurálása a Service Health webhook hasznos adataival
Saját egyéni webhook-integráció beállításához elemezni kell a Service Health értesítésen keresztül küldött JSON-adattartalmat.

Tekintse meg [például a](../azure-monitor/alerts/activity-log-alerts-webhook.md) `ServiceHealth` webhook hasznos adatait.

A szolgáltatás állapotára vonatkozó riasztást a következő helyen ellenőrizheti: `context.eventSource == "ServiceHealth"` . A legfontosabb tulajdonságok a következők:
- **az adat. Context. activityLog. status**
- **az adat. Context. activityLog. Level**
- **az adat. Context. activityLog. subscriptionId**
- **az adat. Context. activityLog. properties. title**
- **az adat. Context. activityLog. properties. impactStartTime**
- **az adat. Context. activityLog. properties. Communication**
- **az adat. Context. activityLog. properties. impactedServices**
- **az adat. Context. activityLog. properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Az incidenshez tartozó Service Health irányítópultra mutató hivatkozás létrehozása
Egy speciális URL-cím létrehozásával létrehozhat egy asztali vagy mobileszközön található Service Health irányítópultra mutató közvetlen hivatkozást. Használja a *trackingId* és a *subscriptionId* első három és utolsó három számjegyét ebben a formátumban:

https <i></i> ://app.Azure.com/h/*&lt; trackingId &gt;* / *&lt; első három és utolsó három számjegye subscriptionId &gt;*

Ha például a *subscriptionId* bba14129-e895-429b-8809-278e836ecdb3, és a *trackingId* a 0DET-Urb, a Service Health URL-cím a következő:

https <i></i> ://app.Azure.com/h/0DET-Urb/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>A probléma súlyosságának észleléséhez használja a szintet
A legalacsonyabbtól a legmagasabb súlyosságig a hasznos adatok **szintje** tulajdonsága *tájékoztató*, *Figyelmeztetés*, *hiba* vagy *kritikus* lehet.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Az érintett szolgáltatások elemzése az incidens hatókörének meghatározásához
Service Health riasztások több régióban és szolgáltatásban felmerülő problémákról is tájékoztatni tudnak. A teljes részletesség érdekében elemezni kell a értékét `impactedServices` .

A benne lévő tartalom egy olyan Escape- [JSON](https://json.org/) -karakterlánc, amely a kihagyás után egy másik JSON-objektumot tartalmaz, amely rendszeresen elemezhető. Például:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

 a következő lesz:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Ez a példa a következő problémákat mutatja be:
- "Riasztások & mérőszámok" Kelet-Ausztrália és Délkelet-Ausztráliában.
- "App Service" a Délkelet-ausztráliai régióban.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Webhook-integráció tesztelése HTTP POST-kérelem használatával

Kövesse az alábbi lépéseket:

1. Hozza létre a küldeni kívánt szolgáltatás-állapot adattartalmát. Tekintse meg a Service Health webhook hasznos adatait az Azure-beli [tevékenység naplójának riasztásait ismertető webhookokban](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Hozzon létre egy HTTP POST-kérelmet a következőképpen:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   "Sikeres 2XX" választ kell kapnia.

1. Nyissa meg a [PagerDuty](https://www.pagerduty.com/) , és ellenőrizze, hogy sikeresen beállította-e az integrációt.

## <a name="next-steps"></a>Következő lépések
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](./service-notifications.md).
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
