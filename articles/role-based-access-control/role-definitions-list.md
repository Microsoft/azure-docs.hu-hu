---
title: Azure-szerepkör-definíciók listázása – Azure RBAC
description: Ismerje meg, hogyan listázhatja az Azure beépített és egyéni szerepköreit Azure Portal, Azure PowerShell, Azure CLI vagy REST API használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/26/2021
ms.author: rolyon
ms.openlocfilehash: f354e3bb7fc0f7ced17d43acacf29c726ce1329c
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629156"
---
# <a name="list-azure-role-definitions"></a>Azure-szerepkör-definíciók listázása

A szerepkör-definíció az engedélyek olyan gyűjteménye, amely elvégezhető, például olvasás, írás és törlés. Általában csak szerepkörnek nevezik. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) több mint 120 [beépített szerepkörrel](built-in-roles.md) rendelkezik, vagy létrehozhat saját egyéni szerepköröket is. Ez a cikk az Azure-erőforrásokhoz való hozzáférés biztosításához használható beépített és egyéni szerepkörök listázását ismerteti.

A Azure Active Directory rendszergazdai szerepköreinek megtekintéséhez tekintse [meg a Azure Active Directory rendszergazdai szerepkör engedélyei](../active-directory/roles/permissions-reference.md)című témakört.

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Kövesse az alábbi lépéseket a Azure Portal összes szerepkörének listázásához.

Ha érdekli a frissített szerepkörök megtekintése, tekintse meg a jelenleg nyilvános előzetes verzióban elérhető **szerepkörök (előzetes verzió)** lapot. A **szerepkörök (előzetes verzió)** lapon ugyanazok a szerepkörök jelennek meg, mint a **szerepkörök lap,** néhány további funkcióval. Használhatja a szerepkörök fület is a szerepkörökkel való együttműködéshez, azonban ha egyéni szerepköröket hoz létre vagy töröl, előfordulhat, hogy manuálisan kell frissítenie a lapot a legutóbbi módosítások megtekintéséhez.

#### <a name="roles"></a>[Szerepkörök](#tab/roles/)

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válasszon ki egy hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat** vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **szerepkörök** fülre az összes beépített és egyéni szerepkör listájának megtekintéséhez.

   Az aktuális hatókörben az egyes szerepkörökhöz hozzárendelt felhasználók és csoportok számát láthatja.

   ![Szerepkörök listája](./media/role-definitions-list/roles-list-current.png)

#### <a name="roles-preview"></a>[Szerepkörök (előzetes verzió)](#tab/roles-preview/)

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válasszon ki egy hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat** vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A beépített és az egyéni szerepkörök listájának megtekintéséhez kattintson a **szerepkörök (előzetes verzió)** fülre.

   ![Szerepkörök listája az előzetes verzió használatával](./media/role-definitions-list/roles-list.png)

1. Egy adott szerepkör engedélyeinek megtekintéséhez kattintson a **nézet** hivatkozásra a **részletek** oszlopban.

    Megjelenik az engedélyek panel.

1. Kattintson az **engedélyek** lapra a kiválasztott szerepkör engedélyeinek megtekintéséhez és kereséséhez.

   ![Szerepkör-engedélyek az előzetes verzió használatával](./media/role-definitions-list/role-permissions.png)

---

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Azure PowerShell összes szerepkörének listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

Egy adott szerepkör részleteinek listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Szerepkör-definíciók listázása JSON formátumban

Ha JSON formátumú szerepkört szeretne listázni, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Szerepkör-definíció engedélyeinek listázása

Egy adott szerepkör engedélyeinek listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Az Azure CLI összes szerepkörének listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list
```

Az alábbi példa felsorolja az összes elérhető szerepkör-definíció nevét és leírását:

```azurecli
az role definition list --output json --query '[].{roleName:roleName, description:description}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role"
  },

  ...

]
```

Az alábbi példa felsorolja az összes beépített szerepkört.

```azurecli
az role definition list --custom-role-only false --output json --query '[].{roleName:roleName, description:description, roleType:roleType}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role",
    "roleType": "BuiltInRole"
  },
  
  ...

]
```

### <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

Egy szerepkör részleteinek listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget.

```azurecli
az role definition list --name {roleName}
```

Az alábbi példa a *közreműködő* szerepkör definícióját sorolja fel:

```azurecli
az role definition list --name "Contributor"
```

```json
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action",
          "Microsoft.Blueprint/blueprintAssignments/write",
          "Microsoft.Blueprint/blueprintAssignments/delete"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Szerepkör-definíció engedélyeinek listázása

Az alábbi példa csak a közreműködő szerepkör *műveleteit* *és a* *hozzájuk* tartozó tevékenységeket sorolja fel.

