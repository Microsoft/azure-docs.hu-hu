---
title: Azure Event Grid – témakörökhöz vagy tartományokhoz tartozó diagnosztikai naplók
description: Ez a cikk az Azure Event Grid-témakör vagy egy tartomány diagnosztikai naplóival kapcsolatos elméleti információkat tartalmaz.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 36ade14932b5d25c7a1fe05632da671de68ba3bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574986"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Diagnosztikai naplók Azure Event Grid témakörökhöz/tartományokhoz
A diagnosztikai beállítások lehetővé teszik Event Grid felhasználók számára a **közzétételi és kézbesítési hibák** naplóinak rögzítését vagy megtekintését egy Storage-fiókban, egy Event hub-ban vagy egy log Analytics-munkaterületen. Ez a cikk a naplókhoz és egy példaként szolgáló naplóbejegyzés sémáját ismerteti.


## <a name="schema-for-publishdelivery-failure-logs"></a>Közzétételi/kézbesítési hibák naplóihoz tartozó séma

| Tulajdonság neve | Adattípus | Leírás |
| ------------- | --------- | ----------- | 
| Idő | DateTime | Az az idő, amikor a naplóbejegyzés létrejött <p>**Példa értéke:**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | Sztring | Az esemény-előfizetés neve <p>**Példa értéke:** "EVENTSUB1"</p> <p>Ez a tulajdonság csak a kézbesítési hibák naplói esetében létezik.</p>  |
| Kategória | Sztring | A napló kategóriájának neve. <p>**Példa értékei:** "DeliveryFailures" vagy "PublishFailures" | 
| OperationName | Sztring | A művelet neve a hibát okozta.<p>**Példa értékei:** Kézbesítési hibák esetén "kézbesítés". |
| Üzenet | Sztring | A hiba okát és egyéb további részleteket ismertető üzenet a felhasználó számára. |
| ResourceId | Sztring | A témakör/tartományi erőforrás erőforrás-azonosítója<p>**Példa értékei:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Példa

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>Következő lépések
Ha szeretné megtudni, hogyan engedélyezheti a diagnosztikai naplókat a témakörök vagy tartományok számára, olvassa el a [diagnosztikai naplók engedélyezése](enable-diagnostic-logs-topic.md)című témakört.
