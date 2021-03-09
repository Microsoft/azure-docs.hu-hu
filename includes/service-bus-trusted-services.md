---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510887"
---
## <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások
Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a tűzfalbeállítások megkerülésének engedélyezése** beállítást, a következő szolgáltatások kapnak hozzáférést a Service Bus erőforrásaihoz.

| Megbízható szolgáltatás | Támogatott használati forgatókönyvek | 
| --------------- | ------------------------- | 
| Azure Event Grid | Lehetővé teszi, hogy a Azure Event Grid eseményeket küldjön a Service Bus névterében lévő várólistákba vagy témakörökbe. A következő lépéseket is végre kell hajtania: <ul><li>A rendszer által hozzárendelt identitás engedélyezése egy témakörhöz vagy tartományhoz</li><li>Az identitás hozzáadása a Azure Service Bus adatfeladói szerepkörhöz a Service Bus névtérben</li><li>Ezt követően konfigurálja azt az esemény-előfizetést, amely egy Service Bus-várólistát vagy-témakört használ végpontként a rendszer által hozzárendelt identitás használatára.</li></ul> <p>További információ: [esemény kézbesítése felügyelt identitással](../articles/event-grid/managed-service-identity.md)</p>|
| Azure API Management | <p>A API Management szolgáltatás lehetővé teszi üzenetek küldését egy Service Bus üzenetsor vagy témakör számára a Service Bus névtérben.</p><ul><li>Az egyéni munkafolyamatokat elindíthatja úgy, hogy üzeneteket küld az Service Bus üzenetsor vagy témakör számára, amikor egy API-t a [Send-Request házirend](../articles/api-management/api-management-sample-send-request.md)használatával hív meg.</li><li>Egy Service Bus üzenetsor vagy témakör is kezelhető egy API-ban. A minta házirendekkel kapcsolatban lásd: [hitelesítés felügyelt identitás használatával egy Service Bus üzenetsor vagy témakör eléréséhez](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). A következő lépéseket is végre kell hajtania:<ol><li>A rendszer által hozzárendelt identitás engedélyezése a API Management példányon. Útmutatásért lásd: [felügyelt identitások használata az Azure API Managementban](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Az identitás hozzáadása a **Azure Service Bus Adatfeladói** szerepkörhöz a Service Bus névtérben</li></ol></li></ul> | 