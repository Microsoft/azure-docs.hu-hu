---
title: A Azure Security Center-riasztások sémái
description: Ez a cikk a biztonsági riasztások Azure Security Center által használt különböző sémákat ismerteti.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 55f8d37d435aa8adeb4d97246ce7b2c7811140be
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557998"
---
# <a name="security-alerts-schemas"></a>Biztonsági riasztások sémái

Ha az előfizetése engedélyezve van az Azure Defender számára, akkor biztonsági riasztásokat fog kapni, amikor a Security Center észleli az erőforrásaik fenyegetését.

Ezeket a biztonsági riasztásokat megtekintheti Azure Security Center **veszélyforrások elleni védelmi** oldalain, illetve külső eszközökkel, például a következőkkel:

- [Azure Sentinel](../sentinel/index.yml) – a Microsoft Felhőbeli natív Siem-je. A Sentinel Connector Azure Security Center riasztásokat küld, és elküldi azokat az Azure Sentinel [log Analytics munkaterületére](../azure-monitor/logs/quick-create-workspace.md) .
- Harmadik féltől származó SIEM-ekkel való adatküldés az [Azure Event Hubsba](../event-hubs/index.yml). Ezután integrálja az Event hub-adatait egy harmadik féltől származó SIEM-mel. További információ a [stream-riasztásokról a Siem, a SOAR vagy az IT Service Management megoldáshoz](export-to-siem.md).
- [A REST API](/rest/api/securitycenter/) – ha a REST API használatával fér hozzá a riasztásokhoz, tekintse meg az [online riasztások API dokumentációját](/rest/api/securitycenter/alerts).

Ha bármilyen programozott módszert használ a riasztások felhasználásához, a megfelelő sémára lesz szüksége az Ön számára releváns mezők megtalálásához. Emellett, ha egy Event hub-ba exportál, vagy a munkafolyamat-automatizálást általános HTTP-összekötővel próbálja elindítani, a sémák segítségével megfelelően elemezheti a JSON-objektumokat.

>[!IMPORTANT]
> A séma némileg eltér az egyes forgatókönyvek esetében, ezért ügyeljen arra, hogy az alábbi megfelelő lapot válassza ki.


## <a name="the-schemas"></a>A sémák 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Munkafolyamat-automatizálás és folyamatos Exportálás az Event hub-ba](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>JSON-minta a Logic Apps, az Event hub és a harmadik féltől származó SIEM-nek elküldett riasztásokhoz

Az alábbiakban megtalálhatja az átadott riasztási események sémáját:

- Az Security Center munkafolyamat-automatizálásában konfigurált Azure Logic app-példányok
- Azure Event hub a Security Center folyamatos exportálási funkciójával

További információ a munkafolyamat-automatizálási szolgáltatásról: [Security Center eseményindítók válaszának automatizálása](workflow-automation.md).

