---
title: Event hub Azure Event Grid események eseménykezelője
description: Ismerteti, hogyan használható az Event hub Azure Event Grid eseményekhez eseménykezelőként.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005629"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event hub Azure Event Grid események eseménykezelője
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő végrehajt egy műveletet az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és az **azure Event Hubs** az egyik. 

Akkor használja a **Event Hubst** , ha a megoldás a Event Gridnál gyorsabban beolvassa az eseményeket, mint amennyit fel tud dolgozni. Ha az események egy Event hub-ban találhatók, az alkalmazás a saját időpontjában képes feldolgozni az Event hub eseményeit. Az események feldolgozását méretezheti úgy, hogy kezelni tudja a bejövő eseményeket.

## <a name="tutorials"></a>Oktatóanyagok
Lásd az alábbi példákat: 

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események irányítása az Azure Event Hubs az Azure CLI-vel](custom-event-to-eventhub.md) | Egyéni eseményt küld egy Event hub-nak egy alkalmazás általi feldolgozáshoz. |
| [Resource Manager-sablon: Event Grid egyéni témakör létrehozása és események küldése az Event hubhoz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Resource Manager-sablon, amely létrehoz egy egyéni témakörhöz tartozó előfizetést. Eseményeket küld egy Azure-Event Hubs. |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST-példák (PUT)


### <a name="event-hub"></a>Eseményközpont

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event hub – kézbesítés felügyelt identitással

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
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Következő lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
