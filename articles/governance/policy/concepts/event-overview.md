---
title: Azure Policy állapot-változási eseményekre való reagálás
description: A Azure Event Grid segítségével előfizethet az alkalmazás-házirend eseményeire, amelyek lehetővé teszik az alkalmazások számára, hogy bonyolult kód nélkül reagáljanak az állami változásokra.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735071"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Azure Policy állapot-változási eseményekre való reagálás

Azure Policy események lehetővé teszik, hogy az alkalmazások reagálni tudjanak az állapot változásaira. Ez az integráció a bonyolult kód vagy a költséges és nem hatékony lekérdezési szolgáltatások szükségessége nélkül történik. Ehelyett az eseményeket [Azure Event Grid](../../../event-grid/index.yml) az előfizetők, például a [Azure Functions](../../../azure-functions/index.yml), a [Azure Logic apps](../../../logic-apps/index.yml), vagy akár a saját egyéni HTTP-figyelő számára is leküldik.
Kritikus fontosságú, hogy csak a ténylegesen használt funkciókért kell fizetnie.

Azure Policy eseményeket a rendszer elküldje a Azure Event Gridnak, amely megbízható kézbesítési szolgáltatásokat biztosít az alkalmazásai számára a részletes újrapróbálkozási házirendek és a kézbesítetlen levelek kézbesítése révén. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](../../../event-grid/delivery-and-retry.md).

A gyakori Azure Policyi esemény a követés, amikor egy erőforrás megfelelőségi állapota megváltozik a szabályzat kiértékelése során. Az eseményvezérelt architektúra hatékony módszer a változásokra való reagálásra ahelyett, hogy az erőforrások megfelelőségi állapotát rögzített ütemterv szerint kellene beszkennelni.

> [!NOTE]
> Azure Policy állapot-változási események küldése Event Gridra, miután egy [kiértékelési trigger](../how-to/get-compliance-data.md#evaluation-triggers) befejezte az erőforrás-értékelést.

A teljes oktatóanyagért lásd: [útvonal-házirend állapotának változási eseményei Event Grid az Azure CLI-vel](../tutorials/route-state-change-events.md) .

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="A források és a kezelők Event Grid modellje" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Elérhető Azure Policy események

Az Event Grid [esemény-előfizetések](../../../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Azure Policy esemény-előfizetések három típusú eseményt tartalmazhatnak:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Házirend megfelelőségi állapotának létrehozásakor következik be. |
| Microsoft. PolicyInsights. PolicyStateChanged | A szabályzat megfelelőségi állapotának megváltozásakor következik be. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Házirend megfelelőségi állapotának törlésekor következik be. |

## <a name="event-schema"></a>Eseményséma

Azure Policy események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Megadhat egy Azure Policy eseményt, amikor a `eventType` tulajdonság a "Microsoft. PolicyInsights" karakterlánccal kezdődik.
Az Event Grid esemény tulajdonságainak használatáról további információt a következő dokumentumban olvashat:  
[Event Grid esemény sémája](../../../event-grid/event-schema.md).

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Annak az erőforrásnak a teljes azonosítója, amelyre a megfelelőségi állapot módosul, beleértve az erőforrás nevét és az erőforrás típusát is. A formátumot használja, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | object | Azure Policy az eseményekre vonatkozó adatgyűjtést. |
| `data.timestamp` | sztring | Az erőforrás Azure Policy általi vizsgálatának időpontja (UTC). Az események rendezéséhez használja ezt a tulajdonságot a legfelső szintű `eventTime` vagy a `time` Tulajdonságok helyett. |
| `data.policyAssignmentId` | sztring | A szabályzat-hozzárendelés erőforrás-azonosítója. |
| `data.policyDefinitionId` | sztring | A házirend-definíció erőforrás-azonosítója. |
| `data.policyDefinitionReferenceId` | sztring | A házirend-definíció hivatkozási azonosítója a kezdeményezési definícióban, ha a házirend-hozzárendelés egy kezdeményezéshez van hozzárendelve. Lehet üres. |
| `data.complianceState` | sztring | Az erőforrás megfelelőségi állapota a szabályzat-hozzárendelés tekintetében. |
| `data.subscriptionId` | sztring | Az erőforrás előfizetés-azonosítója. |
| `data.complianceReasonCode` | sztring | A megfelelőségi ok kódja. Lehet üres. |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Íme egy példa egy házirend állapotának módosítására szolgáló eseményre:

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

További információ: [Azure Policy Events Schema](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

Az Azure Policy eseményeket kezelő alkalmazásoknak az alábbi ajánlott eljárásokat kell követniük:

> [!div class="checklist"]
> - Több előfizetés is konfigurálható az események ugyanahhoz az eseménykezelőhöz való továbbítására, így az események nem feltételezik, hogy egy adott forrásból származnak. Ehelyett ellenőrizze az üzenet témakörét, és győződjön meg arról, hogy a szabályzat-hozzárendelés, a házirend-definíció és az erőforrás állapota módosult.
> - Győződjön `eventType` meg arról, hogy az összes kapott esemény a várt típus.
> - A használatával `data.timestamp` határozhatja meg az események sorrendjét Azure Policyban, a legfelső szintű `eventTime` vagy a `time` Tulajdonságok helyett.
> - A tárgy mezővel érheti el az erőforrást, amely a házirend állapotának módosítását használta.

## <a name="next-steps"></a>Következő lépések

További információ a Event Gridről és az állapot-változási események Azure Policyáról:

- [Az útvonal házirend-állapotának változási eseményei Event Grid az Azure CLI-vel](../tutorials/route-state-change-events.md)
- [Event Grid Azure Policy sémájának részletei](../../../event-grid/event-schema-policy.md)
- [Bevezetés az Event Grid használatába](../../../event-grid/overview.md)