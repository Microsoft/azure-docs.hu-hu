---
title: Azure-szerepkörök kiosztása az REST API-Azure RBAC
description: Ismerje meg, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatások vagy felügyelt identitások számára a REST API és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: d012173adb5e238282e107b832ed9c6895237e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556072"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Azure-szerepkörök kiosztása a REST API használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket a REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Azure-szerepkör kiosztása

Egy szerepkör hozzárendeléséhez használja a [szerepkör-hozzárendeléseket – hozzon létre](/rest/api/authorization/roleassignments/create) REST API, és adja meg a rendszerbiztonsági tag, a szerepkör-definíció és a hatókört. Az API meghívásához hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/write` művelethez. A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-adminisztrátor](built-in-roles.md#user-access-administrator) kap hozzáférést ehhez a művelethez.

1. Használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API, vagy tekintse meg a [beépített szerepköröket](built-in-roles.md) a hozzárendelni kívánt szerepkör-definíció azonosítójának lekéréséhez.

1. Egy GUID-eszköz használatával állítson be egy egyedi azonosítót, amelyet a rendszer a szerepkör-hozzárendelési azonosítóhoz fog használni. Az azonosító formátuma: `00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és szövegtörzstel:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket a szerepkör-hozzárendelés hatókörére.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

    Az előző példában a Microsoft. web egy olyan erőforrás-szolgáltató, amely egy App Service példányra hivatkozik. Hasonlóképpen használhatja bármely más erőforrás-szolgáltatót, és megadhatja a hatókört. További információ: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md) és támogatott [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md).  

1. Cserélje le a *{roleAssignmentId}* helyére a szerepkör-hozzárendelés GUID azonosítóját.

1. A kérelem törzsében cserélje le a *{scope}* helyére a szerepkör-hozzárendelés hatókörét.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleDefinitionId}* helyére a szerepkör-definíció azonosítóját.

1. Cserélje le a *{principalId}* helyére annak a felhasználónak, csoportnak vagy egyszerű szolgáltatásnak az azonosítóját, amely hozzá lesz rendelve a szerepkörhöz.

A következő kérelem és törzs rendeli hozzá a [biztonságimásolat-olvasó](built-in-roles.md#backup-reader) szerepkört egy felhasználóhoz az előfizetés hatókörében:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Az alábbi ábrán egy példa látható a kimenetre:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="next-steps"></a>Következő lépések

- [Azure-beli szerepkör-hozzárendelések listázása a REST API használatával](role-assignments-list-rest.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)
- [Egyéni Azure-szerepkörök létrehozása vagy frissítése a REST API használatával](custom-roles-rest.md)
