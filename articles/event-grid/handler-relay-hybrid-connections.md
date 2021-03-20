---
title: Hibrid kapcsolat továbbítása Azure Event Grid események eseménykezelőként
description: Ismerteti, hogyan használhatók Azure Relay hibrid kapcsolatok Azure Event Grid események eseménykezelői.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 38233a2e103600f07837ce9a1ad8d63fe7e4fb99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91270202"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Hibrid kapcsolat továbbítása Azure Event Grid események eseménykezelőként
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és **Azure Relay** az egyikük. 

Az Azure **Relay hibrid kapcsolatok** használatával eseményeket küldhet a vállalati hálózaton belüli alkalmazásoknak, és nem rendelkezik nyilvánosan elérhető végponttal.

## <a name="tutorials"></a>Oktatóanyagok
Tekintse meg a következő oktatóanyagot, amely egy Azure Relay hibrid kapcsolat eseménykezelőként való használatát szemlélteti. 

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: események küldése hibrid kapcsolódásra](custom-event-to-hybrid-connection.md) | Egyéni eseményt küld egy meglévő hibrid kapcsolatra egy figyelő alkalmazás általi feldolgozáshoz. |

## <a name="rest-example-for-put"></a>REST-példa (PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Következő lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 