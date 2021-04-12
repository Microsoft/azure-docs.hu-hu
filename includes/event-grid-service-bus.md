---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 49e35687820679a1c06cf19e7a26b3b04a1e1318
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363244"
---
## <a name="available-event-types"></a>Elérhető események típusai

Service Bus a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. ServiceBus. ActiveMessagesAvailableWithNoListeners | Akkor következik be, amikor aktív üzenetek vannak egy várólistában vagy előfizetésben, és nincsenek figyelő fogadók. |
| Microsoft. ServiceBus. DeadletterMessagesAvailableWithNoListener | Akkor következik be, amikor aktív üzenetek vannak egy kézbesítetlen levelek várólistájában, és nincsenek aktív figyelők. |
| Microsoft. ServiceBus. ActiveMessagesAvailablePeriodicNotifications | Rendszeres időközönként, ha aktív üzenetek vannak egy várólistában vagy előfizetésben, még akkor is, ha vannak aktív figyelők az adott várólistán vagy előfizetésen. |
| Microsoft. ServiceBus. DeadletterMessagesAvailablePeriodicNotifications | Rendszeres időközönként, ha a várólista vagy előfizetés kézbesítetlen levél entitásában lévő üzenetek akkor is megjelennek, ha aktív figyelők vannak az adott várólista vagy előfizetés kézbesítetlen levél entitásán. | 

## <a name="example-event"></a>Példa eseményre

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Figyelők nélkül elérhető aktív üzenetek
Ez az esemény akkor jön létre, ha aktív üzenetek találhatók egy üzenetsorban vagy előfizetésben, és nincsenek figyelő fogadók.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>Figyelő nélkül elérhető kézbesítetlen levelek-üzenetek

A kézbesítetlen levelek várólistája esemény sémája hasonló. A kézbesítetlen levelek várólistáján legalább egy olyan eseményt kap, amely üzenetekkel és nem aktív fogadókkal rendelkezik.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Időszakos értesítések az aktív üzenetekről
Ez az esemény akkor jön létre rendszeresen, ha aktív üzenetei vannak az adott várólistán vagy előfizetésen, még akkor is, ha az adott várólistán vagy előfizetésen aktív figyelők vannak.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Kézbesítetlen levelek üzenetek – rendszeres értesítések
Ez az esemény akkor jön létre rendszeresen, ha az adott várólistán vagy előfizetésen kézbesítetlen levelek az üzenetek, még akkor is, ha vannak aktív figyelők az adott várólista vagy előfizetés kézbesítetlen levelek entitásán.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Figyelők nélkül elérhető aktív üzenetek
Ez az esemény akkor jön létre, ha aktív üzenetek találhatók egy üzenetsorban vagy előfizetésben, és nincsenek figyelő fogadók.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>Figyelő nélkül elérhető kézbesítetlen levelek-üzenetek

A kézbesítetlen levelek várólistája esemény sémája hasonló. A kézbesítetlen levelek várólistáján legalább egy olyan eseményt kap, amely üzenetekkel és nem aktív fogadókkal rendelkezik.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Időszakos értesítések az aktív üzenetekről
Ez az esemény akkor jön létre rendszeresen, ha aktív üzenetei vannak az adott várólistán vagy előfizetésen, még akkor is, ha az adott várólistán vagy előfizetésen aktív figyelők vannak.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Kézbesítetlen levelek üzenetek – rendszeres értesítések
Ez az esemény akkor jön létre rendszeresen, ha az adott várólistán vagy előfizetésen kézbesítetlen levelek az üzenetek, még akkor is, ha vannak aktív figyelők az adott várólista vagy előfizetés kézbesítetlen levelek entitásán.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

---



### <a name="event-properties"></a>Esemény tulajdonságai

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)
Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | BLOB Storage-események |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `source` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `type` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `time` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | BLOB Storage-események |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `namespaceName` | sztring | A Service Bus névtér, amelyben az erőforrás található. |
| `requestUri` | sztring | Az eseményt kibocsátó adott üzenetsor vagy előfizetés URI azonosítója. |
| `entityType` | sztring | Az eseményeket kibocsátó Service Bus entitás típusa (Üzenetsor vagy előfizetés). |
| `queueName` | sztring | Az üzenetsor aktív üzenetekkel, ha egy várólistára van előfizetni. Az érték null, ha témaköröket vagy előfizetéseket használ. |
| `topicName` | sztring | A témakör az aktív üzenetekkel rendelkező Service Bus-előfizetéshez tartozik. Az érték null, ha üzenetsor használata. |
| `subscriptionName` | sztring | A Service Bus-előfizetés aktív üzenetekkel. Az érték null, ha üzenetsor használata. |