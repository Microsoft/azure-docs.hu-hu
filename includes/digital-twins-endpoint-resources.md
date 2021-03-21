---
author: baanders
description: Az Azure Digital Twins-végpontok létrehozásához szükséges erőforrások fájljának belefoglalása
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054507"
---
### <a name="prerequisite-create-endpoint-resources"></a>Előfeltétel: végponti erőforrások létrehozása

Ahhoz, hogy egy végpontot az Azure digitális Ikrekhöz lehessen kapcsolni, a végponthoz használt Event Grid-témakört, az Event hub-t vagy Service Bus-témakört már léteznie kell.

A következő táblázat segítségével megtekintheti, hogy milyen erőforrásokat kell beállítani a végpont létrehozása előtt.

| Végpont típusa | Szükséges erőforrások (létrehozási utasításokhoz csatolva) |
| --- | --- |
| Event Grid végpont | [Event Grid-témakör](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs végpont | [Event &nbsp; hub- &nbsp; névtér](../articles/event-hubs/event-hubs-create.md)<br/><br/>[Event hub](../articles/event-hubs/event-hubs-create.md)<br/><br/>Választható [engedélyezési szabály](../articles/event-hubs/authorize-access-shared-access-signature.md) a kulcs alapú hitelesítéshez | 
| Service Bus végpont | [Service Bus névtér](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus témakör](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> Választható [engedélyezési szabály](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) a kulcs alapú hitelesítéshez|
