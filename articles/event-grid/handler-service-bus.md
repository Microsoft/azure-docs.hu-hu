---
title: Várólisták és témakörök Service Bus Azure Event Grid események eseménykezelői számára
description: Ismerteti, hogyan használhatók Service Bus várólisták és témakörök Azure Event Grid eseményekhez tartozó eseménykezelőként.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 12b72420e3475b46a4cd61ce5032b478af740dde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97399859"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Várólisták és témakörök Service Bus Azure Event Grid események eseménykezelői számára
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és **Azure Service Bus** az egyikük. 

A szolgáltatás-üzenetsor vagy-témakör kezelőként is használható a Event Grid eseményeihez. 

## <a name="service-bus-queues"></a>Service Bus-üzenetsorok
A Event Gridban lévő eseményeket közvetlenül átirányíthatja Service Bus várólistákba a pufferelés vagy parancs & a vállalati alkalmazásokban való használathoz.

A Azure Portal egy esemény-előfizetés létrehozásakor válassza a **Service Bus várólista** végpont típusaként lehetőséget, majd kattintson a **végpont kiválasztása** elemre Service Bus üzenetsor kiválasztásához.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Service Bus üzenetsor-kezelő hozzáadása a CLI használatával

Az Azure CLI esetében az alábbi példa egy Event Grid-témakör előfizetését és összekapcsolása Service Bus üzenetsor:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus-témakörök

Event Grid közvetlenül is átirányíthatja az eseményeket Service Bus témaköröket az Azure rendszeresemények Service Bus témakörökkel való kezeléséhez, vagy a parancs & vezérlő üzenetkezelési forgatókönyvekhez.

A Azure Portal egy esemény-előfizetés létrehozásakor válassza a **Service Bus témakör** végpont típusa lehetőséget, majd kattintson a **kiválasztás és a végpont** lehetőségre egy Service Bus témakör kiválasztásához.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Service Bus témakör-kezelő hozzáadása a CLI használatával

Az Azure CLI esetében az alábbi példa előfizet és csatlakoztat egy Event Grid-témakört egy Service Bus témakörhöz:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Ha egy eseményt egy Service Bus üzenetsor vagy témakör felügyelt üzenetként küld, a `messageid` közvetítő üzenet egy belső rendszerazonosító.

Az üzenet belső rendszerazonosítóját az esemény ismételt kézbesítése fogja megőrizni, így elkerülhető az ismétlődő kézbesítések elkerülése a Service Bus entitás **ismétlődő észlelésének** bekapcsolásával. Javasoljuk, hogy engedélyezze az ismétlődő észlelés időtartamát a Service Bus entitáson, hogy az esemény élettartama (TTL) vagy az újrapróbálkozások maximális időtartama legyen, attól függően, hogy melyik a hosszabb.

## <a name="rest-examples-for-put"></a>REST-példák (PUT)

### <a name="service-bus-queue"></a>Service Bus üzenetsor

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus üzenetsor – kézbesítés felügyelt identitással

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus témakör

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus témakör – kézbesítés felügyelt identitással

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Következő lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
