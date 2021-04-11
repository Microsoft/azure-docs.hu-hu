---
title: Azure Policy Event Grid forrásként
description: Ez a cikk azt ismerteti, hogyan használható a Azure Policy Event Grid eseményforrásként. Ez biztosítja a sémát és az oktatóanyagra és útmutatókra mutató hivatkozásokat.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735065"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Azure Policy Event Grid forrásként

Ez a cikk a [Azure Policy](../governance/policy/index.yml) eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](./event-schema.md). Emellett a gyors indulások és oktatóanyagok listáját is tartalmazza, amelyekkel Azure Policy lehet használni az esemény forrásaként.

## <a name="available-event-types"></a>Elérhető események típusai

Azure Policy a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Házirend megfelelőségi állapotának létrehozásakor következik be. |
| Microsoft. PolicyInsights. PolicyStateChanged | A szabályzat megfelelőségi állapotának megváltozásakor következik be. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Házirend megfelelőségi állapotának törlésekor következik be. |

## <a name="example-event"></a>Példa eseményre

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)
Az alábbi példa egy házirend állapot létrehozott eseményének sémáját mutatja be: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

A házirend állapotának változási eseményének sémája hasonló: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Az alábbi példa egy házirend állapot létrehozott eseményének sémáját mutatja be: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

A házirend állapotának változási eseményének sémája hasonló: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Esemény tulajdonságai

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Annak az erőforrásnak a teljes azonosítója, amelyre a megfelelőségi állapot módosul, beleértve az erőforrás nevét és az erőforrás típusát is. A formátumot használja, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Azure Policy az eseményekre vonatkozó adatgyűjtést. |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `source` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Annak az erőforrásnak a teljes azonosítója, amelyre a megfelelőségi állapot módosul, beleértve az erőforrás nevét és az erőforrás típusát is. A formátumot használja, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `time` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Azure Policy az eseményekre vonatkozó adatgyűjtést. |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `timestamp` | sztring | Az erőforrás Azure Policy általi vizsgálatának időpontja (UTC). Az események rendezéséhez használja ezt a tulajdonságot a legfelső szintű `eventTime` vagy a `time` Tulajdonságok helyett. |
| `policyAssignmentId` | sztring | A szabályzat-hozzárendelés erőforrás-azonosítója. |
| `policyDefinitionId` | sztring | A házirend-definíció erőforrás-azonosítója. |
| `policyDefinitionReferenceId` | sztring | A házirend-definíció hivatkozási azonosítója a kezdeményezési definícióban, ha a házirend-hozzárendelés egy kezdeményezéshez van hozzárendelve. Lehet üres. |
| `complianceState` | sztring | Az erőforrás megfelelőségi állapota a szabályzat-hozzárendelés tekintetében. |
| `subscriptionId` | sztring | Az erőforrás előfizetés-azonosítója. |
| `complianceReasonCode` | sztring | A megfelelőségi ok kódja. Lehet üres. |

## <a name="next-steps"></a>Következő lépések

- A forgatókönyvek útválasztási Azure Policy állapotának változási eseményeiért lásd: [Event Grid használata a szabályzatok állapotának változási értesítéseihez](../governance/policy/tutorials/route-state-change-events.md).
- A Azure Policy és a Event Grid integrálásának áttekintését lásd: [Event Grid használatával történő reagálás Azure Policy eseményekre](../governance/policy/concepts/event-overview.md).
- A Azure Event Grid bemutatása: [Mi az Event Grid?](./overview.md)
- Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](./subscription-creation-schema.md).