```azurecli
az role definition list --name "Contributor" --output json --query '[].{actions:permissions[0].actions, notActions:permissions[0].notActions}'
```

```json
[
  {
    "actions": [
      "*"
    ],
    "notActions": [
      "Microsoft.Authorization/*/Delete",
      "Microsoft.Authorization/*/Write",
      "Microsoft.Authorization/elevateAccess/Action",
      "Microsoft.Blueprint/blueprintAssignments/write",
      "Microsoft.Blueprint/blueprintAssignments/delete"
    ]
  }
]
```

A következő példa csak a *virtuális gép közreműködői* szerepkörének műveleteit sorolja fel.

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/locations/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/virtualMachineScaleSets/*",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.DevTestLab/schedules/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
    "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

    ...

    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Storage/storageAccounts/read",
    "Microsoft.Support/*"
  ]
]
```

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>Szerepkör-definíciók felsorolása

A szerepkör-definíciók listázásához használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API. Az eredmények pontosításához meg kell adnia egy hatókört és egy opcionális szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet arra a hatókörre, amelyre a szerepkör-definíciókat szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

    Az előző példában a Microsoft. web egy olyan erőforrás-szolgáltató, amely egy App Service példányra hivatkozik. Hasonlóképpen használhatja bármely más erőforrás-szolgáltatót, és megadhatja a hatókört. További információ: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md) és támogatott [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md).  
     
1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a szerepkör-definíciós lista szűréséhez.

    > [!div class="mx-tableFixed"]
    > | Szűrő | Description |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Felsorolja a megadott hatókör és az alhatókörek szerepkör-definícióit. |
    > | `$filter=type+eq+'{type}'` | Felsorolja a megadott típusú szerepkör-definíciókat. A szerepkör típusa lehet `CustomRole` vagy `BuiltInRole` . |

Az alábbi kérelem felsorolja az egyéni szerepkör-definíciókat az előfizetés hatókörében:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

Az alábbi ábrán egy példa látható a kimenetre:

```json
{
    "value": [
        {
            "properties": {
                "roleName": "Billing Reader Plus",
                "type": "CustomRole",
                "description": "Read billing data and download invoices",
                "assignableScopes": [
                    "/subscriptions/{subscriptionId1}"
                ],
                "permissions": [
                    {
                        "actions": [
                            "Microsoft.Authorization/*/read",
                            "Microsoft.Billing/*/read",
                            "Microsoft.Commerce/*/read",
                            "Microsoft.Consumption/*/read",
                            "Microsoft.Management/managementGroups/read",
                            "Microsoft.CostManagement/*/read",
                            "Microsoft.Billing/invoices/download/action",
                            "Microsoft.CostManagement/exports/*"
                        ],
                        "notActions": [
                            "Microsoft.CostManagement/exports/delete"
                        ]
                    }
                ],
                "createdOn": "2020-02-21T04:49:13.7679452Z",
                "updatedOn": "2020-02-21T04:49:13.7679452Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId1}",
            "type": "Microsoft.Authorization/roleDefinitions",
            "name": "{roleDefinitionId1}"
        }
    ]
}
```

### <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

Egy adott szerepkör részleteinek listázásához használja a [Get](/rest/api/authorization/roledefinitions/get) vagy a szerepkör-definíciókat – [get by id](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    A címtár-szintű szerepkör-definícióhoz a következő kérelem használható:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet arra a hatókörre, amelyre a szerepkör-definíciót szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |
     
1. Cserélje le a *{roleDefinitionId}* helyére a szerepkör-definíció azonosítóját.

A következő kérelem felsorolja az [olvasói](built-in-roles.md#reader) szerepkör definícióját:

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

Az alábbi ábrán egy példa látható a kimenetre:

```json
{
    "properties": {
        "roleName": "Reader",
        "type": "BuiltInRole",
        "description": "Lets you view everything, but not make any changes.",
        "assignableScopes": [
            "/"
        ],
        "permissions": [
            {
                "actions": [
                    "*/read"
                ],
                "notActions": []
            }
        ],
        "createdOn": "2015-02-02T21:55:09.8806423Z",
        "updatedOn": "2019-02-05T21:24:35.7424745Z",
        "createdBy": null,
        "updatedBy": null
    },
    "id": "/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
}
```

## <a name="next-steps"></a>Következő lépések

- [Beépített Azure-szerepkörök](built-in-roles.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Azure-beli szerepkör-hozzárendelések listázása a Azure Portal használatával](role-assignments-list-portal.md)
- [Azure-szerepkörök kiosztása a Azure Portal használatával](role-assignments-portal.md)
