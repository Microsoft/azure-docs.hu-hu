---
title: Egyéni adatértékek hozzáadása az Azure-beli eseményekhez Event Hubs
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá egyéni adatértékeket az Azure Event Hubs eseményeihez.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104893472"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Egyéni adatértékek hozzáadása az Azure-beli eseményekhez Event Hubs
Mivel egy esemény főleg a bájtok átlátszatlan készletét tartalmazza, nehéz lehet ezeknek az eseményeknek a felhasználóinak megalapozott döntéseket hoznia a feldolgozásuk módjáról. Annak engedélyezéséhez, hogy az esemény-közzétevők jobb környezetet nyújtsanak a felhasználóknak, az események a kulcs-érték párok halmazának formájában egyéni metaadatokat is tartalmazhatnak. A metaadatok felvételének egyik gyakori forgatókönyve, hogy egy adott esemény által tárolt adatok típusával kapcsolatos célzást biztosítson, hogy a fogyasztók megértsék a saját formátumát, és megfelelően deszerializálják azt.

> [!NOTE]
> Ezt a metaadatokat a Event Hubs szolgáltatás nem használja, vagy semmilyen módon nem értelmezhető. csak az esemény-közzétevők és a fogyasztók közötti koordinációra létezik. 

A következő részben bemutatjuk, hogyan adhat hozzá egyéni adatokat különböző programozási nyelveken lévő eseményekhez. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

A teljes mintakód esetében lásd: [események közzététele egyéni metaadatokkal](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

A teljes kód minta: [események közzététele egyéni metaadatokkal](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

A teljes kód minta esetében lásd: [Event adat-köteg küldése a tulajdonságokkal](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő rövid útmutatók és példákat. 

- Gyors útmutató: [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- Minták a GitHubon: [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [írógéppel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
