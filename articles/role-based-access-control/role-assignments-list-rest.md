---
title: Azure-beli szerepkör-hozzárendelések listázása az REST API-Azure RBAC használatával
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férnek hozzá a REST API és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1d90e83405cf9c560f644277a04012571e1a5a9d
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556902"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Azure-beli szerepkör-hozzárendelések listázása a REST API használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Ez a cikk a szerepkör-hozzárendelések REST API használatával történő listázását ismerteti.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="list-role-assignments"></a>Szerepkör-hozzárendelések felsorolása

Az Azure RBAC a hozzáférés listázásához listázza a szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések listázásához használja a [szerepkör-hozzárendelések-lista](/rest/api/authorization/roleassignments/list) REST API-k egyikét. Az eredmények pontosításához meg kell adnia egy hatókört és egy opcionális szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet arra a hatókörre, amelyre a szerepkör-hozzárendeléseket szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

    Az előző példában a Microsoft. web egy olyan erőforrás-szolgáltató, amely egy App Service példányra hivatkozik. Hasonlóképpen használhatja bármely más erőforrás-szolgáltatót, és megadhatja a hatókört. További információ: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md) és támogatott [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md).  
     
1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a szerepkör-hozzárendelési lista szűréséhez.

    > [!div class="mx-tableFixed"]
    > | Szűrő | Leírás |
    > | --- | --- |
    > | `$filter=atScope()` | Csak a megadott hatókörhöz tartozó szerepkör-hozzárendelések felsorolása, nem tartalmazza a szerepkör-hozzárendeléseket az alhatókörben. |
    > | `$filter=assignedTo('{objectId}')` | Egy adott felhasználó vagy szolgáltatásnév szerepkör-hozzárendeléseinek felsorolása.<br/>Ha a felhasználó egy szerepkör-hozzárendeléssel rendelkező csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik. Ez a szűrő a csoportok esetében tranzitív, ami azt jelenti, hogy ha a felhasználó egy csoport tagja, és a csoport egy másik, szerepkör-hozzárendelést tartalmazó csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik.<br/>Ez a szűrő csak egy felhasználóhoz vagy egy egyszerű szolgáltatáshoz tartozó objektumazonosítót fogad el. Nem lehet átadni egy objektum AZONOSÍTÓját egy csoport számára. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Felsorolja a megadott felhasználóhoz vagy szolgáltatáshoz tartozó szerepkör-hozzárendeléseket, valamint a megadott hatókört. |
    > | `$filter=principalId+eq+'{objectId}'` | Egy adott felhasználó, csoport vagy egyszerű szolgáltatásnév szerepkör-hozzárendeléseinek felsorolása. |

A következő kérelem felsorolja a megadott felhasználóhoz tartozó összes szerepkör-hozzárendelést az előfizetés hatókörében:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

Az alábbi ábrán egy példa látható a kimenetre:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök kiosztása a REST API használatával](role-assignments-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)