További információ a folyamatos exportálásról: [Security Center adatok folyamatos exportálása](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel-és Log Analytics-munkaterületek](#tab/schema-sentinel)

A Sentinel Connector Azure Security Center riasztásokat küld, és elküldi azokat az Azure Sentinel Log Analytics munkaterületére. 

Ha Security Center riasztásokat használó Sentinel-esetet vagy eseményt szeretne létrehozni, szüksége lesz az alább látható riasztások sémájára. 

Az Azure Sentinel szolgáltatással kapcsolatos további információkért tekintse meg [a dokumentációt](../sentinel/index.yml).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-tevékenységnapló](#tab/schema-activitylog)

Azure Security Center naplózza a biztonsági riasztásokat az Azure-tevékenység naplójában lévő eseményekként.

A biztonsági riasztások eseményeit a tevékenység naplójában tekintheti meg, ha a riasztás aktiválása eseményt keresi, ahogy az látható:

[![A riasztás aktiválása esemény tevékenységi naplójának keresése](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>JSON-minta az Azure-tevékenység naplójába küldendő riasztásokhoz

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

|Mező|Leírás|
|----|----|
|**csatornák**|Állandó, "művelet"|
|**correlationId**|A Azure Security Center riasztás azonosítója|
|**Leírás**|A riasztás leírása|
|**eventDataId**|Lásd: correlationId|
|**eventName**|Az érték és a localizedValue almező tartalmazza a riasztás megjelenítendő nevét.|
|**Kategória**|Az érték és a localizedValue almező állandó – "biztonság"|
|**eventTimestamp**|UTC-időbélyeg a riasztás generálásakor|
|**id**|A teljesen minősített riasztás azonosítója|
|**szint**|Állandó, "tájékoztató"|
|**operationId**|Lásd: correlationId|
|**operationName**|Az érték mező állandó – "Microsoft. Security/Locations/Alerts/Activate/Action", a honosított érték pedig "riasztás aktiválása" lesz (potenciálisan honosítható a felhasználó területi beállítása)|
|**resourceGroupName**|Tartalmazni fogja az erőforráscsoport nevét|
|**resourceProviderName**|Az érték és a localizedValue almező állandó – "Microsoft. Security"|
|**resourceType**|Az érték és a localizedValue almező állandó – "Microsoft. Security/Locations/riasztások"|
|**resourceId**|A teljesen minősített Azure-erőforrás azonosítója|
|**állapota**|Az érték és a localizedValue almező állandó – "aktív"|
|**Részállapot**|Az érték és a localizedValue almező üres|
|**submissionTimestamp**|Az esemény küldésének UTC-időbélyege a tevékenység naplójába|
|**subscriptionId**|A feltört erőforrás előfizetési azonosítója|
|**Tulajdonságok**|A riasztáshoz tartozó további tulajdonságok JSON-táska. Ezek az egyik riasztásból a másikra válthatnak, azonban a következő mezők jelennek meg az összes riasztásban:<br>– Súlyosság: a támadás súlyossága<br>-compromisedEntity: a feltört erőforrás neve<br>-remediationSteps: a végrehajtandó szervizelési lépések tömbje<br>-szándék: a riasztás leölési láncra irányuló szándéka. A lehetséges leképezések dokumentálva vannak a [szándékok táblázatában](alerts-reference.md#intentions)|
|**relatedEvents**|Állandó – üres tömb|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

A Microsoft Graph az adatokhoz és az intelligenciához tartozó átjáró a Microsoft 365ban. Egy egységesített programozható modellt biztosít, amellyel a Microsoft 365, a Windows 10 és a Enterprise Mobility + Security rengeteg adatmennyiségét érheti el. A Microsoft Graphban található adatmennyiség használatával olyan szervezeteknek és fogyasztóknak készült alkalmazásokat hozhat létre, amelyek több millió felhasználóval működnek.

Az MS Graphba elküldett biztonsági riasztások sémája és JSON-ábrázolása [a Microsoft Graph dokumentációjában](/graph/api/resources/alert)érhető el.

---


## <a name="next-steps"></a>Következő lépések

Ez a cikk azokat a sémákat ismerteti, amelyeket a Azure Security Center veszélyforrások elleni védelmi eszközei használnak a biztonsági riasztási információk küldésekor.

A biztonsági riasztások Security Centeron kívülről történő elérésének módjaival kapcsolatos további információkért tekintse meg a következő lapokat:

- [Azure Sentinel](../sentinel/index.yml) – a Microsoft Felhőbeli natív Siem-je
- [Azure Event Hubs](../event-hubs/index.yml) – a Microsoft teljes körűen felügyelt, valós idejű adatfeldolgozási szolgáltatása
- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)
- [Log Analytics-munkaterületek](../azure-monitor/logs/quick-create-workspace.md) – a Azure Monitor a log Analytics munkaterületen tárolja a naplózási adatokat, egy olyan tárolót, amely adatokat és konfigurációs adatokat tartalmaz.