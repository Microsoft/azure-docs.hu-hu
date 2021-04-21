---
title: Azure-beli szerepkör-definíciók felsorolása – Azure RBAC
description: Megtudhatja, hogyan sorolja fel az Azure beépített és egyéni szerepköreit a Azure Portal, Azure PowerShell, az Azure CLI vagy az REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/26/2021
ms.author: rolyon
ms.openlocfilehash: b285755d24cdbf1f8ef06eb850fc218a00734f16
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771730"
---
# <a name="list-azure-role-definitions"></a>Azure-beli szerepkör-definíciók felsorolása

A szerepkör-definíciók olyan engedélyek gyűjteményei, amelyek elvégezhetők, például olvasás, írás és törlés. Ezt általában csak szerepkörnek nevezik. [Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) több mint 120 [](built-in-roles.md) beépített szerepkört biztosít, de saját egyéni szerepköröket is létrehozhat. Ez a cikk az Azure-erőforrásokhoz való hozzáférés megadásához használható beépített és egyéni szerepkörök felsorolását ismerteti.

A rendszergazdai szerepkörök listájának Azure Active Directory lásd: Rendszergazdai szerepkör engedélyei a [Azure Active Directory.](../active-directory/roles/permissions-reference.md)

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Az összes szerepkör felsorolása

Kövesse az alábbi lépéseket a lista összes szerepkörének Azure Portal.

Ha meg szeretne jelenni egy frissített szerepkör-felhasználói élményt, tekintse meg a Szerepkörök **(előzetes verzió)** lapot, amely jelenleg nyilvános előzetes verzióban érhető el. A **Szerepkörök (előzetes verzió)** lap néhány  további funkcióval együtt ugyanazt a szerepkörlistát jeleníti meg, mint a Szerepkörök lap. A szerepkörök bármelyikét használhatja a szerepkörökkel való munkához, azonban ha egyéni szerepköröket hoz létre vagy töröl, előfordulhat, hogy manuálisan kell frissítenie az oldalt a legújabb módosításokhoz.

#### <a name="roles"></a>[Szerepkörök](#tab/roles/)

1. A Azure Portal kattintson a **Minden szolgáltatás elemre,** majd válasszon ki egy hatókört. Kiválaszthat például felügyeleti csoportokat, **előfizetéseket,** **erőforráscsoportokat vagy** egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson **a Szerepkörök** fülre az összes beépített és egyéni szerepkör listájának a listájának a listájához.

   Az egyes szerepkörhöz rendelt felhasználók és csoportok számát az aktuális hatókörben láthatja.

   ![Szerepkörök listája](./media/role-definitions-list/roles-list-current.png)

#### <a name="roles-preview"></a>[Szerepkörök (előzetes verzió)](#tab/roles-preview/)

1. A Azure Portal kattintson a **Minden szolgáltatás elemre,** majd válasszon ki egy hatókört. Kiválaszthat például felügyeleti csoportokat, **előfizetéseket,** **erőforráscsoportokat vagy** egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson **a Szerepkörök (előzetes verzió)** fülre az összes beépített és egyéni szerepkör listájának megtekintéséhez.

   ![Szerepkörök listája az előzetes verzióval](./media/role-definitions-list/roles-list.png)

1. Egy adott szerepkör engedélyeinek megtekintéséhez  kattintson a Részletek oszlopBan található Megtekintés **hivatkozásra.**

    Megjelenik az Engedélyek panel.

1. Kattintson az **Engedélyek lapra** a kiválasztott szerepkör engedélyeinek megtekintéséhez és kereséshez.

   ![Szerepkör-engedélyek az előzetes verzió használata esetén](./media/role-definitions-list/role-permissions.png)

---

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Az összes szerepkör felsorolása

A szerepkör összes szerepkörének listához Azure PowerShell [Get-AzRoleDefinition használatával.](/powershell/module/az.resources/get-azroledefinition)

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

### <a name="list-a-role-definition"></a>Szerepkör-definíciók felsorolása

Egy adott szerepkör részleteinek listához használja a [Get-AzRoleDefinition adatokat.](/powershell/module/az.resources/get-azroledefinition)

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

