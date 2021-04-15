---
title: Azure-szerepkörök hozzárendelése a REST API – Azure RBAC
description: Megtudhatja, hogyan adhat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, szolgáltatásnév vagy felügyelt identitás számára a REST API és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 3baf44a4240b23b41ce2e80dc22dbda4c7d0672a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363716"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Azure-szerepkörök hozzárendelése a REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Ez a cikk azt ismerteti, hogyan rendelhet szerepköröket a REST API.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Azure-szerepkör hozzárendelése

Szerepkör hozzárendeléshez használja [](/rest/api/authorization/roleassignments/create) a Szerepkör-hozzárendelések – Létrehozás REST API a rendszerbiztonsági tag, a szerepkör-definíció és a hatókör megadását. Az API hívásához hozzáféréssel kell lennie a `Microsoft.Authorization/roleAssignments/write` művelethez. A beépített szerepkörökből csak [a](built-in-roles.md#owner) Tulajdonos és a Felhasználói [hozzáférés](built-in-roles.md#user-access-administrator) rendszergazdája kap hozzáférést ehhez a művelethez.

1. Használja a [Szerepkör-definíciók – Lista](/rest/api/authorization/roledefinitions/list) REST API vagy tekintse meg a Beépített szerepkörök listában a hozzárendelni kívánt [szerepkör-definíció](built-in-roles.md) azonosítójának lekért azonosítóját.

1. A GUID eszközzel hozzon létre egy egyedi azonosítót, amely a szerepkör-hozzárendelési azonosítóhoz lesz használva. Az azonosító formátuma a következő: `00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és törzstel:

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

1. Az URI-ben cserélje *le a {scope}* helyére a szerepkör-hozzárendelés hatókörét.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

    Az előző példában a microsoft.web egy erőforrás-szolgáltató, amely egy App Service hivatkozik. Hasonlóképpen bármely más erőforrás-szolgáltatót is használhat, és megadhatja a hatókört. További információ: Azure erőforrás-szolgáltatók és [-típusok,](../azure-resource-manager/management/resource-providers-and-types.md) [valamint támogatott Azure-erőforrás-szolgáltatói műveletek.](resource-provider-operations.md)  

1. Cserélje *le a {roleAssignmentId}* helyére a szerepkör-hozzárendelés GUID-azonosítóját.

1. A kérelem törzsében cserélje *le a {scope}* helyére a szerepkör-hozzárendelés hatókörét.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

1. Cserélje *le a {roleDefinitionId} helyére* a szerepkör-definíció azonosítóját.

1. Cserélje *le a {principalId}* helyére a szerepkörhöz hozzárendelni fog felhasználó, csoport vagy szolgáltatásnév objektumazonosítóját.

A következő kérés és [](built-in-roles.md#backup-reader) törzs hozzárendeli a Biztonságimásolat-olvasó szerepkört egy felhasználóhoz az előfizetés hatókörében:

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

Az alábbiakban egy példa látható a kimenetre:

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

### <a name="new-service-principal"></a>Új szolgáltatásnév

Ha létrehoz egy új szolgáltatásnévt, és azonnal megpróbál hozzárendelni egy szerepkört a szolgáltatásnévhez, a szerepkör-hozzárendelés bizonyos esetekben meghiúsulhat. Ha például létrehoz egy új felügyelt identitást, majd megpróbál hozzárendelni egy szerepkört a szolgáltatásnévhez, előfordulhat, hogy a szerepkör-hozzárendelés sikertelen lesz. A hiba oka valószínűleg a replikáció késése. A szolgáltatásnév egy régióban jön létre; A szerepkör-hozzárendelés azonban egy másik régióban is előfordulhat, amely még nem replikálta a szolgáltatásnévvel.

A forgatókönyv megoldásához használja a Szerepkör-hozzárendelések [–](/rest/api/authorization/roleassignments/create) Létrehozás REST API és állítsa a tulajdonságot a `principalType` következőre: `ServicePrincipal` . A vagy újabb `apiVersion` beállítását is `2018-09-01-preview` be kell állítania.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Következő lépések

- [Azure-beli szerepkör-hozzárendelések felsorolása a REST API](role-assignments-list-rest.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)
- [Egyéni Azure-szerepkörök létrehozása vagy frissítése a REST API](custom-roles-rest.md)
