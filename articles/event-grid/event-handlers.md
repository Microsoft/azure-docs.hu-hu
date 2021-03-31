---
title: Eseménykezelők Azure Event Grid
description: A Azure Event Grid támogatott eseménykezelőit ismerteti. Azure Automation, functions, Event Hubs, Hibrid kapcsolatok, Logic Apps, Service Bus, Queue Storage, webhookok.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86117015"
---
# <a name="event-handlers-in-azure-event-grid"></a>Eseménykezelők a Azure Event Gridban
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan az események kezelésére van konfigurálva. Az események kezelésére bármilyen webhookot is használhat. A webhookot nem kell az Azure-ban üzemeltetni az események kezeléséhez. A Event Grid csak a HTTPS webhook-végpontokat támogatja.

## <a name="supported-event-handlers"></a>Támogatott eseménykezelők
A támogatott eseménykezelők a következők: 

- [Webhookok](handler-webhooks.md). Azure Automation runbookok és Logic Apps webhookok használatával támogatottak. 
- [Azure functions](handler-functions.md)
- [Event Hubs](handler-event-hubs.md)
- [Relay hibrid kapcsolatok](handler-relay-hybrid-connections.md)
- [Várólisták és témakörök Service Bus](handler-service-bus.md)
- [Tárolási üzenetsorok](handler-storage-queues.md)

## <a name="next-steps"></a>Következő lépések
- Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
