---
title: ResourceSelector FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Solutions. ResourceSelector felhasználói felületi elemének ismertetése. A meglévő erőforrások listájának beolvasására szolgál.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87099055"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Microsoft. Solutions. ResourceSelector FELHASZNÁLÓIFELÜLET-elem

A ResourceSelector lehetővé teszi, hogy a felhasználók kiválasszon egy meglévő erőforrást egy előfizetésből.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft. Solutions. ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>Séma

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>Példakimenet

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>Megjegyzések

A `resourceType` tulajdonságban adja meg a listában megjeleníteni kívánt erőforrás erőforrás-szolgáltatói névterét és erőforrástípus nevét.

A `filter` tulajdonság korlátozza az erőforrások rendelkezésre álló beállításait. Az eredményeket hely vagy előfizetés alapján korlátozhatja. Ha csak azokat az erőforrásokat szeretné megjeleníteni, amelyek megfelelnek az alapvető beállításoknak, használja az elemet `onBasics` . Az összes erőforrás megjelenítéséhez használja a következőt: `all` . Az alapértelmezett érték `all`.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
