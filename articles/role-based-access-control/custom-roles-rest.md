---
title: Egyéni Azure-szerepkörök létrehozása vagy frissítése a REST API-Azure RBAC használatával
description: Megtudhatja, hogyan listázhat, hozhat létre, frissíthet vagy törölhet egyéni Azure-szerepköröket a REST API és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ab64765fc4e329c8edbf7a4db813113d20d476b3
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557512"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Egyéni Azure-szerepkörök létrehozása vagy frissítése a REST API használatával

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. Ez a cikk az egyéni szerepkörök listázását, létrehozását, frissítését és törlését ismerteti a REST API használatával.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A címtárban lévő összes egyéni szerepkör listázásához használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le a *{Filter}* értéket a szerepkör típusára.

    > [!div class="mx-tableFixed"]
    > | Szűrő | Leírás |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Szűrés a CustomRole típusa alapján |

## <a name="list-custom-roles-at-a-scope"></a>Hatókörhöz tartozó egyéni szerepkörök listázása

Egy hatókörben lévő egyéni szerepkörök listázásához használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre a szerepköröket szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Erőforrás |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le a *{Filter}* értéket a szerepkör típusára.

    > [!div class="mx-tableFixed"]
    > | Szűrő | Leírás |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Szűrés a CustomRole típusa alapján |

## <a name="list-a-custom-role-definition-by-name"></a>Egyéni szerepkör-definíciók listázása név alapján

Ha a megjelenített nevével szeretne információt kapni egy egyéni szerepkörről, használja a [szerepkör-definíciókat – get](/rest/api/authorization/roledefinitions/get) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre a szerepköröket szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Erőforrás |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le a *{Filter}* helyére a szerepkör megjelenített nevét.

    > [!div class="mx-tableFixed"]
    > | Szűrő | Leírás |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Használja a szerepkör pontos megjelenítendő neve URL-kódolású formáját. Például: `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Egyéni szerepkör-definíció listázása azonosító alapján

Ha egyedi azonosítóval szeretne adatokat lekérni egy egyéni szerepkörről, használja a [szerepkör-definíciókat – get](/rest/api/authorization/roledefinitions/get) REST API.

1. Használja a szerepkör [-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API a szerepkör GUID azonosítójának lekéréséhez.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre a szerepköröket szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Erőforrás |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le a *{roleDefinitionId}* helyére a szerepkör-definíció GUID azonosítóját.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához használja a [szerepkör-definíciókat – hozzon létre vagy frissítsen](/rest/api/authorization/roledefinitions/createorupdate) REST API. Az API meghívásához olyan felhasználóval kell bejelentkeznie, akinek van olyan szerepköre, amely `Microsoft.Authorization/roleDefinitions/write` jogosult az összesre `assignableScopes` . A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-kezelő rendszergazda](built-in-roles.md#user-access-administrator) adja meg ezt az engedélyt.

1. Tekintse át azon [erőforrás-szolgáltatói műveletek](resource-provider-operations.md) listáját, amelyek elérhetők az egyéni szerepkör engedélyeinek létrehozásához.

1. Egy GUID-eszköz használatával állítson be egy egyedi azonosítót, amelyet a rendszer az egyéni szerepkör-azonosítóhoz fog használni. Az azonosító formátuma: `00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és szövegtörzstel:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet az `assignableScopes` Egyéni szerepkör első helyére.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le a *{roleDefinitionId}* helyére az egyéni szerepkör GUID azonosítóját.

1. A kérelem törzsén belül cserélje le a *{roleDefinitionId}* azonosítót a GUID azonosítóra.

1. Ha `assignableScopes` az egy előfizetés vagy erőforráscsoport, cserélje le a *(z) {subscriptionId}* vagy *{resourceGroup}* példányokat az azonosítóra.

1. Ha a `assignableScopes` felügyeleti csoport, cserélje le a *{GroupID}* példányt a felügyeleti csoport azonosítójával. A felügyeleti csoport hozzáadása a `assignableScopes` jelenleg előzetes verzióban érhető el.

1. A `actions` tulajdonságban adja hozzá azokat a műveleteket, amelyeket a szerepkör engedélyez.

1. A `notActions` tulajdonságban adja hozzá azokat a műveleteket, amelyek ki vannak zárva az engedélyezettből `actions` .

1. A `roleName` és a `description` Tulajdonságok területen adjon meg egy egyedi szerepkör-nevet és egy leírást. A tulajdonságokkal kapcsolatos további információkért lásd: [Egyéni Azure-szerepkörök](custom-roles.md).

    A következő példa egy kérelem törzsét mutatja be:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez használja a [szerepkör-definíciók – létrehozás vagy frissítés](/rest/api/authorization/roledefinitions/createorupdate) REST API. Az API meghívásához olyan felhasználóval kell bejelentkeznie, akinek van olyan szerepköre, amely `Microsoft.Authorization/roleDefinitions/write` jogosult az összesre `assignableScopes` . A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-kezelő rendszergazda](built-in-roles.md#user-access-administrator) adja meg ezt az engedélyt.

1. Használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) vagy a [szerepkör-definíciók – Get](/rest/api/authorization/roledefinitions/get) REST API az egyéni szerepkörre vonatkozó információk lekéréséhez. További információ: az [Egyéni szerepkörök korábbi listája](#list-custom-roles) szakasz.

1. Kezdje a következő kéréssel:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet az `assignableScopes` Egyéni szerepkör első helyére.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le a *{roleDefinitionId}* helyére az egyéni szerepkör GUID azonosítóját.

1. Az egyéni szerepkörre vonatkozó információk alapján hozzon létre egy kérelem törzsét a következő formátumban:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Frissítse a kérés törzsét az egyéni szerepkörbe felvenni kívánt módosításokkal.

    Az alábbi példa egy olyan kérés törzsét mutatja be, amelyben új diagnosztikai beállításokkal kapcsolatos művelet lett hozzáadva:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja a [szerepkör-definíciókat – törölje](/rest/api/authorization/roledefinitions/delete) REST API. Az API meghívásához olyan felhasználóval kell bejelentkeznie, akinek van olyan szerepköre, amely `Microsoft.Authorization/roleDefinitions/delete` jogosult az összesre `assignableScopes` . A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-kezelő rendszergazda](built-in-roles.md#user-access-administrator) adja meg ezt az engedélyt.

1. Használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) vagy a [szerepkör-definíciók – Get](/rest/api/authorization/roledefinitions/get) REST API az egyéni szerepkör GUID azonosítójának beszerzéséhez. További információ: az [Egyéni szerepkörök korábbi listája](#list-custom-roles) szakasz.

1. Kezdje a következő kéréssel:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelynek az egyéni szerepkörét törölni szeretné.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le a *{roleDefinitionId}* helyére az egyéni szerepkör GUID azonosítóját.

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Azure-szerepkörök kiosztása a REST API használatával](role-assignments-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)
