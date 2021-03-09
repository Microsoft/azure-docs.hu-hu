---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0e487b3ab3663c765c052f2064201865508ef57f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511006"
---
## <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások
Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a tűzfalbeállítások megkerülésének engedélyezése** beállítást, a következő szolgáltatások kapnak hozzáférést a Event Hubs erőforrásaihoz.

| Megbízható szolgáltatás | Támogatott használati forgatókönyvek | 
| --------------- | ------------------------- | 
| Azure Event Grid | Lehetővé teszi, hogy a Azure Event Grid eseményeket küldjön az esemény-huboknak a Event Hubs-névtérben. A következő lépéseket is végre kell hajtania: <ul><li>A rendszer által hozzárendelt identitás engedélyezése egy témakörhöz vagy tartományhoz</li><li>Az identitás hozzáadása az Azure Event Hubs adatfeladói szerepkörhöz a Event Hubs névtérben</li><li>Ezt követően állítsa be azt az esemény-előfizetést, amely az Event hub-t használja végpontként a rendszer által hozzárendelt identitás használatára.</li></ul> <p>További információ: [esemény kézbesítése felügyelt identitással](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (diagnosztikai beállítások) | Lehetővé teszi a Azure Monitor számára diagnosztikai információk küldését az Event Hubs névterében lévő Event hubokba. Azure Monitor tud olvasni az Event hub-ból, és az Event hub-ba is írhat. |
| Azure Stream Analytics | Lehetővé teszi, hogy egy Azure Stream Analytics feladatból beolvassa az adatokat ([input](../articles/stream-analytics/stream-analytics-add-inputs.md)), vagy adatokat írjon ([output](../articles/stream-analytics/event-hubs-output.md)) az Event Hubs névtérbe. <p>**Fontos**: a stream Analytics feladatot úgy kell konfigurálni, hogy **felügyelt identitást** használjon az Event hub eléréséhez. További információ: a [felügyelt identitások használata az Event Hub Azure stream Analytics feladatból való eléréséhez (előzetes verzió)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Lehetővé teszi, hogy a IoT Hub üzeneteket küldjön az Event hub-névtérbe. A következő lépéseket is végre kell hajtania: <ul><li>A rendszer által hozzárendelt identitás engedélyezése az IoT hub számára</li><li>Adja hozzá az identitást az Azure Event Hubs adatfeladói szerepkörhöz a Event Hubs névtérben.</li><li>Ezután konfigurálja a IoT Hub, amely az Event hub-t használja egyéni végpontként az identitás-alapú hitelesítés használatához.</li></ul>
| Azure API Management | <p>A API Management szolgáltatás lehetővé teszi események küldését egy Event hub-ba a Event Hubs-névtérben.</p> <ul><li>Az egyéni munkafolyamatokat elindíthatja, ha eseményeket küld az Event hub-nak, amikor a [Send-Request házirend](../articles/api-management/api-management-sample-send-request.md)használatával API-t hív meg.</li><li>Az Event hub-t az API-k háttereként is kezelheti. Egy minta házirend esetében lásd: [hitelesítés felügyelt identitás használatával az Event hub eléréséhez](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). A következő lépéseket is végre kell hajtania:<ol><li>A rendszer által hozzárendelt identitás engedélyezése a API Management példányon. Útmutatásért lásd: [felügyelt identitások használata az Azure API Managementban](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Az identitás hozzáadása az **Azure Event Hubs Adatfeladói** szerepkörhöz a Event Hubs névtérben</li></ol></li></ul> | 
