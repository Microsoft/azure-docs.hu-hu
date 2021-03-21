---
title: Azure-beli szerepkör-hozzárendelések eltávolítása – Azure RBAC
description: Megtudhatja, hogyan távolíthatja el a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások Azure-erőforrásaihoz való hozzáférést Azure Portal, Azure PowerShell, Azure CLI vagy REST API használatával.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561380"
---
# <a name="remove-azure-role-assignments"></a>Azure-beli szerepkör-hozzárendelések eltávolítása

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../articles/role-based-access-control/overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésére használt engedélyezési rendszer. Az Azure-erőforrásokhoz való hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést. Ez a cikk azt ismerteti, hogyan távolíthatja el a szerepkörök hozzárendeléseit a Azure Portal, a Azure PowerShell, az Azure CLI és a REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

A szerepkör-hozzárendelések eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) vagy a [tulajdonos](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol el szeretné távolítani a hozzáférést.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![A szerepkör-hozzárendelés eltávolításra van kijelölve](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Kattintson az **Eltávolítás** elemre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-remove/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen** gombra.

    Ha olyan üzenet jelenik meg, amely szerint az örökölt szerepkör-hozzárendelések nem távolíthatók el, a rendszer megpróbálja eltávolítani a szerepkör-hozzárendelést egy alárendelt hatókörben. Nyissa meg a hozzáférés-vezérlés (IAM) részt azon a hatókörön, ahol a szerepkör hozzá lett rendelve, és próbálkozzon újra. A hozzáférés-vezérlés (IAM) megfelelő hatókörben való megnyitásának gyorsan megtekinthető a **hatókör** oszlop, és a mellette lévő hivatkozásra kattintva **(örökölt)**.

   ![Szerepkör-hozzárendelési üzenet eltávolítása az örökölt szerepkör-hozzárendelésekhez](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell a [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)használatával eltávolít egy szerepkör-hozzárendelést.

Az alábbi példa eltávolítja a [virtuálisgép-közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelést a *patlong \@ contoso.com* -felhasználótól a *Pharma-Sales* erőforráscsoporthoz:

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Eltávolítja az [olvasó](built-in-roles.md#reader) szerepkört az *Ann Mack Team* CSOPORTból az 22222222-2222-2222-2222-222222222222 azonosítóval egy előfizetési hatókörben.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Eltávolítja a [Számlázási olvasó](built-in-roles.md#billing-reader) szerepkört az *Alain \@ example.com* -felhasználótól a felügyeleti csoport hatókörében.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Ha a következő hibaüzenet jelenik meg: "a megadott információk nem képeznek szerepkör-hozzárendelést", győződjön meg arról, hogy a vagy a paramétereket is megadja `-Scope` `-ResourceGroupName` . További információ: az [Azure RBAC hibáinak megoldása](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben eltávolíthatja a szerepkör-hozzárendelést az [az szerepkör-hozzárendelés törlése](/cli/azure/role/assignment#az_role_assignment_delete)paranccsal.

Az alábbi példa eltávolítja a [virtuálisgép-közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelést a *patlong \@ contoso.com* -felhasználótól a *Pharma-Sales* erőforráscsoporthoz:

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Eltávolítja az [olvasó](built-in-roles.md#reader) szerepkört az *Ann Mack Team* CSOPORTból az 22222222-2222-2222-2222-222222222222 azonosítóval egy előfizetési hatókörben.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Eltávolítja a [Számlázási olvasó](built-in-roles.md#billing-reader) szerepkört az *Alain \@ example.com* -felhasználótól a felügyeleti csoport hatókörében.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST API

A REST API szerepkör-hozzárendelések használatával távolítja el a szerepkör [-](/rest/api/authorization/roleassignments/delete)hozzárendelést.

1. A szerepkör-hozzárendelési azonosító (GUID) beolvasása. Ezt az azonosítót a rendszer a szerepkör-hozzárendelés első létrehozásakor adja vissza, vagy pedig a szerepkör-hozzárendelések listázásával.

1. Kezdje a következő kéréssel:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet a szerepkör-hozzárendelés eltávolítására szolgáló hatókörre.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleAssignmentId}* helyére a szerepkör-hozzárendelés GUID azonosítóját.

A következő kérelem eltávolítja a megadott szerepkör-hozzárendelést az előfizetés hatókörében:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Az alábbi ábrán egy példa látható a kimenetre:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>ARM-sablon

Nem lehet eltávolítani egy szerepkör-hozzárendelést egy Azure Resource Manager sablonnal (ARM-sablon). Szerepkör-hozzárendelés eltávolításához más eszközöket kell használnia, például a Azure Portal, Azure PowerShell, az Azure CLI vagy a REST API.

## <a name="next-steps"></a>Következő lépések

- [Azure-beli szerepkör-hozzárendelések listázása a Azure Portal használatával](role-assignments-list-portal.md)
- [Azure-beli szerepkör-hozzárendelések listázása Azure PowerShell használatával](role-assignments-list-powershell.md)
- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)