### <a name="list-a-role-definition-in-json-format"></a>Szerepkör-definíció listázása JSON formátumban

Egy szerepkör JSON formátumban való listázásához használja a [Get-AzRoleDefinition parancsokat.](/powershell/module/az.resources/get-azroledefinition)

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

### <a name="list-permissions-of-a-role-definition"></a>Szerepkör-definíció engedélyeinek felsorolása

Egy adott szerepkör engedélyeinek listához használja a [Get-AzRoleDefinition használhatja a következőt:](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-all-roles"></a>Az összes szerepkör felsorolása

Az Azure CLI összes szerepkörének listához használja [az az role definition list et.](/cli/azure/role/definition#az_role_definition_list)

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

### <a name="list-a-role-definition"></a>Szerepkör-definíció listába sorolva

Egy szerepkör részleteinek listához használja az [az role definition list et.](/cli/azure/role/definition#az_role_definition_list)

```azurecli
az role definition list --name {roleName}
```

Az alábbi példa a Közreműködő *szerepkör-definíciót* sorolja fel:

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

### <a name="list-permissions-of-a-role-definition"></a>Szerepkör-definíció engedélyeinek felsorolása

Az alábbi példa csak a *műveleteket sorolja fel,* a Közreműködő *szerepkört* *nem.*

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

Az alábbi példa csak a Virtuális gépek közreműködője szerepkör *műveletét sorolja* fel.

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

A szerepkör-definíciók listához használja a [Szerepkör-definíciók – Lista REST API.](/rest/api/authorization/roledefinitions/list) Az eredmények finomításhoz meg kell adnia egy hatókört és egy nem kötelező szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. Az URI-ben cserélje le a *{scope}* helyére azt a hatókört, amelyhez fel szeretné sorolni a szerepkör-definíciókat.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

    Az előző példában a microsoft.web egy erőforrás-szolgáltató, amely egy App Service hivatkozik. Hasonlóképpen bármely más erőforrás-szolgáltatót is használhat, és megadhatja a hatókört. További információ: [Azure-erőforrás-szolgáltatók és](../azure-resource-manager/management/resource-providers-and-types.md) -típusok, valamint támogatott [Azure-erőforrás-szolgáltatói műveletek.](resource-provider-operations.md)  
     
1. Cserélje *le a {filter}* helyére a szerepkör-definíciók listájának szűréséhez alkalmazni kívánt feltételt.

    > [!div class="mx-tableFixed"]
    > | Szűrő | Description |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Felsorolja a megadott hatókör és alhatókör szerepkör-definícióit. |
    > | `$filter=type+eq+'{type}'` | Felsorolja a megadott típusú szerepkör-definíciókat. A szerepkör típusa lehet `CustomRole` vagy `BuiltInRole` . |

A következő kérés az előfizetés hatókörében található egyéni szerepkör-definíciókat sorolja fel:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

Az alábbiakban egy példa látható a kimenetre:

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

### <a name="list-a-role-definition"></a>Szerepkör-definíció listába sorolva

Egy adott szerepkör részleteinek listához használja a Szerepkör-definíciók – Get vagy Role [Definitions](/rest/api/authorization/roledefinitions/get) [– Get By Id](/rest/api/authorization/roledefinitions/getbyid) (Szerepkör-definíciók – Beszerepkedő azonosító alapján) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Címtárszintű szerepkör-definíciókhoz a következő kérést használhatja:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-ben cserélje *le a {scope}* helyére azt a hatókört, amelyhez fel szeretné sorolni a szerepkör-definíciót.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |
     
1. Cserélje *le a {roleDefinitionId} helyére* a szerepkör-definíció azonosítóját.

A következő kérés az Olvasó szerepkör [definícióját sorolja](built-in-roles.md#reader) fel:

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

Az alábbiakban egy példát mutatunk be a kimenetre:

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
- [Azure-beli szerepkör-hozzárendelések felsorolása a Azure Portal](role-assignments-list-portal.md)
- [Azure-szerepkörök hozzárendelése a Azure Portal](role-assignments-portal.md)
